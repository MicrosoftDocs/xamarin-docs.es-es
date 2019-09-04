---
title: Solución de problemas de watchos
description: En este documento se describen los problemas conocidos y las soluciones alternativas para el desarrollo de watchos con Xamarin. Se describen las imágenes con problemas, la adición manual de archivos de controlador de interfaz, el inicio de una aplicación de inspección desde la línea de comandos, etc.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 1777bfe627d0dc579169c954f7799348befbf6b8
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227028"
---
# <a name="watchos-troubleshooting"></a>Solución de problemas de watchos

Esta página contiene información adicional y soluciones alternativas para los problemas que puede encontrar.

- [Problemas conocidos](#knownissues)

- [Quitar el canal alfa de las imágenes de iconos](#noalpha)

- [Agregar manualmente archivos de controlador de interfaz](#add) para Xcode Interface Builder.

- [Iniciar WatchApp desde la línea de comandos](#command_line).

<a name="knownissues" />

## <a name="known-issues"></a>Problemas conocidos

### <a name="general"></a>General

<a name="deploy" />

- Las versiones anteriores de Visual Studio para Mac muestran incorrectamente uno de los iconos de **AppleCompanionSettings** como 88x88 píxeles; Esto da como resultado un **error de icono que falta** si intenta enviar a la tienda de aplicaciones.
    Este icono debe ser 87x87 píxeles (29 unidades para **@3x** pantallas retinas). No se puede corregir en Visual Studio para Mac: edite el recurso de imagen en Xcode o edite manualmente el archivo **Contents. JSON** (para que coincida con [este ejemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

- Si **info. plist** del proyecto de extensión de inspección > identificador de lote de WKApp no está [configurado correctamente](~/ios/watchos/get-started/project-references.md) para coincidir con el **identificador de lote**de la aplicación de inspección, el depurador no podrá conectarse y Visual Studio para Mac esperará con el mensaje *"esperando a que el depurador conectar "* .

- La depuración se admite en el modo de notificaciones, pero puede ser poco confiable. A veces, el reintento funcionará. Confirme que el archivo **info. plist** `WKCompanionAppBundleIdentifier` de la aplicación de inspección está configurado para coincidir con el identificador de paquete de la aplicación de contenedor/primario de iOS (es decir, el que se ejecuta en el iPhone).

- iOS Designer no muestra las flechas de punto de entrada para los controladores de vista o de la interfaz de notificación.

- No se pueden agregar `WKNotificationControllers` dos a un guion gráfico.
    Solución: El `notificationCategory` elemento en el XML del guión gráfico siempre se inserta con `id`el mismo. Para evitar este problema, puede agregar dos (o más) controladores de notificaciones, abrir el archivo de guion gráfico en un editor de texto y `id` , a continuación, cambiar manualmente el elemento para que sea único.

    [![](troubleshooting-images/duplicate-id-sml.png "Abrir el archivo de guion gráfico en un editor de texto y cambiar manualmente el elemento ID para que sea único")](troubleshooting-images/duplicate-id.png#lightbox)

- Es posible que vea el error "no se ha compilado la aplicación" al intentar iniciar la aplicación. Esto ocurre después de una **limpieza** cuando el proyecto de inicio se establece en el proyecto de extensión de inspección.
    La solución consiste en seleccionar **Compilar > volver a generar todo** y, a continuación, volver a iniciar la aplicación.

### <a name="visual-studio"></a>Visual Studio

La compatibilidad con el diseñador de iOS para el kit de inspección *requiere* que la solución se configure correctamente. Si no se establecen las referencias del proyecto (vea [cómo establecer referencias](~/ios/watchos/get-started/project-references.md)), la superficie de diseño no funcionará correctamente.

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>Quitar el canal alfa de las imágenes de iconos

Los iconos no deben contener un canal alfa (el canal alfa define las áreas transparentes de una imagen); en caso contrario, se rechazará la aplicación durante el envío de la tienda de aplicaciones con un error similar al siguiente:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Es fácil quitar el canal alfa en Mac OS X mediante la aplicación de **vista previa** :

1. Abra la imagen de icono en la **vista previa** y, a continuación, elija **archivo > Exportar**.

2. El cuadro de diálogo que aparece incluirá una casilla **alfa** si hay un canal alfa.

    ![](troubleshooting-images/remove-alpha-sml.png "El cuadro de diálogo que aparece incluirá una casilla alfa si hay un canal alfa presente")

3. *Desmarque* la casilla **alfa** y **guarde** el archivo en la ubicación correcta.

4. La imagen del icono ahora debe pasar las comprobaciones de validación de Apple.


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>Agregar manualmente archivos de controlador de interfaz

> [!IMPORTANT]
> La compatibilidad con WatchKit de Xamarin incluye el diseño de guiones gráficos en el diseñador de iOS (tanto en Visual Studio para Mac como en Visual Studio), que no requiere los pasos descritos a continuación. Basta con dar un controlador de interfaz a un nombre de clase en el panel C# de propiedades de Visual Studio para Mac y los archivos de código se crearán automáticamente.


*Si* usa Xcode Interface Builder, siga estos pasos para crear nuevos controladores de interfaz para la aplicación de inspección y habilitar la sincronización con Xcode para que las salidas y las acciones estén disponibles C#en:

1. Abra la aplicación de inspección **interface. Storyboard** en **Xcode Interface Builder**.

    ![](troubleshooting-images/add-6.png "Abrir el guion gráfico en Xcode Interface Builder")

2. Arrastre un nuevo `InterfaceController` al guion gráfico:

    ![](troubleshooting-images/add-1.png "Un InterfaceController")

3. Ahora puede arrastrar controles al controlador de interfaz (por ejemplo, etiquetas y botones), pero no puede crear salidas ni acciones todavía, porque no hay ningún archivo de encabezado **. h** . Los pasos siguientes harán que se cree el archivo de encabezado **. h** necesario.

    ![](troubleshooting-images/add-2.png "Botón en el diseño")

4. Cierre el guión gráfico y vuelva a Visual Studio para Mac. Cree un nuevo C# archivo **MyInterfaceController.CS** (o cualquier nombre que desee) en el proyecto de **extensión de aplicación de inspección** (no en la propia aplicación de inspección donde se encuentra el guion gráfico). Agregue el código siguiente (actualizando el espacio de nombres, className y el nombre del constructor):

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. Cree otro nuevo C# archivo **MyInterfaceController.Designer.CS** en el proyecto de extensión de la **aplicación de inspección** y agregue el código siguiente. Asegúrese de actualizar el espacio de nombres, className y el `Register` atributo:

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > También puede hacer que este archivo sea un nodo secundario del primer archivo arrastrándolo al otro C# archivo en el Panel de solución de Visual Studio para Mac. A continuación, aparecerá de la siguiente manera:

    ![](troubleshooting-images/add-5.png "El panel de solución")

6. Seleccione compilar **> compilar todo** para que la sincronización de Xcode reconozca `Register` la nueva clase (a través del atributo) que usamos.

7. Vuelva a abrir el guion gráfico haciendo clic con el botón derecho en el archivo de guion gráfico de la aplicación de inspección y seleccionando **abrir con > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "Abrir el guion gráfico en Interface Builder")

8. Seleccione el nuevo controlador de interfaz y asígnele el className que definió anteriormente, por ejemplo,. `MyInterfaceController`
    Si todo ha funcionado correctamente, debe aparecer automáticamente en la lista desplegable **clase:** y puede seleccionarlo desde allí.

    ![](troubleshooting-images/add-4.png "Establecer una clase personalizada")

9. Elija la vista **Editor de asistentes** en Xcode (el icono con dos círculos superpuestos) para que pueda ver el guión gráfico y el código en paralelo:

    ![](troubleshooting-images/add-7.png "El elemento de la barra de herramientas del editor de asistentes")

    Cuando el foco esté en el panel de código, asegúrese de consultar el archivo de encabezado **. h** y, si no hace clic con el botón derecho en la barra de ruta de navegación y selecciona el archivo correcto (**MyInterfaceController. h**)

    ![](troubleshooting-images/add-8.png "Seleccionar MyInterfaceController")

10. Ahora puede crear salidas y acciones mediante **Ctrl + arrastre** desde el guión gráfico hasta el archivo de encabezado **. h** .

    ![](troubleshooting-images/add-9.png "Creación de salidas y acciones")

    Cuando suelte el arrastre, se le pedirá que seleccione si desea crear una salida o una acción y elegir su nombre:

    ![](troubleshooting-images/add-a.png "Cuadro de diálogo de salida y acción")

11. Una vez que se guardan los cambios de guion gráfico y se cierra Xcode, vuelva a Visual Studio para Mac. Detectará los cambios en el archivo de encabezado y agregará código automáticamente al archivo **. Designer.CS** :

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

Ahora puede hacer referencia al control (o implementar la acción) en C#.


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>Inicio de la aplicación de inspección desde la línea de comandos

> [!IMPORTANT]
> De forma predeterminada, puede iniciar la aplicación de inspección en el modo de aplicación normal y, además, en los modos de **vista** o de **notificación** mediante [parámetros de ejecución personalizados](~/ios/watchos/get-started/installation.md#custommodes) en Visual Studio para Mac y Visual Studio.


También puede usar la línea de comandos para controlar el simulador de iOS. La herramienta de línea de comandos que se usa para iniciar las aplicaciones de inspección es **Mtouch**.

Este es un ejemplo completo (se ejecuta como una sola línea en el terminal):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

El parámetro que necesita actualizar para reflejar la aplicación es `launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

La ruta de acceso completa al grupo de aplicaciones principal *de la aplicación de iOS que contiene la aplicación de inspección y la extensión*.

> [!NOTE]
> La ruta de acceso que necesita proporcionar es para el *archivo de aplicación de iPhone*, es decir, el que se implementará en el simulador de iOS y que contenga la aplicación de inspección y la de inspección.

Ejemplo:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>Modo de notificación

Para probar el modo de [ **notificación** ](~/ios/watchos/platform/notifications.md)de la aplicación, `watchlaunchmode` establezca el `Notification` parámetro en y proporcione una ruta de acceso a un archivo JSON que contenga una carga de notificación de prueba.

El parámetro payload es *necesario* para el modo de notificación.

Por ejemplo, agregue estos argumentos al comando Mtouch:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>Otros argumentos

Los argumentos restantes se explican a continuación:

### <a name="--sdkroot"></a>--sdkroot

Necesario. Especifica la ruta de acceso a Xcode (6,2 o posterior).

Ejemplo:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--dispositivo

Dispositivo simulador que se va a ejecutar. Esto se puede especificar de dos maneras, ya sea mediante el UDID de un dispositivo específico o mediante una combinación de tiempo de ejecución y tipo de dispositivo.

Los valores exactos varían entre las máquinas y se pueden consultar mediante la herramienta **simctl** de Apple:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

Ejemplo:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**Tiempo de ejecución y tipo de dispositivo**

Ejemplo:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
