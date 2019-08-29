---
title: Modo oscuro en Xamarin. iOS
description: El modo oscuro es una nueva opción para todo el sistema para los temas claros y oscuros. el usuario de iOS ahora puede elegir un tema o permitir que iOS cambie la apariencia dinámicamente.
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: d21afcc7d7b130528e9cceac47840acd49b91f59
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129977"
---
# <a name="dark-mode-in-xamarinios"></a>Modo oscuro en Xamarin. iOS

![Esta API está actualmente en versión preliminar](~/media/shared/preview.png)

El modo oscuro es una opción para todo el sistema para los temas claros y oscuros. los usuarios de iOS ahora pueden elegir el tema o permitir que iOS Cambie dinámicamente la apariencia según el entorno y la hora del día.

Este documento presenta el modo oscuro y admite el modo oscuro en aplicaciones iOS 13.

## <a name="requirements"></a>Requisitos

El modo oscuro requiere iOS 13 y Xcode 11, Xamarin. iOS 12,99 y Visual Studio 2019 o Visual Studio 2019 para Mac con compatibilidad con Xcode 11.

## <a name="turning-on-dark-mode"></a>Activar el modo oscuro

Apple proporciona un menú para desarrolladores en iOS 13 para alternar entre los modos oscuro y claro. En el simulador de iOS 13, Abra **configuración** y elija la sección **desarrollador** y desplácese hasta el conmutador **apariencia oscura** . El cambio se reflejará en todo el entorno del simulador:

![Activar el modo oscuro](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>Activos para los modos claro y oscuro

Ahora, el catálogo de recursos de Visual Studio admite imágenes y colores opcionales para cada modo de apariencia: Universal, oscuro y claro. Al definir las imágenes y los colores de esta manera, iOS elegirá automáticamente la imagen y el color apropiados.

Abra el archivo **assets. xcassets** en el proyecto de iOS y agregue un nuevo conjunto de imágenes. Tenga en cuenta que puede especificar imágenes universales, oscuras y claras en cualquiera de las resoluciones de destino. En la captura de pantalla siguiente, hay una imagen para Dark y Light con el nombre "MicrosoftLogo":

![Activos para los modos claro y oscuro](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets. xcassets** también contiene entradas para **BackgroundColor** y **TitleColor**, que son definiciones de color. Estos colores ahora están disponibles por nombre para su uso en toda la aplicación. Se ha asignado el **BackgroundColor** al fondo de la vista y el **TitleColor** a la etiqueta, tal como se muestra en esta captura de pantalla:

![Activos para los modos claro y oscuro](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>Colores del sistema dinámicos

Apple ha introducido nuevos colores semánticos que ajustan su apariencia de forma dinámica en función de la nueva configuración de modo oscuro.

## <a name="summary"></a>Resumen

En este artículo se ha introducido el modo oscuro para iOS y la especificación de imágenes y colores para cada modo mediante el catálogo de recursos.

## <a name="related-links"></a>Vínculos relacionados

- [Instrucciones de diseño del modo oscuro](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [Colores semánticos](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
