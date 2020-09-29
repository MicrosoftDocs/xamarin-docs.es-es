---
title: 'tvOS en Xamarin: interno'
description: Documentos que describen el funcionamiento interno de tvOS en Xamarin, que se basa en Xamarin. iOS. El contenido de los vínculos describe los ensamblados, las plataformas de destino y los conceptos de iOS relacionados.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 850ddad9c1d83d15f5116bfb4efc58e42164c20d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434978"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS en Xamarin: interno 

## <a name="assemblies"></a>[Ensamblados](~/ios/tvos/internals/assemblies.md)

Lista de los ensamblados admitidos por Xamarin para las aplicaciones de Xamarin. tvOS.

## <a name="target-frameworks"></a>[Versiones de .NET Framework de destino](~/ios/tvos/internals/frameworks.md)

En este artículo se tratan los tipos de marcos de trabajo de destino (bibliotecas de clases base) que están disponibles en Xamarin. tvOS y las implicaciones de seleccionar un destino específico para la aplicación Xamarin. tvOS.

## <a name="related-ios-articles"></a>Artículos relacionados de iOS

Los artículos siguientes son específicos de iOS pero relevantes para tvOS (ya que tvOS 9 es un subconjunto de iOS 9).

### <a name="unified-api"></a>[Unified API](~/cross-platform/macios/unified/index.md)

Presenta las nuevas API unificadas que permiten el uso compartido de código más sencillo entre los códigos base de Apple TV e iOS, así como la presentación de compatibilidad con las API de 64 bits y la compilación de 64 bits.  

### <a name="api-design"></a>[Diseño de API](~/ios/internals/api-design/index.md)

Explica los principios de diseño que hay detrás del enlace de API.

### <a name="limitations"></a>[Limitaciones](~/ios/internals/limitations.md)

En esta sección se muestran los problemas y las limitaciones que se deben tener en cuenta en lo que respecta a Xamarin. iOS, muchos de los cuales se pueden aplicar a Xamarin. tvOS.

### <a name="linker"></a>[Enlazador](~/ios/deploy-test/linker.md)

Explica cómo funciona el enlazador para garantizar el paquete de aplicación más pequeño posible, y cómo modificar su configuración y uso.

### <a name="localization-and-internationalization"></a>[Internacionalización y localización](~/ios/app-fundamentals/localization/index.md)

En esta guía se incluye la adición de codificaciones a una aplicación de Xamarin. iOS para admitir la internacionalización.

### <a name="mtouch"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Notas e información sobre mtouch.exe, la herramienta de la línea de comandos que compila el proyecto en una aplicación que se puede usar con iOS.

### <a name="linking-native-libraries"></a>[Vinculación de bibliotecas nativas](~/ios/platform/native-interop.md)

Xamarin. iOS admite la vinculación con bibliotecas nativas de C y con las bibliotecas de Objective-C. En este documento se describe cómo vincular las bibliotecas nativas de C con el proyecto de Xamarin. iOS. Para obtener información sobre cómo hacer lo mismo con las bibliotecas de Objective-C, consulte el documento sobre &nbsp; [tipos de Objective-c de enlace](~/ios/platform/binding-objective-c/index.md) &nbsp; .

## <a name="objective-c-selectors"></a>[Selectores de Objective-C](~/ios/internals/objective-c-selectors.md)

Notas y uso para llamar directamente a los selectores de Objective-C (métodos).

### <a name="systemdata"></a>[System.Data](~/ios/data-cloud/system.data.md)

Información e instrucciones sobre el uso de System. Data para tener acceso al sistema de base de datos SQLite integrado.

### <a name="threading"></a>[Subprocesamiento](~/ios/app-fundamentals/threading.md)

Notas sobre el uso de subprocesos en aplicaciones de Xamarin. iOS.

### <a name="xib-code-generation"></a>[Generación de código XIB](~/ios/internals/xib-code-generation.md)

Cómo se integra Visual Studio para Mac con Interface Builder de Xcode para que pueda usar Interface Builder para diseñar la interfaz de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)