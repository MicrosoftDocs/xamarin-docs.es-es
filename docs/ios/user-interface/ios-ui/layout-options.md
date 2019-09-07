---
title: Opciones de diseño en Xamarin. iOS
description: En este documento se describen las distintas formas de diseñar interfaces de usuario en Xamarin. iOS. Describe el ajuste automático de tamaño y el diseño automático.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 335c385b5be77736f0e19852eb71f2a2329eb9a2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768255"
---
# <a name="layout-options-in-xamarinios"></a>Opciones de diseño en Xamarin. iOS

Hay dos mecanismos diferentes para controlar el diseño cuando se cambia el tamaño o se gira una vista:

- **Ajuste** automático de tamaño: el inspector de ajuste automático de tamaño del diseñador proporciona una `AutoresizingMask` manera de establecer las propiedades. Esto permitirá que un control se delimite a los bordes de su contenedor o corrija su tamaño. El ajuste automático de tamaño funciona en todas las versiones de iOS. Esto se describe con más detalle a continuación.
- **Diseño automático** : característica, introducida en iOS 6 que permite un control exhaustivo sobre las relaciones de los controles de interfaz de usuario. Permitirá el control de las posiciones de los elementos en relación con otros elementos en la superficie de diseño. Este tema se trata con más detalle en la guía [diseño automático con Xamarin iOS Designer](~/ios/user-interface/designer/designer-auto-layout.md) .

## <a name="autosizing"></a>Cambiar automáticamente

Cuando un usuario cambia el tamaño de una ventana, por ejemplo, cuando se gira el dispositivo y la orientación cambia, el sistema cambiará automáticamente el tamaño de las vistas dentro de esa ventana según sus reglas de ajuste automático de tamaño. Estas reglas se pueden establecer en C# mediante la `AutoresizingMask` propiedad de `UIView` o en el **Panel de propiedades** del diseñador de iOS, como se muestra a continuación:

 [![](layout-options-images/image41.png "Diseñador de Visual Studio para Mac")](layout-options-images/image41.png#lightbox)

Cuando se selecciona un control, esto le permite especificar manualmente la ubicación y las dimensiones del control, así como elegir el comportamiento de ajuste automático de **tamaño** . Como se muestra en la captura de pantalla siguiente, se pueden usar los muelles y los montantes del control de ajuste automático de tamaño para definir la relación de la vista seleccionada con su elemento primario:

 [![](layout-options-images/image42.png "Diseñador de Visual Studio para Mac")](layout-options-images/image42.png#lightbox)

Al ajustar un *muelle* , se cambiará el tamaño de la vista basándose en el ancho o el alto de la vista primaria. Al ajustar *una forma, la* vista mantendrá una distancia constante entre sí misma y su vista primaria, en ese borde concreto.

Esta configuración también se puede establecer en el código:

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

Para probar la configuración de ajuste automático de tamaño, habilite distintas **orientaciones de dispositivo compatibles** en las opciones del proyecto:

 [![](layout-options-images/image43a.png "Configuración de ajuste automático")](layout-options-images/image43a.png#lightbox)

En el código subyacente, podemos usar el código siguiente, que hace que los dos controles de texto cambien de tamaño horizontalmente:

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

También podemos ajustar los controles mediante el diseñador. Si selecciona las transformaciones que se muestran a continuación, la imagen permanecerá alineada a la derecha sin ser cortada en la parte inferior de la vista:

 [![](layout-options-images/autoresize.png "Autorotación")](layout-options-images/autoresize.png#lightbox)

Estas capturas de pantalla muestran cómo cambian el tamaño o la posición de los controles cuando se gira la pantalla:

 [![](layout-options-images/image44a.png "Autorotación")](layout-options-images/image44a.png#lightbox)

Tenga en cuenta que la vista de texto y el campo de texto se ajustan para mantener los mismos márgenes izquierdo `FlexibleWidth` y derecho, debido a la configuración. La imagen tiene el margen superior e izquierdo flexible, lo que significa que conserva los márgenes inferior y derecho, manteniendo la imagen en la vista cuando se gira la pantalla. Normalmente, los diseños complejos requieren una combinación de estos valores en todos los controles visibles para mantener la coherencia de la interfaz de usuario y evitar que los controles se superpongan cuando cambien los límites de la vista (debido a la rotación u otro evento de cambio de tamaño).

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
