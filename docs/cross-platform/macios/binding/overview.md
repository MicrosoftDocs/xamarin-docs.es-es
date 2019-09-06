---
title: Información general de los enlaces de Objective-C
description: En este documento se proporciona información general sobre las distintas C# formas de crear enlaces para código de Objective-C, incluidos los enlaces de línea de comandos, los proyectos de enlace y Sharpie de objetivos. También se explica cómo funciona el enlace.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 3b0e5d12f47ffb46ad009530bcc9c0b373496f63
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279791"
---
# <a name="overview-of-objective-c-bindings"></a>Información general de los enlaces de Objective-C

_Detalles de cómo funciona el proceso de enlace_

El enlace de una biblioteca de Objective-C para su uso con Xamarin toma tres pasos:

1. Escriba una C# "definición de API" para describir cómo se expone la API nativa en .net y cómo se asigna a la base de Objective-C subyacente. Esto se hace mediante construcciones C# estándar como `interface` y varios **atributos** de enlace (vea este [ejemplo sencillo](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Una vez que haya escrito la "definición de la C#API" en, debe compilarla para generar un ensamblado "de enlace". Esto puede hacerse en la [**línea de comandos**](#commandline) o mediante un [**proyecto de enlace**](#bindingproject) en Visual Studio para Mac o Visual Studio.

3. A continuación, se agrega el ensamblado "enlace" al proyecto de aplicación de Xamarin, por lo que puede tener acceso a la funcionalidad nativa mediante la API que ha definido.
   El proyecto de enlace es completamente independiente de los proyectos de aplicación.

   > [!NOTE]
   > El paso 1 se puede automatizar con la ayuda del [**objetivo Sharpie**](#objectivesharpie). Examina la API de Objective-C y genera una "definición C# de API" propuesta. Puede personalizar los archivos creados por Objective Sharpie y usarlos en un proyecto de enlace (o en la línea de comandos) para crear el ensamblado de enlace. Objective Sharpie no crea enlaces por sí solo, es simplemente una parte opcional del proceso más grande.

También puede leer más detalles técnicos de [Cómo funciona](#howitworks), lo que le ayudará a escribir sus enlaces.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Enlaces de línea de comandos

Puede usar el `btouch-native` para Xamarin. iOS (o `bmac-native` si usa Xamarin. Mac) para compilar enlaces directamente. Funciona al pasar a la C# herramienta`btouch-native` `bmac-native` de línea de comandos las definiciones de la API que ha creado manualmente (o usando Objective Sharpie).


La sintaxis general para invocar estas herramientas es:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

El comando anterior generará el archivo `cocos2d.dll` en el directorio actual y contendrá la biblioteca totalmente enlazada que puede usar en el proyecto. Esta es la herramienta que Visual Studio para Mac usa para crear los enlaces si usa un proyecto de enlace (descrito [a continuación](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Proyecto de enlace

Un proyecto de enlace se puede crear en Visual Studio para Mac o Visual Studio (Visual Studio solo admite enlaces de iOS) y facilita la edición y compilación de definiciones de API para el enlace (en comparación con la línea de comandos).

Siga esta [Guía de introducción](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver cómo crear y usar un proyecto de enlace para producir un enlace.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Objective Sharpie es otra herramienta de línea de comandos independiente que ayuda con las fases iniciales de la creación de un enlace. No crea un enlace por sí mismo, sino que automatiza el paso inicial de la generación de una definición de API para la biblioteca nativa de destino.

Lea los [documentos de Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) para aprender a analizar bibliotecas nativas, marcos nativos y CocoaPods en definiciones de API que se pueden integrar en los enlaces.

<a name="howitworks" />

## <a name="how-binding-works"></a>Cómo funciona el enlace

Es posible usar el atributo [[Register]](xref:Foundation.RegisterAttribute) , el atributo [[Export]](xref:Foundation.ExportAttribute) y la [invocación manual del selector de Objective-c](~/ios/internals/objective-c-selectors.md) para enlazar manualmente los tipos de Objective-c nuevos (previamente desenlazados).

En primer lugar, busque un tipo que desee enlazar. A efectos de discusión (y simplicidad), enlazaremos el tipo [NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) (que ya se ha enlazado en [Foundation. NSEnumerator](xref:Foundation.NSEnumerator); la implementación siguiente es solo para fines de ejemplo).

En segundo lugar, es necesario crear C# el tipo. Lo más probable es que quiera colocarlo en un espacio de nombres; Dado que Objective-c no admite espacios de nombres, tendremos que usar el `[Register]` atributo para cambiar el nombre de tipo que Xamarin. iOS registrará con el tiempo de ejecución de Objective-C. El C# tipo también debe heredar de [Foundation. NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

En tercer lugar, revise la documentación de Objective-C y cree instancias de [ObjCRuntime. Selector](xref:ObjCRuntime.Selector) para cada selector que quiera usar. Colóquelos dentro del cuerpo de la clase:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

En cuarto lugar, el tipo deberá proporcionar constructores. *Debe* encadenar la invocación del constructor al constructor de la clase base. Los `[Export]` atributos permiten que el código de Objective-C llame a los constructores con el nombre de selector especificado:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

Quinto, proporcione métodos para cada uno de los selectores declarados en el paso 3. Estos usarán `objc_msgSend()` para invocar el selector en el objeto nativo. Tenga en cuenta el uso de [Runtime. GetNSObject ()](xref:ObjCRuntime.Runtime.GetNSObject*) para `IntPtr` convertir un en un tipo `NSObject` (sub) adecuado. Si desea que se pueda llamar al método desde el código de Objective-C, el miembro *debe* ser **virtual**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Reunir todo:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```
