---
title: ¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: edd5a2c1ed2efdffc9bd28bb06ac19348615eefc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292109"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?

Al compilar una aplicación de iOS con Visual Studio para Mac o Visual Studio 2017, el archivo. dSYM que se necesita para simbolizará informes de bloqueo se colocará en la misma jerarquía de directorios que el archivo de proyecto de la aplicación (. csproj). La ubicación exacta depende de la configuración de compilación del proyecto:

- Si ha habilitado las compilaciones específicas del dispositivo, el. dSYM se puede encontrar en el siguiente directorio:

    **&lt;&gt;directorio de proyecto&lt;/bin/&gt;configuracióndeplataforma&lt; /Device-Builds/dispositivo&gt; &lt;/&gt;&lt;- versión de SO&gt;/**

    Por ejemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Si no ha habilitado las compilaciones específicas del dispositivo, el. dSYM se puede encontrar en el siguiente directorio:

    **&lt;configuración de&gt;la&lt;plataformade/bin/&gt;dedirectoriodeproyecto/&lt;&gt;/**

    Por ejemplo:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Como parte del proceso de compilación, Visual Studio 2017 copia el archivo. dSYM del host de compilación de Mac en Windows. Si no ve un archivo. dSYM en Windows, asegúrese de que ha configurado la configuración de compilación de la aplicación para [crear un archivo. IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vea también

- [Symbolicating de archivos de bloqueo de iOS (Xamarin. iOS)](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmitificación de los registros de bloqueo de la aplicación iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

