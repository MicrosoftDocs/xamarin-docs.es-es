---
title: Introducción a iOS
description: En este documento se describe cómo empezar a usar la inserción de .NET con iOS. Se describen los requisitos y se presenta una aplicación de ejemplo para mostrar cómo enlazar un ensamblado administrado y usar el resultado en un proyecto de Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5697d20077b746d9d33db985111c2d04908d5d01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029760"
---
# <a name="getting-started-with-ios"></a>Introducción a iOS

## <a name="requirements"></a>Requisitos

Además de los requisitos de nuestra guía de [Introducción a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) , también necesitará:

* [Xamarin. iOS 10,11](https://visualstudio.microsoft.com/xamarin/) o posterior

## <a name="hello-world"></a>Hola a todos

En primer lugar, cree un ejemplo Hola mundo C#sencillo en.

### <a name="create-c-sample"></a>Crear C# ejemplo

Abra Visual Studio para Mac, cree un nuevo proyecto de biblioteca de clases de iOS, asígnele el nombre **Hello-from-CSharp**y guárdelo en **~/Projects/Hello-from-CSharp**.

Reemplace el código del archivo **MyClass.CS** por el siguiente fragmento de código:

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Compile el proyecto y el ensamblado resultante se guardará como **~/Projects/Hello-from-CSharp/Hello-from-CSharp/bin/Debug/Hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Enlazar el ensamblado administrado

Una vez que tiene un ensamblado administrado, puede enlazarlo mediante la invocación de la inserción de .NET.

Tal como se describe en la guía de [instalación](~/tools/dotnet-embedding/get-started/install/install.md) , esto se puede hacer como paso posterior a la compilación en el proyecto, con un destino de MSBuild personalizado o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

El marco de trabajo se colocará en **~/Projects/Hello-from-CSharp/Output/Hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Uso de la salida generada en un proyecto de Xcode

Abra Xcode, cree una nueva aplicación de vista única de iOS, asígnele el nombre **Hello-from-CSharp**y seleccione el lenguaje **Objective-C** .

Abra el directorio **~/Projects/Hello-from-CSharp/Output** en Finder, seleccione **Hello-from-CSharp. Framework**, arrástrelo al proyecto de Xcode y colóquelo justo encima de la carpeta **Hello-from-CSharp** del proyecto.

![Marco de arrastrar y colocar](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Asegúrese de que la opción **copiar elementos si es necesario** está activada en el cuadro de diálogo que aparece y haga clic en **Finalizar**.

![Copiar elementos si es necesario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Seleccione el proyecto **Hello-from-CSharp** y navegue hasta la **pestaña General**del destino **Hello-from-CSharp** . En la sección **binario incrustado** , agregue **Hello-from-CSharp. Framework**.

![Archivos binarios incrustados](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra **ViewController. m**y reemplace el contenido por:

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

La inserción de .NET no admite actualmente Bitcode en iOS, que está habilitada para algunas plantillas de proyecto de Xcode. 

Deshabilítelo en la configuración del proyecto:

![Opción Bitcode](../../images/ios-bitcode-option.png)

Por último, ejecute el proyecto de Xcode y se mostrará algo parecido a lo siguiente:

![Hola desde C# el ejemplo que se ejecuta en el simulador](ios-images/hello-from-csharp-ios.png)
