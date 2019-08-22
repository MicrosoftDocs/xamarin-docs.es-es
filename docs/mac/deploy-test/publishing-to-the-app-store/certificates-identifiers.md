---
title: Certificados e identificadores en Xamarin.Mac
description: En esta guía se describe el proceso de creación de los certificados y los identificadores necesarios para publicar una aplicación Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 30731dda1007127caff84eae20696b45803b74f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528998"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificados e identificadores en Xamarin.Mac

_En esta guía se describe el proceso de creación de los certificados y los identificadores necesarios para publicar una aplicación Xamarin.Mac._

## <a name="certificates-and-identifiers"></a>Certificados e identificadores

Visite el [Centro de usuarios registrados de Apple Developer](https://developer.apple.com) para configurar el equipo Mac para desarrollo. A continuación se muestra el menú principal:

[![Centro de usuarios registrados de Apple Developer](certificates-identifiers-images/devcenter01.png "Centro de usuarios registrados de Apple Developer")](certificates-identifiers-images/devcenter01-large.png#lightbox)

Haga clic en el vínculo **Certificates, Identifiers & Profiles (Certificados, identificadores y perfiles)** :

[![Seleccionar certificados, identificadores y perfiles](certificates-identifiers-images/devcenter02.png "Seleccionar certificados, identificadores y perfiles")](certificates-identifiers-images/devcenter02-large.png#lightbox)

Luego, haga clic en el vínculo **Certificados** en la sección **Mac Apps (Aplicaciones Mac)** :

[![Seleccionar el vínculo Certificados](certificates-identifiers-images/devcenter03.png "Seleccionar el vínculo Certificados")](certificates-identifiers-images/devcenter03-large.png#lightbox)

Haga clic en el vínculo **Todas** y luego en el botón **+** :

[![Seleccionar todo y agregar un nuevo elemento](certificates-identifiers-images/certif01.png "Seleccionar todo y agregar un nuevo elemento")](certificates-identifiers-images/certif01-large.png#lightbox)

Desde aquí descargue los **certificados intermedios** (entidades de certificación Worldwide Developer Relations y Developer ID) si fuera necesario. Pero Xcode debería configurarlos automáticamente para el desarrollador.

El resto de esta sección le guía por las cuatro secciones para completar la configuración de una cuenta de desarrollador de Mac.

- **Registrar el identificador de la aplicación de Mac**: el desarrollador tendrá que seguir estos pasos para cada aplicación que escriba.
- **Registrar sistemas macOS**: solo es necesario al agregar equipos para pruebas.
- **Crear certificados**: solo se necesita una vez al configurar los certificados y luego al renovarlos.
- **Crear perfil de aprovisionamiento**: el desarrollador tendrá que seguir estos pasos para cada nueva aplicación escrita y al agregar nuevos sistemas.

Haga clic en el vínculo **Información general** de la parte superior izquierda de la página para volver a este menú en cualquier momento.

### <a name="register-mac-app-id"></a>Registrar el identificador de la aplicación de Mac

El desarrollador debe registrar un identificador de aplicación para cada aplicación escrita. Use los pasos siguientes para crear una entrada para una aplicación de ejemplo básica denominada "MacWriter".

1. Escriba una **descripción de identificador de aplicación** y seleccione los **App Services** que la aplicación vaya a necesitar: 

    [![Escribir la descripción y los App Services](certificates-identifiers-images/devcenter04.png "Escribir la descripción y los App Services")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. Escriba un **Id. de paquete** para la aplicación y haga clic en el botón **Continuar**: 

    [![Escribir un identificador de paquete](certificates-identifiers-images/devcenter05.png "Escribir un identificador de paquete")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. Compruebe la información y haga clic en el botón **Enviar**: 

    [![Comprobar la información](certificates-identifiers-images/devcenter06.png "Comprobar la información")](certificates-identifiers-images/devcenter06-large.png#lightbox)

Algunos **App Services** podrían necesitar configuración adicional (por ejemplo, iCloud). Si ese fuera el caso, seleccione el nuevo identificador de aplicación que acaba de crear y haga clic en el botón **Editar**:

[![Editar el nuevo identificador de aplicación](certificates-identifiers-images/devcenter07.png "Editar el nuevo identificador de aplicación")](certificates-identifiers-images/devcenter07-large.png#lightbox)

Para configurar los servicios iCloud, haga clic en el botón **Editar**:

[![Configurar los servicios iCloud](certificates-identifiers-images/devcenter08.png "Configurar los servicios iCloud")](certificates-identifiers-images/devcenter08-large.png#lightbox)

Desde aquí el desarrollador puede configurar las bases de datos que se van a usar:

[![Configurar las bases de datos](certificates-identifiers-images/devcenter09.png "Configurar las bases de datos")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>Registrar sistemas macOS

Para crear un perfil de aprovisionamiento para realizar pruebas, el desarrollador tendrá que registrar los equipos Mac. Puede registrar un máximo de 100 equipos para probar las aplicaciones de Mac.

En el Centro para desarrolladores de Mac, seleccione **Todos** en la sección **Dispositivos** y haga clic en el botón **+** :

[![Agregar un nuevo equipo](certificates-identifiers-images/devcenter10.png "Agregar un nuevo equipo")](certificates-identifiers-images/devcenter10-large.png#lightbox)

Escriba un **Nombre** y el **UUID** del equipo que va a agregar y haga clic en el botón **Continuar**. Revise la información y haga clic en el botón **Registrar**:

[![Escribir la información del equipo nuevo](certificates-identifiers-images/devcenter11.png "Escribir la información del equipo nuevo")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>Crear certificados

Use la sección Certificados para crear varios tipos distintos de certificados que se usarán para firmar aplicaciones de Mac:

[![Crear un certificado nuevo](certificates-identifiers-images/certif01.png "Crear un certificado nuevo")](certificates-identifiers-images/certif01-large.png#lightbox)

Hay tres tipos principales de certificados:

- **Certificado de desarrollo de Mac**: opcional para el desarrollo de aplicaciones generales, pero necesario si el desarrollador piensa usar características como iCloud o notificaciones de inserción. El desarrollador necesitará un certificado de desarrollo para poder crear perfiles de aprovisionamiento que le permitan acceder a esas características.
- **Mac App Store**: el desarrollador necesitará un certificado para la aplicación y otro certificado para el instalador.
- **Identificador de desarrollador**: si se decide distribuir fuera de Mac App Store, certificados para la aplicación y el instalador.

En las secciones siguientes se proporcionan ejemplos de creación de cada uno de los tipos de certificados anteriores.

#### <a name="mac-development-certificate"></a>Certificado de desarrollo de Mac

Como se ha mencionado anteriormente, el certificado de desarrollo de aplicaciones de Mac no es necesario a menos que se vayan a usar características de macOS como iCloud o notificaciones de inserción.

Haga lo siguiente para crear un nuevo certificado de desarrollo:

1. Active el botón de radio **Mac Development (Desarrollo de Mac)** y haga clic en **Continuar**: 

     [![Agregar un certificado de desarrollo](certificates-identifiers-images/certif02.png "Agregar un certificado de desarrollo")](certificates-identifiers-images/certif02-large.png#lightbox)
2. En la pantalla siguiente se explica cómo usar Acceso a llaves para crear un archivo de solicitud de certificado de firma para cargar: 

    [![La pantalla de carga de Acceso a llaves](certificates-identifiers-images/certif03.png "La pantalla de carga de Acceso a llaves")](certificates-identifiers-images/certif03-large.png#lightbox)
3. Elija un nombre común significativo para el certificado para que sea fácilmente reconocible más adelante cuando se cree el certificado final. Recuerde dónde se guarda el archivo, para poder encontrarlo en el paso siguiente: 

     ![Exportar un certificado](certificates-identifiers-images/image12.png "Exportar un certificado")
4. Un archivo de solicitud de certificado (extensión `.certSigningRequest`) se guardará localmente en el equipo Mac. Recuerde dónde se guarda (la ubicación predeterminada es el escritorio), porque tendrá que elegirlo en el paso siguiente: 

     [![Cargar el archivo de certificado](certificates-identifiers-images/image13.png "Cargar el archivo de certificado")](certificates-identifiers-images/image13-large.png#lightbox)
5. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**: 

     [![Descargar un certificado de desarrollo](certificates-identifiers-images/image15.png "Descargar un certificado de desarrollo")](certificates-identifiers-images/image15-large.png#lightbox)
6. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**. La **utilidad de certificado de desarrollador** mostrará los certificados así: 

     [![La utilidad de certificado de desarrollador](certificates-identifiers-images/image16.png "La utilidad de certificado de desarrollador")](certificates-identifiers-images/image16-large.png#lightbox)
7. También aparecerá en **Acceso a llaves** así: 

     ![Certificado en Acceso a llaves](certificates-identifiers-images/image17.png "Certificado en Acceso a llaves")

Como se ha mencionado anteriormente, el certificado de desarrollador no siempre es necesario, a menos que el desarrollador esté implementando características de macOS como iCloud y notificaciones de inserción. También es necesario crear un **perfil de aprovisionamiento de desarrollo**, que será necesario para probar aplicaciones de Mac App Store.

#### <a name="mac-app-store-certificates"></a>Certificados de Mac App Store

Para publicar una aplicación en el App Store, habrá que crear un certificado de **Mac App Store** que se usará para firmar la aplicación y el paquete de Mac Installer.

1. Seleccione **Mac App Store** como tipo de certificado y haga clic en el botón **Continuar**: 

    [![Crear un certificado de App Store](certificates-identifiers-images/certif04.png "Crear un certificado de App Store")](certificates-identifiers-images/certif04-large.png#lightbox)
2. Seleccione el tipo de certificado que se va a crear (necesitará uno de cada tipo para publicar en el App Store): 

    [![Seleccionar el tipo de certificado](certificates-identifiers-images/certif05.png "Seleccionar el tipo de certificado")](certificates-identifiers-images/certif05-large.png#lightbox)
3. En la página siguiente se explica cómo usar **Acceso a llaves** para generar un archivo de solicitud de certificado. Siga las instrucciones: 

     [![Generar la solicitud de llaves](certificates-identifiers-images/certif06.png "Generar la solicitud de llaves")](certificates-identifiers-images/certif06-large.png#lightbox)
4. Elija un **nombre común** descriptivo: por ejemplo, use el texto "Aplicación del App Store": 

     ![Escribir un nombre descriptivo](certificates-identifiers-images/image20.png "Escribir un nombre descriptivo")
5. Un archivo de solicitud de certificado (extensión `.certSigningRequest`) se guardará localmente en el equipo Mac. Recuerde dónde se guarda (la ubicación predeterminada es el escritorio): 

     [![Guardar el certificado](certificates-identifiers-images/image21.png "Guardar el certificado")](certificates-identifiers-images/image21-large.png#lightbox)
6. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**: 

      [![Descargar el certificado de App Store](certificates-identifiers-images/image23.png "Descargar el certificado de App Store")](certificates-identifiers-images/image23-large.png#lightbox)
7. Haga clic en **Continuar** y siga los mismos pasos que para descargar otro certificado, esta vez para el *instalador*: 

     [![Seleccionar el instalador](certificates-identifiers-images/image24.png "Seleccionar el instalador")](certificates-identifiers-images/image24-large.png#lightbox)
8. Elija un **nombre común** descriptivo: por ejemplo, use el texto "Instalador del App Store": 

     ![Establecer el nombre del certificado](certificates-identifiers-images/image25.png "Establecer el nombre del certificado")
9. Un archivo de solicitud de certificado (extensión `.certSigningRequest`) se guardará localmente en el equipo Mac. Recuerde dónde se guarda (la ubicación predeterminada es el escritorio): 

     [![Cargar el certificado](certificates-identifiers-images/image26.png "Cargar el certificado")](certificates-identifiers-images/image26-large.png#lightbox) 

     [![Descargar el certificado de distribución](certificates-identifiers-images/image28.png "Descargar el certificado de distribución")](certificates-identifiers-images/image28-large.png#lightbox)
10. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**. La utilidad de certificado de desarrollador mostrará los certificados así: 

     [![La utilidad de certificado de desarrollador](certificates-identifiers-images/image29.png "La utilidad de certificado de desarrollador")](certificates-identifiers-images/image29-large.png#lightbox)
11. Los dos nuevos certificados ya serán visibles en **Acceso a llaves**: 

     [![Certificado en Acceso a llaves](certificates-identifiers-images/image30.png "Certificado en Acceso a llaves")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>Certificados de identificador de desarrollador

Para publicar por sí mismo una aplicación de Xamarin.Mac (no publicar a través de Apple App Store), el desarrollador necesita un certificado de identificador de desarrollador para firmar la aplicación para su publicación e instalación.

Haga lo siguiente:

1. En la sección **Certificados**, haga clic en el botón **+** y luego active el botón de radio **Id. de desarrollador**: 

    [![Agregar un identificador de desarrollador](certificates-identifiers-images/certif07.png "Agregar un identificador de desarrollador")](certificates-identifiers-images/certif07-large.png#lightbox)
2. Haga clic en el botón **Continuar** y seleccione el tipo de identificador de desarrollador que va a crear: 

    [![Seleccionar el tipo de identificador de desarrollador](certificates-identifiers-images/certif08.png "Seleccionar el tipo de identificador de desarrollador")](certificates-identifiers-images/certif08-large.png#lightbox)
3. Necesitará dos, uno para firmar la propia aplicación y otro para firmar el instalador de la aplicación. Tenga cuidado al asignar nombres a las solicitudes de certificado de estas claves: use nombres descriptivos que incluyan el texto `Application` y `Installer` para poder distinguirlas más adelante.
4. En la pantalla siguiente se proporcionan instrucciones detalladas sobre cómo crear el certificado, haga clic en el botón **Continuar**: 

    [![Cómo crear el certificado](certificates-identifiers-images/certif09.png "Cómo crear el certificado")](certificates-identifiers-images/certif09-large.png#lightbox)
5. Elija un **nombre común** descriptivo: por ejemplo, use el texto "Aplicación de identificador de desarrollador": 

     ![Escribir un nombre para el certificado](certificates-identifiers-images/image33.png "Escribir un nombre para el certificado")
6. Se guardará un archivo de solicitud de certificado (extensión `.certSigningRequest`) localmente en el equipo Mac. Recuerde dónde se guarda (la ubicación predeterminada es el escritorio): 

     [![Cargar el certificado](certificates-identifiers-images/certif10.png "Cargar el certificado")](certificates-identifiers-images/certif10-large.png#lightbox) 

     [![Descargar el identificador de desarrollador](certificates-identifiers-images/certif11.png "Descargar el identificador de desarrollador")](certificates-identifiers-images/certif11-large.png#lightbox)
7. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**.
8. Haga clic en **Continuar** y siga los mismos pasos que para descargar otro certificado, esta vez para el *instalador*.
9. Elija un **nombre común** descriptivo: por ejemplo, use el texto "Instalador de identificador de desarrollador": 

     ![Escribir un nombre común](certificates-identifiers-images/image38.png "Escribir un nombre común")
10. Un archivo de solicitud de certificado (extensión `.certSigningRequest`) se guardará localmente en el equipo Mac. Recuerde dónde se guarda (la ubicación predeterminada es el escritorio): 

     [![Cargar un certificado](certificates-identifiers-images/certif10.png "Cargar un certificado")](certificates-identifiers-images/certif10-large.png#lightbox)
11. El certificado está disponible para descargar: haga clic en el botón **Descargar** antes de hacer clic en **Listo**: 

     [![Descargar un certificado](certificates-identifiers-images/certif11.png "Descargar un certificado")](certificates-identifiers-images/certif11-large.png#lightbox)
12. Haga clic en **Descargar** para obtener el certificado y haga doble clic para instalarlo en **Acceso a llaves**. La **utilidad de certificado de desarrollador** mostrará los certificados así: 

     [![La utilidad de certificado de desarrollador](certificates-identifiers-images/certif12.png "La utilidad de certificado de desarrollador")](certificates-identifiers-images/certif12-large.png#lightbox)
13. Los siguientes elementos están visibles en **Acceso a llaves**: 

     [![Certificado en Acceso a llaves](certificates-identifiers-images/image43.png "Certificado en Acceso a llaves")](certificates-identifiers-images/image43-large.png#lightbox)


## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
