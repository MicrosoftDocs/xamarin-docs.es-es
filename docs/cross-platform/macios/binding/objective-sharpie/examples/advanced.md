---
title: Ejemplo avanzado (manual) del mundo real
description: En este documento se describe cómo usar la salida de xcodebuild como entrada para el objetivo Sharpie, que proporciona información sobre lo que el objetivo Sharpie hace en el capó.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 2dea16633181d6b1120a5f9a90da685df66e5451
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "79031008"
---
# <a name="advanced-manual-real-world-example"></a>Ejemplo avanzado (manual) del mundo real

**En este ejemplo se usa la [biblioteca pop de Facebook](https://github.com/facebook/pop).**

En esta sección se describe un enfoque más avanzado para el enlace, en el que se usará la herramienta de `xcodebuild` de Apple para compilar primero el proyecto POP y luego deducir manualmente la entrada para el objetivo Sharpie. En esencia, esto abarca el objetivo que está haciendo Sharpie en la sección anterior.

```bash
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Dado que la biblioteca POP tiene un proyecto de Xcode (`pop.xcodeproj`), podemos usar `xcodebuild` para compilar POP. Este proceso puede a su vez generar archivos de encabezado que es posible que el objetivo Sharpie deba analizarse. Este es el motivo por el que es importante crear antes del enlace. Al compilar a través de `xcodebuild` Asegúrese de pasar el mismo identificador de SDK y la misma arquitectura que desea pasar al objetivo Sharpie (y recordar, el objetivo Sharpie 3,0 normalmente puede hacer esto automáticamente):

```bash
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1

=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===

...

CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP

...

** BUILD SUCCEEDED **
```

Habrá mucha información de compilación generada en la consola como parte de `xcodebuild`. Como se mostró anteriormente, podemos ver que se ejecutó un destino "CpHeader", donde los archivos de encabezado se copiaron en un directorio de salida de compilación. Este suele ser el caso y hace que el enlace sea más sencillo: como parte de la compilación de la biblioteca nativa, los archivos de encabezado se copian a menudo en una ubicación consumible "públicamente" que puede facilitar el análisis para el enlace. En este caso, sabemos que los archivos de encabezado de POP están en el directorio `build/Headers`.

Ahora estamos listos para enlazar el POP. Sabemos que queremos compilar para `iphoneos8.1` de SDK con la arquitectura de `arm64`, y que los archivos de encabezado que le interesan se encuentran en `build/Headers` en la desprotección de Git de POP. Si observamos el `build/Headers` directorio, veremos un número de archivos de encabezado:

```bash
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Si observamos `POP.h`, podemos ver que es el archivo de encabezado principal de nivel superior de la biblioteca que `#import`otros archivos. Por este motivo, solo necesitamos pasar `POP.h` al objetivo Sharpie y Clang hará el resto en segundo plano:

```bash
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Observará que se ha pasado un argumento `-scope build/Headers` a Objective Sharpie. Dado que las bibliotecas de C y Objective-C deben `#import` o `#include` otros archivos de encabezado que son detalles de implementación de la biblioteca y no de la API que se desea enlazar, el argumento `-scope` indica a Objective Sharpie que omita cualquier API que no esté definida en un archivo en algún lugar del directorio de `-scope`.

Encontrará que el argumento `-scope` suele ser opcional para las bibliotecas implementadas correctamente, pero no hay ningún daño en proporcionarlo explícitamente. 

> [!TIP]
> Si los encabezados de la biblioteca importan los encabezados del SDK de iOS, por ejemplo, `#import <Foundation.h>`, tendrá que establecer el ámbito; de lo contrario, el objetivo Sharpie generará definiciones de enlace para el encabezado del SDK de iOS que se importó, lo que produce un enlace enorme que probablemente generará errores al compilar el proyecto de enlace. 

Además, se ha especificado `-c -Ibuild/headers`. En primer lugar, el argumento `-c` indica a Objective Sharpie que deje de interpretar los argumentos de la línea de comandos y pase los argumentos subsiguientes _directamente al compilador Clang_. Por lo tanto, `-Ibuild/Headers` es un argumento del compilador Clang que indica a Clang que busque los includes en `build/Headers`, que es donde residen los encabezados de los POP. Sin este argumento, Clang no sabría dónde encontrar los archivos que `POP.h` es `#import`. _Casi todos los "problemas" con el uso de Sharpie objetivo se reducen a averiguar qué pasar a Clang_.

### <a name="completing-the-binding"></a>Completar el enlace

El objetivo Sharpie ha generado ahora `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs` archivos.

Se trata del primer paso básico del objetivo de Sharpie en el enlace, y en algunos casos podría ser todo lo que necesita. Como se indicó anteriormente, el desarrollador normalmente tendrá que modificar manualmente los archivos generados después de que Sharpie objetivo termine de [corregir los problemas](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que no haya podido controlar automáticamente la herramienta.

Una vez completadas las actualizaciones, estos dos archivos ahora se pueden agregar a un proyecto de enlace en Visual Studio para Mac o pasar directamente a las herramientas de `btouch` o `bmac` para generar el enlace final.

Para obtener una descripción detallada del proceso de enlace, consulte las [instrucciones del tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md).
