---
title: Introducción a macOS
description: En este documento se describe cómo empezar a usar la inserción de .NET con macOS. Se describen los requisitos y se presenta una aplicación de ejemplo para mostrar cómo enlazar el ensamblado administrado y usar la salida generada en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: f0e2128bca5d2965395647353cd5a95a4030439f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725283"
---
# <a name="getting-started-with-macos"></a>Introducción a macOS

## <a name="what-you-will-need"></a>Qué necesitará

* Siga las instrucciones de la guía [Getting Started with Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) .

## <a name="hello-world"></a>Hello World

En primer lugar, cree un ejemplo Hola mundo C#sencillo en.

### <a name="create-c-sample"></a>Crear C# ejemplo

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases Mac denominado **Hello-from-CSharp**y guárdelo en **~/Projects/Hello-from-CSharp**.

Reemplace el código del archivo **MyClass.CS** por el siguiente fragmento de código:

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Compile el proyecto. El ensamblado resultante se guardará como **~/Projects/Hello-from-CSharp/Hello-from-CSharp/bin/Debug/Hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Enlazar el ensamblado administrado

Una vez que tiene un ensamblado administrado, puede enlazarlo mediante la invocación de la inserción de .NET.

Tal como se describe en la guía de [instalación](~/tools/dotnet-embedding/get-started/install/install.md) , esto se puede hacer como paso posterior a la compilación en el proyecto, con un destino de MSBuild personalizado o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/Hello-from-CSharp/Output/Hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Uso de la salida generada en un proyecto de Xcode

Abra Xcode y cree una nueva aplicación de coco. Asígnele el nombre **Hello-from-CSharp** y seleccione el lenguaje **Objective-C** .

Abra el directorio **~/Projects/Hello-from-CSharp/Output** en Finder, seleccione **Hello-from-CSharp. Framework**, arrástrelo al proyecto de Xcode y colóquelo justo encima de la carpeta **Hello-from-CSharp** del proyecto.

![Marco de arrastrar y colocar](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Asegúrese de que la opción **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **Finalizar**.

![Copiar elementos si es necesario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Seleccione el proyecto **Hello-from-CSharp** y navegue hasta la pestaña **General** del destino **Hello-from-CSharp** . En la sección **binario incrustado** , agregue **Hello-from-CSharp. Framework**.

![Archivos binarios incrustados](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Abra **ViewController. m**y reemplace el contenido por:

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Por último, ejecute el proyecto de Xcode y se mostrará algo parecido a lo siguiente:

![Hola desde C# el ejemplo que se ejecuta en el simulador](macos-images/hello-from-csharp-mac.png)
