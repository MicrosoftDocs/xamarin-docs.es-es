---
title: " Xamarin.Forms ContentView" Description: "en este artículo se explica cómo usar la clase ContentView para crear un control personalizado, como el ejemplo CardView".
MS. Prod: Xamarin ms. AssetID: 638402E7-CA44-456B-863B-791F6B6B561D ms. Technology: Xamarin-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 08/14/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-contentview"></a>ContentView de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

La Xamarin.Forms [`ContentView`](xref:Xamarin.Forms.ContentView) clase es un tipo de `Layout` que contiene un único elemento secundario y se utiliza normalmente para crear controles personalizados y reutilizables. La `ContentView` clase hereda de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) . En este artículo, y en el ejemplo asociado, se explica cómo crear un `CardView` control personalizado basado en la `ContentView` clase.

En la captura de pantalla siguiente se muestra un `CardView` control que se deriva de la `ContentView` clase:

[![Captura de pantalla de la aplicación de ejemplo CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La `ContentView` clase define una propiedad única:

* [`Content`](xref:Xamarin.Forms.ContentView.Content)es un `View` objeto. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, por lo que puede ser el destino de los enlaces de datos.

`ContentView`También hereda una propiedad de la `TemplatedView` clase:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)es un `ControlTemplate` que puede definir o invalidar la apariencia del control.

Para obtener más información sobre la `ControlTemplate` propiedad, vea [personalizar el aspecto con una ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Crear un control personalizado

La `ContentView` clase ofrece poca funcionalidad, pero se puede usar para crear un control personalizado. El proyecto de ejemplo define un `CardView` elemento de la interfaz de usuario de control-a que muestra una imagen, un título y una descripción en un diseño de tipo tarjeta.

El proceso para crear un control personalizado es:

1. Cree una nueva clase con la `ContentView` plantilla en Visual Studio 2019.
1. Defina cualquier propiedad o evento único en el archivo de código subyacente para el nuevo control personalizado.
1. Cree la interfaz de usuario para el control personalizado.

> [!NOTE]
> Es posible crear un control personalizado cuyo diseño se define en el código en lugar de XAML. Para simplificar, la aplicación de ejemplo solo define una `CardView` clase única con un diseño XAML. Sin embargo, la aplicación de ejemplo contiene una clase **CardViewCodePage** que muestra el proceso de consumo del control personalizado en el código.

### <a name="create-code-behind-properties"></a>Crear propiedades de código subyacente

El `CardView` control personalizado define las siguientes propiedades:

* `CardTitle`: un `string` objeto que representa el título que se muestra en la tarjeta.
* `CardDescription`: un `string` objeto que representa la descripción que se muestra en la tarjeta.
* `IconImageSource`: un `ImageSource` objeto que representa la imagen que se muestra en la tarjeta.
* `IconBackgroundColor`: un `Color` objeto que representa el color de fondo de la imagen que se muestra en la tarjeta.
* `BorderColor`: un `Color` objeto que representa el color del borde de la tarjeta, el borde de la imagen y la línea del divisor.
* `CardColor`: un `Color` objeto que representa el color de fondo de la tarjeta.

> [!NOTE]
> La `BorderColor` propiedad afecta a varios elementos con fines de demostración. Esta propiedad podría dividirse en tres propiedades si es necesario.

Cada propiedad está respaldada por una `BindableProperty` instancia de. La copia de seguridad `BindableProperty` permite aplicar estilo a cada propiedad y enlazarla mediante el patrón MVVM.

En el ejemplo siguiente se muestra cómo crear una copia de seguridad `BindableProperty` :

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La propiedad personalizada usa los `GetValue` `SetValue` métodos y para obtener y establecer los `BindableProperty` valores del objeto:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Para obtener más información sobre los `BindableProperty` objetos, vea [propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definir la interfaz de usuario

La interfaz de usuario del control personalizado usa `ContentView` como elemento raíz para el `CardView` control. En el ejemplo siguiente se muestra el `CardView` código XAML:

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

El `ContentView` elemento establece la `x:Name` propiedad en **this**, que se puede utilizar para tener acceso al objeto enlazado a la `CardView` instancia. Los elementos del diseño establecen enlaces en sus propiedades a los valores definidos en el objeto enlazado.

Para obtener más información sobre el enlace de datos, consulte [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> La `FallbackValue` propiedad proporciona un valor predeterminado en caso de que el enlace sea `null` . Esto también permite que el controlador de [vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) de Visual Studio represente el `CardView` control.

## <a name="instantiate-a-custom-control"></a>Crear una instancia de un control personalizado

Se debe agregar una referencia al espacio de nombres del control personalizado a una página que cree instancias del control personalizado. En el ejemplo siguiente se muestra una referencia de espacio de nombres denominada **controles** agregados a una `ContentPage` instancia en XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Una vez agregada la referencia `CardView` , se pueden crear instancias en XAML y se han definido sus propiedades:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

`CardView`También se puede crear una instancia de un en el código:

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>Personalización de la apariencia con un ControlTemplate

Un control personalizado que se deriva de la `ContentView` clase puede definir la apariencia mediante XAML, código o no puede definir la apariencia en absoluto. Independientemente de cómo se defina la apariencia, un `ControlTemplate` objeto puede invalidar la apariencia con un diseño personalizado.

El `CardView` diseño podría ocupar demasiado espacio en algunos casos de uso. Un `ControlTemplate` puede invalidar el `CardView` diseño para proporcionar una vista más compacta, adecuada para una lista comprimida:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

El enlace de datos de un `ControlTemplate` utiliza la `TemplateBinding` extensión de marcado para especificar enlaces. `ControlTemplate`A continuación, la propiedad se puede establecer en el objeto ControlTemplate definido mediante su `x:Key` valor. En el ejemplo siguiente se muestra la `ControlTemplate` propiedad establecida en una `CardView` instancia de:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Las capturas de pantallas siguientes muestran una `CardView` instancia estándar y cuyo se ha `CardView` `ControlTemplate` invalidado:

[![Captura de pantalla de ControlTemplate de CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Para obtener más información sobre las plantillas de control, consulte [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Vínculos relacionados

* [Aplicación de ejemplo ContentView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).
* [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
