---
title: Trabajar con la aplicación primaria watchos en Xamarin
description: En este documento se describe cómo trabajar con una aplicación primaria watchos en Xamarin. Se describen las extensiones de aplicación de watchos, las aplicaciones de iOS, el almacenamiento compartido y mucho más.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 49f2bdf63c286464073308cd1f17239692aa2395
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567338"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Trabajar con la aplicación primaria watchos en Xamarin

Hay diferentes maneras de comunicarse entre la aplicación de inspección y la aplicación iOS con la que está incluida:

- Las aplicaciones de inspección pueden [ejecutar código](#run-code) en la aplicación primaria en el iPhone.

- Las extensiones de inspección pueden [compartir una ubicación de almacenamiento](#shared-storage) con la aplicación principal de iPhone.

- Use la entrega para pasar datos de una notificación a la aplicación de inspección y enviar el usuario a un controlador de interfaz específico en la aplicación.

También se hace referencia a la aplicación primaria como la aplicación contenedora.

## <a name="run-code"></a>Ejecutar código

En estos dos ejemplos se muestra cómo usar `WCSession` para ejecutar código y enviar mensajes entre una aplicación de inspección y el iPhone emparejado:

- [Supervisión de la conectividad](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>Almacenamiento compartido

Si configura un [grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) , las extensiones de iOS 8 (incluidas las extensiones de inspección) pueden compartir datos con la aplicación primaria.

### <a name="nsuserdefaults"></a>NSUserDefaults

El siguiente código se puede escribir en la extensión de aplicación de inspección y en la aplicación de iPhone primaria para que puedan hacer referencia a un conjunto común de `NSUserDefaults` :

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

<a name="files"></a>

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

Nota: Si la ruta de acceso es `null` , Compruebe la [configuración del grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) para asegurarse de que los perfiles de aprovisionamiento se han configurado correctamente y se han descargado o instalado en el equipo de desarrollo.

Para obtener más información, consulte la documentación sobre las [funcionalidades del grupo de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de WKInterfaceController de Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Uso compartido de datos de Apple con la aplicación contenedora](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
