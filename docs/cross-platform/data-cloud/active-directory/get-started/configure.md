---
title: Paso 2. Configurar el acceso de servicio para la aplicación móvil
description: En este documento se describe cómo proporcionar una aplicación Xamarin con acceso a una aplicación de Azure protegida por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eeac7b0b70b2f11304a374de7522f28d4bcad6c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016671"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Paso 2. Configurar el acceso de servicio para la aplicación móvil

Cada vez que un recurso, por ejemplo, aplicación Web, servicio Web, etc., debe protegerse mediante Azure Active Directory, debe registrarse. Todas las aplicaciones o servicios seguros pueden verse en la pestaña **aplicaciones** . Aquí puede seleccionar la aplicación a la que es necesario tener acceso desde la aplicación móvil y proporcionar acceso a ella.

1. En la pestaña **configurar** , busque la sección **permisos para otras aplicaciones** :

   ![](configure-images/2.1-configure.png "On the Configure tab, locate permissions to other applications section")

2. Haga clic en el botón **Agregar aplicación** . En la ventana emergente siguiente, debería ver una lista de todas las aplicaciones protegidas por Azure Active Directory. Seleccione las aplicaciones a las que se debe tener acceso desde la aplicación móvil.

   ![](configure-images/2.2-add-application.png "Select the applications that needs to be accessed from the mobile application")

3. Después de seleccionar la aplicación, vuelva a seleccionar la aplicación recién agregada en **permisos para otras aplicaciones** y proporcione los derechos adecuados.

   ![](configure-images/2.3-permissions.png "After selecting the application, once again select the newly-added application in permissions to other   applications section and give appropriate rights")

4. Por último, **guarde** la configuración. Estos servicios ahora deben estar disponibles en las aplicaciones móviles.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
