---
title: Trabajar con vistas apiladas tvOS en Xamarin
description: En este documento se describe cómo trabajar con vistas apiladas de tvOS en una aplicación compilada con Xamarin. Proporciona información general de alto nivel sobre las vistas apiladas y describe el diseño automático, el posicionamiento y el ajuste de tamaño de una vista apilada, los usos comunes, la integración con guiones gráficos, etc.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b318f11e866c94ab1f5aa8f6b4f351ac88475ac3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437045"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Trabajar con vistas apiladas tvOS en Xamarin

El control de vista de pila ( `UIStackView` ) aprovecha la eficacia de las clases de diseño y tamaño automáticos para administrar una pila de subvistas, ya sea horizontal o verticalmente, que responde dinámicamente a los cambios de contenido y al tamaño de pantalla del dispositivo Apple TV.

El diseño de todas las subvistas adjuntas a una vista de pila se administra en función de las propiedades definidas por el desarrollador como el eje, la distribución, la alineación y el espaciado:

[![Diagrama de diseño de subvista](stacked-views-images/stacked01.png)](stacked-views-images/stacked01.png#lightbox)

Cuando se usa un `UIStackView` en una aplicación Xamarin. tvOS, el desarrollador puede definir las subvistas dentro de un guion gráfico en el diseñador de iOS o agregando y quitando las subvistas en el código de C#.

## <a name="about-stacked-view-controls"></a>Acerca de los controles de vista apilada

El `UIStackView` está diseñado como una vista de contenedor sin representación y, como tal, no se dibuja en el lienzo como otras subclases de `UIView` . Establecer propiedades como `BackgroundColor` o reemplazar no tendrá `DrawRect` ningún efecto visual.

Hay varias propiedades que controlan el modo en que una vista de pila organizará su colección de subvistas:

- **Eje** : determina si la vista de pila organiza las subvistas **horizontal** o **verticalmente**.
- **Alineación** : controla cómo se alinean las subvistas dentro de la vista de pila.
- **Distribución** : controla cómo se ajusta el tamaño de las subvistas en la vista de pila.
- **Espaciado** : controla el espacio mínimo entre cada subvista de la vista de pila.
- **Referencia relativa** : si es `true` , el espaciado vertical de cada subvista se derivará de su línea base.
- **Márgenes de diseño relativos** : coloca las subvistas en relación con los márgenes de diseño estándar.

Normalmente, usará una vista de pila para organizar un número pequeño de subvistas. Se pueden crear interfaces de usuario más complejas mediante el anidamiento de una o varias vistas de pila dentro de otras.

Puede ajustar aún más la apariencia de la IU agregando restricciones adicionales a las subvistas (por ejemplo, para controlar el alto o el ancho). Sin embargo, se debe tener cuidado de no incluir restricciones conflictivas a las introducidas por la propia vista de pila.

<a name="Auto-Layout-and-Size-Classes"></a>

## <a name="auto-layout-and-size-classes"></a>Clases de diseño y tamaño automáticos

Cuando se agrega una subvista a una vista de pila, su diseño se controla por completo en esa vista de pila mediante las clases de diseño y tamaño automáticas para colocar y ajustar el tamaño de las vistas organizadas.

La vista de pila _anclará_ la primera y la última subvista de su colección a los bordes **superior** e **inferior** de las vistas de pila verticales o los bordes **izquierdo** y **derecho** de las vistas de pila horizontal. Si establece la `LayoutMarginsRelativeArrangement` propiedad en `true` , la vista ancla las subvistas a los márgenes relevantes en lugar del borde.

La vista de pila usa la propiedad de la subvista `IntrinsicContentSize` al calcular el tamaño de las subvistas a lo largo del definido `Axis` (excepto para `FillEqually Distribution` ). `FillEqually Distribution`Cambia el tamaño de todas las subvistas para que tengan el mismo tamaño, rellenando así la vista de pila a lo largo del `Axis` .

A excepción de `Fill Alignment` , la vista de pila usa la propiedad de la subvista `IntrinsicContentSize` para calcular el tamaño de la vista perpendicular a la determinada `Axis` . En el caso de `Fill Alignment` , se ajusta el tamaño de todas las subvistas para que rellenen la vista de pila perpendicular a la determinada `Axis` .

<a name="Positioning-and-Sizing-the-Stack-View"></a>

## <a name="positioning-and-sizing-the-stack-view"></a>Colocar y ajustar el tamaño de la vista de pila

Mientras que la vista de pila tiene control total sobre el diseño de cualquier subvista (basándose en propiedades como `Axis` y `Distribution` ), es necesario colocar la vista de pila ( `UIStackView` ) dentro de su vista primaria mediante las clases de diseño y tamaño automáticas.

Por lo general, esto significa anclar al menos dos bordes de la vista de pila para expandir y contraer, lo que define su posición. Sin restricciones adicionales, se cambiará automáticamente el tamaño de la vista de pila para ajustarse a todas sus subvistas de la manera siguiente:

- El tamaño a lo largo `Axis` de su será la suma de todos los tamaños de las subvistas más el espacio definido entre cada subvista.
- Si la `LayoutMarginsRelativeArrangement` propiedad es `true` , el tamaño de las vistas de pila también incluirá espacio para los márgenes.
- El tamaño perpendicular al `Axis` se establecerá en la subvista más grande de la colección.

Además, puede especificar restricciones para el **alto** y el **ancho**de la vista de pila. En este caso, las subvistas se colocarán (con el tamaño) para rellenar el espacio especificado por la vista de pila, según lo determinado por las `Distribution` `Alignment` propiedades y.

Si la `BaselineRelativeArrangement` propiedad es `true` , las subvistas se organizarán en función de la primera o la última línea de base de la subvista, en lugar de usar la posición **superior**, **inferior** o **Centro* -  **Y** . Se calculan en el contenido de la vista de pila como se indica a continuación:

- Una vista de pila vertical devolverá la primera subvista de la primera línea de base y la última para la última. Si alguna de estas subvistas son vistas de pila, se usará su primera o última línea base.
- Una vista de pila horizontal usará su subvista más alta para la primera y la última línea de base. Si la vista más alta también es una vista de pila, usará la subvista más alta como línea de base.

> [!IMPORTANT]
> La alineación de línea base no funciona en tamaños de subvista expandidos o comprimidos, ya que la línea base se calculará en la posición equivocada. Para la alineación de línea base, asegúrese de que el **alto** de la subvista coincide con el **alto**de la vista de contenido intrínseca.

<a name="Common-Stack-View-Uses"></a>

## <a name="common-stack-view-uses"></a>Usos comunes de la vista de pila

Hay varios tipos de diseño que funcionan bien con los controles de vista de pila. Según Apple, estos son algunos de los usos más comunes:

- **Defina el tamaño a lo largo del eje** : al anclar ambos bordes a lo largo de la vista de pila `Axis` y uno de los bordes adyacentes para establecer la posición, la vista de pila crecerá a lo largo del eje para ajustarse al espacio definido por sus subvistas.
- **Definir la posición de la subvista** : al anclar los bordes adyacentes de la vista de pila a su vista primaria, la vista de pila aumentará en ambas dimensiones para que quepan en las subvistas.
- **Definir el tamaño y la posición de la pila** : al anclar los cuatro bordes de la vista de pila a la vista primaria, la vista de pila organiza las subvistas en función del espacio definido en la vista de pila.
- **Defina el tamaño perpendicular al eje** : al anclar ambos bordes perpendiculares a la vista de pila `Axis` y uno de los bordes a lo largo del eje para establecer la posición, la vista de pila aumentará de forma perpendicular al eje para ajustarse al espacio definido por sus subvistas.

<a name="Stack-Views-and-Storyboards"></a>

## <a name="stack-views-and-storyboards"></a>Vistas de pila y guiones gráficos

La manera más sencilla de trabajar con vistas de pila en una aplicación Xamarin. tvOS es agregarlos a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en el `Main.storyboard` archivo y ábralo para su edición.
1. Diseñe el diseño de los elementos individuales que va a agregar a la vista de pila:

    [![Ejemplo de diseño de elemento](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. Agregue las restricciones necesarias a los elementos para asegurarse de que se escalan correctamente. Este paso es importante cuando el elemento se agrega a la vista de pila.
1. Haga el número necesario de copias (cuatro en este caso):

    [![El número necesario de copias](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. Arrastre una **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista:

    [![Una vista de pila](stacked-views-images/layout03.png)](stacked-views-images/layout03.png#lightbox)
1. Seleccione la vista de la pila, en la **pestaña widget** del **Panel de propiedades** seleccione **relleno** para la **alineación**, **rellene igualmente** para la **distribución** y escriba `25` para el **espaciado**:

    [![La pestaña widget](stacked-views-images/layout04.png)](stacked-views-images/layout04.png#lightbox)
1. Coloque la vista de pila en la pantalla donde quiera y agregue restricciones para mantenerla en la ubicación requerida.
1. Seleccione los elementos individuales y arrástrelos a la vista de pila:

    [![Elementos individuales de la vista de pila](stacked-views-images/layout05.png)](stacked-views-images/layout05.png#lightbox)
1. Se ajustará el diseño y los elementos se organizarán en la vista de pila en función de los atributos establecidos anteriormente.
1. Asigne **nombres** en la **pestaña widget** del **Explorador de propiedades** para trabajar con los controles de interfaz de usuario en el código de C#.
1. Guarde los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y ábralo para su edición.
1. Diseñe el diseño de los elementos individuales que va a agregar a la vista de pila:

    [![Ejemplo de diseño de elemento](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. Agregue las restricciones necesarias a los elementos para asegurarse de que se escalan correctamente. Este paso es importante cuando el elemento se agrega a la vista de pila.
1. Haga el número necesario de copias (cuatro en este caso):

    [![El número necesario de copias](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. Arrastre una **vista de pila** desde el **cuadro de herramientas** y colóquela en la vista:

    [![Una vista de pila](stacked-views-images/layout03-vs.png)](stacked-views-images/layout03-vs.png#lightbox)
1. Seleccione la vista de la pila, en la **pestaña widget** del **Explorador de propiedades** , seleccione **Fill** para la **alineación**, **rellene igualmente** para la **distribución** y escriba `25` para el **espaciado**:

    [![La pestaña widget](stacked-views-images/layout04-vs.png)](stacked-views-images/layout04-vs.png#lightbox)
1. Coloque la vista de pila en la pantalla donde quiera y agregue restricciones para mantenerla en la ubicación requerida.
1. Seleccione los elementos individuales y arrástrelos a la vista de pila:

    [![Elementos individuales de la vista de pila](stacked-views-images/layout05-vs.png)](stacked-views-images/layout05-vs.png#lightbox)
1. Se ajustará el diseño y los elementos se organizarán en la vista de pila en función de los atributos establecidos anteriormente.
1. Asigne **nombres** en la **pestaña widget** del **Explorador de propiedades** para trabajar con los controles de interfaz de usuario en el código de C#.
1. Guarde los cambios.

-----

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a un elemento de la interfaz de usuario (como un `UIButton` ) en el diseñador de iOS al crear un controlador de eventos, nunca se llamará porque Apple TV no tiene una pantalla táctil ni eventos táctiles de soporte técnico. Siempre debe usar el valor predeterminado `Action Type` al crear acciones para los elementos de la interfaz de usuario de tvOS.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md).

En el caso de nuestro ejemplo, hemos expuesto una salida y una acción para el control de segmento y una salida para cada "tarjeta de reproducción". En el código, se oculta y se muestra el reproductor basándose en el segmento actual. Por ejemplo:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

Cuando se ejecuta la aplicación, los cuatro elementos se distribuirán igualmente en nuestra vista de pila:

[![Cuando se ejecuta la aplicación, los cuatro elementos se distribuirán igualmente en nuestra vista de pila.](stacked-views-images/layout06.png)](stacked-views-images/layout06.png#lightbox)

Si se reduce el número de reproductores, las vistas no utilizadas se ocultan y la vista de pila ajusta el diseño para ajustarse:

[![Si se reduce el número de jugadores, las vistas no utilizadas se ocultan y la vista de pila ajusta el diseño para ajustarse](stacked-views-images/layout07.png)](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code"></a>

### <a name="populate-a-stack-view-from-code"></a>Rellenar una vista de pila desde el código

Además de definir completamente el contenido y el diseño de una vista de pila en el diseñador de iOS, puede crearla y quitarla dinámicamente desde el código de C#.

En el ejemplo siguiente se usa una vista de pila para controlar "estrellas" en una revisión (1 a 5):

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

Echemos un vistazo a algunas partes de este código en detalle. En primer lugar, usamos una `if` instrucción para comprobar que no hay más de cinco "estrellas" o menor que cero.

Para agregar una nueva "estrella", se carga su imagen y se establece su **modo de contenido** en **ajuste de escala**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Esto evita que el icono de "estrella" se distorsione cuando se agrega a la vista de pila.

A continuación, se agrega el nuevo icono de "estrella" a la colección de subvistas de la vista de pila:

```csharp
RatingView.AddArrangedSubview(icon);
```

Observará que hemos agregado `UIImageView` a la `UIStackView` `ArrangedSubviews` propiedad del y no a `SubView` . Cualquier vista que desee que la vista de pila controle su diseño debe agregarse a la `ArrangedSubviews` propiedad.

Para quitar una vista previa de una vista de pila, primero se obtiene la subvista que se va a quitar:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

A continuación, es necesario quitarlo de la `ArrangedSubviews` colección y de la vista superior:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Quitar una vista previa de la `ArrangedSubviews` colección lo saca del control de la vista de pila, pero no la quita de la pantalla.

<a name="Dynamically-Changing-Content"></a>

## <a name="dynamically-changing-content"></a>Cambiar el contenido dinámicamente

Una vista de pila ajustará automáticamente el diseño de las subvistas cada vez que se agregue, quite u oculte una vista previa. El diseño también se ajustará si se ajusta alguna propiedad de la vista de pila (por ejemplo, su `Axis` ).

Los cambios de diseño se pueden animar colocándolos en un bloque de animación, por ejemplo:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muchas de las propiedades de la vista de pila se pueden especificar utilizando clases de tamaño dentro de un guion gráfico. Estas propiedades se animarán automáticamente como respuesta a los cambios de tamaño o orientación.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con la vista apilada dentro de una aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)