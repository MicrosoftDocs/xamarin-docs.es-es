---
title: "Xamarin.Forms: Enlaces relativos" description: "En este artículo se explica cómo crear enlaces relativos mediante el uso de la extensión de marcado RelativeSource para establecer el origen de enlace en relación con la posición del destino de enlace."
ms.prod: xamarin ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 12/04/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinforms-relative-bindings"></a>Enlaces relativos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Los enlaces relativos proporcionan la capacidad de establecer el origen de enlace en relación con la posición del destino de enlace. Se crean con la extensión de marcado `RelativeSource` y se establecen como la propiedad `Source` de una expresión de enlace.

La clase `RelativeSourceExtension` admite la extensión de marcado `RelativeSource`, que define las siguientes propiedades:

- `Mode`, del tipo `RelativeBindingSourceMode`, describe la ubicación del origen de enlace en relación con la posición del destino de enlace.
- `AncestorLevel`, del tipo `int`, un nivel de antecesor opcional que se va a buscar, cuando la propiedad `Mode` es `FindAncestor`. Un `AncestorLevel` de `n` omite `n-1` instancias del `AncestorType`.
- `AncestorType`, del tipo `Type`, el tipo de antecesor que se va a buscar, cuando la propiedad `Mode` es `FindAncestor`.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `RelativeSourceExtension` como `RelativeSource`.

La propiedad `Mode` se debe establecer en uno de los miembros de enumeración `RelativeBindingSourceMode`.

- `TemplatedParent` indica el elemento al que se aplica la plantilla, en el que existe el elemento enlazado. Para más información, vea [Enlace a un elemento primario con plantilla](#bind-to-a-templated-parent).
- `Self` indica el elemento en el que se está estableciendo el enlace, lo cual le permite enlazar una propiedad de ese elemento a otra propiedad del mismo elemento. Para más información, vea [Enlace a sí mismo](#bind-to-self).
- `FindAncestor` indica el antecesor en el árbol visual del elemento enlazado. Este modo se debe utilizar para enlazar a un control de antecesor representado por la propiedad `AncestorType`. Para más información, vea [Enlace a un antecesor](#bind-to-an-ancestor).
- `FindAncestorBindingContext` indica el `BindingContext` del antecesor en el árbol visual del elemento enlazado. Este modo se debe utilizar para enlazar al `BindingContext` de un antecesor representado por la propiedad `AncestorType`. Para más información, vea [Enlace a un antecesor](#bind-to-an-ancestor).

La propiedad `Mode` es la propiedad de contenido de la clase `RelativeSourceExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la parte `Mode=` de la expresión.

Para más información sobre las extensiones de marcado de Xamarin.Forms, vea [Extensiones de marcado para el lenguaje XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Enlazar a sí mismo

El modo de enlace relativo `Self` se utiliza para enlazar una propiedad de un elemento a otra propiedad del mismo elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

En este ejemplo, [`BoxView`](xref:Xamarin.Forms.BoxView) establece su propiedad `WidthRequest` en un tamaño fijo y la propiedad `HeightRequest` se enlaza a la propiedad `WidthRequest`. Por lo tanto, ambas propiedades son iguales y, de este modo, se dibuja un cuadrado:

[![Captura de pantalla de un enlace relativo de modo propio, en iOS y Android](relative-bindings-images/self-relative-binding.png "Modo de enlace autorelativo")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modo de enlace autorelativo")

> [!IMPORTANT]
> Al enlazar una propiedad de un elemento a otra propiedad del mismo elemento, las propiedades deben ser del mismo tipo. También puede especificar un convertidor en el enlace para convertir el valor.

Un uso común de este modo de enlace es establecer la `BindingContext` de un objeto en una propiedad en sí misma. El código siguiente muestra un ejemplo de esto:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

En este ejemplo, el `BindingContext` de la página se establece en la propiedad `DefaultViewModel` de sí mismo. Esta propiedad se define en el archivo de código subyacente de la página y proporciona una instancia de ViewModel. El [`ListView`](xref:Xamarin.Forms.ListView) se enlaza a la propiedad `Employees` de ViewModel.

## <a name="bind-to-an-ancestor"></a>Enlazar a un antecesor

Los modos de enlace relativo `FindAncestor` y `FindAncestorBindingContext` se usan para enlazar a elementos primarios de un tipo determinado en el árbol visual. El modo `FindAncestor` se utiliza para enlazar a un elemento primario, que se deriva del tipo [`Element`](xref:Xamarin.Forms.Element). El modo `FindAncestorBindingContext` se utiliza para enlazar a la `BindingContext` de un elemento primario.

> [!WARNING]
> La propiedad `AncestorType` se debe establecer en un `Type` al utilizar los modos de enlace relativo `FindAncestor` y `FindAncestorBindingContext`, de lo contrario se produce una `XamlParseException`.

Si no se establece explícitamente la propiedad `Mode`, al establecer la propiedad `AncestorType` en un tipo que se deriva de [`Element`](xref:Xamarin.Forms.Element) se establecerá implícitamente la propiedad `Mode` en `FindAncestor`. Del mismo modo, si se establece la propiedad `AncestorType` en un tipo que no se deriva de `Element`, se establecerá implícitamente la propiedad `Mode` en `FindAncestorBindingContext`.

> [!NOTE]
> Los enlaces relativos que usan el modo de `FindAncestorBindingContext` se volverán a aplicar cuando cambie la `BindingContext` de cualquier antecesor.

En el código XAML siguiente se muestra un ejemplo en el que la propiedad `Mode` se establecerá implícitamente en `FindAncestorBindingContext`:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

En este ejemplo, el `BindingContext` de la página se establece en la propiedad `DefaultViewModel` de sí mismo. Esta propiedad se define en el archivo de código subyacente de la página y proporciona una instancia de ViewModel. El [`ListView`](xref:Xamarin.Forms.ListView) se enlaza a la propiedad `Employees` de ViewModel. El [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada elemento del `ListView`, contiene una [`Button`](xref:Xamarin.Forms.Button). La propiedad `Command` del botón está enlazada al `DeleteEmployeeCommand` en el ViewModel de su elemento primario. Al pulsar un `Button` se elimina un empleado:

[![Captura de pantalla de un enlace relativo de modo FindAncestor, en iOS y Android](relative-bindings-images/findancestor-relative-binding.png "Modo de enlace relativo FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modo de enlace relativo FindAncestor")

Además, la propiedad `AncestorLevel` opcional puede ayudar a eliminar la ambigüedad en la búsqueda del antecesor en aquellos escenarios en que sea posible que haya más de un antecesor de ese tipo en el árbol visual.

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

En este ejemplo, la propiedad `Label.Text` se enlaza a la propiedad `Text` de la segunda [`Entry`](xref:Xamarin.Forms.Entry) que se encuentra en la ruta de acceso ascendente, comenzando en el elemento de destino del enlace.

> [!NOTE]
> La propiedad `AncestorLevel` debe establecerse en 1 para buscar el antecesor más próximo al elemento de destino de enlace.

## <a name="bind-to-a-templated-parent"></a>Enlazar a un elemento primario con plantilla

El modo de enlace relativo `TemplatedParent` se utiliza para enlazar desde una plantilla de control a la instancia de objeto de tiempo de ejecución a la que se aplica la plantilla (conocida como el elemento primario con plantilla). Este modo solo es aplicable si el enlace relativo está dentro de una plantilla de control y es similar a establecer un `TemplateBinding`.

El siguiente XAML muestra un ejemplo de un modo de enlace relativo `TemplatedParent`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, la [`Frame`](xref:Xamarin.Forms.Frame), que es el elemento raíz del `ControlTemplate`, tiene su `BindingContext` establecido en la instancia de objeto de tiempo de ejecución a la que se aplica la plantilla. Por lo tanto, `Frame` y sus elementos secundarios resuelven sus expresiones de enlace con las propiedades de cada objeto `CardView`:

[![Captura de pantalla de un enlace relativo de modo TemplatedParent, en iOS y Android](relative-bindings-images/templatedparent-relative-binding.png "Modo de enlace relativo TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modo de enlace relativo TemplatedParent")

Para obtener más información sobre las plantillas de control, consulte [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
