---
title: Certificados e identificadores en Xamarin.Mac
description: En esta guía se describe el proceso de creación de los certificados y los identificadores necesarios para publicar una aplicación Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: ae62a695670f50c5385b9279c6d6de79b6d59fb7
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697571"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificados e identificadores en Xamarin.Mac

_En esta guía se describe el proceso de creación de los certificados y los identificadores necesarios para publicar una aplicación Xamarin.Mac._

## <a name="setup"></a>Programa de instalación

Visite el [Centro de usuarios registrados de Apple Developer](https://developer.apple.com) para configurar el equipo Mac para desarrollo. Haga clic en el vínculo **Account** (Cuenta). A continuación se muestra el menú principal:

> [!div class="mx-imgBorder"]
> [![El Centro de usuarios registrados de Apple Developer](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Haga clic en el botón **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) (o el botón de suma cerca del encabezado **Certificates** [Certificados]).

> [!div class="mx-imgBorder"]
> [![Selección de certificados, identificadores y perfiles](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Seleccione un tipo de certificado y haga clic en **Continue** (Continuar):

> [!div class="mx-imgBorder"]
> [![Seleccionar el vínculo Certificados](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

Desde aquí descargue los **certificados intermedios** (entidades de certificación Worldwide Developer Relations y Developer ID) si fuera necesario (último elemento en la parte inferior de la página). Pero Xcode debería configurarlos automáticamente para el desarrollador.

El resto de esta sección le guía a través de las secciones pertinentes para los desarrolladores de Mac:

- **Registrar el identificador de la aplicación de Mac**: el desarrollador tendrá que seguir estos pasos para cada aplicación que escriba.
- **Registrar sistemas macOS**: solo es necesario al agregar equipos para pruebas.
- **Create Certificates** (Crear certificados): solo se necesita una vez al configurar los certificados y luego al renovarlos.
- **Crear perfil de aprovisionamiento**: el desarrollador tendrá que seguir estos pasos para cada nueva aplicación escrita y al agregar nuevos sistemas.

## <a name="register-mac-app-id"></a>Registrar el identificador de la aplicación de Mac

Debe registrar un identificador de aplicación para cada aplicación. Para crear una entrada, siga los pasos que se indican a continuación.

1. Presione "+" (signo más) o **Register an App ID** (Registrar un identificador de aplicación):

    > [!div class="mx-imgBorder"]
    > [![La captura de pantalla muestra Introducción con los identificadores de aplicación en certificados, identificadores y perfiles.](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Elija **App IDs** (Id. de aplicación).

    > [!div class="mx-imgBorder"]
    > [![La captura de pantalla muestra la opción de registrar un nuevo identificador.](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Escriba una **descripción** y seleccione los **App Services** (Servicios de aplicación) que la aplicación vaya a necesitar: a. La plataforma debe ser **macOS** a. Elija una **descripción** (solo se usa en este portal) a. Escriba el **identificador de paquete**, que debe coincidir con **Info.plist** a. Selección de las funciones que requiere la aplicación

    > [!div class="mx-imgBorder"]
    > [![Escribir la descripción y los App Services](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Presione **Continue** (Continuar) para revisar sus selecciones.

1. Si la información es correcta, haga clic en **Register** (Registrar) para completar la instalación:

    > [!div class="mx-imgBorder"]
    > [![Revisión de los datos especificados](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Compruebe la información y haga clic en el botón **Enviar**:

    > [!div class="mx-imgBorder"]
    > ![Comprobación de la información](certificates-identifiers-images/appid05.png)

Algunos **App Services** podrían necesitar configuración adicional (por ejemplo, iCloud). Si ese fuera el caso, seleccione el nuevo identificador de aplicación que acaba de crear y haga clic en el botón **Editar**:

> [!div class="mx-imgBorder"]
> [![Edición del nuevo identificador de aplicación](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Para configurar los servicios iCloud, por ejemplo, haga clic en el botón **Edit** (Editar):

> [!div class="mx-imgBorder"]
> [![Configurar los servicios iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Registro de dispositivos macOS

Para crear un perfil de aprovisionamiento para realizar pruebas, el desarrollador tendrá que registrar los equipos Mac. Se puede registrar un máximo de 100 equipos para las pruebas.

1. En el Centro para desarrolladores de Mac, seleccione **Todos** en la sección **Dispositivos** y haga clic en el botón **+** :

    > [!div class="mx-imgBorder"]
    > [![Agregar un nuevo equipo](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Escriba un **Nombre** y el **UUID** del equipo que va a agregar y haga clic en el botón **Continuar**. Revise la información y haga clic en el botón **Registrar**:

    > [!div class="mx-imgBorder"]
    > [![La captura de pantalla muestra la página para registrar un nuevo dispositivo, donde puede escribir el nombre y el uuid.](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Revisión y confirmación de los datos especificados:

    > [!div class="mx-imgBorder"]
    > [![La captura de pantalla muestra la página para registrar un nuevo dispositivo, donde puede confirmar el nombre y el uuid.](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Crear certificados

Use la sección Certificados para crear varios tipos distintos de certificados que se usarán para firmar aplicaciones de Mac:

> [!div class="mx-imgBorder"]
> [![Creación de un certificado nuevo](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Hay cinco tipos principales de certificado relacionados con el desarrollo de macOS:

- **Desarrollo de Mac**: opcional para el desarrollo de aplicaciones generales, pero necesario si el desarrollador piensa usar características como iCloud o notificaciones de inserción. El desarrollador necesitará un certificado de desarrollo para poder crear perfiles de aprovisionamiento que le permitan acceder a esas características.
- **Distribución de aplicaciones de Mac**: el desarrollador necesitará un certificado para la aplicación y otro certificado para el instalador.
- **Distribución de instaladores de Mac**: el desarrollador necesitará un certificado para la aplicación y otro certificado para el instalador.
- **Instalador del identificador de desarrollador**: certificados para el instalador para distribuir fuera de App Store de Mac.
- **Aplicación del identificador de desarrollador**: certificados para la aplicación para distribuir fuera de App Store de Mac.

En las secciones siguientes se proporcionan ejemplos de creación de algunos de estos tipos de certificados.

### <a name="mac-development-certificate"></a>Certificado de desarrollo de Mac

Como se ha mencionado anteriormente, el certificado de desarrollo de Mac no es necesario a menos que se vayan a usar características de macOS como iCloud o notificaciones de inserción.

Haga lo siguiente para crear un nuevo certificado de desarrollo:

1. Active el botón de radio **Mac Development (Desarrollo de Mac)** y haga clic en **Continuar**:

    > [!div class="mx-imgBorder"]
    > [![Agregar un certificado de desarrollo](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Cargue una _solicitud de una firma de certificado_. El archivo de solicitud de certificado (extensión `.certSigningRequest`) se guardará localmente en el equipo Mac. Haga clic en **Choose file** (Elegir archivo) para seleccionar la solicitud de certificado y, a continuación, presione **Continue** (Continuar).

    > [!div class="mx-imgBorder"]
    > [![Carga de un archivo de solicitud de certificado](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Siga el vínculo [Learn more >](https://help.apple.com/developer-account/#/devbfa00fef7) (Más información) para obtener instrucciones sobre cómo usar **Acceso a llaveros** para crear un archivo de solicitud de certificado.

1. Haga clic en **Download** (Descargar) para obtener el archivo de certificado y haga doble clic para instalarlo en Acceso a llaveros:

    > [!div class="mx-imgBorder"]
    > [![Descarga del archivo de certificado](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Como se ha mencionado anteriormente, el certificado de desarrollador no siempre es necesario, a menos que el desarrollador esté implementando características de macOS como iCloud y notificaciones de inserción. También es necesario crear un **perfil de aprovisionamiento de desarrollo**, que será necesario para probar aplicaciones de Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificados de Mac App Store

Para publicar una aplicación en App Store, necesitará dos certificados:

- el certificado de **distribución de aplicaciones de Mac**, que se usará para firmar la aplicación; y
- el certificado de **distribución de instaladores de Mac**, para firmar el instalador.

> [!TIP]
> Tenga cuidado al asignar nombres a las solicitudes de certificado de estas claves: use nombres descriptivos que incluyan el texto `Application` y `Installer` para poder distinguirlas más adelante.

En primer lugar, cree el certificado del instalador:

1. Seleccione **Mac Installer Distribution** (Distribución de instaladores de Mac) como tipo de certificado y haga clic en el botón **Continue** (Continuar):

    > [!div class="mx-imgBorder"]
    > [![Crear un certificado de App Store](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. En la página siguiente se explica cómo usar **Acceso a llaves** para generar un archivo de solicitud de certificado. Siga las instrucciones:

    > [!div class="mx-imgBorder"]
    > [![Carga de una solicitud de certificado](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Siga el vínculo [Learn more >](https://help.apple.com/developer-account/#/devbfa00fef7) (Más información) para obtener instrucciones sobre cómo usar **Acceso a llaveros** para crear un archivo de solicitud de certificado. Recuerde elegir un nombre de certificado que refleje el _tipo_ de certificado (aplicación o instalador).

1. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**:

    > [!div class="mx-imgBorder"]
    > [![Descarga del certificado de App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Siga los mismos pasos para el certificado de distribución de aplicaciones de Mac.**

![Certificado de distribución de aplicaciones de Mac](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificados de identificador de desarrollador

Para publicar automáticamente una aplicación de Xamarin.Mac (no se publica a través de App Store de Apple), necesitará dos certificados:

- el certificado de **instalador del identificador de desarrollador**, que se usará para firmar la aplicación; y
- el certificador de **aplicación del identificador de desarrollador**, para firmar el instalador.

> [!TIP]
> Tenga cuidado al asignar nombres a las solicitudes de certificado de estas claves: use nombres descriptivos que incluyan el texto `Application` y `Installer` para poder distinguirlas más adelante.

Una vez que haya creado, descargado e instalado certificados, estarán visibles en **Acceso a Llaveros**:

[Lista de certificados de Acceso a Llaveros](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/developer-id/)
