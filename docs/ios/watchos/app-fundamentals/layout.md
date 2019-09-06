---
title: Trabajar con watchOS diseño en Xamarin
description: Este documento describe cómo crear un diseño de watchOS con Xamarin. Describe los controladores de interfaz, grupos, separadores y controles de contenido.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 5272921cade2b9dadd24a4c917e6251ac591e178
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292322"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Trabajar con watchOS diseño en Xamarin

Crear diseños para el Apple Watch [tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md) presenta desafíos únicos.

## <a name="design-tips"></a>Sugerencias de diseño

El punto clave es: asegúrese de la interfaz de usuario legible y utilizable en una pantalla pequeña inspección, con un dedo grande. No caiga en la trampa del diseño para la **simulador de iOS** (que aparece muy grande) y un **puntero del mouse** (que funciona con destinos diminutos táctil)!

- Utilice un fondo negro: crea la ilusión de una pantalla más grande con bisel negro de inspección.

- No se rellena en torno a su diseño de pantalla: el bisel forms un relleno visual natural.

- Se centran en mejorar la legibilidad. Usar tamaños de fuentes y colores con prudencia para asegurarse de que el texto es legible. Use los estilos de texto integrado para obtener compatibilidad automática con el tipo dinámico.

![](layout-images/type.png "Ejemplo de compatibilidad de tipo dinámico")

- Se centran en los tamaños de destino de toque. Botones/tappable filas de tabla con etiquetas de texto deben abarcar toda la pantalla. Apple dice "no coloque nunca más de tres elementos side-by-side", y si se usa iconos y etiquetas de texto no.

- Use la [ `Menu` control](~/ios/watchos/user-interface/menu.md) a la funcionalidad de exponer con menor frecuencia se utiliza para mantener el diseño de su aplicación claro y conciso.


## <a name="implementation"></a>Implementación

Vea el que Kit incluye los siguientes controles para ayudarle a crear diseños de aplicaciones de watch atractiva:

### <a name="interface-controller"></a>Controlador de interfaz

El `WKInterfaceController` es la clase base todas sus escenas.

La superficie de diseño para el controlador de interfaz se comporta como una vertical **grupo**: puede arrastrar otros controles en el controlador de interfaz y estarán automáticamente dispuesto uno encima del otro:

![](layout-images/controller-scene.png "Los controles son automáticamente dispuesto uno encima del otro")

Puede establecer el **posición** y **tamaño** propiedades en cada control para controlar su aspecto:

![](layout-images/positionsize-attributes.png "Establecer las propiedades de posición y el tamaño de cada control")

Cuando se establece el tamaño en **relativa al contenedor** puede proporcionar un valor proporcional y un ajuste de desplazamiento. Esta captura de pantalla muestra un botón que se ha establecido para usar el 80% del ancho de la pantalla de inspección (**0.8**):

![](layout-images/button-attributes.png "Proporcione un valor proporcional y un ajuste de desplazamiento")


### <a name="group"></a>Agrupar

`WKInterfaceGroup` es un contenedor de diseño simple que se puede configurar para apilar controla vertical u horizontalmente. Incluye el espaciado entre cada control de forma predeterminada, pero puede modificar el espaciado (y los márgenes) en el **atributos** inspector.

![](layout-images/group-attributes.png "Modificar el espaciado y los márgenes del inspector de atributos")

Grupos pueden propios tendrán el tamaño y colocados en relación con los controles en torno a ellas, y se pueden anidar grupos para crear diseños complejos.

![](layout-images/group-scene.png "Los grupos se pueden anidar para crear diseños complejos")


### <a name="separator"></a>Separador

El control de separador está pensado para proporcionar orientación visual en su diseño. Utilice separadores (o los colores de fondo o imágenes) para ayudar al usuario a entender el contenido que está relacionado con en la pantalla.

![](layout-images/separator-scene.png "Ejemplo de uso de separadores")

Tenga en cuenta los separadores azules y verdes que no use todo el ancho de la pantalla se han configurado con cualquiera **Fixed** o **relativa al contenedor** tamaños.

### <a name="content-controls"></a>Controles de contenido

Ningún diseño estaría completa sin la `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, y [otros controles](~/ios/watchos/user-interface/index.md).
Estos se pueden colocar en los diseños con **grupos** o la configuración de la posición y el tamaño de cada control.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referencia de diseño de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Hacer referencia a Color & Tipografía de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
