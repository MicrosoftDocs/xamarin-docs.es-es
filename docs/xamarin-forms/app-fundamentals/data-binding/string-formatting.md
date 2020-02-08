---
title: Formato de cadena de Xamarin.Forms
description: En este artículo se explica cómo usar enlaces de datos de Xamarin.Forms para dar formato y mostrar los objetos como cadenas. Esto se logra estableciendo el StringFormat del enlace en una cadena de formato estándar de .NET con un marcador de posición.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: bdd28e1ce6d36a0a025ac43a709af2e38a313526
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940377"
---
# <a name="xamarinforms-string-formatting"></a>Formato de cadena de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

A veces es conveniente usar enlaces de datos para mostrar la representación de cadena de un objeto o valor. Por ejemplo, es posible que quiera utilizar una `Label` para mostrar el valor actual de un `Slider`. En este enlace de datos, `Slider` es el origen y el destino es la propiedad `Text` de `Label`.

Cuando se muestran las cadenas en el código, la herramienta más eficaz es el método [`String.Format`](xref:System.String.Format(System.String,System.Object)) estático. La cadena de formato incluye códigos específicos para distintos tipos de objetos de formato, y puede incluir otros textos junto con los valores a los que se va a aplicar formato. Para obtener más información sobre el formato de cadena, vea [Aplicar formato a tipos en .NET](/dotnet/standard/base-types/formatting-types/).

## <a name="the-stringformat-property"></a>La propiedad StringFormat

Este recurso se transmite a los enlaces de datos: usted establece la propiedad [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) de `Binding` (o la propiedad [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) de la extensión de marcado `Binding`) a una cadena de formato .NET estándar con un marcador de posición:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Tenga en cuenta que la cadena de formato está delimitada por caracteres de comillas simples (apóstrofo) para ayudar al analizador XAML a evitar que trate las llaves como otra extensión de marcado XAML. En caso contrario, esa cadena sin el carácter de comillas simples es la misma cadena que utilizaría para mostrar un valor de punto flotante en una llamada a `String.Format`. Una especificación de formato de `F2` hace que el valor se muestre con dos posiciones decimales.

La propiedad `StringFormat` sólo tiene sentido cuando la propiedad de destino es de tipo `string`, y el modo de enlace es `OneWay` o `TwoWay`. Para los enlaces bidireccionales, el `StringFormat` solo es aplicable para los valores que se pasan desde el origen al destino.

Como verá en el artículo [Xamarin.Forms Binding Path](binding-path.md) (Ruta de acceso de Xamarin.Forms), los enlaces de datos pueden llegar a ser bastante complejos y enrevesados. Al depurar estos enlaces de datos, puede agregar una `Label` en el archivo XAML con un `StringFormat` para mostrar algunos resultados intermedios. Puede resultar útil incluso si se usa solo para mostrar un tipo de objeto.

La página **Formato de cadena** muestra varios usos de la propiedad `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Los enlaces en el `Slider` y el `TimePicker` muestran el uso de las especificaciones de formato específicas de los tipos de datos `double` y `TimeSpan`. El `StringFormat` que muestra el texto de la vista `Entry` muestra cómo especificar las comillas dobles en la cadena de formato con el uso de la entidad HTML `&quot;`.

La siguiente sección en el archivo XAML es un `StackLayout` con un `BindingContext` establecido en una extensión de marcado de `x:Static` que hace referencia a la propiedad `DateTime.Now` estática. El primer enlace no tiene propiedades:

```xaml
<Label Text="{Binding}" />
```

Esto muestra simplemente el valor `DateTime` de `BindingContext` con el formato predeterminado. El segundo enlace muestra la propiedad `Ticks` de `DateTime`, mientras que los otros dos enlaces muestran el propio `DateTime` con un formato específico. Observe este `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Si necesita mostrar llaves a la izquierda o la derecha en la cadena de formato, simplemente use un par de ellas.

Los conjuntos de la última sección establecen el `BindingContext` al valor de `Math.PI` y lo muestran con formato de forma predeterminada y dos tipos diferentes de formato numérico.

Esta es la ejecución del programa:

[![Formato de cadena](string-formatting-images/stringformatting-small.png "Formato de cadena")](string-formatting-images/stringformatting-large.png#lightbox "Formato de cadena")

## <a name="viewmodels-and-string-formatting"></a>ViewModels y formato de cadena

Cuando se usa `Label` y `StringFormat` para mostrar el valor de una vista que también es el destino de una clase ViewModel, puede definir el enlace de la vista para `Label` o desde el ViewModel a `Label`. En general, el segundo enfoque es mejor porque comprueba que funcionan los enlaces entre View y ViewModel.

Este método se muestra en el ejemplo **Mejor selector de colores**, que usa el mismo ViewModel que el programa **Selector de colores simple** que aparece en el artículo [**Xamarin.Forms Binding Mode**](binding-mode.md) (Modo de enlace de Xamarin.Forms):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Ahora hay tres pares de elementos `Slider` y `Label` que están enlazados a la misma propiedad de origen en el objeto `HslColorViewModel`. La única diferencia es que `Label` tiene una propiedad `StringFormat` para mostrar cada valor `Slider`.

[![Mejor selector de colores](string-formatting-images/bettercolorselector-small.png "Mejor selector de colores")](string-formatting-images/bettercolorselector-large.png#lightbox "Mejor selector de colores")

Tal vez se pregunte cómo podría mostrar sus valores RGB (rojos, verdes y azules) en formato hexadecimal de dos dígitos tradicional. Los valores enteros no están directamente disponibles desde la estructura de `Color`. Una solución sería calcular valores enteros de los componentes de color dentro de ViewModel y exponerlos como propiedades. Después puede aplicarles formato utilizando la especificación de formato `X2`.

Otro enfoque es más general: puede escribir un *convertidor de valores de enlace* como se describe en el siguiente artículo, [**Convertidor de valores de enlace de Xamarin.Forms**](converters.md).

Empero, el siguiente artículo explora la [**Ruta de enlace**](binding-path.md) con más detalle y muestra cómo se puede utilizar para hacer referencia a elementos de las colecciones y subpropiedades.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
