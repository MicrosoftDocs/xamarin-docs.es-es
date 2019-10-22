---
ms.openlocfilehash: 31c8d1b781648f2d9c69062c52e71478fc7a496b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529124"
---

La siguiente línea de comandos para especificar una versión de lanzamiento de la solución **SOLUTION_FILE. sln** para el iPhone. La ubicación del IPA se puede establecer especificando la propiedad `IpaPackageDir` en la línea de comandos:

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

**msbuild** no expandirá automáticamente `$( )` expresiones pasadas en la línea de comandos. Por esta razón, se recomienda usar una ruta de acceso completa al establecer el `IpaPackageDir` en la línea de comandos.

Consulte las [notas de la versión de iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) para obtener más información sobre la propiedad `IpaPackageDir`.
