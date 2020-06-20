---
title: 'Xamarin.FormsFormas: línea'
description: La Xamarin.Forms clase line se puede usar para dibujar líneas.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a69c505fe83618f06bafe6a49b8b5ce84aef096b
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101288"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.FormsFormas: línea

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Line` clase se deriva de la `Shape` clase y se puede usar para dibujar líneas. Para obtener información sobre las propiedades que la `Line` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Line` define las siguientes propiedades:

- `X1`, de tipo Double, indica la coordenada x del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `X2`, de tipo Double, indica la coordenada x del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y1`, de tipo Double, indica la coordenada y del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y2`, de tipo Double, indica la coordenada y del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

## <a name="create-a-line"></a>Crear una línea

En el siguiente ejemplo de XAML se muestra cómo dibujar una línea:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="4"
      HeightRequest="120"
      WidthRequest="120" />
```

> [!NOTE]
> Establecer la `Fill` propiedad de `Line` no tiene ningún efecto, ya que una línea no tiene ningún interior.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsColocar](index.md)
