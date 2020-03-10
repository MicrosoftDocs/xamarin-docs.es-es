---
title: Administración de cuentas Apple
description: En este documento se describe cómo usar las características de administración de cuentas de Apple en Visual Studio para Mac y Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: 17607e09a141fd29cd81cde93d812b20e62a9af8
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946246"
---
# <a name="apple-account-management"></a>Administración de cuentas Apple

La interfaz de administración de cuentas de Apple en Visual Studio proporciona una manera de ver la información de los equipos de desarrollo asociados a un identificador de Apple. Permite hacer lo siguiente:

- **Agregar cuentas de desarrollador de Apple**
- **Ver certificados de firma y perfiles de aprovisionamiento**
- **Crear nuevos certificados de firma**
- **Descargar perfiles de aprovisionamiento existentes**

> [!IMPORTANT]
> Las herramientas de Xamarin para la administración de cuentas de Apple solo muestran información sobre las cuentas de desarrollador de Apple de pago. Para obtener información sobre cómo probar una aplicación en un dispositivo sin una cuenta de desarrollador de Apple de pago, consulte la guía [aprovisionamiento gratuito de aplicaciones de Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="requirements"></a>Requisitos

La administración de cuentas de Apple está disponible en Visual Studio para Mac, Visual Studio 2019 y Visual Studio 2017 (versión 15,7 y posteriores). También debe tener una cuenta de desarrollador de Apple de pago para usar esta característica. Puede encontrar más información sobre las cuentas de desarrollador de Apple en la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

> [!NOTE]
> Antes de empezar, asegúrese de aceptar primero todos los contratos de licencia de usuario en el [portal para desarrolladores de Apple](https://developer.apple.com/account/).

## <a name="add-an-apple-developer-account"></a>Agregar una cuenta de desarrollador de Apple

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Vaya a **Visual Studio > preferencias > cuenta de desarrollador de Apple** y haga clic en el botón **+** para abrir el cuadro de diálogo de inicio de sesión:

    ![AScreenshot de la página de cuentas de desarrollador de Apple en preferencias de Visual Studio para Mac.](apple-account-management-images/add-account-vsm.png)

2. Escriba el identificador y la contraseña de Apple y haga clic en **iniciar sesión**. Esto guardará las credenciales en la cadena de claves segura de este equipo.

3. Seleccione **permitir siempre** en el cuadro de diálogo de alerta para permitir que Visual Studio use las credenciales:

    ![Cuadro de diálogo permitir alerta siempre](apple-account-management-images/image4.png)

4. Una vez que la cuenta se haya agregado correctamente, verá el identificador de Apple y los equipos de los que forma parte su identificador de Apple:

    ![Cuadro de diálogo de la cuenta de desarrollador de Apple con cuentas agregadas](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Si usa Visual Studio 2017 o Visual Studio 2019 (versión 16,4 y anterior), debe [emparejarse con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) antes de continuar.

1. Vaya a **herramientas > opciones > Xamarin > cuentas de Apple** y haga clic en **Agregar**:

    ![Captura de pantalla de la página de cuentas de Apple en opciones de Visual Studio.](apple-account-management-images/add-account-vsw.png)

2. Escriba el identificador y la contraseña de Apple y haga clic en **iniciar sesión**.

3. Una vez que la cuenta se haya agregado correctamente, verá el identificador de Apple y los equipos de los que forma parte su identificador de Apple:

    ![Captura de pantalla de la página de la cuenta de desarrollador con cuentas agregadas.](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>Ver certificados de firma y perfiles de aprovisionamiento

Seleccione un equipo y haga clic en **Ver detalles..** . para abrir un cuadro de diálogo que muestra una lista de identidades de firma y perfiles de aprovisionamiento que están instalados en el equipo.

El cuadro de diálogo Detalles del equipo muestra una lista de identidades de firma, organizadas por tipo. La columna **Estado** le informa si el certificado es: 

- **Válido** : la identidad de firma (tanto el certificado como la clave privada) está instalada en el equipo y no ha expirado.

- **No en la cadena de claves** : hay una identidad de firma válida en el servidor de Apple. Para instalarlo en el equipo, se debe exportar desde otro equipo. No se puede descargar la identidad de firma del portal para desarrolladores de Apple, ya que no contendrá la clave privada.

- **Falta la clave privada** : se ha instalado un certificado sin clave privada en la cadena de claves.

- **Expired** : el certificado ha expirado. Debe quitarlo de la cadena de claves.

  ![información del cuadro de diálogo Detalles del equipo](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>Crear un certificado de firma

Para crear una nueva identidad de firma, haga clic en **crear certificado** para abrir el menú desplegable y seleccione el [tipo de certificado](https://help.apple.com/xcode/mac/current/#/dev80c6204ec) que desea crear. Si tiene los permisos correctos, aparecerá una nueva identidad de firma después de unos segundos.

Si una opción de la lista desplegable está atenuada y no está seleccionada, significa que no tiene los permisos de equipo correctos para crear este tipo de certificado.

## <a name="download-provisioning-profiles"></a>Descargar perfiles de aprovisionamiento

En el cuadro de diálogo Detalles del equipo también se muestra una lista de todos los perfiles de aprovisionamiento conectados a su cuenta de desarrollador. Puede descargar todos los perfiles de aprovisionamiento en el equipo local haciendo clic en **descargar todos los perfiles**.


## <a name="troubleshoot"></a>Solucionar problemas

- Puede tardar varias horas en aprobar una nueva cuenta de desarrollador de Apple. No podrá habilitar el aprovisionamiento automático hasta que se haya aprobado la cuenta.

- Si se produce un error al agregar una cuenta de desarrollador de Apple con el mensaje `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, asegúrese de que el ID. de Apple que usa tiene una pertenencia activa al programa para desarrolladores de Apple. Para usar una cuenta de pago de Apple Developer, consulte la guía [aprovisionamiento gratuito de aplicaciones de Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

- Si se produce un error al intentar crear un nuevo certificado de firma con el `You have reached the limit for certificates of this type`de errores, se generará el número máximo de certificados permitidos. Para solucionarlo, vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/account/ios/certificate/distribution) y revoque uno de los certificados de producción.

- Si tiene problemas para iniciar sesión en su cuenta en Visual Studio para Mac, una posible solución es abrir la aplicación de cadena de claves y, en **categoría** , seleccionar **contraseñas**. Busque `deliver.` y elimine todas las entradas encontradas.

## <a name="known-issues"></a>Problemas conocidos

- El destino predeterminado de los perfiles de aprovisionamiento de distribución será App Store. Los perfiles In House o Ad Hoc deben crearse manualmente.
