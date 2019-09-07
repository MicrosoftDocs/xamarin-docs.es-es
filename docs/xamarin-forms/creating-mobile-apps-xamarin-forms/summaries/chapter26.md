---
title: Resumen del capítulo 26. Diseños personalizados
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 26. Diseños personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770937"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumen del capítulo 26. Diseños personalizados

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms incluye varias clases derivadas de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` y
- `RelativeLayout`.

Este capítulo describe cómo crear sus propias clases que derivan de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Información general de diseño

No hay ningún sistema centralizado que controla el diseño de Xamarin.Forms. Cada elemento es responsable de determinar cuál debe ser su propio tamaño y cómo representarse a sí misma a un área determinada.

### <a name="parents-and-children"></a>Elementos primarios y secundarios

Todos los elementos que tiene elementos secundarios es responsable de colocar a los elementos secundarios dentro de sí mismo. Es el elemento primario que en última instancia determina el tamaño de sus elementos secundarios debe basarse en el tamaño que tiene disponibles y el tamaño del elemento secundario que desea que esté.

### <a name="sizing-and-positioning"></a>Cambiar el tamaño y posición

Diseño comienza en la parte superior del árbol visual con la página y, a continuación, recorre todas las ramas. El método público más importante en el diseño es [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) definido por `VisualElement`. Todos los elementos que es un elemento primario para otras llamadas a los elementos `Layout` para cada uno de sus elementos secundarios para dar el elemento secundario de un tamaño y posición con respecto a sí mismo en forma de un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) valor. Estos `Layout` propagan las llamadas a través del árbol visual.

Una llamada a `Layout` es necesaria para un elemento que aparezcan en la pantalla y hace que las siguientes propiedades de solo lectura debe establecerse. Son coherentes con la `Rectangle` pasa al método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) de tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) de tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) de tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) de tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) de tipo `double`

Anteriores a la `Layout` llamar, `Height` y `Width` tienen valores ficticios de &ndash;1.

Una llamada a `Layout` también desencadena las llamadas a los métodos protegidos siguientes:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que llama a
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que se puede invalidar.

Por último, se desencadena el evento siguiente:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

El `OnSizeAllocated` método se reemplaza por `Page` y `Layout`, que son las clases solo dos de Xamarin.Forms que puede tener elementos secundarios. Las llamadas de método invalidado

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) para `Page` derivados y [ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) para `Layout` derivados, que llama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para `Page` derivados y [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para `Layout` derivados.

`LayoutChildren` a continuación, llama a `Layout` para cada uno de los elementos secundarios del elemento. Si al menos un elemento secundario tiene un nuevo `Bounds` establecer, a continuación, se desencadena el evento siguiente:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) para `Page` derivados y [ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged) para `Layout` derivados

### <a name="constraints-and-size-requests"></a>Las restricciones y las solicitudes de tamaño

Para `LayoutChildren` llamar de forma inteligente `Layout` en todos sus elementos secundarios, debe conocer una *preferido* o *deseado* tamaño para los elementos secundarios. Por lo tanto, las llamadas a `Layout` para cada uno de los elementos secundarios se suelen ir precedidos por las llamadas a

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Después de que se ha publicado el libro, el `GetSizeRequest` fue en desuso y se reemplazan por método

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

El `Measure` método admite la [ `Margin` ](xref:Xamarin.Forms.View.Margin) propiedad e incluye un argumento de tipo [ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags), que tiene dos miembros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) para no incluir los márgenes

Para muchos elementos, `GetSizeRequest` o `Measure` Obtiene el tamaño del elemento nativo de su representador. Ambos métodos tienen parámetros de anchura y altura *restricciones*. Por ejemplo, un `Label` usará la restricción de ancho para determinar cómo ajustar varias líneas de texto.

Ambos `GetSizeRequest`y `Measure` devuelven un valor de tipo [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest), que tiene dos propiedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) de tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) de tipo `Size`

Muy a menudo, estos dos valores son iguales y el `Minimum` normalmente se puede omitir el valor.

`VisualElement` También define un método protegido similar a `GetSizeRequest` que se llama desde `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) Devuelve un `SizeRequest` valor

Ahora está en desuso y se reemplazan por ese método:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Cada clase que derive de `Layout` o `Layout<T>` debe invalidar `OnSizeRequest` o `OnMeasure`. Esto es donde una clase de diseño determina su propio tamaño, que normalmente se basa en el tamaño de sus elementos secundarios, que obtiene mediante una llamada a `GetSizeRequest` o `Measure` en los elementos secundarios. Antes y después de llamar a `OnSizeRequest` o `OnMeasure`, `GetSizeRequest` o `Measure` realiza ajustes en función de las siguientes propiedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)de tipo `double`, afecta a la `Request` propiedad de `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de tipo `double`, afecta a la `Request` propiedad de `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) de tipo `double`, afecta a la `Minimum` propiedad de `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) de tipo `double`, afecta a la `Minimum` propiedad de `SizeRequest`

### <a name="infinite-constraints"></a>Restricciones infinitas

Pasan los argumentos de restricción a `GetSizeRequest` (o `Measure`) y `OnSizeRequest` (o `OnMeasure`) puede ser infinito (es decir, los valores de `Double.PositiveInfinity`). Sin embargo, el `SizeRequest` devuelto a partir de estos métodos no pueden contener dimensiones infinitas.

Infinitas restricciones indican que el tamaño solicitado debe reflejar el tamaño natural del elemento. Vertical `StackLayout` llamadas `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de altura infinito. Llama un diseño de pila horizontal `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de ancho infinito. Un `AbsoluteLayout` llamadas `GetSizeRequest` (o `Measure`) en sus elementos secundarios con restricciones de ancho y alto infinitos.

### <a name="peeking-inside-the-process"></a>Inspeccionar dentro del proceso

El [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) restricción de muestra y el tamaño solicitan información sobre un diseño sencillo.

## <a name="deriving-from-layoutview"></a>Derivar de la\<vista de diseño >

Derive una clase de diseño personalizado de `Layout<View>`. Tiene dos responsabilidades:

- Invalidar `OnMeasure` para llamar a `Measure` en elementos secundarios de la del diseño. Devolver un tamaño solicitado para el diseño propio
- Invalidar `LayoutChildren` para llamar a `Layout` en elementos secundarios del todo el diseño

El `for` o `foreach` bucle en estas invalidaciones debe omitir cualquier elemento secundario cuyo `IsVisible` propiedad está establecida en `false`.

Una llamada a `OnMeasure` no está garantizado. `OnMeasure` no se llamará si el elemento primario del diseño es que rigen el tamaño del diseño (por ejemplo, un diseño que llena una página). Por este motivo, `LayoutChildren` no puede depender de tamaños secundarios obtenidos durante el `OnMeasure` llamar. Muy a menudo, `LayoutChildren` debe llamar propio `Measure` en elementos secundarios del diseño, o puede implementar algún tipo de tamaño de almacenamiento en caché lógica (que se explicará más adelante).

### <a name="an-easy-example"></a>Un ejemplo sencillo

El [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) ejemplo contiene una representación simplificada [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) clase y una demostración de su uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Vertical y horizontal posicionamiento simplificado

Uno de los trabajos que `VerticalStack` debe llevar a cabo se produce durante el `LayoutChildren` invalidar. El método usa el hijo `HorizontalOptions` propiedad para determinar cómo colocar el elemento secundario dentro de su ranura en la `VerticalStack`. En su lugar, puede llamar al método estático [ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Este método llama a `Measure` en la secundaria y usa su `HorizontalOptions` y `VerticalOptions` propiedades para colocar el elemento secundario dentro del rectángulo especificado.

### <a name="invalidation"></a>Invalidación

A menudo un cambio en la propiedad de un elemento afecta a cómo aparece dicho elemento en el diseño. El diseño debe ser invalidado para desencadenar un nuevo diseño.

`VisualElement` define un método protegido [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), por lo general que es invocado por el controlador de cambio de propiedad de cualquier propiedad enlazable cuyo cambio afecta el tamaño del elemento. El `InvalidateMeasure` método activa un [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) eventos.

El `Layout` clase define un método protegido similar denominado [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout), que un `Layout` derivado debe llamar a cualquier cambio que afecta a cómo coloca y ajusta el tamaño de sus elementos secundarios.

### <a name="some-rules-for-coding-layouts"></a>Algunas reglas para la codificación de diseños

1. Propiedades definidas por `Layout<T>` derivados deben estar respaldados por propiedades enlazables y deben llamar los controladores de cambio de propiedad `InvalidateLayout`.

2. Un `Layout<T>` derivado que define las propiedades enlazables adjuntas debe reemplazar [ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*) para agregar un controlador de cambio de propiedad a sus elementos secundarios y [ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*) que se quitarán controlador. El controlador debe comprobar los cambios en estos adjunta propiedades enlazables y responder mediante una llamada a `InvalidateLayout`.

3. Un `Layout<T>` derivada que implementa una caché de tamaños secundarios debe reemplazar `InvalidateLayout` y [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) y borrar la memoria caché cuando se llama a estos métodos.

### <a name="a-layout-with-properties"></a>Un diseño con propiedades

El [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) supone que todos sus elementos secundarios son el mismo tamaño y ajusta los elementos secundarios de una fila (o columna) a la siguiente. Define un `Orientation` propiedad como `StackLayout`, y `ColumnSpacing` y `RowSpacing` propiedades como `Grid`, y lo almacena en caché los tamaños de los secundarios.

El [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) ejemplo pone un `WrapLayout` en un `ScrollView` para mostrar las fotografías almacenadas.

### <a name="no-unconstrained-dimensions-allowed"></a>¡No hay dimensiones sin restricciones permitidos!

El [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca está pensada para mostrar sus elementos secundarios dentro de sí mismo. Por lo tanto, no se puede tratar con dimensiones sin restricciones y produce una excepción si se encuentra uno.

El [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) muestra `UniformGridLayout`:

[![Captura de pantalla triple de la cuadrícula de fotos](images/ch26fg08-small.png "uniforme diseño de cuadrícula")](images/ch26fg08-large.png#lightbox "uniforme diseño de cuadrícula")

### <a name="overlapping-children"></a>Elementos secundarios superpuestos

Un `Layout<T>` derivado puede superponerse a sus elementos secundarios. Sin embargo, los elementos secundarios se representan en su orden en el `Children` colección y no el orden en que sus `Layout` se llama a métodos.

La `Layout` clase define dos métodos que le permiten mover un elemento secundario dentro de la colección:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) Para mover a un elemento secundario al principio de la colección
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) Para mover a un elemento secundario al final de la colección

Para los elementos secundarios superpuestos, los elementos secundarios al final de la colección aparecen visualmente encima de los elementos secundarios al principio de la colección.

El [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define una propiedad adjunta para indicar el orden de procesamiento y permitir, por tanto, uno de sus elementos secundarios que se mostrará sobre los demás. El [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) muestra esto:

[![Triple captura de pantalla de cuadrícula de archivos de tarjeta de alumno](images/ch26fg10-small.png "superposición de elementos secundarios del diseño")](images/ch26fg10-large.png#lightbox "elementos secundarios superpuestos de diseño")

### <a name="more-attached-bindable-properties"></a>Más conectado propiedades enlazables

El [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define las propiedades enlazables adjuntas para especificar dos `Point` valores y un valor del grosor y manipula `BoxView` elementos que son similares a las líneas.

El [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) ejemplo que usa para dibujar un cubo 3D.

### <a name="layout-and-layoutto"></a>Diseño y LayoutTo

Un `Layout<T>` derivado puede llamar a `LayoutTo` lugar `Layout` para animar el diseño. El [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) clase hace esto y el [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) ejemplo muestra.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 26 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Capítulo 26 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Crear diseños personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
