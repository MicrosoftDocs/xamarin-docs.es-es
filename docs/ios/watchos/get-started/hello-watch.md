---
title: 'Hola, watchos: tutorial'
description: En este documento se proporciona un tutorial sobre la creación de una aplicación de watchos sencilla con Xamarin. Describe cómo trabajar en Visual Studio y Visual Studio para Mac, trabajar con guiones gráficos y responder a eventos en el código.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/14/2016
ms.openlocfilehash: 89f3de944004404cdc1f4d192426a50f6746fe73
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928612"
---
# <a name="hello-watchos--walkthrough"></a>Hola, watchos: tutorial

Una vez que haya creado una solución que siga los pasos de [instalación e instalación](~/ios/watchos/get-started/installation.md), tendrá 3 proyectos:

- La aplicación primaria de iOS que se usa para la configuración u otras tareas administrativas en el dispositivo. (Con otros tipos de extensiones de iOS, a menudo se conoce como la aplicación "contenedor"). Con las aplicaciones de inspección, es posible que los usuarios empiecen a ejecutar la aplicación de **inspección sin ejecutar** la aplicación primaria.
- La extensión de inspección que contiene el código de programa de la aplicación de inspección. etc
- La aplicación de inspección, que contiene los recursos de imagen y guion gráfico que se representan en el reloj.

Compruebe que las [referencias son correctas](~/ios/watchos/get-started/project-references.md): que la aplicación primaria tiene una referencia a la aplicación Watch y que la aplicación Watch tiene una referencia a la extensión.

Confirme que los identificadores de lote siguen la \* Convención. watchkitextension \* . watchkitapp y que el archivo info. plist de la extensión tiene el valor de **identificador de paquete WKApp** establecido en el identificador de paquete de la aplicación de inspección.

Ahora debería poder ejecutar la aplicación de inspección, pero como el archivo de guion gráfico en la aplicación de inspección está en blanco, no podrá saber.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![El Explorador de soluciones](hello-watch-images/projectstructure.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![El Explorador de soluciones](hello-watch-images/vs-projectstructure.png)

-----

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Haga doble clic en interface. Storyboard en la aplicación de inspección para iniciar Xamarin iOS Designer (si está en un equipo Mac, también puede hacer clic con el botón derecho y **abrirlo con > Xcode Interface Builder** ).

1. Asegúrese de que los paneles **cuadro de herramientas** y **propiedades** estén visibles,
1. Haga clic para seleccionar el controlador de interfaz,
1. Establezca el identificador y el título del controlador de interfaz en **interfaceController** y **HI Watch** .
1. Compruebe que la **clase** está establecida en  **InterfaceController**

    ![Establecer el identificador y el título del controlador de interfaz en interfaceController y HI Watch](hello-watch-images/interfacecontrollerattributes.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Haga doble clic en interface. Storyboard en la aplicación de inspección para editarlo con Xamarin iOS Designer en Visual Studio:

1. Abra el panel Propiedades.
1. Cambie la clase a **InterfaceController** ;
1. Haga clic en el controlador de interfaz; etc
1. Establezca el identificador y el título del controlador de interfaz en **interfaceController** y **HI Watch** .

    ![Establecer el identificador y el título del controlador de interfaz en interfaceController y HI Watch](hello-watch-images/vs-interfacecontrollerattributes.png)

-----

Cree la interfaz de usuario:

1. En el panel del **cuadro de herramientas** ,
1. Arrastre y coloque un **botón** y una **etiqueta** en la escena.
1. Establezca el texto y los atributos de los controles, como se muestra a continuación:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Establezca el texto y los atributos de los controles como se muestra](hello-watch-images/draganddrop.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Establezca el texto y los atributos de los controles como se muestra](hello-watch-images/vs-draganddrop.png)

-----

1. Establezca el **nombre** en el panel de **propiedades** de cada control. En este ejemplo hemos usado `myButton` y `myLabel` .

1. Seleccione el botón del guión gráfico y vaya a la lista de **eventos** del panel de **propiedades** .

1. Cree una nueva **acción** escribiendo `OnButtonPress` y presionando **entrar** .
  La acción aparecerá en la lista y se creará automáticamente un método parcial en C#.

![La acción OnButtonPress agregada a un botón](hello-watch-images/buttonaction.png)

Después de guardar el guión gráfico, el **InterfaceController.Designer.CS** se actualiza con los nombres y las acciones del control. Si abre este archivo una vez que se ha actualizado, puede ver cómo `RegisterAttribute` corresponde al controlador y cómo se corresponden los controles de interfaz de usuario con las variables de instancia de C# marcadas con `OutletAttribute` y cómo se asignan las acciones a los métodos parciales etiquetados con `ActionAttribute` :

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Ahora Abra **InterfaceController.CS** ( *no* InterfaceController.Designer.CS) y agregue el código siguiente:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Este código debe ser bastante transparente: la variable de instancia `clickCount` se incrementa cada vez que `OnButtonPress` se llama a la función. El texto de `myLabel` se cambia para reflejar este recuento; por `myLabel` supuesto, es el nombre de una de las salidas creadas en Xcode. La `partial` función es la implementación de la función asociada al nombre de la acción especificada.

Si aún no es el proyecto de inicio,

1. Haga clic con el botón derecho en el proyecto de extensión de inspección y elija **establecer como proyecto de inicio** .

1. Establezca el destino de implementación en una imagen del simulador compatible con el kit de inspección (como iPhone 6 iOS 8,2).

1. Haga clic en el botón **depurar** para desencadenar una compilación y un inicio del simulador.

    [![Elementos de la interfaz de Visual Studio](hello-watch-images/readytodebug-sml.png)](hello-watch-images/readytodebug.png#lightbox)

Cuando se inicie el simulador, presione el botón para incrementar la etiqueta.
Enhorabuena, tiene una aplicación de Watch.

![La aplicación que se ejecuta en el simulador](hello-watch-images/running.png)

## <a name="related-links"></a>Vínculos relacionados

- [Instalación e instalación](~/ios/watchos/get-started/installation.md)
- [Vídeo de la primera aplicación de inspección](https://blog.xamarin.com/your-first-watch-kit-app/)
