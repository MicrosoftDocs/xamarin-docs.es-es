---
title: Aspectos básicos de la aplicación Xamarin. iOS
description: Este documento contiene vínculos a varias guías que describen conceptos básicos para el desarrollo de Xamarin. iOS, como la seguridad del transporte de aplicaciones, el fondo, los eventos y el subprocesamiento.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/21/2017
ms.openlocfilehash: 59257dafc1d92756feb85046df43de7b9da0cc42
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290144"
---
# <a name="xamarinios-application-fundamentals"></a>Aspectos básicos de la aplicación Xamarin. iOS

En esta sección se proporciona una guía sobre algunas de las tareas más comunes o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Xamarin. iOS (anteriormente MonoTouch).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accesibilidad](~/ios/app-fundamentals/accessibility.md)

En este documento se describen varias API y herramientas que se pueden utilizar para ayudar a crear aplicaciones que son accesibles tanto como sea posible a todos los usuarios.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)

En este artículo se presentarán los cambios de seguridad que aplica la seguridad de transporte de aplicaciones en una aplicación de iOS 9 y esto significa que para los proyectos de Xamarin. iOS, se tratarán las opciones de configuración de ATS y se explicará cómo no participar en ATS, si es necesario. Dado que ATS está habilitado de forma predeterminada, todas las conexiones a Internet no seguras producirán una excepción en las aplicaciones de iOS 9 (a menos que lo permita explícitamente).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)

El procesamiento en segundo plano o el fondo es el proceso de permitir que las aplicaciones realicen tareas en segundo plano mientras otra aplicación se está ejecutando en primer plano. Esta guía sirve como introducción al procesamiento en segundo plano en iOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Crear aplicaciones de iOS en código](~/ios/app-fundamentals/ios-code-only.md)

En este artículo se examina cómo crear aplicaciones de iOS completamente en el código mediante Visual Studio y Visual Studio para Mac. Se muestra cómo empezar a partir de una plantilla de proyecto vacía para crear una pantalla de aplicación en un controlador mediante la creación de una jerarquía de vistas de UIKit. Luego se explica cómo crear vistas personalizadas que se pueden cargar en un controlador.

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[Serialización de excepciones](~/ios/platform/exception-marshaling.md)

Describe cómo se calculan las referencias de Objective-C y las excepciones administradas entre los marcos nativos y administrados.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)

En este artículo se presentan las tecnologías clave de iOS que se usan para recibir devoluciones de llamada y para rellenar los controles de interfaz de usuario con datos. Estas tecnologías son los eventos, los protocolos y los delegados; en C#este artículo se explica lo que es cada uno de estos y cómo se usa cada uno de ellos. Muestra cómo Xamarin. iOS usa controles iOS para exponer eventos conocidos de .NET, así como la forma en que Xamarin. iOS proporciona compatibilidad con conceptos de Objective-C, como protocolos y delegados (los delegados C# de Objective-c no se deben confundir con los delegados). En este artículo también se proporcionan ejemplos que muestran cómo se usan los protocolos como base para los delegados de Objective-C y en escenarios que no son de delegado.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md)

Xamarin. iOS puede usar las mismas clases System.IO para trabajar con archivos y directorios en iOS que se usarían en cualquier aplicación .NET. Sin embargo, a pesar de los conocidos métodos y clases, iOS implementa algunas restricciones en los archivos que se pueden crear o a los que se puede tener acceso, y también proporciona características especiales para determinados directorios. En este artículo se describen estas restricciones y características, y se muestra cómo funciona el acceso a archivos en una aplicación Xamarin. iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)

En este artículo se examina el uso de imágenes en Xamarin. iOS, tanto imágenes de soporte de aplicaciones (como iconos, carga de imágenes, etc.) e imágenes dentro de las aplicaciones (por ejemplo, imágenes aplicadas a controles). También se explica cómo usar Visual Studio para Mac para incorporar imágenes y cómo interactuar con imágenes desde el código.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localización](~/ios/app-fundamentals/localization/index.md)

En esta guía se incluye la adición de codificaciones a una aplicación de Xamarin. iOS para admitir la internacionalización.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabajar con listas de propiedades](~/ios/app-fundamentals/index.md)

En este documento se presenta el editor de la lista de propiedades gráficas y avanzadas (. plist) de Visual Studio para Mac para trabajar con info. plist y contitles. plist. Muestra la configuración de iconos e imágenes de inicio de la aplicación iOS, y muestra cómo se especifican las funcionalidades de la aplicación (derechos) desde dentro Visual Studio para Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabajar con seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md)

Apple ha realizado varias mejoras en la seguridad y la privacidad en iOS 10 (y versiones posteriores) que ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y a garantizar la privacidad del usuario final. En este artículo se trata la implementación de estas características en una aplicación Xamarin. iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Subprocesamiento](~/ios/app-fundamentals/threading.md)

En este artículo se describen los subprocesos en una aplicación de Xamarin. iOS y se habla un poco sobre el grupo de subprocesos de .NET, las aplicaciones con capacidad de respuesta y la recolección de elementos no utilizados.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/ios/app-fundamentals/touch/index.md)

Las pantallas táctiles en muchos de los dispositivos de hoy en día permiten a los usuarios interactuar de forma rápida y eficaz con los dispositivos de una manera natural e intuitiva. Esta interacción no se limita solo a la sencilla detección táctil: también es posible usar gestos. Por ejemplo, el gesto de alejar en zoom es un ejemplo muy común: al alejar una parte de la pantalla con dos dedos, el usuario puede acercar o alejar. En esta guía se examinan los gestos táctiles y de iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabajar con valores predeterminados del usuario](~/ios/app-fundamentals/user-defaults.md)

La `NSUserDefaults` clase proporciona una manera para que las aplicaciones y extensiones de iOS interactúen con el sistema predeterminado de todo el sistema mediante programación. Al usar el sistema de valores predeterminados, el usuario puede configurar el comportamiento o el estilo de una aplicación para que se adapte a sus preferencias (según el diseño de la aplicación). Por ejemplo, para presentar los datos en métricas frente a medidas de los Imperials o seleccionar un tema de la interfaz de usuario determinado.
