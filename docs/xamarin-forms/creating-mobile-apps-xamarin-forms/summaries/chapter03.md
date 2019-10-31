---
title: Resumen del capítulo 3. Más profunda en texto
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 3. Más profunda en texto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980931"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumen del capítulo 3. Más profunda en texto

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

En este capítulo se explora la vista [`Label`](xref:Xamarin.Forms.Label) con más profundidad, como el color, las fuentes y el formato.

## <a name="wrapping-paragraphs"></a>Párrafos de ajuste

Cuando la propiedad [`Text`](xref:Xamarin.Forms.Label.Text) de `Label` contiene texto largo, `Label` lo ajusta automáticamente a varias líneas como se muestra en el ejemplo [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . Puede incrustar códigos Unicode como ' \u2014 ' para el guión em o C# caracteres como ' \r ' para interrumpir la conexión a una nueva línea.

Cuando las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de una `Label` se establecen en `LayoutOptions.Fill`, el tamaño total de la `Label` se rige por el espacio que su contenedor pone a disposición. Se dice que el `Label` está *restringido*. El tamaño de la `Label` es el tamaño de su contenedor.

Cuando las propiedades `HorizontalOptions` y `VerticalOptions` se establecen en valores distintos de `LayoutOptions.Fill`, el tamaño de la `Label` se rige por el espacio necesario para representar el texto, hasta el tamaño que su contenedor pone a disposición del `Label`. Se dice que el `Label` no tiene *restricciones* y determina su propio tamaño.

(Nota: Los términos *restringidos* y sin *restricciones* pueden ser un contador intuitivo, ya que una vista sin restricciones suele ser menor que una vista restringida. Además, estos términos no se usan de forma coherente en los primeros capítulos del libro).

Una vista como una `Label` se puede restringir en una dimensión y sin restricciones en la otra. Un `Label` solo ajustará el texto en varias líneas si está restringida horizontalmente.

Si una `Label` está restringida, podría ocupar bastante más espacio de lo necesario para el texto. El texto se puede colocar en el área global del `Label`. Establezca la propiedad [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) en un miembro de la enumeración [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [`Center`](xref:Xamarin.Forms.TextAlignment.Center)o [`End`](xref:Xamarin.Forms.TextAlignment.Center)) para controlar la alineación de todas las líneas del párrafo. El valor predeterminado es `Start` y alinea a la izquierda el texto.

Establezca la propiedad [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) en un miembro de la enumeración `TextAlignment` para colocar el texto en la parte superior, central o inferior del área ocupada por el `Label`.

Establezca la propiedad [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) en un miembro de la enumeración [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) para controlar cómo el múltiplo las líneas de un salto de párrafo o se truncan.

## <a name="text-and-background-colors"></a>Colores de texto y de fondo

Establezca las propiedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor) y [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de `Label` en [`Color`](xref:Xamarin.Forms.Color) valores para controlar el color del texto y el fondo.

El `BackgroundColor` se aplica al fondo de todo el área ocupada por el `Label`. Dependiendo de las propiedades `HorizontalOptions` y `VerticalOptions`, ese tamaño puede ser considerablemente mayor que el área necesaria para mostrar el texto. Puede usar el color para experimentar con varios valores de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`y `VerticalTextAlignment` para ver cómo afectan al tamaño y la posición del `Label`y el tamaño y la posición del texto dentro de la `Label`.

## <a name="the-color-structure"></a>La estructura de color

La estructura [`Color`](xref:Xamarin.Forms.Color) permite especificar los colores como valores rojo-verde-azul (RGB) o valores Hue-saturación-luminosidad (HSL), o con un nombre de color. También hay disponible un canal alfa para indicar la transparencia.

Use un constructor de `Color` para especificar:

- [sombreado gris](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valor RGB con transparencia](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Los argumentos son `double` valores comprendidos entre 0 y 1.

También puede usar varios métodos estáticos para crear `Color` valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) para los valores RGB de `double` de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) para valores RGB enteros de 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) para los valores RGB de `double` con transparencia
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) para valores RGB enteros con transparencia
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) para los valores de `double` HSL con transparencia
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) para un valor de `uint` calculado como (B + 256 \* (G + 256 \* (R + 256 \* a))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) para un formato de `string` de dígitos hexadecimales con el formato "#AARRGGBB" o "#RRGGBB" o "#ARGB", donde cada letra corresponde a un dígito hexadecimal para los canales alfa, rojo, verde y azul. Este método es el principal que se usa para las conversiones de color XAML, como se describe en el [capítulo 7, XAML frente a código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una vez creado, un valor `Color` es inmutable. Las características del color se pueden obtener a partir de las siguientes propiedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Todos ellos son `double` valores comprendidos entre 0 y 1.

`Color` también define los campos de solo lectura estáticos públicos 240 para los colores comunes. En el momento en que se escribió el libro, solo estaban disponibles 17 colores comunes.

Otro campo estático público de solo lectura define un color con todos los canales de color establecidos en cero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Varios métodos de instancia permiten modificar un color existente para crear un nuevo color:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Por último, dos propiedades estáticas de solo lectura definen el valor de color especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos los canales establecidos en &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` está diseñado para aplicar la combinación de colores de la plataforma y, por consiguiente, tiene un significado diferente en contextos diferentes en distintas plataformas. De forma predeterminada, las combinaciones de colores de la plataforma son:

- iOS: Texto oscuro sobre un fondo claro
- Android: Texto claro sobre un fondo oscuro (en el libro) o texto oscuro sobre un fondo claro (para el diseño de material a través de AppCompat en la rama **principal** del repositorio de código de ejemplo)
- UWP Texto oscuro sobre un fondo claro

El valor de `Color.Accent` da como resultado un color específico de la plataforma (y a veces seleccionable por el usuario) que es visible en un fondo oscuro o claro.

## <a name="changing-the-application-color-scheme"></a>Cambiar la combinación de colores de la aplicación

Las distintas plataformas tienen una combinación de colores predeterminada, tal como se muestra en la lista anterior.

Cuando el destino es Android, se puede cambiar a un esquema de luz oscura especificando un tema claro en el archivo Android. manifest. XML o [agregando AppCompat y el diseño de material](~/xamarin-forms/platform/android/appcompat-material-design.md).

En el caso de las plataformas de Windows, el usuario selecciona normalmente el tema de color, pero puede Agregar un conjunto de atributos `RequestedTheme` a `Light` o `Dark` en el archivo app. XAML de la plataforma. De forma predeterminada, el archivo app. Xaml del proyecto UWP contiene un atributo `RequestedTheme` establecido en `Light`.

## <a name="font-sizes-and-attributes"></a>Tamaños de fuente y atributos

Establezca la propiedad [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) de `Label` en una cadena como "Times Roman" para seleccionar una familia de fuentes. Sin embargo, debe especificar una familia de fuentes que sea compatible con la plataforma en cuestión y que las plataformas sean incoherentes en este sentido.

Establezca la propiedad [`FontSize`](xref:Xamarin.Forms.Label.FontSize) de `Label` en un `double` para especificar el alto aproximado de la fuente. Vea el [capítulo 5](chapter05.md)sobre el tratamiento de los tamaños para obtener más detalles sobre cómo elegir de forma inteligente los tamaños de fuente.

También puede obtener uno de los distintos tamaños preestablecidos de fuente dependientes de la plataforma. El método estático [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) y la [sobrecarga](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) devuelven ambos un valor de tamaño de fuente `double` apropiado para la plataforma en función de los miembros de la enumeración [`NamedSize`](xref:Xamarin.Forms.NamedSize) ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [`Micro`](xref:Xamarin.Forms.NamedSize.Micro), [`Small`](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)y [`Large`](xref:Xamarin.Forms.NamedSize.Large)). El valor devuelto del miembro `Medium` no es necesariamente el mismo que el `Default`. En el ejemplo [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) se muestra texto con estos tamaños con nombre.

Establezca la propiedad [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) de `Label` en un miembro de estos [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) enumeración, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold), [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)o [`None`](xref:Xamarin.Forms.FontAttributes.None). Puede combinar los miembros `Bold` y `Italic` con el C# operador OR bit a bit.

## <a name="formatted-text"></a>Texto con formato

En todos los ejemplos hasta ahora, todo el texto que muestra el `Label` tiene un formato uniforme. Para modificar el formato de una cadena de texto, no establezca la propiedad `Text` de `Label`. En su lugar, establezca la propiedad [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) en un objeto de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString).

`FormattedString` tiene una propiedad [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) que es una colección de objetos [`Span`](xref:Xamarin.Forms.Span) . Cada `Span` objeto tiene sus propias propiedades [`Text`](xref:Xamarin.Forms.Span.Text), [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily), [`FontSize`](xref:Xamarin.Forms.Span.FontSize), [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes), [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)y [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

En el ejemplo [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) se muestra cómo usar la propiedad `FormattedText` para una sola línea de texto y [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) se muestra la técnica de un párrafo completo, como se muestra a continuación:

[![Captura de pantalla triple de un párrafo con formato variable](images/ch03fg06-small.png "Texto de etiqueta con formato de variable")](images/ch03fg06-large.png#lightbox "Texto de etiqueta con formato de variable")

El programa [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) usa un solo `Label` y un objeto `FormattedString` para mostrar todos los tamaños de fuente con nombre para cada plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 3 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Ejemplos de capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Ejemplos de F# capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [Trabajar con colores](~/xamarin-forms/user-interface/colors.md)
