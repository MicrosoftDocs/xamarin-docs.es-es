---
title: Paso 1. Registro de una aplicación para usar Azure Active Directory
description: En este documento se describe cómo registrar una aplicación de Azure con Azure Active Directory para que los clientes móviles puedan acceder de forma segura a ella.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 732c1ee241b39a4bb1422b8c27820631bf8c6b0c
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199051"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Paso 1. Registro de una aplicación para usar Azure Active Directory

1. Vaya a [windowsazure.com](https://manage.windowsazure.com) e inicie sesión con su cuenta de Microsoft o de la organización en Azure portal. Si no tiene una suscripción a Azure, puede obtener una versión de prueba de [Azure.com](https://www.azure.com)

2. Después de iniciar sesión, vaya a la sección **Active Directory** (1) y elija el directorio en el que desea registrar la aplicación (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "y elija el directorio en el que desea registrar la aplicación")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Haga clic en **Agregar** para crear una nueva aplicación y, después, seleccione **Agregar una aplicación que mi organización está desarrollando** .

   [![](register-images/02.-add-new-application-sml.jpg "Agregar una aplicación que mi organización está desarrollando")](register-images/02.-add-new-application.jpg#lightbox)

4. En la siguiente pantalla, asigne un nombre a la aplicación (por ejemplo, XAM: DEMO).
   Asegúrese de seleccionar **aplicación de cliente nativo** como tipo de aplicación.

   ![](register-images/03.-app-name.jpg "Asegúrese de seleccionar aplicación de cliente nativo como tipo de aplicación.")

5. En la última pantalla, proporcione un*URI* de redireccionamiento que sea único para la aplicación, ya que volverá a este URI cuando se complete la autenticación.

   ![](register-images/04.-app-redirect.jpg "En la última pantalla, proporcione un URI de redirección que sea único para la aplicación, ya que volverá a este URI cuando se complete la autenticación.")

6. Una vez creada la aplicación, vaya a la pestaña **configurar** . Anote el **identificador de cliente** que usaremos en nuestra aplicación más adelante. Además, en esta pantalla puede dar acceso a la aplicación móvil a Active Directory o agregar otra aplicación como Web API o Office365, que puede usar la aplicación móvil una vez que se complete la autenticación.

   ![](register-images/05.-configure.jpg "Además, en esta pantalla puede dar acceso a la aplicación móvil a Active Directory o agregar otra aplicación como Web API o Office365.")



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
