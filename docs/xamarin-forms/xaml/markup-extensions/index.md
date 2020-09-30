---
title: Extensiones de marcado XAML
description: En el artículo se explica cómo usar Xamarin.Forms las extensiones de marcado XAML para ampliar la eficacia y flexibilidad de XAML, permitiendo que los atributos de elemento se establezcan a partir de orígenes que no sean cadenas de texto literal.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98eb35697aee9022a837a7b0b531edb0c53b2239
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562150"
---
# <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Las extensiones de marcado XAML ayudan a ampliar la eficacia y flexibilidad de XAML, ya que permiten establecer atributos de elementos a partir de orígenes que no sean cadenas de texto literal.

Por ejemplo, normalmente se establece la `Color` propiedad de la siguiente `BoxView` manera:

```xaml
<BoxView Color="Blue" />
```

O bien, puede establecerlo en un valor de color RGB hexadecimal:

```xaml
<BoxView Color="#FF0080" />
```

En cualquier caso, la cadena de texto establecida en el `Color` atributo se convierte en un `Color` valor por la [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) clase.

En su lugar, es preferible establecer el `Color` atributo a partir de un valor almacenado en un diccionario de recursos, o del valor de una propiedad estática de una clase que haya creado, o de una propiedad de tipo `Color` de otro elemento de la página, o construido a partir de valores de matiz, saturación y luminosidad independientes.

Todas estas opciones son posibles con las extensiones de marcado XAML. Pero no deje que la frase "extensiones de marcado" SCARE: las extensiones de marcado XAML *no* son extensiones de XML. Incluso con las extensiones de marcado XAML, XAML siempre es XML legal.

Una extensión de marcado es en realidad una forma diferente de expresar un atributo de un elemento. Las extensiones de marcado XAML normalmente se pueden identificar mediante una configuración de atributo que se incluye entre llaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Cualquier valor de atributo entre llaves *siempre* es una extensión de marcado XAML. Sin embargo, como verá, también se puede hacer referencia a las extensiones de marcado XAML sin el uso de llaves.

Este artículo se divide en dos partes:

## <a name="consuming-xaml-markup-extensions"></a>[Consumo de extensiones de marcado XAML](consuming.md)  

Usar las extensiones de marcado XAML definidas en Xamarin.Forms .

## <a name="creating-xaml-markup-extensions"></a>[Creación de extensiones de marcado XAML](creating.md)

Escriba sus propias extensiones de marcado XAML personalizadas.

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensiones de marcado XAML del Xamarin.Forms libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)