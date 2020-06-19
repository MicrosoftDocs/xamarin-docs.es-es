---
title: 'Xamarin.FormsFormas: Polígono'
description: La Xamarin.Forms clase Polygon se puede usar para dibujar polígonos, que son una serie de líneas conectadas que forman formas cerradas.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de1848f7439e62e84daea56defa3f4583f5758ea
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84947355"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsFormas: Polígono

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polygon` clase se deriva de la `Shape` clase y se puede usar para dibujar polígonos, que son una serie de líneas conectadas que forman formas cerradas. Para obtener información sobre las propiedades que la `Polygon` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Polygon` define las siguientes propiedades:

- `Points`, de tipo `PointCollection` , que es una colección de `Point` estructuras que describen los puntos de vértice del polígono.
- `FillRule`, de tipo `FillRule` , que especifica cómo se determina el relleno interior de la forma. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

## <a name="create-a-polygon"></a>Crear un polígono

En el siguiente ejemplo de XAML se muestra cómo dibujar un polígono:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         HeightRequest="100"
         WidthRequest="200" />
```

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapedemos/)
- [Xamarin.FormsColocar](index.md)
