---
title: Paso 2. Configurar el acceso de servicio para la aplicación móvil
description: En este documento se describe cómo proporcionar una aplicación Xamarin con acceso a una aplicación de Azure protegida por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 2e1a96b96ea8738e162a9c49a5f7c6927bd2b0d3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931760"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Paso 2. Configurar el acceso de servicio para la aplicación móvil

Cada vez que un recurso, por ejemplo, aplicación Web, servicio Web, etc., debe protegerse mediante Azure Active Directory, debe registrarse. Todas las aplicaciones o servicios seguros pueden verse en la pestaña **aplicaciones** . Aquí puede seleccionar la aplicación a la que es necesario tener acceso desde la aplicación móvil y proporcionar acceso a ella.

1. En la pestaña **configurar** , busque la sección **permisos para otras aplicaciones** :

   ![En la pestaña configurar, busque la sección permisos para otras aplicaciones](configure-images/2.1-configure.png)

2. Haga clic en el botón **Agregar aplicación** . En la ventana emergente siguiente, debería ver una lista de todas las aplicaciones protegidas por Azure Active Directory. Seleccione las aplicaciones a las que se debe tener acceso desde la aplicación móvil.

   ![Seleccione las aplicaciones a las que se debe tener acceso desde la aplicación móvil.](configure-images/2.2-add-application.png)

3. Después de seleccionar la aplicación, vuelva a seleccionar la aplicación recién agregada en **permisos para otras aplicaciones** y proporcione los derechos adecuados.

   ![Después de seleccionar la aplicación, vuelva a seleccionar la aplicación recién agregada en permisos para otras aplicaciones y proporcione los derechos adecuados.](configure-images/2.3-permissions.png)

4. Por último, **guarde** la configuración. Estos servicios ahora deben estar disponibles en las aplicaciones móviles.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
