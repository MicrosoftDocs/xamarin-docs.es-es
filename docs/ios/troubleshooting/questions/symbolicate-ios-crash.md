---
title: ¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 418a0196849099da03983085aca9ceed2077207b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030915"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?

Al compilar una aplicación de iOS con Visual Studio para Mac o Visual Studio 2017, el archivo. dSYM que se necesita para simbolizará informes de bloqueo se colocará en la misma jerarquía de directorios que el archivo de proyecto de la aplicación (. csproj). La ubicación exacta depende de la configuración de compilación del proyecto:

- Si ha habilitado las compilaciones específicas del dispositivo, el. dSYM se puede encontrar en el siguiente directorio:

    **&lt;directorio de proyecto&gt;/bin/&lt;Platform&gt;/&lt;Configuration&gt;/Device-Builds/&lt;Device&gt;-&lt;os-version&gt;/**

    Por ejemplo:
  
    **TestApp/bin/iPhone/Release/Device-compilaciones/iPhone 8.4-11.3.1/**

- Si no ha habilitado las compilaciones específicas del dispositivo, el. dSYM se puede encontrar en el siguiente directorio:

    **&lt;directorio de proyecto&gt;/bin/&lt;Platform&gt;/&lt;Configuration&gt;/**

    Por ejemplo:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Como parte del proceso de compilación, Visual Studio 2017 copia el archivo. dSYM del host de compilación de Mac en Windows. Si no ve un archivo. dSYM en Windows, asegúrese de que ha configurado la configuración de compilación de la aplicación para [crear un archivo. IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vea también

- [Symbolicating de archivos de bloqueo de iOS (Xamarin. iOS)](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmitificación de los registros de bloqueo de la aplicación iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
