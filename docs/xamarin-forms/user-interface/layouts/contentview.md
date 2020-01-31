---
title: ContentView de Xamarin. Forms
description: En este artículo se explica cómo usar la clase ContentView para crear un control personalizado, como el CardView de ejemplo.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: 712aa43fb4959b766786c8fd0969ef2c2c8f00ef
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955734"
---
# <a name="xamarinforms-contentview"></a>ContentView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

La clase de [`ContentView`](xref:Xamarin.Forms.ContentView) de Xamarin. Forms es un tipo de `Layout` que contiene un solo elemento secundario y se utiliza normalmente para crear controles personalizados y reutilizables. La clase `ContentView` hereda de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). En este artículo, y en el ejemplo asociado, se explica cómo crear un control de `CardView` personalizado basado en la clase `ContentView`.

En la captura de pantalla siguiente se muestra un control de `CardView` que deriva de la clase `ContentView`:

[![captura de pantalla de la aplicación de ejemplo de CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La clase `ContentView` define una propiedad única:

* [`Content`](xref:Xamarin.Forms.ContentView.Content) es un objeto `View`. Esta propiedad está respaldada por un objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , por lo que puede ser el destino de los enlaces de datos.

La `ContentView` también hereda una propiedad de la clase `TemplatedView`:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) es una `ControlTemplate` que puede definir o invalidar la apariencia del control.

Para obtener más información sobre la propiedad `ControlTemplate`, vea [personalizar la apariencia con una ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Crear un control personalizado

La clase `ContentView` ofrece poca funcionalidad, pero se puede usar para crear un control personalizado. El proyecto de ejemplo define un elemento de la interfaz de usuario de `CardView` control, que muestra una imagen, un título y una descripción en un diseño de tipo tarjeta.

El proceso para crear un control personalizado es:

1. Cree una nueva clase con la `ContentView` plantilla en Visual Studio 2019.
1. Defina cualquier propiedad o evento único en el archivo de código subyacente para el nuevo control personalizado.
1. Cree la interfaz de usuario para el control personalizado.

> [!NOTE]
> Es posible crear un control personalizado cuyo diseño se define en el código en lugar de XAML. Para simplificar, la aplicación de ejemplo solo define una clase de `CardView` única con un diseño XAML. Sin embargo, la aplicación de ejemplo contiene una clase **CardViewCodePage** que muestra el proceso de consumo del control personalizado en el código.

### <a name="create-code-behind-properties"></a>Crear propiedades de código subyacente

El control personalizado `CardView` define las siguientes propiedades:

* `CardTitle`: objeto `string` que representa el título que se muestra en la tarjeta.
* `CardDescription`: objeto `string` que representa la descripción que se muestra en la tarjeta.
* `IconImageSource`: objeto `ImageSource` que representa la imagen que se muestra en la tarjeta.
* `IconBackgroundColor`: objeto `Color` que representa el color de fondo de la imagen que se muestra en la tarjeta.
* `BorderColor`: un objeto `Color` que representa el color del borde de la tarjeta, el borde de la imagen y la línea del divisor.
* `CardColor`: objeto `Color` que representa el color de fondo de la tarjeta.

> [!NOTE]
> La propiedad `BorderColor` afecta a varios elementos con fines de demostración. Esta propiedad podría dividirse en tres propiedades si es necesario.

Cada propiedad está respaldada por una instancia de `BindableProperty`. La `BindableProperty` de respaldo permite aplicar estilo a cada propiedad y enlazarla mediante el patrón MVVM.

En el ejemplo siguiente se muestra cómo crear una `BindableProperty`de respaldo:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La propiedad personalizada usa los métodos `GetValue` y `SetValue` para obtener y establecer los valores del objeto `BindableProperty`:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Para obtener más información sobre los objetos de `BindableProperty`, consulte [propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definir la interfaz de usuario

La interfaz de usuario del control personalizado utiliza un `ContentView` como elemento raíz para el control `CardView`. En el ejemplo siguiente se muestra el `CardView` XAML:

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

El elemento `ContentView` establece la propiedad `x:Name` en **, que**se puede utilizar para tener acceso al objeto enlazado a la instancia de `CardView`. Los elementos del diseño establecen enlaces en sus propiedades a los valores definidos en el objeto enlazado.

Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> La propiedad `FallbackValue` proporciona un valor predeterminado en caso de que el enlace sea `null`. Esto también permite que el controlador de [vista previa XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) de Visual Studio represente el control `CardView`.

## <a name="instantiate-a-custom-control"></a>Crear una instancia de un control personalizado

Se debe agregar una referencia al espacio de nombres del control personalizado a una página que cree instancias del control personalizado. En el ejemplo siguiente se muestra una referencia de espacio de nombres denominada **controles** agregados a una instancia de `ContentPage` en XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Una vez agregada la referencia, se puede crear una instancia de `CardView` en XAML y se han definido sus propiedades:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

También se puede crear una instancia de `CardView` en el código:

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

Un control personalizado que se deriva de la clase `ContentView` puede definir la apariencia mediante XAML, código o no puede definir la apariencia en absoluto. Independientemente de cómo se defina la apariencia, un objeto `ControlTemplate` puede invalidar la apariencia con un diseño personalizado.

El diseño de `CardView` podría ocupar demasiado espacio en algunos casos de uso. Un `ControlTemplate` puede invalidar el diseño de `CardView` para proporcionar una vista más compacta, adecuada para una lista comprimida:

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

El enlace de datos en un `ControlTemplate` utiliza la extensión de marcado `TemplateBinding` para especificar enlaces. A continuación, la propiedad `ControlTemplate` se puede establecer en el objeto ControlTemplate definido mediante su valor `x:Key`. En el ejemplo siguiente se muestra la propiedad `ControlTemplate` establecida en una instancia de `CardView`:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

En las siguientes capturas de pantallas se muestra una instancia de `CardView` estándar y `CardView` de los que se ha invalidado `ControlTemplate`:

[![captura de pantalla de CardView ControlTemplate](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Para obtener más información sobre las plantillas de control, consulte [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Vínculos relacionados

* [Aplicación de ejemplo ContentView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).
* [Plantillas de control de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
