---
title: Diseño de doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo usar TwoPaneView de Xamarin.Forms para optimizar la experiencia de la aplicación para dispositivos de doble pantalla como Surface Duo y Surface Neo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28d4b3da44cc1a022b70c0de0720be747e047f9f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138897"
---
# <a name="xamarinforms-dual-screen-layout"></a>Diseño de doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La clase `TwoPaneView` representa un contenedor con dos vistas que ajustan el tamaño y la posición del contenido en el espacio disponible, ya sea en paralelo o de arriba a abajo. `TwoPaneView` hereda de `Grid`, por lo que la manera más sencilla de pensar en estas propiedades es como si se aplicaran a una cuadrícula.

## <a name="set-up-twopaneview"></a>Configuración de TwoPaneView

La propiedad `TwoPaneView.Source` puede tomar un URI o una ruta de acceso de archivo local. La reproducción comenzará inmediatamente después de abrir los elementos multimedia:

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Descripción de los modos de TwoPaneView

Solo puede estar activo uno de estos modos:

- `SinglePane` actualmente solo hay un panel visible.
- `Wide` los dos paneles se disponen en horizontal. Un panel se sitúa a la izquierda y el otro a la derecha. Cuando se muestra en dos pantallas, en este modo el dispositivo está en vertical.
- `Tall` los dos paneles se disponen en vertical. Un panel está en la parte superior y el otro en la parte inferior. Cuando se muestra en dos pantallas, en este modo el dispositivo está en horizontal.

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Control de TwoPaneView cuando solo está en una pantalla

Las siguientes propiedades se aplican cuando `TwoPaneView` ocupa una sola pantalla:

- `MinTallModeHeight` indica el alto mínimo que debe tener el control para entrar en modo Tall.
- `MinWideModeWidth` indica el ancho mínimo que debe tener el control para entrar en modo Wide.
- `Pane1Length` establece el ancho de Panel1 en el modo Wide, el alto de Panel1 en modo Tall y no tiene ningún efecto en el modo SinglePane.
- `Pane2Length` establece el ancho de Panel2 en el modo Wide, el alto de Panel2 en modo Tall y no tiene ningún efecto en el modo SinglePane.

> [!IMPORTANT]
> Si `TwoPaneView` se distribuye en dos pantallas, estas propiedades no tienen ningún efecto.

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Propiedades que se aplican cuando se está en una pantalla o en dos

Las siguientes propiedades se aplican cuando `TwoPaneView` ocupa una sola pantalla o dos pantallas:

- `TallModeConfiguration` indica, cuando está en modo Tall, la disposición izquierda/derecha, o bien si solo quiere un panel visible según lo que se haya definido mediante TwoPaneViewPriority.
- `WideModeConfiguration` cuando está en modo Wide, indica la disposición arriba/abajo, o bien si solo quiere un panel visible según lo que se haya definido mediante TwoPaneViewPriority.
- `PanePriority` determina si se muestra Panel1 o Panel2 en el modo SinglePane.

## <a name="related-links"></a>Vínculos relacionados

- [DualScreen (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
