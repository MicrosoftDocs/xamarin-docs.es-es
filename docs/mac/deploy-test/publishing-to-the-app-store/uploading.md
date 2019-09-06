---
title: Cargar en Mac App Store
description: En este documento se describe cómo usar iTunes Connect para cargar una aplicación Xamarin.Mac en el Mac App Store. También se indica la información que necesita iTunes Connect para completar el proceso.
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: b0bb58fee1c9eaeee391b3a838b3fa2921e97a57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284264"
---
# <a name="upload-to-mac-app-store"></a>Cargar en Mac App Store

_En esta guía se describe el proceso de carga de una aplicación Xamarin.Mac para su publicación en el Mac App Store._

Las aplicaciones se envían para la aprobación de Mac App Store a través de [iTunes Connect](http://itunesconnect.apple.com/).

1. Elija crear una **aplicación macOS**: 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. Escriba el nombre de la aplicación y otros detalles. El desarrollador solo puede elegir un **id. de paquete** que se haya creado previamente: 

    [![](uploading-images/image66.png "Selección del identificador de paquete")](uploading-images/image66.png#lightbox)

3. Seleccione la fecha de disponibilidad y el precio. Independientemente de la fecha de disponibilidad que seleccione el desarrollador, la aplicación solo estará disponible para la venta después de que se haya aprobado. Este valor se puede establecer en un futuro más lejano si el desarrollador quiere tener más control sobre la fecha de disponibilidad real: 

    [![](uploading-images/image67.png "Establecimiento de la fecha de disponibilidad y el precio")](uploading-images/image67.png#lightbox)

4. Especifique la información de la aplicación, incluida la categoría de App Store a la que pertenece: 

    [![](uploading-images/image68.png "Especificación de la información de la aplicación")](uploading-images/image68.png#lightbox) 

    Seleccione las clasificaciones aplicables: 

    [![](uploading-images/image69.png "Establecimiento de las clasificaciones de la aplicación")](uploading-images/image69.png#lightbox) 

    Descripción, palabras clave y direcciones URL de contacto: 

    [![](uploading-images/image70.png "Edición de la descripción, las palabras clave y las direcciones URL de contacto")](uploading-images/image70.png#lightbox) 

    Información de contacto y consejos para los revisores de App Store: 

    [![](uploading-images/image71.png "Edición de la información de contacto y los consejos para los revisores de App Store")](uploading-images/image71.png#lightbox) 

    Por último, capturas de pantalla: 

    [![](uploading-images/image72.png "Agregar las capturas de pantalla necesarias")](uploading-images/image72.png#lightbox) 

    Las capturas de pantalla deben estar en formato JPG, TIF o PNG, con un tamaño en píxeles de 1280 x 800, 1440 x 900, 2880 x 1800 o 2560 x 1600. Pulse **Guardar** para finalizar.

5. Se mostrará la información de la aplicación para que la revise. Haga clic en **Ver detalles** para cambiar el estado: 

    [![](uploading-images/image73.png "Consulta de los detalles de la aplicación")](uploading-images/image73.png#lightbox)

6. En la vista de detalles, haga clic en Ready to Upload Binary (Listo para cargar binario) para enviar el archivo de paquete de aplicación: 

    [![](uploading-images/image74.png "Selección de Listo para cargar binario")](uploading-images/image74.png#lightbox)

7. Responda a la pregunta de criptografía: 

    [![](uploading-images/image75.png "Respuesta a la pregunta de criptografía")](uploading-images/image75.png#lightbox)

8. El sitio le indicará cuándo está listo para aceptar el archivo de paquete de aplicación: 

    [![](uploading-images/image76.png "Notificación de aceptación")](uploading-images/image76.png#lightbox)

9. Inicie el cargador de aplicaciones y asegúrese de que ha iniciado sesión con el id. de Apple.
Seleccione **Deliver Your App** (Entregar la aplicación) para continuar: 

    [![](uploading-images/image77.png "Interfaz del cargador de aplicaciones")](uploading-images/image77.png#lightbox)

10. Seleccione en la lista de aplicaciones en el estado **Ready to Upload Binary** (Listo para cargar binario) y haga clic en **Siguiente**: 

    [![](uploading-images/image78.png "Selección de la aplicación para cargar")](uploading-images/image78.png#lightbox)

11. Revise los metadatos de la aplicación y haga clic en **Elegir…** para buscar el archivo de paquete: 

    [![](uploading-images/image79.png "Revisión de los metadatos de la aplicación")](uploading-images/image79.png#lightbox)

12. Busque el archivo de paquete que compiló en Visual Studio para Mac con la configuración de compilación de App Store: 

    [![](uploading-images/image80.png "Selección del archivo para cargar")](uploading-images/image80.png#lightbox)

13. Pulse **Enviar**: 

    [![](uploading-images/image81.png "Envío de la aplicación")](uploading-images/image81.png#lightbox)

14. El paquete se validará y se notificarán los errores. Corrija estos errores y vuelva a cargarlo. Cuando la carga se complete correctamente, la aplicación se enviará automáticamente para que lo revise el equipo de App Store: 

    [![](uploading-images/image82.png "Ejemplo de errores de carga")](uploading-images/image82.png#lightbox)

Cuando la aplicación se apruebe, estará disponible para su descarga o compra en Mac App Store.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
