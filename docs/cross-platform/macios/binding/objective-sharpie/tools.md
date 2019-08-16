---
title: Comando & de herramientas de Sharpie de objetivos
description: En este documento se proporciona información general sobre las herramientas incluidas con Objective Sharpie y los argumentos de línea de comandos que se usan con ellas.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: ddfe0f99991808214a6006c9504d267179adf2ab
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521859"
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

|Herramienta|DESCRIPCIÓN|
|--- |--- |
|**xcode**|Proporciona información sobre la instalación actual de Xcode y las versiones de los SDK de iOS y Mac que están disponibles. Esta información se utilizará más adelante cuando se generen los enlaces.|
|**pod**|Busca, configura, instala (en un directorio local) y enlaza las bibliotecas [CocoaPod](https://cocoapods.org/) de Objective-C disponibles en el repositorio de especificaciones maestras. Esta herramienta evalúa el CocoaPod instalado para deducir automáticamente la entrada correcta que se va a `bind` pasar a la herramienta siguiente. Novedad en 3,0|
|**bind**|Analiza los archivos de encabezado (`*.h`) de la biblioteca Objective-C en los archivos [ApiDefinition.CS y StructsAndEnums.CS](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) iniciales.|
|**update**|Comprueba si hay versiones más recientes de Objective Sharpie y descarga e inicia el instalador, si hay alguno disponible.|
|**verify-docs**|Muestra información detallada acerca `[Verify]` de los atributos.|
|**Documentación**|Navega a este documento en el explorador Web predeterminado.|

Para obtener ayuda sobre una herramienta específica de Sharpie de objetivos, escriba el nombre de la herramienta `-help` y la opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Antes de que podamos iniciar el proceso de enlace, necesitamos obtener información sobre nuestros SDK instalados actuales escribiendo el siguiente comando en el terminal `sharpie xcode -sdks`. El resultado puede ser diferente en función de las versiones de Xcode que tenga instaladas. Objective Sharpie busca SDK instalados en cualquier `Xcode*.app` en el `/Applications` directorio:

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

En el caso anterior, podemos ver que tenemos instalado el `iphoneos9.1` SDK en nuestro equipo y que tiene `arm64` compatibilidad con la arquitectura. Este valor se usará para todos los ejemplos de esta sección. Con esta información, estamos preparados para analizar los archivos de encabezado de la biblioteca Objective-C en el proyecto `ApiDefinition.cs` inicial `StructsAndEnums.cs` y en el proyecto de enlace.
