---
ms.openlocfilehash: 31c8d1b781648f2d9c69062c52e71478fc7a496b
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "69529124"
---

La siguiente línea de comandos para especificar una versión de lanzamiento de la solución **SOLUTION_FILE. sln** para el iPhone. La ubicación del IPA se puede establecer especificando la `IpaPackageDir` propiedad en la línea de comandos:

- En el equipo Mac, mediante **xbuild**:

  ```
  xbuild /p:Configuration="Release" \ 
         /p:Platform="iPhone" \ 
         /p:IpaPackageDir="$HOME/Builds" \
         /t:Build MyProject.sln
  ```

El comando **xbuild** se encuentra normalmente en el directorio **/Library/Frameworks/mono.Framework/Commands**.

- En Windows, con **msbuild**:

  ```
  msbuild /p:Configuration="Release" 
          /p:Platform="iPhone" 
          /p:IpaPackageDir="%USERPROFILE%\Builds" 
          /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
          /t:Build MyProject.sln
  ```

**msbuild** no expandirá `$( )` automáticamente las expresiones pasadas en la línea de comandos. Por esta razón, se recomienda usar una ruta de acceso completa al establecer `IpaPackageDir` en la línea de comandos.

Consulte las [notas de la versión de iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) para obtener más `IpaPackageDir` información sobre la propiedad.
