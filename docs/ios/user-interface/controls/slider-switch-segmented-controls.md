---
title: Controles deslizantes, modificadores y controles segmentados en Xamarin. iOS
description: En este documento se describen las diapositivas, los conmutadores y los controles segmentados en Xamarin. iOS, que describen cómo trabajar con ellos mediante programación y en el diseñador de iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f7b27c9f1010be313810b4b0f289ef792efd47a3
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568651"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Controles deslizantes, modificadores y controles segmentados en Xamarin. iOS

<a name="Sliders"></a>

## <a name="sliders"></a>Controles deslizantes

El control deslizante permite la selección simple de un valor numérico dentro de un intervalo. El valor predeterminado del control es un valor comprendido entre 0 y 1, pero estos límites se pueden personalizar.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

En la captura de pantalla siguiente se muestran las propiedades que se pueden modificar en el diseñador:

 [![](slider-switch-segmented-controls-images/image26a.png "Slider Properties")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Puede establecer estos valores en el código tal como se muestra a continuación, incluyendo la conexión de un controlador para mostrar el valor seleccionado actualmente en un `UILabel` control:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

También puede personalizar la apariencia visual del control deslizante estableciendo

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

El control deslizante personalizado tiene el siguiente aspecto:

 [![](slider-switch-segmented-controls-images/image27a.png "Custom Slider")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Actualmente hay un [error](https://stackoverflow.com/a/19496179) `ThumbTint` que hace que el no se represente en tiempo de ejecución según lo esperado. Puede Agregar la siguiente línea de código **antes** del código anterior como solución alternativa. [[Origen](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Puede usar cualquier imagen, ya que se invalidará, pero asegúrese de que se coloca _en_ el directorio de recursos y se llama en el código.

<a name="Switch"></a>

## <a name="switch"></a>Modificador

iOS usa `UISwitch` como entrada booleana que se puede representar mediante un botón de radio en otras plataformas. El usuario puede *manipular el control* moviendo el control entre las posiciones **ON/OFF** .

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

La apariencia del modificador se puede personalizar en el **Panel de propiedades** del diseñador, lo que le permitirá controlar el estado predeterminado, los colores **de tinta de encendido o apagado** y una **imagen de activado/desactivado**. Esto se muestra en la imagen siguiente:

 [![](slider-switch-segmented-controls-images/image29a.png "Switch Properties")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Las propiedades del modificador también se pueden establecer en el código; por ejemplo, el código siguiente mostrará un modificador con el valor predeterminado `On` :

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls"></a>

## <a name="segmented-controls"></a>Controles segmentados

Un control segmentado es una manera organizada de permitir que los usuarios interactúen con un pequeño número de opciones. Se organiza horizontalmente y cada segmento funciona como un botón independiente. Al usar el diseñador, el control segmentado se puede encontrar en el **cuadro de herramientas > controles**y debe ser similar a la siguiente imagen:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Una característica única del diseñador permite seleccionar cada segmento individualmente en la superficie de diseño, como se muestra a continuación:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Esto permite que el Panel de propiedades se use para controlar de forma más precisa las propiedades de cada segmento. Puede ver las propiedades editables en la siguiente captura de pantalla:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Se debe tener en cuenta que el estilo de control segmentado ha quedado en desuso en iOS7 y, por tanto, el ajuste de las opciones de este en una aplicación iOS7 no tendrá ningún efecto.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controlador de alertas](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
