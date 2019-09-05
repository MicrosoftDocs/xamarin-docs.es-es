---
title: Uso de mtouch para agrupar aplicaciones Xamarin.iOS
description: En este documento se describe mtouch, una herramienta que controla muchos de los pasos necesarios para convertir una aplicación Xamarin.iOS en una agrupación, iniciarla en el simulador e implementarla en un dispositivo físico.
ms.prod: xamarin
ms.assetid: BCA491DA-E4C1-8689-3EC9-E4C72495A798
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: 437db399d1a8cafb19dbc57d7f2ea4141900d8db
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292358"
---
# <a name="using-mtouch-to-bundle-xamarinios-apps"></a>Uso de mtouch para agrupar aplicaciones Xamarin.iOS

Las aplicaciones de iPhone se incluyen como conjuntos de aplicaciones. Son directorios con la extensión `.app` que contienen código, datos, archivos de configuración y un manifiesto que iPhone usa para obtener información sobre la aplicación.

El proceso de convertir un ejecutable de .NET en una aplicación suele controlarse con el comando `mtouch`, una herramienta que integra muchos de los pasos necesarios para convertir la aplicación en un grupo. Esta herramienta también se utiliza para iniciar la aplicación en el simulador e implementar el software en un dispositivo iPhone o iPod Touch real.

## <a name="detailed-instructions"></a>Instrucciones detalladas

Consulte la página del manual [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) para saber todos los posibles usos de la herramienta mtouch.

## <a name="installation"></a>Instalación

En un equipo Mac, `mtouch` se integra con Xamarin.iOS. Se puede encontrar en el siguiente directorio:

**/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**

Para que `mtouch` resulte más cómodo de utilizar, agregue su directorio principal a la variable de entorno `PATH`.  

Por ejemplo, para hacer esto en Bash, agregue la siguiente línea al final del archivo **~/.bash_profile**:

```bash
export PATH=$PATH:/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin
```

> [!WARNING]
> Para usar `mtouch`, no se base en la existencia de **/Developer/MonoTouch/usr/bin**, un vínculo simbólico que apunta a **/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**. Dicho vínculo simbólico existe solo para mantener la compatibilidad con versiones de MonoTouch anteriores que no se instalaron en **/Library/Frameworks/...** y pueden desaparecer en una versión futura.

## <a name="building"></a>Compilación

El comando `mtouch` puede compilar el código de tres formas diferentes:

- Compilación para pruebas del simulador
- Compilación para la implementación de dispositivos
- Implementación del ejecutable en el dispositivo


### <a name="building-for-the-simulator"></a>Compilación para el simulador

Para empezar, el escenario más habitual es probar la aplicación en el simulador, por lo que usará `mtouch -sim` para compilar el código en un paquete del simulador. Esto se hace como sigue:

```bash
$ mtouch -sim Hello.app hello.exe
```

### <a name="building-for-the-device"></a>Compilación para dispositivos

Para compilar software para el dispositivo, compile la aplicación con la opción `mtouch -dev`; además, necesita proporcionar el nombre del certificado usado para firmar la aplicación. A continuación se muestra cómo se compila la aplicación para el dispositivo:

```bash
$ mtouch -dev -c "iPhone Developer: Miguel de Icaza" foo.exe
```

En este caso particular, usamos el certificado "Desarrollador de iPhone: Miguel de Icaza" para firmar la aplicación. Este paso es muy importante, ya que, sin él, el dispositivo físico se negará a cargar la aplicación.

 <a name="Running_your_Application" />


## <a name="running-your-application"></a>Ejecución de la aplicación


### <a name="launching-on-the-simulator"></a>Inicio en el simulador

El inicio en el simulador es muy sencillo una vez que disponga de un conjunto de aplicaciones:

```bash
$ mtouch --sdkroot /Applications/Xcode.app -launchsim Hello.app 
```

Si la marca `--sdkroot` no se define, el resultado predeterminado será la ruta de acceso xcode-select, con la siguiente advertencia:

> Por ejemplo, advertencia MT0061: Ningún Xcode.app especificado (uso de --sdkroot); uso de Xcode del sistema según se indica en "xcode-select --print-path": /Applications/Xcode.app/Contents/Developer 

La línea de comandos anterior generará un resultado como este:

```bash
Launching application
Application launched
PID: 98460
Press enter to terminate the application
```



Se recomienda encarecidamente que también mantenga un registro de la salida estándar y de los archivos de errores estándar para facilitar la depuración. La salida de `Console.WriteLine` es `stdout`, y la salida de `Console.Error.WriteLine` y cualquier otro mensaje de error en tiempo de ejecución es `stderr`.

Para ello, use las marcas `--stdout` y `--stderr`:

```bash
../../tools/mtouch/mtouch --launchsim=Hello.app --stdout=output --stderr=error
```

Si se produce un error en la aplicación, puede ver la salida y el error para diagnosticar el problema.


### <a name="deploying-to-a-device"></a>Implementar en un dispositivo

Para implementar en el dispositivo, necesita aprovisionar el dispositivo según se describe en el documento sobre [administración de dispositivos](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/00-About_the_iOS_Application_Development_Workflow/introduction.html) de Apple. Una vez aprovisionado correctamente el dispositivo, puede usar el comando mtouch para implementar una ".app" compilada en el dispositivo. Para ello, use este comando:

```bash
$ mtouch —sdkroot /Applications/Xcode.app -installdev=MyApp.app
```

Si la marca `--sdkroot` no se define, el resultado predeterminado será la ruta de acceso xcode-select, con la siguiente advertencia:

> Por ejemplo, advertencia MT0061: Ningún Xcode.app especificado (uso de --sdkroot); uso de Xcode del sistema según se indica en "xcode-select --print-path": /Applications/Xcode.app/Contents/Developer 

Estos pasos se realizan normalmente en Visual Studio para Mac.

## <a name="reference"></a>Referencia

Consulte la página del manual [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) para obtener información detallada sobre las otras opciones de la línea de comandos.



## <a name="related-links"></a>Vínculos relacionados

- [mtouch(1)](http://iosapi.xamarin.com/?link=man%3amtouch(1))
