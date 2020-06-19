---
title: 'Xamarin.FormsFormas: Polyline'
description: La Xamarin.Forms clase Polyline se puede usar para dibujar una serie de líneas rectas conectadas.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6a137de367a99026cf44c92a86afe5f3c6f0cf2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84947347"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.FormsFormas: Polyline

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polyline` clase se deriva de la `Shape` clase y se puede usar para dibujar una serie de líneas rectas conectadas. Para obtener información sobre las propiedades que la `Polyline` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Polyline` define las siguientes propiedades:

- `Points`, de tipo `PointCollection` , que es una colección de `Point` estructuras que describen los puntos de vértice de la polilínea.
- `FillRule`, de tipo `FillRule` , que especifica cómo se combinan las áreas de intersección de la polilínea. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

## <a name="create-a-polyline"></a>Crear una polilínea

En el siguiente ejemplo de XAML se muestra cómo dibujar un polígono:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          HeightRequest="100"
          WidthRequest="500" />
```

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapedemos/)
- [Xamarin.FormsColocar](index.md)
