---
title: 'Xamarin.Forms Formas: línea'
description: La Xamarin.Forms clase line se puede usar para dibujar líneas.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 962800c5deb546ddf6a74d1f52ffaf60734ae463
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585850"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.Forms Formas: línea

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Line` clase se deriva de la `Shape` clase y se puede usar para dibujar líneas. Para obtener información sobre las propiedades que la `Line` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Line` define las siguientes propiedades:

- `X1`, de tipo Double, indica la coordenada x del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y1`, de tipo Double, indica la coordenada y del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `X2`, de tipo Double, indica la coordenada x del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y2`, de tipo Double, indica la coordenada y del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

Para obtener información acerca de cómo controlar el modo en que se dibujan los extremos de línea, vea [extremos de línea de control](index.md#control-line-ends).

## <a name="create-a-line"></a>Crear una línea

Para dibujar una línea, cree un `Line` objeto y establezca sus `X1` `Y1` propiedades y en su punto inicial, y sus `X2` `Y` propiedades y en su punto final. Además, establezca su `Stroke` propiedad en un [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado de porque una línea sin trazo no es visible. Para obtener más información sobre los `Brush` objetos, vea [ Xamarin.Forms pinceles](~/xamarin-forms/user-interface/brushes/index.md).

> [!NOTE]
> Establecer la `Fill` propiedad de `Line` no tiene ningún efecto, ya que una línea no tiene ningún interior.

En el siguiente ejemplo de XAML se muestra cómo dibujar una línea:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

En este ejemplo, se dibuja una línea diagonal roja desde (40, 0) a (0120):

![Línea diagonal](line-images/line.png "Línea")

Dado que `X1` las `Y1` propiedades,, `X2` y `Y2` tienen valores predeterminados de 0, es posible dibujar algunas líneas con la sintaxis mínima:

```xaml
<Line Stroke="Red"
      X2="200" />
```

En este ejemplo, se define una línea horizontal que tiene una longitud de 200 unidades independientes del dispositivo. Dado que las otras propiedades son 0 de forma predeterminada, se dibuja una línea de (0,0) a (200, 0).

En el siguiente ejemplo de XAML se muestra cómo dibujar una línea discontinua:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

En este ejemplo, se dibuja una línea diagonal discontinua azul oscura de (40, 0) a (0120):

![Línea discontinua](line-images/dashed-line.png "Línea discontinua")

Para obtener más información sobre cómo dibujar una línea discontinua, vea [dibujar formas discontinuas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
- [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md)
