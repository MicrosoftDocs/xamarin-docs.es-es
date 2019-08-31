---
title: Referencia a bibliotecas nativas en Xamarin. iOS
description: En este documento se describe cómo vincular bibliotecas nativas de C a una aplicación de Xamarin. iOS. Describe cómo crear bibliotecas nativas universales y obtener acceso a métodos de C#C desde.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: eb49be3e52258864d61521bbe1dc388e9f6d1210
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198299"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Referencia a bibliotecas nativas en Xamarin. iOS

Xamarin. iOS admite la vinculación con bibliotecas nativas de C y con las bibliotecas de Objective-C. En este documento se describe cómo vincular las bibliotecas nativas de C con el proyecto de Xamarin. iOS. Para obtener información sobre cómo hacer lo mismo con las bibliotecas de Objective-C, consulte nuestro documento sobre [tipos de Objective-c de enlace](~/ios/platform/binding-objective-c/index.md) .

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Compilar bibliotecas nativas universales (i386, ARMv7 y ARM64)

A menudo es conveniente compilar las bibliotecas nativas para cada una de las plataformas admitidas para el desarrollo de iOS (i386 para el simulador y ARMv7/ARM64 para los propios dispositivos). Si ya tiene un proyecto de Xcode para la biblioteca, esto es muy fácil de hacer.

Para compilar la versión i386 de la biblioteca nativa, ejecute el siguiente comando desde un terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Esto producirá una biblioteca estática nativa en `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copie (o mueva) el archivo de almacenamiento de la biblioteca (libMyLibrary. a) a una versión segura para su uso posterior, asignándole un nombre único (como **libMyLibrary-i386. a**) para que no entre en conflicto con las versiones arm64 y ARMv7 de la misma biblioteca que va a compilar a continuación.

Para compilar la versión ARM64 de la biblioteca nativa, ejecute el siguiente comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Esta vez, la biblioteca nativa compilada se `MyProject.xcodeproj/build/Release-iphoneos/`encontrará en. Una vez más, copie (o mueva) este archivo en una ubicación segura y cambie su nombre a algo como **libMyLibrary-arm64. a** para que no entre en conflicto.

Ahora compile la versión ARMv7 de la biblioteca:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copie (o mueva) el archivo de biblioteca resultante en la misma ubicación en la que ha movido las otras dos versiones de la biblioteca y cambie su nombre a algo parecido a **libMyLibrary-ARMv7. a**.

Para crear un binario universal, puede usar la `lipo` herramienta de la siguiente manera:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Esto crea `libMyLibrary.a` que será una biblioteca universal (FAT) que será adecuada para su uso en todos los destinos de desarrollo de iOS.


### <a name="missing-required-architecture-i386"></a>Falta la arquitectura necesaria i386

Si va a recibir un `does not implement methodSignatureForSelector` mensaje `does not implement doesNotRecognizeSelector` o en la salida en tiempo de ejecución al intentar consumir una biblioteca de Objective-C en el simulador de iOS, es probable que la biblioteca no se compile para la arquitectura i386 (consulte la creación de un [nativo universal nativo). ](#building_native)Sección de bibliotecas anterior).

Para comprobar las arquitecturas admitidas por una biblioteca determinada, use el siguiente comando en el terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Donde `/full/path/to/` es la ruta de acceso completa a la biblioteca que `libraryname.a` se está consumiendo y es el nombre de la biblioteca en cuestión.

Si tiene el origen en la biblioteca, debe compilarlo y agruparlo también para la arquitectura i386, si quiere probar la aplicación en el simulador de iOS.

### <a name="linking-your-library"></a>Vinculación de la biblioteca

Cualquier biblioteca de terceros que consuma debe vincularse estáticamente a la aplicación. 

Si desea vincular estáticamente la biblioteca "libMyLibrary. a" que recibió de Internet o compilar con Xcode, deberá realizar algunas acciones:

- Colocar la biblioteca en el proyecto
- Configuración de Xamarin. iOS para vincular la biblioteca
- Obtenga acceso a los métodos de la biblioteca.


Para **colocar la biblioteca en el proyecto**, seleccione el proyecto en el explorador de soluciones y presione **comando + opción + a**. Vaya a libMyLibrary. a y agréguelo al proyecto. Cuando se le solicite, indique Visual Studio para Mac o Visual Studio para copiarlo en el proyecto. Después de agregarlo, busque libFoo. a en el proyecto, haga clic con el botón derecho en él y establezca la **acción de compilación** en **ninguno**.

Para **configurar Xamarin. iOS para vincular la biblioteca**, en las opciones de proyecto para el ejecutable final (no la propia biblioteca, pero el programa final) debe agregar el argumento adicional de la **compilación de iOS**(estas son parte de las opciones del proyecto) "-gcc_flags" opción seguida de una cadena entrecomillada que contiene todas las bibliotecas adicionales que se necesitan para el programa, por ejemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

En el ejemplo anterior se vinculará **libMyLibrary. a**

Puede usar `-gcc_flags` para especificar cualquier conjunto de argumentos de línea de comandos que se van a pasar al compilador gcc que se usa para realizar el vínculo final del archivo ejecutable. Por ejemplo, esta línea de comandos también hace referencia a CFNetwork Framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Si la biblioteca nativa contiene C++ código, también debe pasar la marca-CXX en los "argumentos adicionales" para que Xamarin. iOS sepa usar el compilador correcto. En C++ el caso de las opciones anteriores tendría el siguiente aspecto:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Obtener acceso a métodos de C desde C&#35;

Hay dos tipos de bibliotecas nativas disponibles en iOS:

- Bibliotecas compartidas que forman parte del sistema operativo.

- Bibliotecas estáticas que se distribuyen con la aplicación.


Para tener acceso a los métodos definidos en cualquiera de ellos, se usa la [funcionalidad P/Invoke de mono](https://www.mono-project.com/docs/advanced/pinvoke/) , que es la misma tecnología que usaría en .net, que es aproximadamente:

- Determinar la función de C que se desea invocar
- Determinar su firma
- Determinar en qué biblioteca reside
- Escribir la declaración P/Invoke adecuada

Al usar P/Invoke, debe especificar la ruta de acceso de la biblioteca con la que está vinculando. Al usar bibliotecas compartidas de iOS, puede codificar la ruta de acceso o puede usar las constantes de conveniencia que hemos definido en `Constants`nuestro, estas constantes deben cubrir las bibliotecas compartidas de iOS.

Por ejemplo, si desea invocar el método UIRectFrameUsingBlendMode de la biblioteca UIKit de Apple que tiene esta firma en C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La declaración P/Invoke tendría el siguiente aspecto:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Las constantes. UIKitLibrary es simplemente una constante definida como "/System/Library/Frameworks/UIKit.framework/UIKit", el punto de entrada nos permite especificar opcionalmente el nombre externo (UIRectFramUsingBlendMode) al tiempo que se expone un C#nombre diferente en, el RectFrameUsingBlendMode más corta.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Acceso a C Dylibs

Si tiene la necesidad de usar un dylib de C en la aplicación de Xamarin. iOS, hay un poco de configuración adicional que se requiere antes de llamar `DllImport` al atributo.

Por ejemplo, si tenemos un `Animal.dylib` con un `Animal_Version` método al que llamaremos en nuestra aplicación, necesitamos informar a Xamarin. iOS de la ubicación de la biblioteca antes de intentar consumirlo.

Para ello, edite el `Main.CS` archivo y haga que tenga un aspecto similar al siguiente:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Donde `/full/path/to/` es la ruta de acceso completa al dylib que se va a consumir. Con este código en su lugar, podemos vincular al método `Animal_Version` de la manera siguiente:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Dado que solo se pueden usar bibliotecas estáticas en iOS, no hay ninguna biblioteca compartida externa con la que vincular, por lo que el parámetro Path en el atributo DllImport `__Internal` debe usar el nombre especial (tenga en cuenta el doble carácter de subrayado al principio del nombre) en lugar de nombre de la ruta de acceso.

Esto obliga a DllImport a buscar el símbolo del método al que hace referencia en el programa actual, en lugar de intentar cargarlo desde una biblioteca compartida.

