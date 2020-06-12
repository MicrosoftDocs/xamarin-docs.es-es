---
title: Introducción a los enlaces de Objective-C
description: En este documento se proporciona información general sobre las distintas formas de crear enlaces de C# para código de Objective-C, incluidos los enlaces de línea de comandos, los proyectos de enlace y Objective Sharpie. También se explica cómo funciona el enlace.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: ca83f6ced2e9c2f5380d3bf760e00d613cb0acb0
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570978"
---
# <a name="overview-of-objective-c-bindings"></a>Introducción a los enlaces de Objective-C

_Detalles de cómo funciona el proceso de enlace_

El enlace de una biblioteca de Objective-C para su uso con Xamarin consta de tres pasos:

1. Escriba una "definición de API" de C# para describir cómo se expone la API nativa en .NET y cómo se asigna a la base de Objective-C subyacente. Para ello se emplean construcciones de C# estándar como `interface` y varios **atributos** de enlace (consulte este [ejemplo sencillo](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Cuando haya escrito la "definición de API" en C#, debe compilarla para generar un ensamblado "de enlace". Para ello se puede usar la [**línea de comandos**](#command-line-bindings) o un [**proyecto de enlace**](#bindingproject) de Visual Studio para Mac o Visual Studio.

3. Ese "enlace" se agrega entonces al proyecto de aplicación de Xamarin, por lo que puede acceder a la funcionalidad nativa mediante la API que ha definido.
   El proyecto de enlace es completamente independiente de los proyectos de aplicación.

   > [!NOTE]
   > El paso 1 se puede automatizar con la ayuda de [**Objective Sharpie**](#objectivesharpie). Esta utilidad examina la API de Objective-C y genera una "definición de API" de C# propuesta. Puede personalizar los archivos creados con Objective Sharpie y usarlos en un proyecto de enlace (o en la línea de comandos) para crear el ensamblado de enlace. Objective Sharpie no crea enlaces por sí solo, es simplemente una parte opcional del proceso más grande.

También puede leer más detalles técnicos de [cómo funciona](#howitworks), lo que le ayudará a escribir sus enlaces.

## <a name="command-line-bindings"></a>Enlaces de línea de comandos

Puede usar `btouch-native` para Xamarin.iOS (o `bmac-native` si usa Xamarin.Mac) para crear enlaces directamente. Lo que hace es pasar las definiciones de API de C# que ha creado a mano (o con Objective Sharpie) a la herramienta de línea de comandos (`btouch-native` para iOS o `bmac-native` para Mac).

La sintaxis general para invocar estas herramientas es la siguiente:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

El comando anterior generará el archivo `cocos2d.dll` en el directorio actual y contendrá la biblioteca completamente enlazada que puede usar en el proyecto. Esta es la herramienta que usa Visual Studio para Mac para crear los enlaces si se utiliza un proyecto de enlace (descrito [a continuación](#bindingproject)).

<a name="bindingproject"></a>

## <a name="binding-project"></a>Proyecto de enlace

Un proyecto de enlace se puede crear en Visual Studio para Mac o Visual Studio (Visual Studio solo admite enlaces de iOS) y facilita la edición y compilación de definiciones de API para el enlace (en comparación con la línea de comandos).

Siga esta [guía de introducción](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver cómo crear y usar un proyecto de enlace para producir un enlace.

<a name="objectivesharpie"></a>

## <a name="objective-sharpie"></a>Objective Sharpie

Objective Sharpie es otra herramienta de línea de comandos independiente que ayuda con las fases iniciales de creación de un enlace. No crea un enlace por sí misma, sino que automatiza el paso inicial de generar una definición de API para la biblioteca nativa de destino.

Lea los [documentos de Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) para aprender a analizar bibliotecas nativas, marcos nativos y CocoaPods en definiciones de API que se pueden crear en enlaces.

<a name="howitworks"></a>

## <a name="how-binding-works"></a>Funcionamiento del enlace

Es posible usar el atributo [[Register]](xref:Foundation.RegisterAttribute), el atributo [[Export]](xref:Foundation.ExportAttribute) y la [invocación manual del selector de Objective-C](~/ios/internals/objective-c-selectors.md) juntos para enlazar manualmente nuevos tipos (anteriormente desenlazados) de Objective-C.

En primer lugar, busque un tipo que quiera enlazar. Con fines explicativos (y por simplicidad), se va a enlazar el tipo [NSEnumerator](https://developer.apple.com/documentation/foundation/nsenumerator) (que ya se ha enlazado en [Foundation.NSEnumerator](xref:Foundation.NSEnumerator); la implementación siguiente sirve solo como ejemplo).

En segundo lugar, es necesario crear el tipo de C#. Lo más probable es que quiera colocarlo en un espacio de nombres; dado que Objective-C no admite espacios de nombres, tendremos que usar el atributo `[Register]` para cambiar el nombre del tipo que Xamarin.iOS registrará en el entorno de ejecución de Objective-C. El tipo de C# también debe heredarse de [Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

En tercer lugar, revise la documentación de Objective-C y cree instancias de [ObjCRuntime.Selector](xref:ObjCRuntime.Selector) para cada selector que quiera usar. Colóquelas dentro del cuerpo de la clase:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

En cuarto lugar, el tipo deberá proporcionar constructores. *Debe* encadenar la invocación del constructor al constructor de la clase base. Los atributos `[Export]` permiten que el código de Objective-C llame a los constructores con el nombre de selector especificado:

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

Quinto, proporcione métodos para cada uno de los selectores declarados en el paso 3. Estos usarán `objc_msgSend()` para invocar el selector en el objeto nativo. Tenga en cuenta el uso de [Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*) para convertir `IntPtr` en un (sub)tipo de `NSObject` con el tipo adecuado. Si quiere que se pueda llamar al método desde código de Objective-C, el miembro *debe* ser **virtual**.

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

En resumen:

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
