---
title: Comando & de herramientas de Sharpie de objetivos
description: En este documento se proporciona información general sobre las herramientas incluidas con Objective Sharpie y los argumentos de línea de comandos que se usan con ellas.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: davidortinau
ms.author: daortin
ms.date: 10/05/2015
ms.openlocfilehash: 2179154aa6dc78a8b0b6b418d780e7996f8e557d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015922"
---
# <a name="objective-sharpie-tools--commands"></a>Comando & de herramientas de Sharpie de objetivos

_Información general de las herramientas incluidas con Objective Sharpie y los argumentos de la línea de comandos para usarlas._

Una vez que el objetivo Sharpie esté [instalado](~/cross-platform/macios/binding/objective-sharpie/get-started.md)correctamente, abra un terminal y familiarícese con el objetivo de los *comandos* Sharpie tiene que ofrecer:

```
$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

Objective Sharpie proporciona las siguientes herramientas:

|Herramienta|Descripción|
|--- |--- |
|**Xcode**|Proporciona información sobre la instalación actual de Xcode y las versiones de los SDK de iOS y Mac que están disponibles. Esta información se utilizará más adelante cuando se generen los enlaces.|
|**caja**|Busca, configura, instala (en un directorio local) y enlaza las bibliotecas [CocoaPod](https://cocoapods.org/) de Objective-C disponibles en el repositorio de especificaciones maestras. Esta herramienta evalúa el CocoaPod instalado para deducir automáticamente la entrada correcta que se va a pasar a la `bind` herramienta siguiente. Novedad en 3,0|
|**bind**|Analiza los archivos de encabezado (`*.h`) de la biblioteca Objective-C en los archivos [ApiDefinition.CS y StructsAndEnums.CS](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) iniciales.|
|**update**|Comprueba si hay versiones más recientes de Objective Sharpie y descarga e inicia el instalador, si hay alguno disponible.|
|**Verify-docs**|Muestra información detallada acerca de los atributos de `[Verify]`.|
|**Documentación**|Navega a este documento en el explorador Web predeterminado.|

Para obtener ayuda sobre una herramienta específica de Sharpie de objetivos, escriba el nombre de la herramienta y la opción `-help`. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Antes de que podamos iniciar el proceso de enlace, necesitamos obtener información sobre nuestros SDK instalados actuales escribiendo el siguiente comando en la `sharpie xcode -sdks`de terminal. El resultado puede ser diferente en función de las versiones de Xcode que tenga instaladas. Objective Sharpie busca SDK instalados en cualquier `Xcode*.app` en el directorio `/Applications`:

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

En las versiones anteriores, podemos ver que tenemos el SDK de `iphoneos9.1` instalado en la máquina y que tiene `arm64` compatibilidad con la arquitectura. Este valor se usará para todos los ejemplos de esta sección. Con esta información, estamos preparados para analizar los archivos de encabezado de una biblioteca de Objective-C en los `ApiDefinition.cs` iniciales y `StructsAndEnums.cs` para el proyecto de enlace.
