---
title: Solución de problemas de aplicaciones de tvOS compiladas con Xamarin
description: En este artículo se proporcionan varias sugerencias para ayudarle a solucionar problemas durante el desarrollo de una aplicación de tvOS compilada con Xamarin. Se describen los problemas conocidos y los errores específicos.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: f54edead87459282ccd6a44225269542fad9d0e4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769100"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Solución de problemas de aplicaciones de tvOS compiladas con Xamarin

_En este artículo se tratan los problemas conocidos que pueden surgir al trabajar con el soporte técnico de tvOS de Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemas conocidos

La versión actual de la compatibilidad con tvOS de Xamarin tiene los siguientes problemas conocidos:

- **Plataforma mono** : criptografía de mono 4,3. ProtectedData no puede descifrar los datos de mono 4,2. Como resultado, los paquetes Nuget no se restaurarán con el error `Data unprotection failed` cuando se configure un origen de Nuget protegido.
  - **Solución alternativa** : en Visual Studio para Mac tendrá que volver a agregar los orígenes de paquetes NuGet que usen la autenticación de contraseña antes de volver a intentar restaurar los paquetes.
- **Visual Studio para Mac con el F# complemento** : error al crear una F# plantilla de Android en Windows. Sigue funcionando correctamente en Mac.
- **Xamarin. Mac** : cuando se ejecuta el proyecto de plantilla unificada de Xamarin. Mac con la `Unsupported`plataforma de destino `Could not connect to the debugger` establecida en, puede aparecer la ventana emergente.
  - **Posible solución alternativa** : degradar la versión de plataforma mono disponible en nuestro canal estable.
- **Xamarin Visual Studio & Xamarin. iOS** : al implementar aplicaciones de WatchKit en Visual Studio, puede aparecer `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` el error.

Informe de los errores que encuentre en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Solución de problemas

En las secciones siguientes se enumeran algunos problemas conocidos que pueden producirse al usar tvOS 9 con Xamarin. tvOS y la solución a esos problemas:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Ejecutable no válido: el ejecutable no contiene Bitcode

Al intentar enviar una aplicación Xamarin. tvOS a la tienda de aplicaciones de Apple TV, es posible que reciba un mensaje de error con el formato _"ejecutable no válido: el ejecutable no contiene Bitcode"_ .

Para solucionar este problema, haga lo siguiente:

1. En Visual Studio para Mac, haga clic con el botón derecho en el archivo de proyecto de Xamarin. tvOS en el **Explorador de soluciones** y seleccione **Opciones**.
2. Seleccione **compilar tvOS** y asegúrese de que está en la configuración de **lanzamiento** : 

    [![](troubleshooting-images/ts01.png "Seleccionar las opciones de compilación de tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Agregue `--bitcode=asmonly` al campo **argumentos de Mtouch adicionales** y haga clic en el botón **Aceptar** .
4. Vuelva a compilar la aplicación en la configuración de **lanzamiento** .

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Comprobando que la aplicación tvOS contiene Bitcode

Para comprobar que la compilación de la aplicación Xamarin. tvOS contiene Bitcode, abra la aplicación terminal y escriba lo siguiente:

```csharp
otool -l /path/to/your/tv.app/tv
```

En la salida, busque lo siguiente:

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr`y `size` serán diferentes, pero otros campos deben ser idénticos.

Tendrá que asegurarse de que las bibliotecas estáticas (`.a`) de terceros que está usando se crearon con las bibliotecas de tvOS (no bibliotecas de iOS) y que también incluyen información de Bitcode.

En el caso de las `size` aplicaciones o bibliotecas que incluyen Bitcode válidos, será mayor que uno. Hay algunas situaciones en las que una biblioteca puede tener el marcador Bitcode, pero no contener Bitcode válidos. Por ejemplo:

**Bitcode no válido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode válido** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Observe que la diferencia `size` entre las dos bibliotecas en el ejemplo de la lista se ejecuta antes. La biblioteca debe generarse a partir de una compilación de archivo de Xcode con Bitcode `ENABLE_BITCODE`habilitado (configuración de Xcode) como solución para este problema de tamaño.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Las aplicaciones que solo contienen el segmento arm64 también deben tener "arm64" en la lista de UIRequiredDeviceCapabilities de info. plist.

Al enviar una aplicación a la tienda de aplicaciones de Apple TV para su publicación, es posible que reciba un error con el formato:

_"Las aplicaciones que solo contienen el segmento arm64 también deben tener" arm64 "en la lista de UIRequiredDeviceCapabilities de info. plist"_

Si esto ocurre, edite `Info.plist` el archivo y asegúrese de que tiene las claves siguientes:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

Vuelva a compilar la aplicación para su lanzamiento y vuelva a enviarla a iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Ejecución de la tarea "MTouch"--error

Si usa una biblioteca de terceros (por ejemplo, monogame) y se produce un error en la compilación de la versión con una larga serie `Task "MTouch" execution -- FAILED`de mensajes de `-gcc_flags="-framework OpenAL"` error que acaban en, pruebe a agregar a sus **argumentos táctiles adicionales**:

[![](troubleshooting-images/mtouch01.png "Ejecución de la tarea MTouch")](troubleshooting-images/mtouch01.png#lightbox)

También debe incluir `--bitcode=asmonly` en los **argumentos táctiles adicionales**, tener las opciones del vinculador establecidas para **vincular todo** y realizar una compilación limpia.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Error de ITMS-90471. Falta el icono grande

Si recibe un mensaje con el formato "ITMS-90471 error. Falta el icono grande "al intentar enviar una aplicación Xamarin. tvOS a la tienda de aplicaciones de Apple TV para su lanzamiento. Compruebe lo siguiente:

1. Asegúrese de que ha incluido los recursos de iconos grandes en `Assets.car` el archivo que creó mediante la documentación de iconos de la [aplicación](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) .
2. Asegúrese de que incluyó el `Assets.car` archivo de la documentación sobre [Cómo trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) en el paquete de aplicación final.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Agrupación no válida: una aplicación que admita controladores de juegos también debe admitir Apple TV Remote

o 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Paquete no válido: las aplicaciones de Apple TV con el marco GameController deben incluir la clave GCSupportedGameControllers en el archivo info. plist de la aplicación.

Los dispositivos de juego se pueden usar para mejorar el juego y proporcionar una sensación de inmersión en un juego. También se pueden usar para controlar la interfaz de Apple TV estándar, por lo que el usuario no tiene que cambiar entre el dispositivo remoto y el controlador.

Si va a enviar una aplicación de Xamarin. tvOS con compatibilidad con el dispositivo de juego al App Store de Apple TV y recibe un mensaje de error con el formato:

_Se han detectado uno o más problemas con la entrega reciente para "nombre de aplicación". La entrega se realizó correctamente, pero es posible que quiera corregir los siguientes problemas en la siguiente entrega:_

_Agrupación no válida: una aplicación que admita controladores de juego también debe ser compatible con Apple TV Remote._

o 

_Paquete no válido: las aplicaciones de Apple TV con el marco GameController deben incluir la clave GCSupportedGameControllers en info. plist de la aplicación._

La solución consiste en agregar compatibilidad con el Siri remoto (`GCMicroGamepad`) al archivo de `Info.plist` la aplicación. Apple ha agregado el perfil micro Game Controller para tener como destino el Siri remoto. Por ejemplo, incluya las claves siguientes:

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> Los dispositivos de juegos Bluetooth son una compra opcional que los usuarios finales pueden realizar, la aplicación no puede obligar al usuario a comprar uno. Si la aplicación es compatible con los dispositivos de juego, también debe admitir el Siri remoto para que todos los usuarios de Apple TV puedan usar el juego.

Para obtener más información, consulte la sección [Working with Game Controllers](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) de nuestra documentación de [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Versión de .NET Framework de destino incompatible:. NetPortable, versión = v 4.5, perfil = Profile78

Al intentar incluir una biblioteca de clases portable (PCL) en un proyecto de Xamarin. tvOS, es posible que reciba un mensaje en forma de:

_Versión de .NET Framework de destino incompatible:. NetPortable, versión = v 4.5, perfil = Profile78_

Para solucionar este problema, agregue un archivo XML denominado `Xamarin.TVOS.xml` con el siguiente contenido:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

En la siguiente ruta de acceso:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Tenga en cuenta que el número de Perfil de la ruta de acceso debe coincidir con el número de Perfil de la PCL.

Con este archivo en su lugar, debería poder agregar correctamente el archivo PCL al proyecto Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
