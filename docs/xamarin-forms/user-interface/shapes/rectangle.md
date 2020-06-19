---
title: 'Xamarin.FormsFormas: rectángulo'
description: La Xamarin.Forms clase Rectangle se puede usar para dibujar rectángulos.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 979b7ab610091ea805237874eb25f4e052716010
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84947354"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.FormsFormas: rectángulo

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Rectangle` clase se deriva de la `Shape` clase y se puede usar para dibujar rectángulos. Para obtener información sobre las propiedades que la `Rectangle` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Rectangle` define las siguientes propiedades:

- `RadiusX`, de tipo `double` , que es el radio del eje x que se usa para redondear las esquinas del rectángulo. El valor predeterminado de esta propiedad es 0,0.
- `RadiusY`, de tipo `double` , que es el radio del eje y que se usa para redondear las esquinas del rectángulo. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La `Rectangle` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` .

## <a name="create-a-rectangle"></a>Crear un rectángulo

En el siguiente ejemplo de XAML se muestra cómo dibujar un rectángulo relleno con esquinas redondeadas:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           RadiusX="12"
           RadiusY="24"           
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapedemos/)
- [Xamarin.FormsColocar](index.md)
