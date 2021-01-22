---
title: Trabajar con la configuración de watchos en Xamarin
description: En este documento se describe cómo trabajar con la configuración de watchos en Xamarin. En él se explica cómo agregar la configuración a una solución de la aplicación de inspección, con la configuración de la aplicación y la aplicación Apple Watch en el iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7a3839cb7c747879f1549fb8f6715fded62d45bb
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697727"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Trabajar con la configuración de watchos en Xamarin

Apple Watch aplicaciones pueden usar la misma funcionalidad de configuración que las aplicaciones de iOS: la interfaz de usuario de la configuración se muestra en la aplicación **Apple Watch** iPhone, pero los valores son accesibles tanto en la aplicación de iPhone como en la extensión de inspección.

![Apple Watch aplicaciones pueden usar la misma funcionalidad de configuración que las aplicaciones iOS](settings-images/intro.png)

La configuración se almacenará en una ubicación de archivos compartidos que sea accesible tanto para la aplicación iOS como para la extensión de la aplicación Watch, definida por un **grupo de aplicaciones**. Debe [configurar un grupo de aplicaciones](~/ios/watchos/app-fundamentals/app-groups.md) antes de agregar la configuración siguiendo las instrucciones que se indican a continuación.

## <a name="add-settings-in-a-watch-solution"></a>Agregar configuración en una solución de inspección

En la **aplicación de iPhone** de la solución (*no* la aplicación de inspección o la extensión):

1. Haga clic con el botón derecho en **agregar > nuevo archivo...** y elija **Settings. bundle** (no puede editar el nombre en el cuadro de diálogo **nuevo archivo** ):

   [![Agregar un nuevo paquete de configuración](settings-images/settings-add-sml.png)](settings-images/settings-add.png#lightbox)

2. Cambie el nombre a **Settings-Watch. bundle** (seleccione y escriba **comando + R** para cambiar el nombre):

   ![Cambiar el nombre de la agrupación](settings-images/settings-rename.png)

3. Agregue una nueva clave `ApplicationGroupContainerIdentifier` al archivo **root. plist** con el valor establecido en el grupo de aplicaciones que ha configurado (por ejemplo, `group.com.xamarin.WatchSettings` en el ejemplo):

   [![Agregar una clave de ApplicationGroupContainerIdentifier al archivo root. plist](settings-images/settings-appgroup-sml.png)](settings-images/settings-appgroup.png#lightbox)

4. Edite **Settings-Watch. bundle/root. plist** para que contenga las opciones que quiere usar; el archivo de plantilla contiene un grupo.
  TextField, alternar modificador y control deslizante de forma predeterminada (que puede eliminar y reemplazar por su propia configuración):

  [![Editar Settings-Watch. bundle/root. plist](settings-images/rootplist-sml.png)](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>Uso de la configuración de la aplicación de inspección

Para acceder a los valores seleccionados por el usuario, cree una `NSUserDefaults` instancia de mediante el grupo de aplicaciones y especifique `NSUserDefaultsType.SuiteName` :

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch aplicación

[![La nueva aplicación de Apple Watch en el iPhone](settings-images/settings-app-sml.png)](settings-images/settings-app.png#lightbox)

Los usuarios interactuarán con la configuración a través de la nueva aplicación de **Apple Watch** en su iPhone. Esta aplicación permite al usuario Mostrar u ocultar aplicaciones en el reloj y editar también la configuración expuesta mediante el uso **de Settings-Watch. bundle**.

![La captura de pantalla muestra WatchKitSettings en la aplicación.](settings-images/applewatch-1.png) ![La captura de pantalla muestra WatchTodo en la aplicación.](settings-images/applewatch-2.png)

## <a name="related-links"></a>Vínculos relacionados

- [Documento de configuración de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
