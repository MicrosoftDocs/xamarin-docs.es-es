---
title: Resumen del capítulo 4. Desplazamiento de la pila
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 4. Desplazamiento de la pila'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 66e4f52e87a4398dd2e09d2d128f43de9a71a665
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760839"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumen del capítulo 4. Desplazamiento de la pila

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Este capítulo se dedica principalmente para introducir el concepto de *diseño*, que es el término general para las clases y las técnicas que utiliza Xamarin.Forms para organizar la presentación visual de varias vistas en la página.

Diseño implica varias clases que derivan de [ `Layout` ](xref:Xamarin.Forms.Layout) y [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1). Este capítulo se centra en [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> El [ `FlexLayout` ](~/xamarin-forms/user-interface/layouts/flex-layout.md) introducido en 3.0 de Xamarin.Forms se puede usar de maneras que son similares a `StackLayout` pero con más flexibilidad.

También ha introducido en este capítulo son el [ `ScrollView` ](xref:Xamarin.Forms.ScrollView), [ `Frame` ](xref:Xamarin.Forms.Frame), y [ `BoxView` ](xref:Xamarin.Forms.BoxView) clases.

## <a name="stacks-of-views"></a>Pilas de vistas

[`StackLayout`](xref:Xamarin.Forms.StackLayout) se deriva de `Layout<View>` y hereda un [ `Children` ](xref:Xamarin.Forms.Layout`1) propiedad de tipo `IList<View>`. Agregar varios elementos de vista a esta colección, y `StackLayout` mostrarlos en una pila horizontal o vertical.

Establecer el [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propiedad de `StackLayout` a un miembro de la [ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation) enumeración, ya sea [ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical) o [ `Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). De manera predeterminada, es `Vertical`.

Establecer el [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) propiedad de `StackLayout` a un `double` valor para especificar un espacio entre los elementos secundarios. El valor predeterminado es 6.

En el código, puede agregar elementos a la `Children` colección de `StackLayout` en un `for` o `foreach` bucle como se muestra en el [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) ejemplo, o bien se puede inicializar el `Children` colección con una lista de las vistas individuales como se muestra en [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Los elementos secundarios deben derivar de `View` , pero puede incluir otros `StackLayout` objetos.

## <a name="scrolling-content"></a>Desplazarse por el contenido

Si un `StackLayout` contiene demasiados elementos secundarios deben mostrarse en una página, puede colocar el `StackLayout` en un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) para permitir el desplazamiento.

Establecer el [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) propiedad de `ScrollView` a la vista que desee desplazarse. Esto suele ser un `StackLayout`, pero puede ser cualquier vista.

Establecer el [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) propiedad de `ScrollView` a un miembro de la [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) propiedad [ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical), [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal), o [ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both). De manera predeterminada, es `Vertical`. Si el contenido de un `ScrollView` es un `StackLayout`, las dos orientaciones deben ser coherentes.

El [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) ejemplo muestra el uso de `ScrollView` y `StackLayout` para mostrar los colores disponibles. El ejemplo también muestra cómo usar la reflexión de .NET para obtener todas las propiedades estáticas públicas y los campos de la `Color` estructura sin la necesidad de enumerarlos de forma explícita.

## <a name="the-expands-option"></a>La opción se expande

Cuando un `StackLayout` pilas de sus elementos secundarios, cada elemento secundario ocupa una posición concreta dentro de la altura total del `StackLayout` que depende de tamaño del elemento secundario y la configuración de su `HorizontalOptions` y `VerticalOptions` propiedades. Estas propiedades se asignan los valores de tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

El `LayoutOptions` estructura define dos propiedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) del tipo de enumeración [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment) con cuatro miembros [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), y [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de tipo `bool`

Para su comodidad, el `LayoutOptions` estructura define también ocho campos de solo lectura estáticos del tipo `LayoutOptions` que abarcan todas las combinaciones de las propiedades de la instancia de dos:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La siguiente discusión implica un `StackLayout` con una orientación vertical de forma predeterminada. Horizontal `StackLayout` es análoga.

Vertical `StackLayout`, `HorizontalOptions` configuración determina cómo un elemento secundario se coloca horizontalmente dentro del ancho de la `StackLayout`. Un `Alignment` de `Start`, `Center`, o `End` hace que el elemento secundario se horizontalmente sin restricciones. El elemento secundario determina su ancho y se coloca en la izquierda, centro o derecha de la `StackLayout`. El `Fill` opción hace que el elemento secundario necesario restringir horizontalmente y rellene el ancho de la `StackLayout`.

Vertical `StackLayout`, cada elemento secundario es verticalmente sin restricciones y obtiene una vertical ranura según el alto del elemento secundario, en cuyo caso el `VerticalOptions` configuración es irrelevante.

Si la vertical `StackLayout` es sin restricciones&mdash;decir si su `VerticalOptions` configuración es `Start`, `Center`, o `End`, a continuación, el alto de la `StackLayout` es la altura total de sus elementos secundarios.

Sin embargo, si la vertical `StackLayout` verticalmente está restringido&mdash;si su `VerticalOptions` configuración es `Fill` &mdash;, a continuación, el alto de la `StackLayout` será el alto de su contenedor, que podría ser mayor que el total alto de sus elementos secundarios. Si es así, y si tiene al menos un elemento secundario un `VerticalOptions` con un `Expands` marca de `true`, a continuación, el espacio adicional en el `StackLayout` se asignan de forma equitativa entre todos los elementos secundarios con un `Expands` marca de `true`. La altura total de los elementos secundarios, a continuación, será igual a la altura de la `StackLayout`y el `Alignment` forma parte de la `VerticalOptions` configuración determina cómo el elemento secundario se coloca en la ranura.

Esto se muestra en el [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) ejemplo.

## <a name="frame-and-boxview"></a>Marco y BoxView

Estas dos vistas rectangulares a menudo se usan para fines de presentación.

El [ `Frame` ](xref:Xamarin.Forms.Frame) vista muestra un marco rectangular en torno a otra vista, que puede ser un diseño, como `StackLayout`. `Frame` hereda un [ `Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad desde [ `ContentView` ](xref:Xamarin.Forms.ContentView) que establece en la vista que se mostrará en el `Frame`. El `Frame` es transparente, de forma predeterminada. Establezca las tres propiedades siguientes para personalizar la apariencia del marco:

- El [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor) propiedad para que sea visible. Es habitual para establecer `OutlineColor` a `Color.Accent` cuando no conoce el esquema de color subyacente.
- El [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow) propiedad puede establecerse en `true` para mostrar una sombra negra en dispositivos iOS.
- Establecer el [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedad a un `Thickness` valor dejar un espacio entre el marco y el marco de contenido. El valor predeterminado es de 20 unidades en todos los lados.

El `Frame` tiene un valor predeterminado `HorizontalOptions` y `VerticalOptions` valores de `LayoutOptions.Fill`, lo que significa que el `Frame` rellenará su contenedor. Con otras opciones, el tamaño de la `Frame` se basa en el tamaño de su contenido.

El `Frame` se muestra en el [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) ejemplo.

El [ `BoxView` ](xref:Xamarin.Forms.BoxView) muestra un área rectangular del color especificado por su [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propiedad.

Si el `BoxView` está restringido (su `HorizontalOptions` y `VerticalOptions` propiedades tienen sus valores predeterminados de `LayoutOptions.Fill`), el `BoxView` rellena el espacio disponible para él. Si el `BoxView` es sin restricciones (con `HorizontalOptions` y `LayoutOptions` configuración de `Start`, `Center`, o `End`), tiene una dimensión predeterminada del cuadrado de 40 unidades. Un `BoxView` puede restringir en una dimensión y sin restricciones en el otro.

A menudo, estableceremos la [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) y [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propiedades de `BoxView` para darle un tamaño específico. Esto se muestra en el [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) ejemplo.

Puede usar varias instancias de `StackLayout` para combinar una `BoxView` y varias `Label` instancias en un `Frame` para mostrar un color determinado y, a continuación, coloque cada una de estas vistas en un `StackLayout` en un `ScrollView` para crear el atractivo lista de colores se muestra en el [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) ejemplo:

[![Captura de pantalla triple de bloques de color](images/ch04fg11-small.png "lista de colores")](images/ch04fg11-large.png#lightbox "lista de colores")

## <a name="a-scrollview-in-a-stacklayout"></a>¿Un ScrollView en un StackLayout?

Colocar un `StackLayout` en un `ScrollView` es común, pero poner un `ScrollView` en un `StackLayout` a veces también es conveniente. En teoría, esto no debería ser posible porque los elementos secundarios de una vertical `StackLayout` son verticalmente sin restricciones. Pero un `ScrollView` debe restringirse verticalmente. Se debe proporcionar un alto específico para que, a continuación, puede determinar el tamaño de su elemento secundario para el desplazamiento.

El truco consiste en dar el `ScrollView` secundarios de la `StackLayout` un `VerticalOptions` de `FillAndExpand`. Esto se muestra en el [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) ejemplo.

El **BlackCat** ejemplo también muestra cómo definir y acceder a los recursos de programa que se incrustan en la biblioteca compartida. También puede lograrse con proyectos de activos compartidos (SAPs) pero el proceso es un poco más complicado, como el [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) muestra.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 4 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Ejemplos de capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Capítulo 4 F# ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
