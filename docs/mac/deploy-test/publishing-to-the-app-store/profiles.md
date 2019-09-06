---
title: Perfiles de aprovisionamiento para aplicaciones Xamarin.Mac
description: En esta guía se describe el proceso de creación de los perfiles de aprovisionamiento necesarios para publicar una aplicación Xamarin.Mac.
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 04/12/2017
ms.openlocfilehash: c0a4766abf8ded591bf348f2c2a7ba2283cdde00
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290241"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Perfiles de aprovisionamiento para aplicaciones Xamarin.Mac

Los perfiles de aprovisionamiento permiten a los desarrolladores incorporar en sus aplicaciones de Xamarin.Mac varias características específicas de macOS (antes conocido como Mac OS X), como iCloud y notificaciones de inserción. Es necesario crear, descargar e instalar un perfil de aprovisionamiento de Mac para cada aplicación que se desarrolle y que use estas características.

[![](profiles-images/certif13.png "Portal de aprovisionamiento de Apple")](profiles-images/certif13.png#lightbox)

<a name="Development_Provisioning_Profile" />

## <a name="development-provisioning-profile"></a>Perfil de aprovisionamiento de desarrollo

Un perfil de aprovisionamiento de desarrollo permite que una aplicación destinada a Mac App Store se pruebe en los equipos específicos que se han configurado en el perfil. Esto es especialmente importante cuando se usan características de macOS como iCloud y las notificaciones de inserción.

> [!NOTE]
> El desarrollador debe haber creado un certificado de desarrollo de Mac para poder crear un perfil de aprovisionamiento de desarrollo. Complete los detalles como se indica en la captura de pantalla para generar un **perfil de aprovisionamiento de desarrollo** que se pueda usar para crear compilaciones. Debe haber un certificado de desarrollo de Mac válido disponible para su selección en el cuadro **Certificado** y, como mínimo, un sistema registrado para las pruebas.

Haga lo siguiente:

1. Seleccione el tipo de perfil de aprovisionamiento que se va a crear y haga clic en el botón **Continuar**: 

    [![](profiles-images/certif14.png "Selección del tipo de perfil")](profiles-images/certif14.png#lightbox)
2. Seleccione el identificador de la aplicación para la que se va a crear el perfil y haga clic en el botón **Continuar**: 

    [![](profiles-images/certif15.png "Selección del identificador de la aplicación")](profiles-images/certif15.png#lightbox)
3. Seleccione el identificador de desarrollador usado para firmar el perfil y haga clic en **Continuar**: 

    [![](profiles-images/certif16.png "Selección del identificador del desarrollador")](profiles-images/certif16.png#lightbox)
4. Seleccione los equipos en los que se puede usar este perfil y haga clic en **Continuar**: 

    [![](profiles-images/certif17.png "Selección de los equipos permitidos")](profiles-images/certif17.png#lightbox)
5. Ahora, especifique un **nombre de perfil** y haga clic en el botón **Generar**: 

    [![](profiles-images/certif18.png "Generación del perfil")](profiles-images/certif18.png#lightbox)
6. Haga clic en el botón **Descargar** para descargar el nuevo perfil: 

    [![](profiles-images/certif19.png "Descarga del perfil")](profiles-images/certif19.png#lightbox)
7. Los perfiles de aprovisionamiento de desarrollo se instalan en el panel de preferencias de perfiles de la aplicación **Preferencias del sistema** de Mac: 

    [![](profiles-images/certif20.png "Instalación del perfil")](profiles-images/certif20.png#lightbox)
8. En el panel de preferencias de perfiles se mostrarán todos los perfiles instalados: 

    [![](profiles-images/image47.png "Consulta de todos los perfiles instalados")](profiles-images/image47.png#lightbox)
9. El perfil también aparecerá en la **utilidad de certificados de desarrollador** en caso de que tenga que volver a descargarse: 

    [![](profiles-images/image48.png "Utilidad de certificado de desarrollador")](profiles-images/image48.png#lightbox)

Será necesario crear un perfil de aprovisionamiento de desarrollo para cada aplicación nueva o cuando se agregue un equipo nuevo para realizar pruebas en él.

<a name="Production_Provisioning_Profile" />

## <a name="production-provisioning-profile"></a>Perfil de aprovisionamiento de producción

Los perfiles de aprovisionamiento de producción son necesarios para compilar un paquete para enviarlo a Mac App Store.

Haga lo siguiente:

1. Seleccione el tipo de perfil que se va a crear y haga clic en el botón **Continuar**: 

    [![](profiles-images/certif21.png "Selección del tipo de perfil")](profiles-images/certif21.png#lightbox)
2. Seleccione el identificador de la aplicación para la que se va a crear el perfil y haga clic en el botón **Continuar**: 

    [![](profiles-images/certif15.png "Selección del identificador de la aplicación")](profiles-images/certif15.png#lightbox)
3. Seleccione el identificador de empresa para firmar el perfil y haga clic en el botón **Continuar**: 

    [![](profiles-images/certif23.png "Selección del identificador de empresa")](profiles-images/certif23.png#lightbox)
4. Especifique un **nombre de perfil** y haga clic en el botón **Generar**: 

    [![](profiles-images/certif24.png "Generación del perfil")](profiles-images/certif24.png#lightbox)
5. Haga clic en **Descargar** para obtener el archivo del perfil de aprovisionamiento (extensión `.provisionprofile`): 

    [![](profiles-images/certif25.png "Descarga del perfil")](profiles-images/certif25.png#lightbox)
6. Arrástrelo al **organizador de Xcode** o haga doble clic en él para instalarlo. El perfil aparecerá en el organizador de Xcode: 

    [![](profiles-images/image51.png "Instalación del perfil")](profiles-images/image51.png#lightbox)
7. El perfil de aprovisionamiento también aparecerá en la lista: 

    [![](profiles-images/certif26.png "Consulta de los perfiles instalados")](profiles-images/certif26.png#lightbox)


Si el desarrollador cambia en algún momento las características que un identificador de aplicación usa (por ejemplo, habilita iCloud o las notificaciones de inserción), debe volver a crear los perfiles de aprovisionamiento para ese identificador de aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
