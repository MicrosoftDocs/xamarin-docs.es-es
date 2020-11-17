---
title: Resumen del capítulo 7. XAML o código
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 7. XAML o código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d69c5c96138024f6a45d71013b259b2c47a20da3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370188"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumen del capítulo 7. XAML o código

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

Xamarin.Forms admite un lenguaje de marcado basado en XML denominado lenguaje XAML. XAML proporciona una alternativa a C# para definir el diseño de la interfaz de usuario de una aplicación de Xamarin.Forms y para definir enlaces entre elementos de la interfaz de usuario y datos subyacentes.

## <a name="properties-and-attributes"></a>Propiedades y atributos

Las clases y estructuras de Xamarin.Forms se convierten en elementos XML en XAML, y las propiedades de estas clases y estructuras se convierten en atributos XML. Para que se creen instancias de ella en XAML, una clase debe tener generalmente un constructor sin parámetros público. Cualquier propiedad establecida en XAML debe tener descriptores de acceso `set` públicos.

En el caso de las propiedades de los tipos de datos básicos (`string`, `double`, `bool`, etc.), el analizador de XAML usa los métodos de `TryParse` estándar para convertir los valores de atributo en estos tipos. El analizador XAML también puede controlar fácilmente los tipos de enumeración y puede combinar miembros de enumeración si el tipo de enumeración se marca con el atributo `Flags`.

Para ayudar al analizador XAML, los tipos más complejos (o las propiedades de esos tipos) pueden incluir un objeto [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) que identifica una clase que se deriva de [`TypeConverter`](xref:Xamarin.Forms.TypeConverter), que admite la conversión de valores de cadena a esos tipos. Por ejemplo, [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) convierte nombres y cadenas de color, como "#rrggbb", en valores `Color`.

## <a name="property-element-syntax"></a>Sintaxis de propiedad-elemento

En XAML, las clases y los objetos creados a partir de ellas se expresan como elementos XML. Estos se conocen como *elementos de objeto*. La mayoría de las propiedades de estos objetos se expresan como atributos XML. Se denominan *atributos de propiedad*.

A veces, una propiedad se debe establecer en un objeto que no se puede expresar como una cadena simple. En tal caso, XAML admite una etiqueta denominada *elemento de propiedad* formada por el nombre de la clase y el nombre de la propiedad separados por un punto. Después, un elemento de objeto puede aparecer dentro de un par de etiquetas de elemento de propiedad.

## <a name="adding-a-xaml-page-to-your-project"></a>Adición de una página XAML al proyecto

Una biblioteca de clases portable de Xamarin.Forms puede contener una página XAML cuando se crea por primera vez, o puede agregar una página XAML a un proyecto existente. En el cuadro de diálogo para agregar un nuevo elemento, elija el elemento que hace referencia a una página XAML, o `ContentPage` y XAML. (No es `ContentView`).

> [!NOTE]
> Las opciones de Visual Studio han cambiado desde que se escribió este capítulo.

Se crean dos archivos: un archivo XAML con la extensión de nombre de archivo.xaml, y un archivo de C# con la extensión .xaml.cs. Al archivo de C# a menudo se le llama *código subyacente* del archivo XAML. El archivo de código subyacente es una definición de clase parcial que se deriva de `ContentPage`. En tiempo de compilación, el XAML se analiza y se genera otra definición de clase parcial para la misma clase. Esta clase generada incluye un método denominado `InitializeComponent` al que se llama desde el constructor del archivo de código subyacente.

En el tiempo de ejecución, al finalizar la llamada de `InitializeComponent`, se han creado instancias de todos los elementos del archivo XAML y se han inicializado como si se hubieran creado en el código de C#.

El elemento raíz del archivo XAML es `ContentPage`. La etiqueta raíz contiene al menos dos declaraciones de espacio de nombres XML: una para los elementos de Xamarin.Forms y otra que define un prefijo `x` para elementos y atributos intrínsecos a todas las implementaciones de XAML. La etiqueta raíz también contiene un atributo `x:Class` que indica el espacio de nombres y el nombre de la clase que se deriva de `ContentPage`. Esto coincide con el espacio de nombres y el nombre de clase en el archivo de código subyacente.

La combinación de XAML y código se muestra en el ejemplo [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07).

## <a name="the-xaml-compiler"></a>Compilador XAML

Xamarin.Forms tiene un compilador XAML, pero su uso es opcional en función del uso de [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Si no se compila el XAML, el XAML se analiza en tiempo de compilación y el archivo XAML se incrusta en PCL, donde también se analiza en el tiempo de ejecución. Si se compila el código XAML, el proceso de compilación lo convierte en un formato binario y el procesamiento en tiempo de ejecución es más eficaz.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidad de la plataforma en el archivo XAML

En XAML, la [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) se puede usar para seleccionar el marcado dependiente de la plataforma. Se trata de una clase genérica y debe crearse una instancia de con un atributo `x:TypeArguments` que coincida con el tipo de destino. La clase [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) es similar pero se usa con mucha menos frecuencia.

El uso de `OnPlatform` ha cambiado desde que se publicó el libro. Originalmente se usaba junto con las propiedades denominadas `iOS`, `Android`y `WinPhone`. Ahora se usa con objetos secundarios de [`On`](xref:Xamarin.Forms.On). Establezca la propiedad [`Platform`](xref:Xamarin.Forms.On.Platform) en una cadena coherente con los campos `const` públicos de la clase [`Device`](xref:Xamarin.Forms.Device). Establezca la propiedad [`Value`](xref:Xamarin.Forms.On.Value) en un valor coherente con el atributo `x:TypeArguments` de la etiqueta `OnPlatform`.

`OnPlatform` se muestra en el ejemplo [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList), así llamado porque contiene bloques de XAML prácticamente idénticos. La existencia de este marcado redundante sugiere que debe haber disponibles técnicas para reducirlo.

## <a name="the-content-property-attributes"></a>Atributos de la propiedad de contenido

Algunos elementos de propiedad se producen con mucha frecuencia, como la etiqueta `<ContentPage.Content>` en el elemento raíz de `ContentPage` o la etiqueta `<StackLayout.Children>` que incluye los elementos secundarios de `StackLayout`.

Cada clase puede identificar una propiedad con [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) en la clase. Para esta propiedad, no se requieren etiquetas de elemento de propiedad. `ContentPage` define su propiedad de contenido como `Content`, y `Layout<T>` (la clase de la que deriva `StackLayout`) define su propiedad de contenido como `Children`. Estas etiquetas de elemento de propiedad no son necesarias.

El elemento de propiedad de `Label` es `Text`.

## <a name="formatted-text"></a>Texto con formato

El ejemplo [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contiene varios ejemplos de configuración de las propiedades `Text` y `FormattedText` de `Label`. En XAML, los objetos `Span` aparecen como elementos secundarios del objeto `FormattedString`.

 Cuando una cadena de varias líneas se establece en la propiedad `Text`, los caracteres de fin de línea se convierten en caracteres de espacio, pero los caracteres de fin de línea se conservan cuando una cadena de varias líneas aparece como contenido de las etiquetas `Label` o `Label.Text`:

 [![Captura de pantalla triple de uso compartido de variaciones de texto](images/ch07fg03-small.png "Variaciones de texto con formato")](images/ch07fg03-large.png#lightbox "Variaciones de texto con formato")

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 7 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Ejemplos del capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Ejemplo de F# del capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [XAML Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Conceptos básicos de XAML)
