---
title: Administración de cuentas Apple
description: En este documento se describe cómo usar las características de administración de cuentas de Apple en Visual Studio para Mac y Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 9629d775b45951279178dffa3600e7cd5073dd38
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290629"
---
# <a name="apple-account-management"></a>Administración de cuentas Apple

La interfaz de administración de cuentas de Apple proporciona una manera de ver todos los equipos de desarrollo asociados a un identificador de Apple. También permite ver más detalles sobre cada equipo mostrando una lista de _identidades de firma_ y _perfiles de aprovisionamiento_ que están instalados en el equipo.

La autenticación de su ID de Apple se realiza en la línea de comandos con [Fastlane](https://fastlane.tools/). Fastlane debe estar instalado en el equipo para que se autentique correctamente. Encontrará más información en Fastlane y cómo instalarlo en las guías de [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) .

El cuadro de diálogo de la cuenta de Apple le permite hacer lo siguiente:

- **Crear y administrar certificados**
- **Crear y administrar perfiles de aprovisionamiento**

En esta guía se describe la información sobre cómo hacerlo.

> [!NOTE]
> Las herramientas de Xamarin para la administración de cuentas de Apple solo muestran información sobre las cuentas de desarrollador de Apple de pago. Para obtener información sobre cómo probar una aplicación en un dispositivo sin una cuenta de desarrollador de Apple de pago, consulte la guía [aprovisionamiento gratuito de aplicaciones de Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

También puede usar las herramientas de aprovisionamiento automático de iOS para crear y administrar automáticamente las identidades de firma, los ID. de aplicación y los perfiles de aprovisionamiento. Para obtener más información sobre el uso de estas características, consulte la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="requirements"></a>Requisitos

La administración de cuentas de Apple está disponible en Visual Studio para Mac, Visual Studio 2019 y Visual Studio 2017 (versión 15,7 y posteriores).

Para usar esta característica, debe tener una cuenta de desarrollador de Apple. Puede encontrar más información sobre las cuentas de desarrollador de Apple en la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

- Asegúrese de que está conectado a Internet. Esto se debe a que Fastlane se comunica directamente con el portal para desarrolladores de Apple.
- Asegúrese de que tiene [las herramientas de Fastlane instaladas](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Asegúrese de que tiene las herramientas de Fastlane [https://download.fastlane.tools](https://download.fastlane.tools)más recientes de.
- Antes de empezar, asegúrese de aceptar los contratos de licencia de usuario en el [portal para desarrolladores](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Agregar una cuenta de desarrollador de Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Para abrir el cuadro de diálogo de administración de cuentas, vaya a **Visual Studio > preferencias > cuenta de desarrollador de Apple**:

    ![Opciones de la cuenta de desarrollador de Apple](apple-account-management-images/image1.png)

2. Presione el **+** botón para mostrar el cuadro de diálogo de inicio de sesión, como se muestra a continuación: 

    ![cuadro de diálogo Fastlane.](apple-account-management-images/image2.png)

3. Escriba el identificador y la contraseña de Apple y haga clic en el botón **iniciar sesión** . Esto guardará las credenciales en la cadena de claves segura de este equipo. [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) se utiliza para administrar las credenciales de forma segura y pasarlas al portal para desarrolladores de Apple.
 
4. Seleccione **permitir siempre** en el cuadro de diálogo de alerta para permitir que Visual Studio use las credenciales:

    ![Cuadro de diálogo permitir alerta siempre](apple-account-management-images/image4.png)

5. Una vez que la cuenta se haya agregado correctamente, verá el identificador de Apple y los equipos de los que forma parte su identificador de Apple.

    ![Cuadro de diálogo de la cuenta de desarrollador de Apple con cuentas agregadas](apple-account-management-images/image5.png)

6. Seleccione cualquier equipo y presione la tecla **Ver detalles..** . . Se mostrará una lista de todas las identidades de firma y los perfiles de aprovisionamiento que están instalados en el equipo:

    ![Pantalla ver detalles que muestra las identidades de firma y los perfiles de aprovisionamiento en el equipo](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Antes de empezar a agregar su identificador de Apple a Visual Studio 2019, asegúrese de que el entorno de desarrollo está [emparejado con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Para abrir la ventana Administración de cuentas, vaya a **herramientas > opciones > Xamarin > cuentas de Apple**:

    ![Pantalla de opciones de cuentas de Apple](apple-account-management-images/prov1.png)

1. Seleccione el botón **Agregar** y escriba el ID. y la contraseña de Apple:

    ![cuadro de diálogo nombre de usuario y contraseña](apple-account-management-images/prov1a.png)

1. Una vez que la cuenta se haya agregado correctamente, verá el identificador de Apple y los equipos de los que forma parte su identificador de Apple.
 
1. Seleccione cualquier equipo y presione la tecla **Ver detalles..** . . Se mostrará una lista de todas las identidades de firma y los perfiles de aprovisionamiento que están instalados en el equipo:

    ![cuadro de diálogo nombre de usuario y contraseña](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Administrar identidades de firma y perfiles de aprovisionamiento

El cuadro de diálogo Detalles del equipo muestra una lista de identidades de firma, organizadas por tipo. La columna **Estado** le informa si el certificado es: 

- **Válido** : la identidad de firma (tanto el certificado como la clave privada) está instalada en el equipo y no ha expirado.

- **No en la cadena de claves** : hay una identidad de firma válida en el servidor de Apple. Para instalarlo en el equipo, se debe exportar desde otro equipo. No se puede descargar la identidad de firma del portal para desarrolladores de Apple, ya que no contendrá la clave privada.

- **Falta la clave privada** : se ha instalado un certificado sin clave privada en la cadena de claves.

- **Expired** : el certificado ha expirado. Debe quitarlo de la cadena de claves.

  ![información del cuadro de diálogo Detalles del equipo](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Crear identidades de firma

Para crear una nueva identidad de firma, seleccione el botón desplegable **crear certificado** y seleccione el tipo que necesite. Si tiene los permisos correctos, aparecerá una nueva identidad de firma después de unos segundos.

Si una opción de la lista desplegable está atenuada y no está seleccionada, significa que no tiene los permisos de equipo correctos para crear este tipo de certificado.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Opciones de crear certificado](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Opciones de crear certificado](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Descargar perfiles de aprovisionamiento

En el cuadro de diálogo Detalles del equipo también se muestra una lista de todos los perfiles de aprovisionamiento conectados a su cuenta de desarrollador. Puede descargar todos los perfiles de aprovisionamiento en el equipo local presionando el botón **descargar todos los perfiles** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Descarga de la sección de perfiles de aprovisionamiento](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Descarga de la sección de perfiles de aprovisionamiento](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS Bundle Signing

Para obtener información sobre la implementación de la aplicación en un dispositivo, consulte la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="troubleshooting"></a>Solución de problemas

### <a name="view-details-dialog-is-empty"></a>El cuadro de diálogo ver detalles está vacío

Este es actualmente un problema conocido relacionado con el [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)de errores. Asegúrese de que está usando la versión estable más reciente de Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Si tiene problemas para iniciar sesión en su cuenta, intente lo siguiente:

- Abra la aplicación de cadena de claves y, en categoría, seleccione *contraseñas*. `deliver.`Busque y elimine todas las entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Error al agregar la cuenta. Inicie sesión con una contraseña específica de la aplicación "

Esto se debe a que la autenticación en dos fases está habilitada en su cuenta. Asegúrese de que está usando la versión estable más reciente de Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>No se pudo crear el nuevo certificado
"Ha alcanzado el límite para los certificados de este tipo"

![cuadro de diálogo límite de certificado](apple-account-management-images/image10.png)

Se ha generado el número máximo de certificados permitidos. Para solucionarlo, vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/account/ios/certificate/distribution) y revoque uno de los certificados de producción.

## <a name="known-issues"></a>Problemas conocidos

- El destino predeterminado de los perfiles de aprovisionamiento de distribución será App Store. Los perfiles In House o Ad Hoc deben crearse manualmente.
