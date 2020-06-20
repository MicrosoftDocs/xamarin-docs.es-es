---
title: Xamarin.FormsColocar
description: Xamarin.FormsLas formas son tipos de vistas que permiten dibujar formas en la pantalla.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c7c552abe724dca6b06265b73346a399d35c3cb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101393"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsColocar

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` es un tipo de [`View`](xref:Xamarin.Forms.View) que permite dibujar una forma en la pantalla. `Shape`los objetos se pueden usar dentro de las clases de diseño y la mayoría de los controles, ya que la `Shape` clase se deriva de la `View` clase.

Xamarin.FormsLas formas están disponibles en el `Xamarin.Forms.Shapes` espacio de nombres de iOS, Android, MacOS, el plataforma universal de Windows (UWP) y el Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLas formas actualmente son experimentales y solo se pueden usar si se establece la `Shapes_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

`Shape` define las siguientes propiedades:

- `Aspect`, de tipo `Stretch` , describe cómo la forma rellena su espacio asignado. El valor predeterminado de esta propiedad es `Stretch.None`.
- `Fill`, de tipo `Color` , indica el color usado para dibujar el interior de la forma.
- `Stroke`, de tipo `Color` , indica el color usado para dibujar el contorno de la forma.
- `StrokeDashArray`, de tipo `DoubleCollection` , que representa una colección de `double` valores que indican el modelo de guiones y espacios que se usan para esquematizar una forma.
- `StrokeDashOffset`, de tipo `double` , especifica la distancia dentro del modelo de guiones donde comienza un guión. El valor predeterminado de esta propiedad es 0,0.
- `StrokeLineCap`, de tipo `PenLineCap` , describe la forma al principio y al final de una línea o segmento. El valor predeterminado de esta propiedad es `PenLineCap.Flat`.
- `StrokeLineJoin`, de tipo `PenLineJoin` , especifica el tipo de combinación que se usa en los vértices de una forma. El valor predeterminado de esta propiedad es `PenLineJoin.Miter`.
- `StrokeThickness`, de tipo `double` , indica el ancho del contorno de la forma. El valor predeterminado de esta propiedad es 1,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Xamarin.Formsdefine un número de objetos que derivan de la `Shape` clase. Estos incluyen `Ellipse`, `Line`, `Path`, `Polygon`, `Polyline` y `Rectangle`.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
