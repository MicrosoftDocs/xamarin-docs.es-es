---
title: Iconos de documentos personalizados en Xamarin. iOS
description: En este artículo se describe cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se va a usar como un icono de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: 09fc582182729d3d8e17b85ac0a3ecc4bdcfce7e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939820"
---
# <a name="custom-document-icons-in-xamarinios"></a>Iconos de documentos personalizados en Xamarin. iOS

_En este artículo se describe cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se va a usar como un icono de tipo de documento personalizado._

Si una aplicación de Xamarin. iOS admite la carga de un tipo de documento concreto, el desarrollador puede proporcionar iconos que el sistema usará cuando encuentre el tipo de documento, como cuando un usuario mantiene presionados los datos adjuntos en la *aplicación de correo* como se muestra aquí:

 [![Ejemplo de iconos de tipo de documento](custom-document-types-images/17.png)](custom-document-types-images/17.png#lightbox)

El desarrollador puede agregar información de tipo de documento para un formato de archivo que la aplicación es capaz de abrir incluyendo entradas del diccionario para la `CFBundleTypeName` cadena y la `LSItemContentTypes` matriz en la de la aplicación `Info.plist` . Los iconos del tipo de documento van en la `CFBundleTypeIconFiles` matriz. Si no se proporciona un icono de documento, iOS derivará uno del icono de la aplicación.
Se pueden proporcionar iconos para varios tamaños, optimizados para las distintas resoluciones de dispositivo. 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para asignar estos valores en Visual Studio para Mac, use la sección **tipos de documento** en la pestaña **avanzadas** del `Info.plist` Editor para agregar el tipo de documento y asignarle iconos de imagen. Por ejemplo, esta es una captura de pantalla que muestra el registro para la compatibilidad con PDF:

 [![La sección tipos de documento de la pestaña Opciones avanzadas del editor de ' info. plist '](custom-document-types-images/18.png)](custom-document-types-images/18.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para asignar estos valores en Visual Studio, use la sección **tipos de documento** en la pestaña **Opciones avanzadas** de la `Info.plist` :

 ![Abra la sección tipos de documento en la pestaña Opciones avanzadas.](custom-document-types-images/doc01w.png)

Haga clic en el botón **Agregar tipo de documento** y rellene los campos obligatorios:

![Formulario agregar tipo de documento](custom-document-types-images/doc02w.png)

-----

Para obtener más información sobre los tipos de documento, consulte los temas de referencia de los [identificadores de tipos uniformes](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) de Apple y [programación de interacción de documentos para iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instrucciones de creación de iconos e imágenes personalizadas](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
