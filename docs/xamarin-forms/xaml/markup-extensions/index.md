---
title: Extensiones de marcado XAML
description: El artículo explica cómo usar las extensiones de marcado XAML de Xamarin.Forms para ampliar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento que se pueden establecer desde orígenes que no sean cadenas de texto literal.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 40a50ecf93c8b77577e8e74df2e134b0ce3f80b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767538"
---
# <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Las extensiones de marcado XAML ayudar a ampliar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento que se pueden establecer desde orígenes que no sean cadenas de texto literal.

Por ejemplo, normalmente, debe establecer el `Color` propiedad de `BoxView` similar al siguiente:

```xaml
<BoxView Color="Blue" />
```

O bien, puede establecerlo en un valor hexadecimal del color RGB:

```xaml
<BoxView Color="#FF0080" />
```

En cualquier caso, la cadena de texto se establece en el `Color` atributo se convierte en un `Color` valor por el [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) clase.

Es posible que prefiera en su lugar, establezca el `Color` desde un valor almacenado en un diccionario de recursos, o desde el valor de una propiedad estática de una clase que ha creado o una propiedad de tipo de atributo `Color` de otro elemento en la página, o construido desde Separe los valores de matiz, saturación y luminosidad.

Todas estas opciones son posibles mediante las extensiones de marcado XAML. Pero no deje que la frase "extensiones de marcado" SCARE: Las extensiones de marcado XAML *no* son extensiones de XML. Incluso con las extensiones de marcado XAML, XAML es siempre XML legal.

Una extensión de marcado es simplemente una manera diferente para expresar un atributo de un elemento. Las extensiones de marcado XAML son normalmente identificables mediante una configuración de atributo que se encierra entre llaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Es cualquier valor de atributo están entre llaves *siempre* una extensión de marcado XAML. Sin embargo, como verá, las extensiones de marcado XAML también pueden hacer referencia sin el uso de llaves.

En este artículo se divide en dos partes:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Consumo de extensiones de marcado XAML](consuming.md)  

Use las extensiones de marcado XAML definidas en Xamarin.Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Creación de extensiones de marcado XAML](creating.md)

Escribir sus propias extensiones de marcado XAML personalizados.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensiones de marcado XAML de Xamarin.Forms libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
