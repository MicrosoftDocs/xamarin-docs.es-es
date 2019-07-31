---
title: Creación de extensiones de marcado XAML
description: En este artículo se explica cómo definir sus propias extensiones de marcado XAML de Xamarin.Forms personalizados. Una extensión de marcado XAML es una clase que implementa la interfaz IMarkupExtension o<T> IMarkupExtension.
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 4d26713f258a8c97abd4b4e9970ebdd4d490f485
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655863"
---
# <a name="creating-xaml-markup-extensions"></a>Creación de extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

En el nivel de programación, una extensión de marcado XAML es una clase que implementa el [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) o [ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interfaz. Puede explorar el código fuente de las extensiones de marcado estándar se describe a continuación, en el [ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del repositorio de Xamarin.Forms GitHub.

También es posible definir sus propias extensiones de marcado XAML personalizados derivando de `IMarkupExtension` o `IMarkupExtension<T>`. Use el formulario genérico si la extensión de marcado Obtiene un valor de un tipo determinado. Este es el caso con varias de las extensiones de marcado de Xamarin.Forms:

- `TypeExtension` se deriva de `IMarkupExtension<Type>`
- `ArrayExtension` se deriva de `IMarkupExtension<Array>`
- `DynamicResourceExtension` se deriva de `IMarkupExtension<DynamicResource>`
- `BindingExtension` se deriva de `IMarkupExtension<BindingBase>`
- `ConstraintExpression` se deriva de `IMarkupExtension<Constraint>`

Los dos `IMarkupExtension` interfaces definen solo un método denominado `ProvideValue`:

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

Puesto que `IMarkupExtension<T>` se deriva de `IMarkupExtension` e incluye el `new` palabra clave en `ProvideValue`, contiene ambos `ProvideValue` métodos.

Muy a menudo, las extensiones de marcado XAML definen propiedades que contribuyen al valor devuelto. (La excepción obvia es `NullExtension`, en el que `ProvideValue` simplemente devuelve `null`.) El `ProvideValue` método tiene un único argumento de tipo `IServiceProvider` que se tratará más adelante en este artículo.

## <a name="a-markup-extension-for-specifying-color"></a>Una extensión de marcado para especificar Color

La siguiente extensión de marcado XAML le permite construir un `Color` valor mediante los componentes de matiz, saturación y luminosidad. Define las cuatro propiedades de los cuatro componentes del color, incluido un componente alfa que se inicializa en 1. La clase se deriva de `IMarkupExtension<Color>` para indicar un `Color` valor devuelto:

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

Dado que `IMarkupExtension<T>` deriva `IMarkupExtension`, la clase debe contener dos `ProvideValue` métodos, uno que devuelve `Color` y otra que devuelve `object`, pero el segundo método simplemente puede llamar primero al método.

El **demostración de Color HSL** página muestra una variedad de formas que `HslColorExtension` puede aparecer en un archivo XAML para especificar el color para un `BoxView`:

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

Tenga en cuenta que, cuando `HslColorExtension` es una etiqueta XML, las cuatro propiedades se establecen como atributos, pero cuando aparece entre llaves, las cuatro propiedades están separadas por comas sin comillas. Los valores predeterminados de `H`, `S`, y `L` son 0 y el valor predeterminado de `A` es 1, por lo que esas propiedades se pueden omitir si quiere que estén establecidas en valores predeterminados. El último ejemplo muestra un ejemplo donde la luminosidad es 0, lo que normalmente se produce en negro, pero el canal alfa es 0,5, por lo que es semitransparente y aparece atenuado en el fondo blanco de la página:

[![Demostración de Color HSL](creating-images/hslcolordemo-small.png "demostración de Color HSL")](creating-images/hslcolordemo-large.png#lightbox "demostración de Color HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Una extensión de marcado para tener acceso a los mapas de bits

El argumento `ProvideValue` es un objeto que implementa el [ `IServiceProvider` ](xref:System.IServiceProvider) interfaz, que se define en .NET `System` espacio de nombres. Esta interfaz tiene un miembro, un método denominado `GetService` con un `Type` argumento.

El `ImageResourceExtension` clase se muestra a continuación muestra un uso posible de `IServiceProvider` y `GetService` para obtener un `IXmlLineInfoProvider` objeto que puede proporcionar información de línea y el carácter que indica donde se detectó un error determinado. En este caso, se produce una excepción cuando el `Source` no se estableció la propiedad:

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

`ImageResourceExtension` es útil cuando un archivo XAML necesita acceder a un archivo de imagen almacenado como un recurso incrustado en el proyecto de biblioteca .NET Standard. Usa el `Source` propiedad para llamar a estático `ImageSource.FromResource` método. Este método requiere un nombre de recurso completo, que está formado por el nombre del ensamblado, el nombre de carpeta y el nombre de archivo separadas por puntos. El segundo argumento para el `ImageSource.FromResource` método proporciona el nombre del ensamblado y solo es necesaria para la versión se basa en UWP. No obstante, `ImageSource.FromResource` debe llamarse desde el ensamblado que contiene el mapa de bits, lo que significa que esta extensión de recursos XAML no puede formar parte de una biblioteca externa a menos que las imágenes también se encuentran en dicha biblioteca. (Consulte la [ **imágenes incrustadas** ](~/xamarin-forms/user-interface/images.md#embedded-images) artículo para obtener más información sobre cómo acceder a los mapas de bits que se almacenan como recursos incrustados.)

Aunque `ImageResourceExtension` requiere la `Source` propiedad se establece, el `Source` propiedad se indica en un atributo como la propiedad de contenido de la clase. Esto significa que el `Source=` se puede omitir la parte de la expresión entre llaves. En el **demostración del recurso de imagen** página, el `Image` elementos capturar dos imágenes con el nombre de carpeta y el nombre de archivo separadas por puntos:

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

Este es el programa que se ejecuta:

[![Demostración de recursos de imagen](creating-images/imageresourcedemo-small.png "demostración del recurso de imagen")](creating-images/imageresourcedemo-large.png#lightbox "demostración del recurso de imagen")

## <a name="service-providers"></a>Proveedores de servicios

Mediante el uso de la `IServiceProvider` argumento `ProvideValue`, las extensiones de marcado XAML pueden obtener acceso a información útil sobre el archivo XAML en el que está usando. Pero para usar el `IServiceProvider` argumento correctamente, deberá saber qué tipo de servicios están disponibles en contextos determinados. La mejor manera para obtener una descripción de esta característica es estudiar el código fuente de las extensiones de marcado XAML existentes en el [ **MarkupExtensions** carpeta](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) en el repositorio de Xamarin.Forms en GitHub. Tenga en cuenta que algunos tipos de servicios son internos para Xamarin.Forms.

En algunas extensiones de marcado XAML, este servicio puede ser útil:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

El `IProvideValueTarget` interfaz define dos propiedades, `TargetObject` y `TargetProperty`. Cuando se obtiene esta información en el `ImageResourceExtension` (clase), `TargetObject` es el `Image` y `TargetProperty` es un `BindableProperty` de objeto para el `Source` propiedad de `Image`. Se trata de la propiedad en el que se ha establecido la extensión de marcado XAML.

El `GetService` llamada con un argumento de `typeof(IProvideValueTarget)` realmente devuelve un objeto de tipo `SimpleValueTargetProvider`, que se define en el `Xamarin.Forms.Xaml.Internals` espacio de nombres. Si convierte el valor devuelto de `GetService` a ese tipo, también puede acceder un `ParentObjects` propiedad, que es una matriz que contiene el `Image` elemento, el `Grid` primario y el `ImageResourceDemoPage` primario de la `Grid`.

## <a name="conclusion"></a>Conclusión

Las extensiones de marcado XAML desempeñan un papel fundamental en XAML mediante la extensión de la capacidad de establecer los atributos de una variedad de orígenes. Además, si las extensiones de marcado XAML existentes no proporcionan exactamente lo que necesita, también puede escribir su propio.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensiones de marcado XAML de Xamarin.Forms libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
