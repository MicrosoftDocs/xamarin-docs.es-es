---
title: Migración de un enlace a Unified API
description: En este artículo se describen los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para que admita las API unificadas para las aplicaciones de Xamarin. IOS y Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: d545daa10f9f771cd1708adf32569a49f6c4e709
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453511"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migración de un enlace a Unified API

_En este artículo se describen los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para que admita las API unificadas para las aplicaciones de Xamarin. IOS y Xamarin. Mac._

## <a name="overview"></a>Información general

A partir del 1 de febrero de 2015, Apple requiere que todos los envíos a iTunes y a Mac App Store deban ser aplicaciones de 64 bits. Como resultado, cualquier nueva aplicación de Xamarin. iOS o Xamarin. Mac tendrá que usar el nuevo Unified API en lugar de las API clásicas de MonoTouch y MonoMac existentes para admitir 64 bits.

Además, cualquier proyecto de enlace de Xamarin también debe admitir las nuevas API unificadas que se incluirán en un proyecto de Xamarin. iOS o Xamarin. Mac de 64 bits. En este artículo se explican los pasos necesarios para actualizar un proyecto de enlace existente para usar el Unified API.

## <a name="requirements"></a>Requisitos

Para completar los pasos que se describen en este artículo, es necesario lo siguiente:

- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac instalada y configurada en el equipo de desarrollo.
- **Apple Mac** : se necesita un equipo Mac de Apple para compilar proyectos de enlace para iOS y Mac.

Los proyectos de enlace no se admiten en Visual Studio en un equipo Windows.

## <a name="modify-the-using-statements"></a>Modificar las instrucciones Using

Las API unificadas hacen que sea más fácil que nunca compartir código entre Mac e iOS, así como permitir la compatibilidad con aplicaciones 32 y 64 bits con el mismo binario. Al quitar los prefijos _MonoMac_ y _MonoTouch_ de los espacios de nombres, se consigue un uso compartido más sencillo en los proyectos de aplicación de Xamarin. Mac y Xamarin. iOS.

Como resultado, tendremos que modificar cualquiera de nuestros contratos de enlace (y otros `.cs` archivos del proyecto de enlace) para quitar los prefijos _MonoMac_ y _MonoTouch_ de nuestras `using` instrucciones.

Por ejemplo, dadas las siguientes instrucciones Using en un contrato de enlace:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Quitaremos el `MonoTouch` prefijo, lo que provocaría lo siguiente:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

De nuevo, tendremos que hacer esto para cualquier `.cs` archivo del proyecto de enlace. Con este cambio en su lugar, el siguiente paso es actualizar nuestro proyecto de enlace para usar los nuevos tipos de datos nativos.

Para obtener más información sobre el Unified API, consulte la documentación de [Unified API](~/cross-platform/macios/unified/index.md) . Para obtener más información acerca de la compatibilidad con aplicaciones de 32 y 64 bits e información sobre los marcos de trabajo, consulte la documentación de consideraciones sobre la [plataforma de bits 32 y 64](~/cross-platform/macios/32-and-64/index.md) .

## <a name="update-to-native-data-types"></a>Actualizar tipos de datos nativos

Objective-C asigna el `NSInteger` tipo de datos a `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits. Para que coincida con este comportamiento, el nuevo Unified API reemplaza los usos anteriores de `int` (que en .net se definen como siempre `System.Int32` ) a un nuevo tipo de datos: `System.nint` .

Junto con el nuevo `nint` tipo de datos, el Unified API introduce `nuint` los `nfloat` tipos y, para asignar también a los `NSUInteger` `CGFloat` tipos y.

Dado lo anterior, necesitamos revisar nuestra API y asegurarse de que todas las instancias de `NSInteger` , `NSUInteger` y `CGFloat` que anteriormente se asignaron a `int` , `uint` y se `float` actualizan a los nuevos `nint` `nuint` `nfloat` tipos y.

Por ejemplo, dada una definición de método de Objective-C de:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Si el contrato de enlace anterior tenía la siguiente definición:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Actualizaremos el nuevo enlace para que sea:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

Si se está asignando a una biblioteca de terceros de la versión más reciente que la que se había vinculado inicialmente a, es necesario revisar los `.h` archivos de encabezado de la biblioteca y ver si se está saliendo, las llamadas explícitas a `int` , `int32_t` , o se han `unsigned int` `uint32_t` `float` actualizado para que sean `NSInteger` , `NSUInteger` o `CGFloat` . Si es así, `nint` `nuint` `nfloat` también se deben realizar las mismas modificaciones en los tipos, y.

Para obtener más información sobre estos cambios de tipos de datos, vea el documento [tipos nativos](~/cross-platform/macios/nativetypes.md) .

## <a name="update-the-coregraphics-types"></a>Actualización de los tipos de CoreGraphics

Los tipos de datos de punto, tamaño y rectángulo que se usan con `CoreGraphics` el uso de los bits 32 o 64, en función del dispositivo en el que se ejecutan. Cuando Xamarin enlazaba originalmente las API de iOS y Mac, usamos estructuras de datos existentes que ocurrieron para coincidir con los tipos de datos de `System.Drawing` ( `RectangleF` por ejemplo,).

Debido a los requisitos para admitir 64 bits y los nuevos tipos de datos nativos, es necesario realizar los siguientes ajustes en el código existente cuando se llama a `CoreGraphic` los métodos:

- **CGRect** : Úselo `CGRect` en lugar de `RectangleF` al definir regiones rectangulares de punto flotante.
- **CGSize** : use `CGSize` en lugar de `SizeF` cuando defina tamaños de punto flotante (ancho y alto).
- **CGPoint** : se usa `CGPoint` en lugar de `PointF` cuando se define una ubicación de punto flotante (coordenadas X e y).

Dado lo anterior, tendremos que revisar nuestra API y asegurarnos de que cualquier instancia de `CGRect` , `CGSize` o a `CGPoint` la que se haya enlazado anteriormente, o que se haya enlazado `RectangleF` `SizeF` `PointF` al tipo nativo `CGRect` `CGSize` o `CGPoint` directamente.

Por ejemplo, dado un inicializador de Objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Si el enlace anterior incluía el código siguiente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Actualizaremos el código para:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Una vez realizados todos los cambios de código, es necesario modificar nuestro proyecto de enlace o crear el archivo para enlazarlo con las API unificadas.

## <a name="modify-the-binding-project"></a>Modificar el proyecto de enlace

Como último paso para actualizar nuestro proyecto de enlace para usar las API unificadas, es necesario modificar el `MakeFile` que usamos para compilar el proyecto o el tipo de proyecto de Xamarin (si se enlaza desde dentro de Visual Studio para Mac) e indicar a _btouch_ que enlace con las API unificadas en lugar de las clásicas.

### <a name="updating-a-makefile"></a>Actualizar un archivo make

Si usamos un archivo make para compilar el proyecto de enlace en Xamarin. DLL, tendremos que incluir la opción de `--new-style` línea de comandos y llamar a `btouch-native` en lugar de `btouch` .

Como se indica a continuación `MakeFile` :

<!--markdownlint-disable MD010 -->
```makefile
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch

all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

Tenemos que cambiar de llamada `btouch` a `btouch-native` , por lo que ajustaremos la definición de macro de la siguiente manera:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Actualizaremos la llamada a `btouch` y agregaremos la `--new-style` opción de la siguiente manera:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

Ahora podemos ejecutar nuestro `MakeFile` como normal para compilar la nueva versión de 64 bits de la API.

### <a name="updating-a-binding-project-type"></a>Actualizar un tipo de proyecto de enlace

Si usamos una plantilla de proyecto de enlace de Visual Studio para Mac para compilar nuestra API, deberá actualizarla a la nueva versión de Unified API de la plantilla de proyecto de enlace. La forma más fácil de hacerlo es iniciar un nuevo proyecto de enlace de Unified API y copiar en todo el código y la configuración existentes.

Siga estos pasos:

1. Inicie Visual Studio para Mac:
2. Seleccionar **archivo**  >  **nueva**  >  **solución..** .
3. En el cuadro de diálogo Nueva solución, seleccione **iOS**  >  **Unified API**  >  **proyecto de enlace de iOS**: 

    [![En el cuadro de diálogo Nueva solución, seleccione iOS/Unified API/proyecto de enlace de iOS.](update-binding-images/image01new.png)](update-binding-images/image01new.png#lightbox)
4. En el cuadro de diálogo ' configurar el nuevo proyecto ', escriba un **nombre** para el nuevo proyecto de enlace y haga clic en el botón **Aceptar** .
5. Incluya la versión de 64 bits de la biblioteca de Objective-C para la que va a crear enlaces.
6. Copie el código fuente del proyecto existente de 32 bits Classic API Binding (como los `ApiDefinition.cs` `StructsAndEnums.cs` archivos y).
7. Realice los cambios anteriores indicados en los archivos de código fuente.

Una vez realizados todos estos cambios, puede compilar la nueva versión de 64 bits de la API como lo haría con la versión de 32 bits.

## <a name="summary"></a>Resumen

En este artículo hemos mostrado los cambios que deben realizarse en un proyecto de enlace de Xamarin existente para admitir las nuevas API unificadas y los dispositivos de 64 bits y los pasos necesarios para compilar la nueva versión compatible de 64 bits de una API.

## <a name="related-links"></a>Vínculos relacionados

- [Mac e iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [Consideraciones de la plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Actualización de aplicaciones iOS existentes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](/samples/xamarin/ios-samples/bindingsample/)