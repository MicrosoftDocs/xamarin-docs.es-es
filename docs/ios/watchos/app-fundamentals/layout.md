---
title: Trabajar con el diseño de watchos en Xamarin
description: En este documento se describe cómo crear un diseño de watchos con Xamarin. Se describen los controladores de interfaz, los grupos, los separadores y los controles de contenido.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1fdb7a10bd767085ba8758fa2e026cc36c93639a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436921"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Trabajar con el diseño de watchos en Xamarin

El diseño de diseños para los [tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md) Apple Watch presenta desafíos únicos.

## <a name="design-tips"></a>Sugerencias de diseño

El punto clave es hacer que la interfaz de usuario sea legible y utilizable en una pequeña pantalla de inspección, con un dedo grande. No se incluya en la captura del diseño del **simulador de iOS** (que aparece muy grande) y un **puntero del mouse** (que funciona con pequeños destinos táctiles).

- Usar un fondo negro: crea la ilusión de una pantalla más grande con el bisel negro del reloj.

- No rellenar el diseño de pantalla: el bisel forma un relleno visual natural.

- Céntrese en la legibilidad. Use tamaños de fuente y colores de forma prudente para asegurarse de que el texto se puede leer. Use los estilos de texto integrados para obtener compatibilidad automática con tipos dinámicos.

![Ejemplo de compatibilidad con tipos dinámicos](layout-images/type.png)

- Céntrese en los tamaños de destino táctil. Los botones o las filas de la tabla tappable con etiquetas de texto deben abarcar toda la pantalla. Apple dice "Nunca colocar más de tres elementos en paralelo", y si usa iconos y no etiquetas de texto.

- Use el [ `Menu` control](~/ios/watchos/user-interface/menu.md) para exponer la funcionalidad usada con menos frecuencia para que el diseño de la aplicación sea claro y conciso.

## <a name="implementation"></a>Implementación

El kit de inspección incluye los siguientes controles para ayudarle a crear diseños de aplicaciones de inspección atractivos:

### <a name="interface-controller"></a>Controlador de interfaz

`WKInterfaceController`Es la clase base de todas las escenas.

La superficie de diseño del controlador de interfaz se comporta como un **Grupo**vertical: puede arrastrar otros controles al controlador de la interfaz y se colocarán automáticamente uno por encima del otro:

![Los controles se colocan automáticamente uno por encima del otro](layout-images/controller-scene.png)

Puede establecer las propiedades **posición** y **tamaño** de cada control para controlar su apariencia:

![Establecer las propiedades de posición y tamaño en cada control](layout-images/positionsize-attributes.png)

Cuando el tamaño se establece en **relativo al contenedor** , puede proporcionar un valor proporcional y un ajuste de desplazamiento. Esta captura de pantalla muestra un botón que se ha configurado para usar el 80% del ancho de la pantalla de inspección (**0,8**):

![Proporcionar un valor proporcional y un ajuste de desplazamiento](layout-images/button-attributes.png)

### <a name="group"></a>Group (Grupo)

`WKInterfaceGroup` es un contenedor de diseño simple que se puede configurar para apilar controles vertical u horizontalmente. Incluye el espaciado entre cada control de forma predeterminada, pero puede modificar el espaciado (e indefinido) en el inspector de **atributos** .

![Modificar el espaciado y los indefinidos en el inspector de atributos](layout-images/group-attributes.png)

Los grupos se pueden cambiar de tamaño y colocar en relación con los controles alrededor de ellos, y los grupos se pueden anidar para crear diseños complejos.

![Los grupos se pueden anidar para crear diseños complejos.](layout-images/group-scene.png)

### <a name="separator"></a>Separador

El control de separador está pensado para ayudar a proporcionar orientación visual en el diseño. Utilice separadores (o colores de fondo o imágenes) para ayudar al usuario a comprender qué contenido está relacionado en la pantalla.

![Ejemplo de uso del separador](layout-images/separator-scene.png)

Tenga en cuenta que los separadores azules y verdes que no usan el ancho completo de la pantalla se han configurado con tamaños de contenedor **fijos** o **relativos** .

### <a name="content-controls"></a>Controles de contenido

Ningún diseño se completará sin los `Label` `Image` controles,,,,, `Button` `Switch` `Slider` `Map` y [other controls](~/ios/watchos/user-interface/index.md).
Se pueden colocar en los diseños mediante **grupos** o la configuración de posición y tamaño de cada control.

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referencia de diseño de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Referencia tipográfica de color & de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)