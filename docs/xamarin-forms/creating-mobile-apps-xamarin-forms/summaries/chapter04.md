---
title: Resumen del capítulo 4. Desplazamiento de la pila
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 4. Desplazamiento de la pila'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b86bfe1e343cd50f4a133726d16364b3efb8bee
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198270"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumen del capítulo 4. Desplazamiento de la pila

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Este capítulo está dedicado principalmente a introducir el concepto de *diseño*, que es el término general para las clases y técnicas que usa Xamarin.Forms para organizar la presentación visual de varias vistas en la página.

El diseño implica varias clases que derivan de [`Layout`](xref:Xamarin.Forms.Layout) y [`Layout<T>`](xref:Xamarin.Forms.Layout`1). Este capítulo se centra en [`StackLayout`](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> La clase [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introducida en Xamarin.Forms 3.0 se puede usar de manera similar a `StackLayout` pero con mayor flexibilidad.

También se han introducido en este capítulo las clases [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`Frame`](xref:Xamarin.Forms.Frame) y [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="stacks-of-views"></a>Pilas de vistas

[`StackLayout`](xref:Xamarin.Forms.StackLayout) deriva de `Layout<View>` y hereda una propiedad [`Children`](xref:Xamarin.Forms.Layout`1) de tipo `IList<View>`. Agrega varios elementos de vista a esta colección y `StackLayout` los muestra en una pila horizontal o vertical.

Establezca la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) de `StackLayout` en un miembro de la enumeración [`StackOrientation`](xref:Xamarin.Forms.StackOrientation), de [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) o de [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). De manera predeterminada, es `Vertical`.

Establezca la propiedad [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) de `StackLayout` en un valor `double` para especificar un espaciado entre los elementos secundarios. El valor predeterminado es 6.

En el código, puede agregar elementos a la colección `Children` de `StackLayout` en un bucle de `for` o `foreach`, tal como se muestra en el ejemplo [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop), o puede inicializar la colección `Children` con una lista de las vistas individuales, tal como se muestra en [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Los elementos secundarios deben derivar de `View` pero pueden incluir otros objetos `StackLayout`.

## <a name="scrolling-content"></a>Desplazamiento por el contenido

Si una clase `StackLayout` contiene demasiados elementos secundarios para mostrarlos en una página, puede colocar el `StackLayout` en un [`ScrollView`](xref:Xamarin.Forms.ScrollView) para permitir el desplazamiento.

Establezca la propiedad [`Content`](xref:Xamarin.Forms.ScrollView.Content) de `ScrollView` en la vista que desea desplazarse. Suele ser una vista `StackLayout`, pero puede ser cualquier otra.

Establezca la propiedad [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) de `ScrollView` en un miembro de la propiedad [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation), [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical), [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) o [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both). De manera predeterminada, es `Vertical`. Si el contenido de una vista `ScrollView` es una `StackLayout`, las dos orientaciones deben ser coherentes.

En el ejemplo [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) se muestra el uso de `ScrollView` y `StackLayout` para mostrar los colores disponibles. En el ejemplo también se muestra cómo usar la reflexión de .NET para obtener todas las propiedades y campos estáticos públicos de la estructura `Color` sin necesidad de enumerarlos explícitamente.

## <a name="the-expands-option"></a>La opción Expands

Cuando una vist a`StackLayout` apila sus elementos secundarios, cada uno de ellos ocupa un espacio determinado en el alto total de la vista `StackLayout`, que depende del tamaño del elemento secundario y de la configuración de sus propiedades `HorizontalOptions` y `VerticalOptions`. A estas propiedades se les asignan valores de tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions).

La estructura `LayoutOptions` define dos propiedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) del tipo de enumeración [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) con cuatro miembros, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End) y [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de tipo `bool`

Para su comodidad, la estructura de `LayoutOptions` también define ocho campos de solo lectura estáticos de tipo `LayoutOptions` que abarcan todas las combinaciones de las dos propiedades de instancia:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

En el siguiente debate se incluye una vista `StackLayout` con una orientación vertical predeterminada. La vista `StackLayout` horizontal es análoga.

En el caso de una vista `StackLayout` vertical, el valor de `HorizontalOptions` determina cómo se coloca horizontalmente un elemento secundario dentro del ancho de `StackLayout`. Un valor `Alignment` de `Start`, `Center`o `End` hace que el elemento secundario no se restrinja horizontalmente. El elemento secundario determina su propio ancho y se coloca a la izquierda, en el centro o a la derecha de la vista `StackLayout`. La opción `Fill` hace que el elemento secundario se restrinja horizontalmente y rellene el ancho de `StackLayout`.

En el caso de una vista `StackLayout` vertical, cada elemento secundario no tiene restricciones vertical y obtiene una ranura vertical según el alto del elemento secundario, en cuyo caso el valor `VerticalOptions` es irrelevante.

Si la propia vista `StackLayout` vertical no está restringida &mdash;es decir, si su valor de `VerticalOptions` es `Start`, `Center`o `End`, el alto de la vista `StackLayout` es el alto total de sus elementos secundarios.

Sin embargo, si la vista `StackLayout` vertical está restringida verticalmente &mdash;es decir, si su valor de `VerticalOptions` es `Fill`&mdash;, el alto de `StackLayout` será el alto de su contenedor, que puede ser mayor que el alto total de sus elementos secundarios. Si es así, y si al menos un elemento secundario tiene un valor de `VerticalOptions` con una marca `Expands` de `true`, el espacio adicional de la vista `StackLayout` se asigna equitativamente entre todos los elementos secundarios con una marca `Expands` de `true`. El alto total de los elementos secundarios será igual al alto de la vista `StackLayout` y la parte `Alignment` del valor de `VerticalOptions` determina cómo se coloca verticalmente el elemento secundario en su espacio.

Esto se muestra en el ejemplo [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo).

## <a name="frame-and-boxview"></a>Frame y BoxView

Estas dos vistas rectangulares se usan a menudo para fines de presentación.

En la vista [`Frame`](xref:Xamarin.Forms.Frame) se muestra un marco rectangular alrededor de otra vista, que puede ser un diseño como `StackLayout`. `Frame` hereda una propiedad [`Content`](xref:Xamarin.Forms.ContentView.Content) de [`ContentView`](xref:Xamarin.Forms.ContentView) que se establece en la vista que se va a mostrar en el `Frame`. De forma predeterminada, la vista `Frame` es transparente. Establezca las tres propiedades siguientes para personalizar la apariencia del marco:

- La propiedad [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) para que sea visible. Es habitual establecer `OutlineColor` en `Color.Accent` cuando no se conoce la combinación de colores subyacente.
- La propiedad [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) se puede establecer en `true` para mostrar una sombra negra en dispositivos iOS.
- Establezca la propiedad [`Padding`](xref:Xamarin.Forms.Layout.Padding) en un valor `Thickness` para dejar un espacio entre el marco y el contenido del marco. El valor predeterminado es 20 unidades en todos los lados.

La vista `Frame` tiene los valores predeterminados de `HorizontalOptions` y `VerticalOptions` de `LayoutOptions.Fill`, lo que significa que la vista `Frame` rellenará su contenedor. Con otras opciones, el tamaño de la vista `Frame` se basa en el tamaño de su contenido.

La vista `Frame` se muestra en el ejemplo [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText).

En la vista [`BoxView`](xref:Xamarin.Forms.BoxView) se muestra un área rectangular de color especificada por su propiedad [`Color`](xref:Xamarin.Forms.BoxView.Color).

Si la vista `BoxView` está restringida (sus propiedades `HorizontalOptions` y `VerticalOptions` tienen su configuración predeterminada de `LayoutOptions.Fill`), `BoxView` rellena el espacio disponible. Si la vista `BoxView` no está restringida (con los valores de `HorizontalOptions` y `LayoutOptions` de `Start`, `Center`o `End`), tiene una dimensión predeterminada de 40 unidades cuadradas. Una vista `BoxView` se puede restringir en una dimensión y sin restricciones en la otra.

A menudo, establecerá las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de la vista `BoxView` para darle un tamaño específico. Esto se muestra en el ejemplo [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView).

Puede usar varias instancias de `StackLayout` para combinar una vista `BoxView` y varias instancias de `Label` en una vista `Frame` para mostrar un color determinado y, a continuación, colocar cada una de estas vistas en una vista `StackLayout` de un `ScrollView` para crear la lista atractiva de colores que se muestra en el ejemplo [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks):

[![Captura de pantalla triple de bloques de color](images/ch04fg11-small.png "Lista de colores")](images/ch04fg11-large.png#lightbox "Lista de colores")

## <a name="a-scrollview-in-a-stacklayout"></a>¿Una vista ScrollView en una vista StackLayout?

La colocación de una vista `StackLayout` en una vista `ScrollView` es habitual, pero también conviene colocar unaa `ScrollView` en una `StackLayout`. En teoría, esto no debería ser posible porque los elementos secundarios de una vista `StackLayout` vertical no están restringidos verticalmente. Pero una vista `ScrollView` debe estar restringida verticalmente. Se le debe proporcionar un alto específico para que pueda determinar el tamaño de su elemento secundario para el desplazamiento.

El truco consiste en dar al elemento secundario de `ScrollView` de la vista `StackLayout` un valor `VerticalOptions` de `FillAndExpand`. Esto último se muestra en el ejemplo [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat).

En el ejemplo **BlackCat** también se muestra cómo definir y obtener acceso a los recursos de programa que están insertados en la biblioteca compartida. Esto también se puede lograr con proyectos de recursos compartidos (SAP), pero el proceso es un poco más complicado, como se muestra en el ejemplo [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 4 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Ejemplos del capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Ejemplos de F# en el capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scrollview.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
