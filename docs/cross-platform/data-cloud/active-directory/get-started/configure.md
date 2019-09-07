---
title: Paso 2. Configurar el acceso de servicio para la aplicación móvil
description: En este documento se describe cómo proporcionar una aplicación Xamarin con acceso a una aplicación de Azure protegida por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: ec5dd15ffb838d7062c8c769375289e7b07b24d2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766368"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Paso 2. Configurar el acceso de servicio para la aplicación móvil

Cada vez que un recurso, por ejemplo, aplicación Web, servicio Web, etc., debe protegerse mediante Azure Active Directory, debe registrarse. Todas las aplicaciones o servicios seguros pueden verse en la pestaña **aplicaciones** . Aquí puede seleccionar la aplicación a la que es necesario tener acceso desde la aplicación móvil y proporcionar acceso a ella.

1. En la pestaña **configurar** , busque la sección **permisos para otras aplicaciones** :

   ![](configure-images/2.1-configure.png "En la pestaña configurar, busque la sección permisos para otras aplicaciones")

2. Haga clic en el botón **Agregar aplicación** . En la ventana emergente siguiente, debería ver una lista de todas las aplicaciones protegidas por Azure Active Directory. Seleccione las aplicaciones a las que se debe tener acceso desde la aplicación móvil.

   ![](configure-images/2.2-add-application.png "Seleccione las aplicaciones a las que se debe tener acceso desde la aplicación móvil.")

3. Después de seleccionar la aplicación, vuelva a seleccionar la aplicación recién agregada en **permisos para otras aplicaciones** y proporcione los derechos adecuados.

   ![](configure-images/2.3-permissions.png "Después de seleccionar la aplicación, vuelva a seleccionar la aplicación recién agregada en permisos para otras aplicaciones y proporcione los derechos adecuados.")

4. Por último, **guarde** la configuración. Estos servicios ahora deben estar disponibles en las aplicaciones móviles.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
