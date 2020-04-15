---
title: Cargar en Mac App Store
description: En este documento se describe cómo usar iTunes Connect para cargar una aplicación Xamarin.Mac en el Mac App Store. También se indica la información que necesita iTunes Connect para completar el proceso.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 5569e10c059dec26137aadb814101992ed7e9f7e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725056"
---
# <a name="upload-to-mac-app-store"></a>Cargar en Mac App Store

_En esta guía se describe el proceso de carga de una aplicación Xamarin.Mac para su publicación en el Mac App Store._

Las aplicaciones se envían para la aprobación de Mac App Store a través de [iTunes Connect](https://itunesconnect.apple.com/). También necesitará la herramienta [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) de App Store.

1. Elija crear una **aplicación macOS**:

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Escriba el nombre de la aplicación y otros detalles. El desarrollador solo puede elegir un **id. de paquete** que se haya creado previamente:

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. Seleccione la fecha de disponibilidad y el precio. Independientemente de la fecha de disponibilidad que seleccione el desarrollador, la aplicación solo estará disponible para la venta después de que se haya aprobado. Este valor se puede establecer en un futuro más lejano si el desarrollador quiere tener más control sobre la fecha de disponibilidad real:

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. Especifique la información de la aplicación, incluida la categoría de App Store a la que pertenece:

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox)

    Seleccione las clasificaciones aplicables:

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox)

    Descripción, palabras clave y direcciones URL de contacto:

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox)

    Información de contacto y consejos para los revisores de App Store:

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox)

    Por último, capturas de pantalla:

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox)

    Las capturas de pantalla deben estar en formato JPG, TIF o PNG, con un tamaño en píxeles de 1280 x 800, 1440 x 900, 2880 x 1800 o 2560 x 1600. Pulse **Guardar** para finalizar.

5. Se mostrará la información de la aplicación para que la revise. Haga clic en **Ver detalles** para cambiar el estado:

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. En la vista de detalles, haga clic en Ready to Upload Binary (Listo para cargar binario) para enviar el archivo de paquete de aplicación:

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. Responda a la pregunta de criptografía:

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. El sitio le indicará cuándo está listo para aceptar el archivo de paquete de aplicación:

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. Inicie **Transporter**, inicie sesión con su ID de Apple y, a continuación, elija **ADD APP** (AGREGAR APLICACIÓN):

    [![](uploading-images/transporter01-sml.png "The Application Loader interface")](uploading-images/transporter01.png#lightbox)

    Siga las instrucciones para cargar el paquete de la aplicación en iTunes Connect.

    > [!NOTE]
    > [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) reemplaza a la herramienta **Application Loader** que se utilizaba con Xcode 10 y versiones anteriores.
    > Application Loader ya no está disponible en Xcode 11 o posterior.

Cuando la aplicación se apruebe, estará disponible para su descarga o compra en Mac App Store.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/developer-id/)
