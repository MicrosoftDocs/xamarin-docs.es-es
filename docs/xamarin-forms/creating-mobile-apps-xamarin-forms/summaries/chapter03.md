---
title: Resumen del capítulo 3. Más profunda en texto
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 3. Más profunda en texto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e37175240825c0fed350589649469c99f1bbf69a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771223"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumen del capítulo 3. Más profunda en texto

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Este capítulo se explora el [ `Label` ](xref:Xamarin.Forms.Label) vista con más detalle, incluido el color, fuentes y el formato.

## <a name="wrapping-paragraphs"></a>Puntos de ajuste

Cuando el [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedad de `Label` contiene texto largo, `Label` automáticamente ajusta en varias líneas como se muestra en el [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) ejemplo. Puede insertar códigos Unicode, como '\u2014' para el guión o caracteres de C# como '\r' interrumpir a una nueva línea.

Cuando el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) las propiedades de un `Label` se establecen en `LayoutOptions.Fill`, el tamaño general de la `Label` se rige por el espacio que su contenedor hace que estén disponibles. El `Label` se dice que *restringida*. El tamaño de la `Label` es el tamaño de su contenedor.

Cuando el `HorizontalOptions` y `VerticalOptions` propiedades se establecen en valores distintos de `LayoutOptions.Fill`, el tamaño de la `Label` se rige por el espacio necesario para representar el texto, hasta el tamaño de su contenedor hace que estén disponible para el `Label`. El `Label` se dice que *sin restricciones* y determina su propio tamaño.

(Nota: Los términos *restringidos* y sin *restricciones* pueden ser un contador intuitivo, ya que una vista sin restricciones suele ser menor que una vista restringida. Además, estos términos no se usan sistemáticamente en los primeros capítulos del libro.)

Una vista, como un `Label` puede restringir en una dimensión y sin restricciones en el otro. Un `Label` sólo ajustar texto en varias líneas si está restringido horizontalmente.

Si un `Label` está restringido, podría ocupar mucho más espacio que el necesario para el texto. El texto se puede colocar dentro del área general de la `Label`. Establecer el [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) propiedad a un miembro de la [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumeración ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center), o [ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) para controlar la alineación de todas las líneas del párrafo. El valor predeterminado es `Start` y el texto alinea a la izquierda.

Establecer el [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) propiedad a un miembro de la `TextAlignment` enumeración para colocar el texto en la parte superior, el centro o la parte inferior del área ocupado por el `Label`.

Establecer el [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propiedad a un miembro de la [ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode) enumeración ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), o [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) para control de cómo el varias líneas en un salto de párrafo o se truncan.

## <a name="text-and-background-colors"></a>Colores de fondo y texto

Establecer el [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) y [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propiedades de `Label` a [ `Color` ](xref:Xamarin.Forms.Color) valores para controlar el color del texto y fondo.

El `BackgroundColor` se aplica al fondo de toda el área ocupado por el `Label`. En función de la `HorizontalOptions` y `VerticalOptions` propiedades, que el tamaño puede ser considerablemente mayor que el área requerida para mostrar el texto. Puede usar para experimentar con distintos valores de color `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, y `VerticalTextAlignment` para ver cómo afectan al tamaño y la posición de la `Label`y el tamaño y la posición del texto dentro de la `Label`.

## <a name="the-color-structure"></a>La estructura de Color

El [ `Color` ](xref:Xamarin.Forms.Color) estructura le permite especificar los colores como valores de rojo, verde y azul (RGB) o valores de matiz-saturación-luminosidad HSL (), o con un nombre de color. También está disponible para indicar la transparencia de un canal alfa.

Use un `Color` constructor para especificar:

- un [tonos de gris](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valor RGB de transparencia](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Los argumentos son `double` valores comprendidos entre 0 y 1.

También puede usar varios métodos estáticos para crear `Color` valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) para los valores RGB de entero comprendido entre 0 y 255.
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) para `double` valores RGB con transparencia
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) para los valores RGB de enteros con transparencia
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) para `double` HSL (valores) con transparencia
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) para un `uint` valor se calcula como (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) para un `string` formato de dígitos hexadecimales en el formulario "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", donde cada letra corresponde a un dígito hexadecimal para la versión alfa, rojo, verdes y azules de los canales. Este método es el principal utiliza para las conversiones de color XAML, como se describe en [capítulo 7, XAML frente a código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una vez creado, un `Color` valor es inmutable. Pueden obtener las características del color de las siguientes propiedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Estos son todos `double` valores comprendidos entre 0 y 1.

`Color` También define 240 campos estáticos públicos de sólo lectura para los colores comunes. En el momento en que se escribió el libro, han sólo 17 colores comunes.

Otro campo de solo lectura por estático público define un color con todos los canales de color que se establece en cero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Varios métodos de instancia permiten la modificación de un color existente para crear un nuevo color:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Por último, dos propiedades estáticas de sólo lectura definen valor de color especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos los canales se establece en &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` está diseñado para aplicar la combinación de colores de la plataforma y, por lo tanto tiene un significado diferente en contextos diferentes en distintas plataformas. De forma predeterminada, las combinaciones de colores de la plataforma son:

- iOS Texto oscuro sobre un fondo claro
- Android: Texto claro sobre un fondo oscuro (en el libro) o texto oscuro sobre un fondo claro (para el diseño de material a través de AppCompat en la rama **principal** del repositorio de código de ejemplo)
- UWP: Texto oscuro sobre un fondo claro

El `Color.Accent` valor resultados en un color específico de la plataforma (y a veces seleccionable por el usuario) que está visible en un fondo oscuro o claro.

## <a name="changing-the-application-color-scheme"></a>Cambiar la combinación de colores de la aplicación

Las diferentes plataformas tienen una combinación de colores predeterminada como se muestra en la lista anterior.

Cuando el destino es Android, es posible cambiar a un esquema de la luz de oscuro especificando un tema claro en el archivo Android.Manifest.xml o [agregar AppCompat y Material Design](~/xamarin-forms/platform/android/appcompat-material-design.md).

Para las plataformas Windows, normalmente se selecciona el tema de color por el usuario, pero puede agregar un `RequestedTheme` atributo establecido en `Light` o `Dark` en el archivo App.xaml de la plataforma. De forma predeterminada, el archivo App.xaml en el proyecto de UWP contiene un `RequestedTheme` atributo establecido en `Light`.

## <a name="font-sizes-and-attributes"></a>Atributos y los tamaños de fuente

Establecer el [ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily) propiedad de `Label` en una cadena como "Times Roman" para seleccionar una familia de fuentes. Sin embargo, debe especificar una familia de fuentes que se admite en la plataforma concreta, y las plataformas en este sentido son incoherentes.

Establecer el [ `FontSize` ](xref:Xamarin.Forms.Label.FontSize) propiedad de `Label` a un `double` para especificar el alto aproximado de la fuente. Consulte [capítulo 5, tratar con tamaños](chapter05.md), para obtener más detalles sobre la elección de forma inteligente los tamaños de fuente.

Como alternativa, puede obtener uno de varios tamaños de fuente preestablecido depende de la plataforma. Estático [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) método y [sobrecarga](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) ambos devuelven un `double` el valor de tamaño de fuente adecuado para la plataforma en función de los miembros de la [ `NamedSize` ](xref:Xamarin.Forms.NamedSize)enumeración ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro), [ `Small` ](xref:Xamarin.Forms.NamedSize.Small), [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium),  y [ `Large` ](xref:Xamarin.Forms.NamedSize.Large)). El valor devuelto de la `Medium` miembro no es necesariamente el mismo que `Default`. El [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) ejemplo muestra el texto con estos tamaños con nombre.

Establecer el [ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes) propiedad de `Label` a un miembro de estos [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes) enumeración, [ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold), [ `Italic` ](xref:Xamarin.Forms.FontAttributes.Italic), o [ `None` ](xref:Xamarin.Forms.FontAttributes.None). Puede combinar el `Bold` y `Italic` miembros con el bit a bit OR operadores de C#.

## <a name="formatted-text"></a>Texto con formato

En todos los ejemplos presentados hasta ahora, todo el texto muestra el `Label` se ha dado formato uniformemente. Para variar el formato dentro de una cadena de texto, no establezca la `Text` propiedad de `Label`. En su lugar, establezca el [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propiedad a un objeto de tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString).

`FormattedString` tiene un [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propiedad que es una colección de [ `Span` ](xref:Xamarin.Forms.Span) objetos. Cada `Span` objeto tiene su propio [ `Text` ](xref:Xamarin.Forms.Span.Text), [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize), [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes), [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor), y [ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor) propiedades.

El [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) ejemplo muestra cómo utilizar el `FormattedText` propiedad para una sola línea de texto, y [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demuestra esta técnica para todo el párrafo, como se muestra aquí:

[![Captura de pantalla triple de variable con formato de párrafo](images/ch03fg06-small.png "texto de la etiqueta con el formato de Variable")](images/ch03fg06-large.png#lightbox "texto de la etiqueta con el formato de Variable")

El [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programa usa una sola `Label` y un `FormattedString` objeto para mostrar todos los tamaños de fuente con nombre para cada plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 3 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Ejemplos del capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Capítulo 3 F# ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [Trabajar con colores](~/xamarin-forms/user-interface/colors.md)
