---
title: Iconos de documentos personalizados en Xamarin. iOS
description: En este artículo se describe cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se va a usar como un icono de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: ac8ee96d6183f9a62233d217c75b03da15605bd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004222"
---
# <a name="custom-document-icons-in-xamarinios"></a>Iconos de documentos personalizados en Xamarin. iOS

_En este artículo se describe cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se va a usar como un icono de tipo de documento personalizado._

Si una aplicación de Xamarin. iOS admite la carga de un tipo de documento concreto, el desarrollador puede proporcionar iconos que el sistema usará cuando encuentre el tipo de documento, como cuando un usuario mantiene presionados los datos adjuntos en la *aplicación de correo* como se muestra aquí:

 [![](custom-document-types-images/17.png "An example of document type icons")](custom-document-types-images/17.png#lightbox)

El desarrollador puede agregar información de tipo de documento para un formato de archivo que la aplicación es capaz de abrir incluyendo entradas de diccionario para la cadena de `CFBundleTypeName` y la matriz de `LSItemContentTypes` en el `Info.plist`de la aplicación. Los iconos del tipo de documento van en la matriz de `CFBundleTypeIconFiles`. Si no se proporciona un icono de documento, iOS derivará uno del icono de la aplicación.
Se pueden proporcionar iconos para varios tamaños, optimizados para las distintas resoluciones de dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para asignar estos valores en Visual Studio para Mac, use la sección **tipos de documento** en la pestaña **Opciones avanzadas** del editor de `Info.plist` para agregar el tipo de documento y asignarle iconos de imagen. Por ejemplo, esta es una captura de pantalla que muestra el registro para la compatibilidad con PDF:

 [![](custom-document-types-images/18.png "The Document Types section under the Advanced tab on the `Info.plist` editor")](custom-document-types-images/18.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para asignar estos valores en Visual Studio, use la sección **tipos de documento** en la pestaña **Opciones avanzadas** de la `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Open the Document Types section under the Advanced tab")

Haga clic en el botón **Agregar tipo de documento** y rellene los campos obligatorios:

![](custom-document-types-images/doc02w.png "The Add Document Type form")

-----

Para obtener más información sobre los tipos de documento, consulte los temas de referencia de los [identificadores de tipos uniformes](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) de Apple y [programación de interacción de documentos para iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instrucciones de creación de iconos e imágenes personalizadas](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
