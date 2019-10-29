---
title: Trabajar con la aplicación primaria watchos en Xamarin
description: En este documento se describe cómo trabajar con una aplicación primaria watchos en Xamarin. Se describen las extensiones de aplicaciones de WatchKit, las aplicaciones de iOS, el almacenamiento compartido y mucho más.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001476"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabajar con la aplicación primaria watchos en Xamarin

> [!IMPORTANT]
> El acceso a la aplicación principal con los ejemplos siguientes solo funciona en aplicaciones de inspección de watchos 1.

Hay diferentes maneras de comunicarse entre la aplicación de inspección y la aplicación iOS con la que está incluida:

- Las extensiones de inspección pueden [llamar a un método](#code) en la aplicación primaria que se ejecuta en segundo plano en el iPhone.

- Las extensiones de inspección pueden [compartir una ubicación de almacenamiento](#storage) con la aplicación principal de iPhone.

- Usar la entrega para pasar datos de un vistazo o una notificación a la aplicación de inspección, enviando el usuario a un controlador de interfaz específico en la aplicación.

También se hace referencia a la aplicación primaria como la aplicación contenedora.

<a name="code" />

## <a name="run-code"></a>Ejecutar código

La comunicación entre una extensión de inspección y la aplicación de iPhone primaria se muestra en el [ejemplo GpsWatch](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
La extensión de inspección puede solicitar a la aplicación de iOS primaria que realice algún procesamiento en su nombre mediante el método de `OpenParentApplication`.

Esto es especialmente útil para las tareas de larga ejecución (incluidas las solicitudes de red): solo la aplicación de iOS primaria puede aprovechar el procesamiento en segundo plano para completar estas tareas y guardar los datos recuperados en una ubicación accesible para la extensión de inspección.

### <a name="watch-kit-app-extension"></a>Extensión de aplicación del kit de inspección

Llame al `WKInterfaceController.OpenParentApplication` en la extensión de la aplicación de inspección. Devuelve un `bool` que indica si la solicitud del método se envió correctamente o no. Compruebe el parámetro `error` en el `Action` de respuesta para determinar si se ha producido algún error durante el método que se ejecuta en la aplicación de iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```

### <a name="ios-app"></a>Aplicación iOS

Todas las llamadas de una extensión de aplicación de inspección se enrutan a través del método de `HandleWatchKitExtensionRequest` de la aplicación de iPhone.
Si va a realizar diferentes solicitudes en la aplicación de inspección, este método tendrá que consultar el Diccionario de `userInfo` para determinar cómo procesar la solicitud.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```

<a name="storage" />

## <a name="shared-storage"></a>Almacenamiento compartido

Si configura un [grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) , las extensiones de iOS 8 (incluidas las extensiones de inspección) pueden compartir datos con la aplicación primaria.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

El siguiente código se puede escribir en la extensión de la aplicación de inspección y en la aplicación de iPhone primaria para que puedan hacer referencia a un conjunto común de `NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>Archivos

La extensión de inspección y aplicación de iOS también puede compartir archivos mediante una ruta de acceso de archivo común.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Nota: Si la ruta de acceso es `null` Compruebe la [configuración del grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) para asegurarse de que los perfiles de aprovisionamiento se han configurado correctamente y se han descargado o instalado en el equipo de desarrollo.

Para obtener más información, consulte la documentación sobre las [funcionalidades del grupo de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="wormholesharp"></a>WormHoleSharp

Un conocido mecanismo de código abierto para watchos 1 (basado en [MMWormHole](https://github.com/mutualmobile/MMWormhole)) para pasar datos o comandos entre la aplicación primaria y la aplicación de inspección.

Puede configurar el túnel espacial mediante un grupo de aplicaciones como este en la aplicación iOS y la extensión de inspección:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Descargue la C# versión [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).

## <a name="related-links"></a>Vínculos relacionados

- [GpsWatch (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (ejemplo)](https://github.com/Clancey/WormHoleSharp)
- [Referencia de WKInterfaceController de Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Uso compartido de datos de Apple con la aplicación contenedora](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
