---
title: "diseños enlazables en Xamarin.Forms " Description: "los diseños enlazables permiten a las clases de diseño generar su contenido enlazando con una colección de elementos, con la opción de establecer la apariencia de cada elemento con un DataTemplate".
MS. Prod: Xamarin ms. AssetID: 824C3319-20A0-42D0-8632-CDECD98349C3 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/09/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="bindable-layouts-in-xamarinforms"></a>Diseños enlazables enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Los diseños enlazables habilitan cualquier clase de diseño que derive de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase para generar su contenido enlazando con una colección de elementos, con la opción de establecer la apariencia de cada elemento con [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Los diseños enlazables se proporcionan mediante la `BindableLayout` clase, que expone las siguientes propiedades adjuntas:

- `ItemsSource`: especifica la colección de `IEnumerable` elementos que se mostrarán en el diseño.
- `ItemTemplate`: especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados por el diseño.
- `ItemTemplateSelector`: especifica el [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

> [!NOTE]
> La `ItemTemplate` propiedad tiene prioridad cuando `ItemTemplate` `ItemTemplateSelector` se establecen las propiedades y.

Además, la `BindableLayout` clase expone las siguientes propiedades enlazables:

- `EmptyView`: especifica la `string` vista o que se mostrará cuando la `ItemsSource` propiedad sea o `null` cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. El valor predeterminado es `null`.
- `EmptyViewTemplate`: especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se mostrará cuando la `ItemsSource` propiedad sea `null` o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. El valor predeterminado es `null`.

> [!NOTE]
> La `EmptyViewTemplate` propiedad tiene prioridad cuando `EmptyView` `EmptyViewTemplate` se establecen las propiedades y.

Todas estas propiedades se pueden adjuntar a [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) las [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) clases,, [`Grid`](xref:Xamarin.Forms.Grid) , [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) y [`StackLayout`](xref:Xamarin.Forms.StackLayout) , que se derivan de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase.

La `Layout<T>` clase expone una [`Children`](xref:Xamarin.Forms.Layout`1.Children) colección, a la que se agregan los elementos secundarios de un diseño. Cuando la `BinableLayout.ItemsSource` propiedad se establece en una colección de elementos y se adjunta a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase derivada de, cada elemento de la colección se agrega a la `Layout<T>.Children` colección para que la muestre el diseño. La `Layout<T>` clase derivada de actualizará sus vistas secundarias cuando cambie la colección subyacente. Para obtener más información sobre el Xamarin.Forms ciclo de diseño, vea [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Los diseños enlazables solo deben usarse cuando la colección de elementos que se va a mostrar es pequeña, y no se requiere desplazamiento y selección. Aunque el desplazamiento se puede proporcionar encapsulando un diseño enlazable en una [`ScrollView`](xref:Xamarin.Forms.ScrollView) , no se recomienda que los diseños enlazables carezcan de la virtualización de la interfaz de usuario. Cuando se requiere desplazamiento, [`ListView`](xref:Xamarin.Forms.ListView) se debe usar una vista desplazable que incluya la virtualización de la interfaz de usuario, como o [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Si no se observa esta recomendación, pueden producirse problemas de rendimiento.

> [!IMPORTANT]
> Aunque es técnicamente posible asociar un diseño enlazable a cualquier clase de diseño que deriva de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase, no siempre es práctico hacerlo, especialmente en el caso de las [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) clases, [`Grid`](xref:Xamarin.Forms.Grid) y [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Por ejemplo, considere el escenario en el que desea mostrar una colección de datos en [`Grid`](xref:Xamarin.Forms.Grid) con un diseño enlazable, donde cada elemento de la colección es un objeto que contiene varias propiedades. Cada fila de `Grid` debe mostrar un objeto de la colección, donde cada columna de `Grid` muestra una de las propiedades del objeto. Dado que [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para el diseño enlazable solo puede contener un único objeto, es necesario que ese objeto sea una clase de diseño que contenga varias vistas que muestren una de las propiedades del objeto en una `Grid` columna específica. Aunque este escenario se puede realizar con diseños enlazables, da como resultado un elemento primario `Grid` que contiene un elemento secundario `Grid` para cada elemento de la colección enlazada, lo que supone un uso muy ineficaz y problemático del `Grid` diseño.

## <a name="populate-a-bindable-layout-with-data"></a>Rellenar un diseño enlazable con datos

Un diseño enlazable se rellena con datos estableciendo su `ItemsSource` propiedad en cualquier colección que implemente `IEnumerable` y asócielo a una [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase derivada de:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

El código de C# equivalente es el siguiente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Cuando `BindableLayout.ItemsSource` se establece la propiedad adjunta en un diseño, pero `BindableLayout.ItemTemplate` no se establece la propiedad adjunta, se mostrarán todos los elementos de la colección en un objeto `IEnumerable` [`Label`](xref:Xamarin.Forms.Label) creado por la `BindableLayout` clase.

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento en el diseño enlazable se puede definir estableciendo la `BindableLayout.ItemTemplate` propiedad adjunta en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

En este ejemplo, se mostrarán todos los elementos de la `TopFollowers` colección mediante una `CircleImage` vista definida en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

![Diseño enlazable con un DataTemplate](bindable-layouts-images/top-followers.png "Diseño enlazable con una plantilla de datos")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en el diseño enlazable se puede elegir en tiempo de ejecución, en función del valor del elemento, estableciendo la `BindableLayout.ItemTemplateSelector` propiedad adjunta en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

El código de C# equivalente es el siguiente:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

El [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) usado en la aplicación de ejemplo se muestra en el ejemplo siguiente:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

La `TechItemTemplateSelector` clase define `DefaultTemplate` `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) las propiedades y que se establecen en distintas plantillas de datos. El `OnSelectTemplate` método devuelve `XamarinFormsTemplate` , que muestra un elemento en rojo oscuro con un corazón junto a él, cuando el elemento es igual a " Xamarin.Forms ". Cuando el elemento no es igual a " Xamarin.Forms ", el `OnSelectTemplate` método devuelve `DefaultTemplate` , que muestra un elemento con el color predeterminado de [`Label`](xref:Xamarin.Forms.Label) :

![Diseño enlazable con un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Diseño enlazable con un selector de plantillas de datos")

Para más información sobre los selectores de plantilla de datos, consulte [creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no estén disponibles

La `EmptyView` propiedad se puede establecer en una cadena, que se mostrará [`Label`](xref:Xamarin.Forms.Label) cuando la `ItemsSource` propiedad sea `null` , o cuando la colección especificada por la `ItemsSource` propiedad sea `null` o esté vacía. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

El resultado es que cuando la colección enlazada a datos es `null` , se muestra la cadena establecida como el valor de la `EmptyView` propiedad:

[![Captura de pantalla de una vista vacía de una cadena de diseño enlazable, en iOS y Android](bindable-layouts-images/emptyview-string.png "Vista vacía de cadena de diseño enlazable")](bindable-layouts-images/emptyview-string-large.png#lightbox "Vista vacía de cadena de diseño enlazable")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar vistas cuando los datos no están disponibles

La `EmptyView` propiedad se puede establecer en una vista, que se mostrará cuando la `ItemsSource` propiedad sea `null` o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo de XAML siguiente `EmptyView` se muestra la propiedad establecida en una vista que contiene varias vistas secundarias:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

El resultado es que cuando la colección enlazada a datos es `null` , [`StackLayout`](xref:Xamarin.Forms.StackLayout) se muestran y sus vistas secundarias.

[![Captura de pantalla de una vista vacía de diseño enlazable con varias vistas, en iOS y Android](bindable-layouts-images/emptyview-views.png "Vista vacía de diseño enlazable")](bindable-layouts-images/emptyview-views-large.png#lightbox "Vista vacía de diseño enlazable")

Del mismo modo, `EmptyViewTemplate` se puede establecer en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que se mostrará cuando la `ItemsSource` propiedad sea `null` , o cuando la colección especificada por la `ItemsSource` propiedad sea o esté `null` vacía. `DataTemplate`Puede contener una vista única o una vista que contiene varias vistas secundarias. Además, el `BindingContext` de se `EmptyViewTemplate` heredará del `BindingContext` de `BindableLayout` . En el siguiente ejemplo de XAML `EmptyViewTemplate` se muestra la propiedad establecida en un `DataTemplate` que contiene una sola vista:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

El resultado es que cuando la colección enlazada a datos es `null` , [`Label`](xref:Xamarin.Forms.Label) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se muestra en el:

[![Captura de pantalla de una plantilla de vista vacía de diseño enlazable, en iOS y Android](bindable-layouts-images/emptyviewtemplate.png "Plantilla de vista vacía de diseño enlazable")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Plantilla de vista vacía de diseño enlazable")

> [!NOTE]
> La `EmptyViewTemplate` propiedad no se puede establecer mediante [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) .

## <a name="choose-an-emptyview-at-runtime"></a>Elegir un EmptyView en tiempo de ejecución

Las vistas que se mostrarán como `EmptyView` cuando los datos no estén disponibles, se pueden definir como [`ContentView`](xref:Xamarin.Forms.ContentView) objetos en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . `EmptyView`A continuación, la propiedad se puede establecer en un específico `ContentView` , en función de alguna lógica de negocios, en tiempo de ejecución. En el código XAML siguiente se muestra un ejemplo de este escenario:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

El XAML define dos [`ContentView`](xref:Xamarin.Forms.ContentView) objetos en el nivel de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , con el [`Switch`](xref:Xamarin.Forms.Switch) objeto que controla qué `ContentView` objeto se establecerá como el valor de la `EmptyView` propiedad. Cuando `Switch` se alterna, el controlador de `OnEmptyViewSwitchToggled` eventos ejecuta el `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

El `ToggleEmptyView` método establece la `EmptyView` propiedad del `stackLayout` objeto en uno de los dos [`ContentView`](xref:Xamarin.Forms.ContentView) objetos almacenados en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , basándose en el valor de la [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propiedad. A continuación, cuando la colección enlazada a datos es `null` , `ContentView` se muestra el objeto establecido como la `EmptyView` propiedad:

[![Captura de pantalla de la opción Vista vacía en tiempo de ejecución, en iOS y Android](bindable-layouts-images/emptyview-runtime.png "Vista vacía del diseño enlazable, opción en tiempo de ejecución")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Vista vacía del diseño enlazable, opción en tiempo de ejecución")

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de diseño enlazable (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.FormsPlantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
