---
title: Resumen del capítulo 26. Diseños personalizados
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 26. Diseños personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: deb46d1a70e7c707c998be8669b4af3b8e8d7ead
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136609"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumen del capítulo 26. Diseños personalizados

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms incluye varias clases derivadas de [`Layout<View>`](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` y
- `RelativeLayout`.

En este capítulo se describe cómo crear sus propias clases que se derivan de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Información general sobre el diseño

No hay ningún sistema centralizado que controle el diseño de Xamarin.Forms. Cada elemento es responsable de determinar cuál debe ser su propio tamaño y cómo representarse en un área determinada.

### <a name="parents-and-children"></a>Elementos primarios y secundarios

Cada elemento que tenga elementos secundarios es responsable de colocar esos elementos secundarios en su interior. Es el elemento primario el que determina en última instancia el tamaño de sus elementos secundarios en función del tamaño que tiene disponible y el tamaño que el elemento secundario desea tener.

### <a name="sizing-and-positioning"></a>Ajuste del tamaño y el posicionamiento

El diseño comienza en la parte superior del árbol visual con la página y luego continúa a través de todas las ramas. El método público más importante del diseño es [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)), definido por `VisualElement`. Cada elemento que tiene un rango superior respecto a otros elementos llama a `Layout` para cada uno de sus elementos secundarios para darle a estos un tamaño y una posición con relación a sí mismo en forma de un valor [`Rectangle`](xref:Xamarin.Forms.Rectangle). Estas llamadas a `Layout` se propagan a través del árbol visual.

Se requiere una llamada a `Layout` para que un elemento aparezca en la pantalla, y hace que se establezcan las siguientes propiedades de solo lectura. Son coherentes con los `Rectangle` pasados al método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) de tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) de tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) de tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) de tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) de tipo `double`

Antes de la llamada a `Layout`, `Height` y `Width` tienen valores ficticios de &ndash;1.

Una llamada a `Layout` también desencadena llamadas a los siguientes métodos protegidos:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que llama.
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que puede reemplazarse.

Por último, se desencadena el siguiente evento:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

El método `OnSizeAllocated` se reemplaza por `Page` y `Layout`, que son las dos únicas clases de Xamarin.Forms que pueden tener elementos secundarios. El método reemplazado llama a:

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) para los derivados de `Page` y [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) para los derivados de `Layout`, que llama.
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para los derivados de `Page` y [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para los derivados de `Layout`.

`LayoutChildren` a continuación, llama a `Layout` para cada uno de los elementos secundarios del elemento. Si al menos un elemento secundario tiene un nuevo valor de `Bounds`, se desencadena el siguiente evento:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) para los derivados de `Page` y [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) para los derivados de `Layout`.

### <a name="constraints-and-size-requests"></a>Restricciones y solicitudes de tamaño

Para que `LayoutChildren` llame de forma inteligente a `Layout` en todos sus elementos secundarios, debe conocer un tamaño *preferido* o *deseado* para los elementos secundarios. Por lo tanto, las llamadas a `Layout` para cada uno de los elementos secundarios están precedidas por llamadas a

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Una vez publicado el libro, el método de `GetSizeRequest` ha quedado en desuso y se ha reemplazado por

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

El método `Measure` aloja la propiedad [`Margin`](xref:Xamarin.Forms.View.Margin) e incluye un argumento de tipo [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags), que tiene dos miembros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) para no incluir márgenes

Para muchos elementos, `GetSizeRequest` o `Measure` obtienen el tamaño nativo del elemento de su representador. Ambos métodos tienen parámetros para las *restricciones* de ancho y alto. Por ejemplo, `Label` utilizará la restricción de ancho para determinar cómo se ajustan varias líneas de texto.

Tanto `GetSizeRequest` como `Measure` devuelven un valor de tipo [`SizeRequest`](xref:Xamarin.Forms.SizeRequest), que tiene dos propiedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) de tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) de tipo `Size`

Con frecuencia, estos dos valores son el mismo y, por lo general, el valor `Minimum` se puede pasar por alto.

`VisualElement` también define un método protegido similar a `GetSizeRequest` al que se llama desde `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) devuelve un valor `SizeRequest`.

Ese método ahora está en desuso y se reemplaza por:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Cada clase que se deriva de `Layout` o `Layout<T>` debe invalidar `OnSizeRequest` o `OnMeasure`. Aquí es donde una clase de diseño determina su propio tamaño, que generalmente se basa en el tamaño de sus elementos secundarios, que obtiene llamando a `GetSizeRequest` o `Measure` en los elementos secundarios. Antes y después de llamar a `OnSizeRequest` o `OnMeasure`, `GetSizeRequest` o `Measure` realiza ajustes en función de las siguientes propiedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de tipo `double`, afecta a la propiedad `Request` de `SizeRequest`.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de tipo `double`, afecta a la propiedad `Request` de `SizeRequest`.
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) de tipo `double`, afecta a la propiedad `Minimum` de `SizeRequest`.
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) de tipo `double`, afecta a la propiedad `Minimum` de `SizeRequest`.

### <a name="infinite-constraints"></a>Restricciones infinitas

Los argumentos de restricción pasados a `GetSizeRequest` (o `Measure`) y `OnSizeRequest` (o `OnMeasure`) pueden ser infinitos (es decir, valores de `Double.PositiveInfinity`). Sin embargo, los `SizeRequest` devueltos de estos métodos no pueden contener dimensiones infinitas.

Las restricciones infinitas indican que el tamaño solicitado debe reflejar el tamaño natural del elemento. Un `StackLayout` vertical llama a `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de alto infinito. Un diseño de pila horizontal llama a `GetSizeRequest` (o `Measure`) en sus elementos secundarios con una restricción de ancho infinito. Un `AbsoluteLayout` llama a `GetSizeRequest` (o `Measure`) en sus elementos secundarios con restricciones de ancho y alto infinitos.

### <a name="peeking-inside-the-process"></a>Inspección dentro del proceso

El elemento [**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) muestra la información de la solicitud de restricción y tamaño para un diseño sencillo.

## <a name="deriving-from-layoutview"></a>Derivación de Layout\<View>

Una clase de diseño personalizado se deriva de `Layout<View>`. Tiene dos responsabilidades:

- Invalidar `OnMeasure` para llamar a `Measure` en todos los elementos secundarios del diseño. Devolver un tamaño solicitado para el propio diseño.
- Invalidar `LayoutChildren` para llamar a `Layout` en todos los elementos secundarios del diseño.

Los bucles `for` o `foreach` de estas invalidaciones deberían omitir cualquier elemento secundario cuya propiedad `IsVisible` esté establecida en `false`.

No se garantiza una llamada a `OnMeasure`. No se llamará a `OnMeasure` si el elemento primario del diseño controla el tamaño del diseño (por ejemplo, un diseño que llene una página). Por esta razón, `LayoutChildren` no puede basarse en los tamaños secundarios obtenidos durante la llamada a `OnMeasure`. Con mucha frecuencia, `LayoutChildren` debe llamar a `Measure` en los elementos secundarios del diseño, o puede implementar algún tipo de lógica de almacenamiento en caché del tamaño (que se tratará más adelante).

### <a name="an-easy-example"></a>Un ejemplo sencillo

El ejemplo [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contiene una clase [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) simplificada y una demostración de su uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posicionamiento vertical y horizontal simplificado

Uno de los trabajos que `VerticalStack` debe realizar se produce durante la invalidación de `LayoutChildren`. El método usa la propiedad `HorizontalOptions` del elemento secundario para determinar cómo colocar el elemento secundario dentro de su espacio en `VerticalStack`. En su lugar, puede llamar al método estático [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Este método llama a `Measure` en el elemento secundario y utiliza sus propiedades `HorizontalOptions` y `VerticalOptions` para colocar el elemento secundario dentro del rectángulo especificado.

### <a name="invalidation"></a>Invalidación

A menudo, un cambio en la propiedad de un elemento afecta al modo en que ese elemento aparece en el diseño. El diseño se debe invalidar para desencadenar un nuevo diseño.

`VisualElement` define un método protegido [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), al que se suele llamar mediante el controlador modificado por la propiedad de cualquier propiedad enlazable cuyo cambio afecte al tamaño del elemento. El método `InvalidateMeasure` activa un evento [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated).

La clase `Layout` define un método protegido similar denominado [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout), al que un derivado de `Layout` debe llamar para cualquier cambio que afecte al modo en que coloca y ajusta el tamaño de sus elementos secundarios.

### <a name="some-rules-for-coding-layouts"></a>Algunas reglas para la codificación de diseños

1. Las propiedades definidas por derivados de `Layout<T>` deben estar respaldadas por propiedades enlazables y los controladores modificados por propiedad deben llamar a `InvalidateLayout`.

2. Un derivado de `Layout<T>` que define las propiedades enlazables asociadas debe reemplazar a [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) para agregar un controlador modificado por propiedad a sus elementos secundarios y [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) para quitar ese controlador. El controlador debe comprobar si hay cambios en estas propiedades enlazables asociadas y responder llamando a `InvalidateLayout`.

3. Un derivado de `Layout<T>` que implementa una memoria caché de tamaños secundarios debe reemplazar a `InvalidateLayout` y [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) y borrar la memoria caché cuando se llama a estos métodos.

### <a name="a-layout-with-properties"></a>Un diseño con propiedades

En la clase [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) de [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) se supone que todos sus elementos secundarios tienen el mismo tamaño, y se ajustan los elementos secundarios de una fila (o columna) a la siguiente. Define una propiedad `Orientation` como `StackLayout`, y las propiedades `ColumnSpacing` y `RowSpacing` como `Grid`, y almacena en caché los tamaños secundarios.

En el ejemplo de [**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) se coloca un `WrapLayout` en un `ScrollView` para mostrar las fotos en existencias.

### <a name="no-unconstrained-dimensions-allowed"></a>No se permiten dimensiones sin restricciones.

El valor [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) está diseñada para mostrar todos sus elementos secundarios en su interior. Por lo tanto, no puede tratar con dimensiones sin restricciones y genera una excepción si se encuentra una.

En el ejemplo [**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) se muestra `UniformGridLayout`:

[![Captura de pantalla triple de la cuadrícula fotográfica](images/ch26fg08-small.png "Diseño de cuadrícula uniforme")](images/ch26fg08-large.png#lightbox "Diseño de cuadrícula uniforme")

### <a name="overlapping-children"></a>Elementos secundarios superpuestos

Un derivado de `Layout<T>` puede superponerse a sus elementos secundarios. Sin embargo, los elementos secundarios se representan en su orden en la colección de `Children` y no en el orden en que se llama a los métodos de `Layout`.

La clase `Layout` define dos métodos que le permiten desplazar un elemento secundario dentro de la colección:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) para desplazar un elemento secundario al principio de la colección
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) para desplazar un elemento secundario al final de la colección

En el caso de los elementos secundarios superpuestos, los elementos secundarios al final de la colección aparecen visualmente encima de los elementos secundarios al principio de la colección.

La clase [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) de la biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define una propiedad asociada para indicar el orden de representación y, por tanto, permitir que uno de sus elementos secundarios se muestre encima de los demás. En el ejemplo [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) se muestra lo siguiente:

[![Captura de pantalla triple de la cuadrícula de archivos de la tarjeta de alumnos](images/ch26fg10-small.png "Superposición de elementos secundarios de diseño")](images/ch26fg10-large.png#lightbox "Superposición de elementos secundarios de diseño")

### <a name="more-attached-bindable-properties"></a>Más propiedades enlazables asociadas

La clase [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define las propiedades enlazables asociadas para especificar dos valores de `Point` y un valor de grosor y manipula los elementos de `BoxView` para que se parezca a las líneas.

El ejemplo [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) se usa para dibujar un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout y LayoutTo

Un derivado de `Layout<T>` puede llamar a `LayoutTo` en lugar de `Layout` para animar el diseño. La clase [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) se ocupa de ello, y el ejemplo [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) lo demuestra.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 26 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Ejemplos del capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
