---
title: Propiedades, métodos y eventos de controles comunes de Xamarin. Forms
description: En este artículo se describen las propiedades, los métodos y los eventos comunes definidos en la clase VisualElement, que se suelen usar en las clases derivadas.
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 6d10e665c6461655440ddfb2c524cb56a14337f6
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305772"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Propiedades, métodos y eventos de controles comunes de Xamarin. Forms

La clase de `VisualElement` de Xamarin. Forms es la clase base para la mayoría de los controles que se usan en una aplicación de Xamarin. Forms. La clase `VisualElement` define muchas [propiedades](#properties), [métodos](#methods)y [eventos](#events) que se usan en las clases derivadas.

## <a name="properties"></a>Propiedades

Las siguientes propiedades están disponibles en las instancias de `VisualElement`. Para obtener una lista completa, consulte las propiedades de la [API de VisualElement](xref:Xamarin.Forms.VisualElement#properties).

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

La propiedad `AnchorX` es un valor `double` que define el punto central en el eje X para las transformaciones como la escala y la rotación. El valor predeterminado es 0,5.

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

La propiedad `AnchorY` es un valor `double` que define el punto central en el eje X para las transformaciones como la escala y la rotación. El valor predeterminado es 0,5.

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

La propiedad `BackgroundColor` es una `Color` que determina el color de fondo del control. Si no se establece, el fondo será el objeto `Color` predeterminado, que se representa como transparente.

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

La propiedad `Behaviors` es una `List` de objetos `Behavior`. Los comportamientos permiten asociar la funcionalidad reutilizable a los elementos agregándolos a la lista de `Behaviors`. Para obtener más información sobre la clase `Behavior`, consulte [comportamientos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/index.md).

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

La propiedad `Bounds` es un objeto de `Rectangle` de solo lectura que representa el espacio ocupado por el control. El valor de la propiedad `Bounds` se asigna durante el ciclo de diseño. El `struct` `Rectangle` contiene propiedades y métodos útiles para probar la intersección y la contención de los rectángulos. Para obtener más información, consulte la [API de rectángulo de Xamarin. Forms](xref:Xamarin.Forms.Rectangle).

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

La propiedad `Effects` es una `List` de objetos `Effect`, heredada de la clase `Element`(XREF: Xamarin. Forms. Element). Los efectos permiten personalizar los controles nativos y se suelen usar para pequeños cambios de estilo. Para obtener más información sobre la clase `Effect`, vea [efectos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

La propiedad `FlowDirection` es un valor de enumeración `FlowDirection`. La dirección de flujo se puede establecer en `MatchParent`, `LeftToRight`o `RightToLeft` y determina el orden y la dirección del diseño. La propiedad `FlowDirection` se usa normalmente para admitir idiomas que se leen de derecha a izquierda.

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

La propiedad `Height` es un valor de `double` de solo lectura que describe el alto representado del control. La propiedad `Height` se calcula durante el ciclo de diseño y no se puede establecer directamente. El alto de un control se puede solicitar mediante la [propiedad HeightRequest](#heightrequest).

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

La propiedad `HeightRequest` es un valor `double` que determina el alto deseado del control. Es posible que el alto absoluto del control no coincida con el valor solicitado. Para obtener más información, consulte propiedades de la [solicitud](#request-properties).

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

La propiedad `InputTransparent` es una `bool` que determina si el control recibe datos proporcionados por el usuario. El valor predeterminado es `false`, asegurándose de que el elemento recibe la entrada. Esta propiedad se transfiere a los elementos secundarios cuando se establece. Si se establece la propiedad `InputTransparent` en `true` en una clase de diseño, todos los elementos del diseño no recibirán la entrada.

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

La propiedad `IsEnabled` es un valor `bool` que determina si el control reacciona a los datos proporcionados por el usuario. El valor predeterminado es `true`. Si establece esta propiedad en false, impedirá que el control acepte datos proporcionados por el usuario.

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

La propiedad `IsFocused` es un valor `bool` que describe si el control es actualmente el objeto que tiene el foco. Si se llama al método [`Focus`](#focus) en el control, el valor de `IsFocused` se establecerá en true. Al llamar al método [`Unfocus`](#unfocus) se establecerá esta propiedad en false.

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

La propiedad `IsTabStop` es un valor `bool` que define si el control recibe el foco cuando el usuario avanza por los controles con la tecla TAB. Si esta propiedad es false, la propiedad `TabIndex` no tendrá ningún efecto.

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

La propiedad `IsVisible` es un valor `bool` que determina si se representa el control. No se mostrarán los controles con la propiedad `IsVisible` establecida en false, no se tendrán en cuenta para los cálculos de espacio durante el ciclo de diseño y no podrán aceptar la entrada del usuario.

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

La propiedad `MinimumHeightRequest` es un valor `double` que determina cómo se controla el desbordamiento cuando dos elementos compiten por el espacio limitado. El establecimiento de la propiedad `MinimumHeightRequest` permite que el proceso de diseño escale el elemento hacia abajo hasta la dimensión mínima solicitada. Si no se especifica ningún `MinimumHeightRequest`, el valor predeterminado es-1 y el proceso de diseño considerará que el `HeightRequest` es el valor mínimo. Esto significa que los elementos sin valor `MinimumHeightRequest` no tendrán un alto escalable.

Para obtener más información, vea [propiedades de solicitud mínima](#minimum-request-properties).

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

La propiedad `MinimumWidthRequest` es un valor `double` que determina cómo se controla el desbordamiento cuando dos elementos compiten por el espacio limitado. El establecimiento de la propiedad `MinimumWidthRequest` permite que el proceso de diseño escale el elemento hacia abajo hasta la dimensión mínima solicitada. Si no se especifica ningún `MinimumWidthRequest`, el valor predeterminado es-1 y el proceso de diseño considerará que el `WidthRequest` es el valor mínimo. Esto significa que los elementos sin valor `MinimumWidthRequest` no tendrán un ancho escalable.

Para obtener más información, vea [propiedades de solicitud mínima](#minimum-request-properties).

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

La propiedad `Opacity` es un valor `double` de cero a uno que determina la opacidad del control durante la representación. El valor predeterminado de esta propiedad es 1,0. Se fijarán los valores fuera del intervalo de 0 a 1. La propiedad `Opacity` solo se aplica si se `true`la propiedad [`IsVisible`](#isvisible) . La opacidad se aplica de forma iterativa. Por lo tanto, si un control principal tiene una opacidad de 0,5 y su elemento secundario tiene una opacidad de 0,5, el elemento secundario se representará con un valor de opacidad de 0,25 vigente. Establecer la propiedad `Opacity` de un control de entrada en 0 tiene un comportamiento indefinido.

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

La propiedad `Parent` se hereda de la clase `Element`. Esta propiedad es un objeto `Element` que es el elemento primario del control. Normalmente, la propiedad `Parent` se establece automáticamente en un elemento cuando se agrega como elemento secundario de otro elemento.

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

La propiedad `Resources` es una instancia de `ResourceDictionary` que se rellena con pares clave-valor que normalmente se rellenan en tiempo de ejecución desde XAML. Este diccionario permite a los desarrolladores de aplicaciones reutilizar los objetos definidos en XAML en tiempo de compilación y en tiempo de ejecución. Las claves del diccionario se rellenan a partir del atributo `x:Key` de la etiqueta XAML. El objeto creado a partir de XAML se inserta en el `ResourceDictionary` para la clave especificada. una vez inicializado.

Para obtener más información, vea [diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

La propiedad `Rotation` es un valor `double` entre cero y 360 que define el giro sobre el eje Z en grados. El valor predeterminado de esta propiedad es 0. La rotación se aplica en relación con los valores de [`AnchorX`](#anchorx) y [`AnchorY`](#anchory) .

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

La propiedad `RotationX` es un valor `double` entre cero y 360 que define el giro sobre el eje X en grados. El valor predeterminado de esta propiedad es 0. La rotación se aplica en relación con los valores de [`AnchorX`](#anchorx) y [`AnchorY`](#anchory) .

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

La propiedad `RotationY` es un valor `double` entre cero y 360 que define el giro sobre el eje Y en grados. El valor predeterminado de esta propiedad es 0. La rotación se aplica en relación con los valores de [`AnchorX`](#anchorx) y [`AnchorY`](#anchory) .

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

La propiedad `Scale` es un valor `double` que define la escala del control. El valor predeterminado de esta propiedad es 1,0. La escala se aplica en relación con los valores de [`AnchorX`](#anchorx) y [`AnchorY`](#anchory) .

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

La propiedad `ScaleX` es un valor `double` que define la escala del control a lo largo del eje X. El valor predeterminado de esta propiedad es 1,0. La propiedad `ScaleX` se aplica en relación con el valor [`AnchorX`](#anchorx) .

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

La propiedad `ScaleY` es un valor `double` que define la escala del control a lo largo del eje Y. El valor predeterminado de esta propiedad es 1,0. La propiedad `ScaleY` se aplica en relación con el valor [`AnchorY`](#anchory) .

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

La propiedad `Style` se hereda de la clase `NavigableElement`. Esta propiedad es una instancia de la clase `Style`. La clase `Style` contiene desencadenadores, establecedores y comportamientos que definen la apariencia y el comportamiento de los elementos visuales. Para obtener más información, vea [estilos XAML de Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/index.md).

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

La propiedad `StyleClass` es una lista de objetos `string` que representan los nombres de las clases `Style`. Esta propiedad se hereda de la clase `NavigableElement`. La propiedad `StyleClass` permite aplicar varios atributos de estilo a una instancia de `VisualElement`. Para obtener más información, vea [clases de estilo de Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

La propiedad `TabIndex` es un valor `int` que define el orden de control al avanzar por los controles con la tecla TAB. La propiedad `TabIndex` es la implementación de la propiedad definida en la interfaz de `ITabStopElement`, que implementa la clase `VisualElement`.

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

La propiedad `TranslationX` es un valor `double` que define la conversión Delta que se va a aplicar en el eje X. La traducción se aplica después del diseño y se utiliza normalmente para aplicar animaciones. La conversión de un elemento fuera de los límites de su contenedor primario My impide que se funcionen las entradas.

Para obtener más información, vea [animación en Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

La propiedad `TranslationY` es un valor `double` que define la conversión Delta que se va a aplicar en el eje Y. La traducción se aplica después del diseño y se utiliza normalmente para aplicar animaciones. La conversión de un elemento fuera de los límites de su contenedor primario My impide que se funcionen las entradas.

Para obtener más información, vea [animación en Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

La propiedad `Triggers` es una `List` de solo lectura de objetos `TriggerBase`. Los desencadenadores permiten a los desarrolladores de aplicaciones expresar acciones en XAML que cambian la apariencia visual de los controles en respuesta a los cambios de eventos o propiedades. Para obtener más información, vea [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

La propiedad `Visual` es una instancia de `IVisual` que permite crear representadores y aplicarlos de forma selectiva a las instancias de `VisualElement`. La propiedad `Visual` se establece para que coincida con su elemento primario, por lo que la definición de un representador en un componente también se aplicará a los elementos secundarios de ese componente. Si no se establece ningún representador personalizado en un control o en sus antecesores, se usará el representador de Xamarin. Forms predeterminado. Para obtener más información, vea [Visual Xamarin. Forms](~/xamarin-forms/user-interface/visual/index.md).

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

La propiedad `Width` es un valor de `double` de solo lectura que describe el ancho representado del control. La propiedad `Width` se calcula durante el ciclo de diseño y no se puede establecer directamente. El ancho de un control se puede solicitar mediante la [propiedad WidthRequest](#widthrequest).

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

La propiedad `WidthRequest` es un valor `double` que determina el ancho deseado del control. El ancho absoluto del control puede no coincidir con el valor solicitado. Para obtener más información, consulte propiedades de la [solicitud](#request-properties).

### [`X`](xref:Xamarin.Forms.VisualElement.X)

La propiedad `X` es un valor de `double` de solo lectura que describe la posición X actual del control.

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

La propiedad `Y` es un valor de `double` de solo lectura que describe la posición Y actual del control.

## <a name="methods"></a>Métodos

Los métodos siguientes están disponibles en la clase `VisualElement`. Para obtener una lista completa, consulte métodos de la [API de VisualElement](xref:Xamarin.Forms.VisualElement#methods).

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

El método `FindByName` se hereda de la clase `Element` y tiene la siguiente firma:

```csharp
public object FindByName (string name)
```

Este método busca en todos los elementos secundarios el argumento `name` proporcionado y devuelve el elemento que tiene el nombre especificado. Si no se encuentra alguna coincidencia, se devuelve `null`.

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

El método `Focus` intenta establecer el foco en el elemento. Este método tiene la siguiente firma:

```csharp
public bool Focus ()
```

El método `Focus` devuelve `true` si se estableció correctamente el foco de teclado y `false` si la llamada al método no produjo un cambio de foco. El elemento debe ser capaz de recibir el foco para que este método funcione. La llamada al método `Focus` en elementos que están fuera de la definición o que no están en funcionamiento tiene un comportamiento indefinido.

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

El método `Unfocus` intenta quitar el foco en el elemento. Este método tiene la siguiente firma:

```csharp
public void Unfocus ()
```

El elemento ya debe tener el foco para que este método funcione.

## <a name="events"></a>Events

Los eventos siguientes están disponibles en la clase `VisualElement`. Para obtener una lista completa, vea [eventos de Xamarin. Forms VisualElement](xref:Xamarin.Forms.VisualElement#events).

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

El evento `Focused` se desencadena cuando la instancia de `VisualElement` recibe el foco. Este evento no se traspasa a través de la pila de Xamarin. Forms, sino que se recibe directamente del control nativo. El establecedor de la propiedad [`IsFocused`](#isfocused) emite este evento.

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

El evento `SizeChanged` se desencadena cuando cambian las propiedades `Height` o `Width` de la instancia de `VisualElement`. Si los desarrolladores desean responder directamente al cambio de tamaño, en lugar de responder al evento posterior al cambio, deben implementar en su lugar el método virtual [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) .

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

El evento `Unfocused` se desencadena cuando la instancia de `VisualElement` pierde el foco. Este evento no se traspasa a través de la pila de Xamarin. Forms, sino que se recibe directamente del control nativo. El establecedor de la propiedad [`IsFocused`](#isfocused) emite este evento.

## <a name="units-of-measurement"></a>Unidades de medida

Todas las plataformas Android, iOS y UWP tienen unidades de medida diferentes que pueden variar en los dispositivos. Xamarin. Forms usa una unidad de medida independiente de la plataforma que normaliza las unidades entre dispositivos y plataformas. Hay 160 unidades por pulgada o 64 unidades por centímetro en Xamarin. Forms.

## <a name="request-properties"></a>Propiedades de solicitud

Las propiedades cuyos nombres contienen "Request" definen un valor deseado, que puede no coincidir con el valor representado real. Por ejemplo, `HeightRequest` puede establecerse en 150 pero si el diseño solo permite espacio para unidades 100, el `Height` representado del control solo será 100. El tamaño representado se ve afectado por el espacio disponible y los componentes contenidos.

## <a name="minimum-request-properties"></a>Propiedades de solicitud mínimas

Las propiedades de solicitud mínima incluyen `MinimumHeightRequest` y `MinimumWidthRequest`, y están diseñadas para permitir un control más preciso sobre cómo los elementos controlan el desbordamiento entre sí. Sin embargo, el comportamiento de diseño relacionado con estas propiedades tiene algunas consideraciones importantes.

### <a name="unspecified-minimum-property-values"></a>Valores de propiedad mínimos no especificados

Si no se establece un valor mínimo, el valor predeterminado de la propiedad minimum es-1. El proceso de diseño omite este valor y considera que el valor absoluto es el mínimo. La consecuencia práctica de este comportamiento es que **no se** reducirá un elemento sin ningún valor mínimo especificado. **Se** reducirá un elemento con un valor mínimo especificado.

En el siguiente código XAML se muestran dos elementos `BoxView` en un `StackLayout`horizontal:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

La primera instancia de `BoxView` solicita un ancho de 500 y no especifica un ancho mínimo. La segunda instancia de `BoxView` solicita un ancho de 500 y un ancho mínimo de 250. Si el elemento `StackLayout` primario no es lo suficientemente ancho como para contener ambos componentes en el ancho solicitado, el proceso de diseño tendrá en cuenta la primera instancia de `BoxView` para tener un ancho mínimo de 500 porque no se especifica ningún otro mínimo válido. La segunda instancia de `BoxView` puede reducir verticalmente a 250 y se reducirá para ajustarse hasta que su ancho alcance 250 unidades.

Si el comportamiento deseado es que la primera instancia de `BoxView` se reduzca verticalmente sin ningún ancho mínimo, el `MinimumWidthRequest` debe establecerse en un valor válido, como 0.

### <a name="minimum-and-absolute-property-values"></a>Valores de las propiedades Minimum y Absolute

El comportamiento es indefinido si el valor mínimo es mayor que el valor absoluto. Por ejemplo, si `MinimumWidthRequest` se establece en 100, la propiedad `WidthRequest` nunca debe superar 100. Al especificar un valor de propiedad mínimo, siempre debe especificar un valor absoluto para asegurarse de que el valor absoluto sea mayor que el valor mínimo.

### <a name="minimum-properties-within-a-grid"></a>Propiedades mínimas dentro de una cuadrícula

`Grid` diseños tienen su propio sistema para ajustar el tamaño relativo de las filas y las columnas. El uso de `MinimumWidthRequest` o `MinimumHeightRequest` dentro de un diseño de `Grid` no tendrá ningún efecto. Para obtener más información, consulte [cuadrícula de Xamarin. Forms](~/xamarin-forms/user-interface/layouts/grid.md).

## <a name="related-links"></a>Vínculos relacionados

* [Documentación de la API de VisualElement](xref:Xamarin.Forms.VisualElement)
