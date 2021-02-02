---
title: Firma de aplicaciones Xamarin.Mac con un identificador de desarrollador
description: En este documento se describe cómo firmar una aplicación Xamarin.Mac con un identificador de desarrollador para que se pueda distribuir fuera del Mac App Store. Se explican también las opciones de firma de código y la compilación.
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7e2d676b577e835bafe5cdd8d6bc48229a1a1a06
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697584"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>Firma de aplicaciones Xamarin.Mac con un identificador de desarrollador

Si el desarrollador tiene previsto distribuir una aplicación directamente a los usuarios de macOS, Apple recomienda que firme el código con su identificador de desarrollador para que se pueda instalarse en sistemas macOS con el **equipo selector** habilitado. Si la aplicación no se ha firmado, el **equipo selector** impedirá con un mensaje de alerta que los usuarios realicen la instalación (es posible omitir esta restricción si se mantiene presionada la tecla Control durante el inicio).

Obtenga más información sobre [Identificador del desarrollador y equipo selector](https://developer.apple.com/developer-id/) y [Distribuir fuera del Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) en el sitio web de Apple.

## <a name="code-signing-options"></a>Opciones de firma de código

Para compilar una aplicación para la implementación directamente en los usuarios (NO a través de Mac App Store), establezca que la **configuración de firma** use el **identificador de desarrollador**. Asegúrese de editar la configuración de **lanzamiento**.

 [![Las opciones de la firma de Mac](signing-images/config02.png)](signing-images/config02.png#lightbox)

## <a name="build"></a>Compilar

Antes de compilar, asegúrese de que ha seleccionado la configuración correcta y elija crear un paquete de instalación en la configuración de **Mac Build** (Compilación de Mac):

[![Las opciones de compilación](signing-images/config03.png)](signing-images/config03.png#lightbox)

Al compilar la aplicación, se le solicitará al desarrollador que use los dos certificados:

 [![Captura de pantalla que muestra el cuadro de diálogo de permitir acceso para codesign.](signing-images/image57.png)](signing-images/image57.png#lightbox)

 [![Captura de pantalla que muestra el cuadro de diálogo de permitir acceso para la compilación del producto.](signing-images/image58.png)](signing-images/image58.png#lightbox)

Una vez que se ha compilado la aplicación, el desarrollador puede hacer clic con el botón derecho en el proyecto y seleccionar **Abrir carpeta contenedora** para buscar el archivo de paquete (en el directorio `bin/Release`). Este archivo de paquete incluye un instalador para la aplicación, por lo que se puede distribuir a cualquier usuario de macOS para su instalación.

 [![Seleccionar el paquete de aplicación en el buscador](signing-images/image59.png)](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/developer-id/)
