---
title: Crear objetos de interfaz de usuario en Xamarin. iOS
description: En este documento se proporciona información general sobre cómo crear una interfaz de usuario en Xamarin. iOS. Describe el diseñador de iOS, Xcode Interface Builder, C#y guiones gráficos.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: dd935e6feb14d9077c1ad98a2e7e10e67646dc4f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650119"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Crear objetos de interfaz de usuario en Xamarin. iOS

Apple agrupa los elementos de funcionalidad relacionados en "Marcos", que equivalen a los espacios de nombres de Xamarin. iOS. `UIKit`es el espacio de nombres que contiene todos los controles de interfaz de usuario para iOS.

Siempre que el código necesite hacer referencia a un control de la interfaz de usuario, como una etiqueta o un botón, Recuerde incluir la siguiente instrucción using:

```csharp
using UIKit;
```

Todos los controles descritos en este capítulo se encuentran en el espacio de nombres UIKit y cada nombre de clase `UI` de control de usuario tiene el prefijo.

Puede editar controles y diseños de interfaz de usuario de tres maneras:

-  **[Xamarin iOS Designer](~/ios/user-interface/designer/index.md)** : Use el diseñador de diseño integrado de Xamarin para diseñar pantallas. Haga doble clic en los archivos Storyboard o XIB para editarlos con el diseñador integrado.
-  **Interface Builder Xcode** : arrastre controles a los diseños de pantalla con Interface Builder. Abra el archivo Storyboard o XIB en Xcode; para ello, haga clic con el botón derecho en el archivo en el **Panel de solución** y elija **abrir con > Xcode Interface Builder**.
-  **Usar C#**  : los controles también se pueden construir mediante programación con código y agregarse a la jerarquía de vistas.

Se pueden agregar nuevos archivos de guion gráfico y XIB haciendo clic con el botón derecho en un proyecto de iOS y eligiendo **agregar > nuevo archivo..** ..

Sea cual sea el método que use, las propiedades de control y los eventos C# todavía se pueden manipular con en la lógica de la aplicación.

## <a name="using-xamarin-ios-designer"></a>Usar Xamarin iOS Designer

Para empezar a crear la interfaz de usuario en el diseñador de iOS, haga doble clic en un archivo de guion gráfico. Los controles se pueden arrastrar a la superficie de diseño desde el **cuadro de herramientas** , como se muestra a continuación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Cuadro de herramientas")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Cuadro de herramientas-Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Cuando se selecciona un control en la superficie de diseño, el **Panel de propiedades** mostrará los atributos de ese control. El **Widget > identidad > campo Nombre** , que se rellena en la siguiente captura de pantalla, se usa como nombre de la *toma* . Así es cómo se puede hacer referencia al control C#en:

 [![](creating-ui-objects-images/image3b.png "Panel de widgets de propiedades")](creating-ui-objects-images/image3b.png#lightbox)

Para profundizar en el uso del diseñador de iOS, consulte la guía de [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md) .

## <a name="using-xcode-interface-builder"></a>Usar Xcode Interface Builder

Si no está familiarizado con el uso de Interface Builder, consulte los documentos de [Interface Builder](https://developer.apple.com/xcode/interface-builder/) de Apple.

Para abrir un guion gráfico en Xcode, haga clic con el botón derecho para acceder al menú contextual del archivo de guion gráfico y elija Abrir con la **Interface Builder Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Menú contextual de guion gráfico: Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Menú contextual de guion gráfico: Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Los controles se pueden arrastrar hasta el Superficie de diseño desde la **biblioteca de objetos** que se muestra a continuación:

 [![](creating-ui-objects-images/image5a.png "Biblioteca de objetos de Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Al diseñar la interfaz de usuario con Interface Builder debe crear una **salida** para cada control al que desea hacer referencia C#. Para ello, active el editor del **Asistente** con el botón central **Editor** del botón de la barra de herramientas de Xcode:

 [![](creating-ui-objects-images/image6a.png "Botón del editor de asistentes")](creating-ui-objects-images/image6a.png#lightbox)

Haga clic en un objeto de interfaz de usuario. después, **arrastre el control** hasta el archivo. h. Para **controlar**la operación de arrastrar, mantenga presionada la tecla control y, después, haga clic y mantenga presionado el objeto de interfaz de usuario para el que está creando la salida (o acción). Mantenga presionada la tecla control mientras arrastra el archivo de encabezado. Termine de arrastrar por debajo `@interface` de la definición. Debe aparecer una línea azul con una salida de inserción de leyenda o una colección de tomas, tal como se muestra en la captura de pantalla siguiente.

Al liberar el clic se le pedirá que proporcione un nombre para la salida, que se usará para crear una C# propiedad a la que se pueda hacer referencia en el código:

 [![](creating-ui-objects-images/image8a.png "Creación de una salida")](creating-ui-objects-images/image8a.png#lightbox)

Para más información sobre cómo se integra el Interface Builder de Xcode con Visual Studio para Mac, consulte el documento de [generación de código Xib](~/ios/internals/xib-code-generation.md#generated) .

## <a name="using-c"></a>UtilizanC#

Si decide crear mediante programación un objeto de interfaz de usuario con C# (en un controlador de vista o vista, por ejemplo), siga estos pasos:

-  Declare un campo de nivel de clase para el objeto de interfaz de usuario. Cree el propio control una vez, `ViewDidLoad` en por ejemplo. A continuación, se puede hacer referencia al objeto a través de los métodos de ciclo de vida del controlador de vista (por ejemplo,
`ViewWillAppear`).
-  Cree un `CGRect` que defina el marco del control (sus coordenadas X e y en la pantalla, así como su ancho y alto). Deberá asegurarse de que tiene una `using CoreGraphics` Directiva para ello.
-  Llame al constructor para crear y asignar el control.
-  Establezca las propiedades o los controladores de eventos.
-  Llame `Add()` a para agregar el control a la jerarquía de vistas.

Este es un ejemplo sencillo de creación de `UILabel` un en un controlador de C#vista mediante:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>Usar C# los guiones gráficos y

Cuando se agregan controladores de vista al Superficie de diseño, se C# crean dos archivos correspondientes en el proyecto. En este ejemplo, `ControlsViewController.cs` y `ControlsViewController.designer.cs` se han creado automáticamente:

 [![](creating-ui-objects-images/image9b.png "Clase parcial ViewController")](creating-ui-objects-images/image9b.png#lightbox)

El `MainViewController.cs` archivo está pensado para *el código*. Aquí es donde se `View` implementan los métodos `ViewDidLoad` de `ViewWillAppear` ciclo de vida como y, donde puede agregar sus propias propiedades, campos y métodos.

`ControlsViewController.designer.cs` Se genera código que contiene una clase parcial. Al asignar un nombre a un control en la superficie de diseño en Visual Studio para Mac, o crear una salida o acción en Xcode, se agrega una propiedad correspondiente, o un método parcial, al archivo del diseñador (designer.cs). En el código siguiente se muestra un ejemplo de código generado para dos botones y una vista de texto, donde uno de los botones `TouchUpInside` también tiene un evento.

Estos elementos de la clase parcial permiten al código hacer referencia a los controles y responder a las acciones que se declaran en la superficie de diseño:

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

El `designer.cs` archivo no se debe editar manualmente: el IDE (Visual Studio para Mac o Visual Studio) es responsable de mantenerlo sincronizado con el guion gráfico.

Cuando los objetos de interfaz de usuario se agregan `View` mediante `ViewController`programación a un objeto o, se crea una instancia y se administran las referencias a los objetos y, por tanto, no se requiere ningún archivo del diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
