---
title: Creación de extensiones de marcado XAML
description: En este artículo se explica cómo definir sus propias Xamarin.Forms extensiones de marcado XAML personalizadas. Una extensión de marcado XAML es una clase que implementa la interfaz IMarkupExtension o IMarkupExtension <T> .
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87153223c79365053bfc05b2a8a97c9aa1d4c4a4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369056"
---
# <a name="creating-xaml-markup-extensions"></a>Creación de extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

En el nivel de programación, una extensión de marcado XAML es una clase que implementa [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) la [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interfaz o. Puede explorar el código fuente de las extensiones de marcado estándar que se describen a continuación en el [directorio **MarkupExtensions**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del Xamarin.Forms repositorio de github.

También es posible definir sus propias extensiones de marcado XAML personalizadas mediante la derivación de `IMarkupExtension` o `IMarkupExtension<T>` . Use la forma genérica si la extensión de marcado obtiene un valor de un tipo determinado. Este es el caso con varias de las Xamarin.Forms extensiones de marcado:

- `TypeExtension` deriva de `IMarkupExtension<Type>`.
- `ArrayExtension` deriva de `IMarkupExtension<Array>`.
- `DynamicResourceExtension` deriva de `IMarkupExtension<DynamicResource>`.
- `BindingExtension` deriva de `IMarkupExtension<BindingBase>`.
- `ConstraintExpression` deriva de `IMarkupExtension<Constraint>`.

Las dos `IMarkupExtension` interfaces definen solo un método, denominado `ProvideValue` :

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Dado que `IMarkupExtension<T>` deriva de `IMarkupExtension` e incluye la `new` palabra clave en `ProvideValue` , contiene ambos `ProvideValue` métodos.

Con mucha frecuencia, las extensiones de marcado XAML definen propiedades que contribuyen al valor devuelto. (La excepción obvia es `NullExtension` , en la que `ProvideValue` simplemente devuelve `null` ). El `ProvideValue` método tiene un único argumento de tipo `IServiceProvider` que se tratará más adelante en este artículo.

## <a name="a-markup-extension-for-specifying-color"></a>Una extensión de marcado para especificar el color

La siguiente extensión de marcado XAML le permite construir un `Color` valor mediante los componentes Hue, saturación y luminosidad. Define cuatro propiedades para los cuatro componentes del color, incluido un componente alfa que se inicializa en 1. La clase se deriva de `IMarkupExtension<Color>` para indicar un `Color` valor devuelto:

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Dado que `IMarkupExtension<T>` deriva de `IMarkupExtension` , la clase debe contener dos `ProvideValue` métodos, uno que devuelva `Color` y otro que devuelva `object` , pero el segundo método simplemente puede llamar al primer método.

La página de **demostración de color HSL** muestra varias formas que `HslColorExtension` pueden aparecer en un archivo XAML para especificar el color de un `BoxView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Tenga en cuenta que cuando `HslColorExtension` es una etiqueta XML, las cuatro propiedades se establecen como atributos, pero cuando aparecen entre llaves, las cuatro propiedades se separan mediante comas sin comillas. Los valores predeterminados de `H` , `S` y `L` son 0, y el valor predeterminado de `A` es 1, por lo que se pueden omitir esas propiedades si desea que se establezcan en valores predeterminados. En el último ejemplo se muestra un ejemplo en el que la luminosidad es 0, que normalmente resulta en negro, pero el canal alfa es 0,5, por lo que es la mitad transparente y aparece atenuada en el fondo blanco de la página:

[![Demostración de color HSL](creating-images/hslcolordemo-small.png "Demostración de color HSL")](creating-images/hslcolordemo-large.png#lightbox "Demostración de color HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Una extensión de marcado para tener acceso a mapas de bits

El argumento de `ProvideValue` es un objeto que implementa la [`IServiceProvider`](xref:System.IServiceProvider) interfaz, que se define en el espacio de `System` nombres .net. Esta interfaz tiene un miembro, un método denominado `GetService` con un `Type` argumento.

La `ImageResourceExtension` clase que se muestra a continuación muestra un posible uso de `IServiceProvider` y `GetService` para obtener un `IXmlLineInfoProvider` objeto que pueda proporcionar información de línea y de carácter que indique dónde se detectó un error determinado. En este caso, se produce una excepción cuando no se ha `Source` establecido la propiedad:

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` resulta útil cuando un archivo XAML necesita tener acceso a un archivo de imagen almacenado como un recurso incrustado en el proyecto de biblioteca de .NET Standard. Utiliza la `Source` propiedad para llamar al método estático `ImageSource.FromResource` . Este método requiere un nombre de recurso completo, que consta del nombre del ensamblado, el nombre de la carpeta y el nombre de archivo separados por puntos. El segundo argumento del `ImageSource.FromResource` método proporciona el nombre del ensamblado y solo es necesario para las compilaciones de versión en UWP. Independientemente de, `ImageSource.FromResource` se debe llamar al método desde el ensamblado que contiene el mapa de bits, lo que significa que esta extensión de recursos XAML no puede formar parte de una biblioteca externa a menos que las imágenes estén también en esa biblioteca. (Consulte el artículo [**imágenes incrustadas**](~/xamarin-forms/user-interface/images.md#embedded-images) para obtener más información sobre el acceso a mapas de bits almacenados como recursos incrustados).

Aunque `ImageResourceExtension` requiere `Source` que se establezca la propiedad, la `Source` propiedad se indica en un atributo como la propiedad de contenido de la clase. Esto significa que `Source=` se puede omitir la parte de la expresión entre llaves. En la página de **demostración de recursos de imagen** , los `Image` elementos capturan dos imágenes con el nombre de la carpeta y el nombre de archivo separados por puntos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Esta es la ejecución del programa:

[![Demostración de recursos de imagen](creating-images/imageresourcedemo-small.png "Demostración de recursos de imagen")](creating-images/imageresourcedemo-large.png#lightbox "Demostración de recursos de imagen")

## <a name="service-providers"></a>Proveedores de servicios

Al usar el `IServiceProvider` argumento para `ProvideValue` , las extensiones de marcado XAML pueden obtener acceso a información útil sobre el archivo XAML en el que se usan. Pero para usar el `IServiceProvider` argumento correctamente, debe saber qué tipo de servicios están disponibles en contextos concretos. La mejor manera de comprender esta característica es estudiar el código fuente de las extensiones de marcado XAML existentes en la [carpeta **MarkupExtensions**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del Xamarin.Forms repositorio en github. Tenga en cuenta que algunos tipos de servicios son internos a Xamarin.Forms .

En algunas extensiones de marcado XAML, este servicio puede ser útil:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

La `IProvideValueTarget` interfaz define dos propiedades, `TargetObject` y `TargetProperty` . Cuando esta información se obtiene en la `ImageResourceExtension` clase, `TargetObject` es `Image` y `TargetProperty` es un `BindableProperty` objeto para la `Source` propiedad de `Image` . Esta es la propiedad en la que se ha establecido la extensión de marcado XAML.

La `GetService` llamada a con un argumento de `typeof(IProvideValueTarget)` realmente devuelve un objeto de tipo `SimpleValueTargetProvider` , que se define en el `Xamarin.Forms.Xaml.Internals` espacio de nombres. Si convierte el valor devuelto de `GetService` a ese tipo, también puede tener acceso a una `ParentObjects` propiedad, que es una matriz que contiene el `Image` elemento, el elemento `Grid` primario y el elemento `ImageResourceDemoPage` primario de `Grid` .

## <a name="conclusion"></a>Conclusión

Las extensiones de marcado XAML desempeñan un papel fundamental en XAML mediante la ampliación de la capacidad de establecer atributos de diversos orígenes. Además, si las extensiones de marcado XAML existentes no proporcionan exactamente lo que necesita, también puede escribir las suyas propias.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensiones de marcado XAML del Xamarin.Forms libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)