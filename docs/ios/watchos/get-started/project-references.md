---
title: Referencias de proyectos de watchos en Xamarin
description: En este documento se describe la relación entre una aplicación de iOS, una aplicación de inspección y una extensión de aplicación de inspección. Describe las referencias de proyecto y los identificadores de agrupación.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 96873e1bff34a4ef3ed76d675ca0a2b5c03f0d72
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997246"
---
# <a name="watchos-project-references-in-xamarin"></a>Referencias de proyectos de watchos en Xamarin

_Explicación de la relación entre la aplicación de iOS, la aplicación de inspección y la extensión de inspección._

Los tres proyectos de una solución watchos se *configuran automáticamente* para que se hagan referencia entre sí de una manera específica para que las aplicaciones de watchos 3 se compilen y se agrupen correctamente. Estas referencias de proyecto y la configuración del identificador de lote se describen a continuación como referencia.

## <a name="project-references"></a>Referencias del proyecto

Para ver las referencias, haga doble clic en los nodos referencias de cada proyecto:

- **aplicación de inspección** de referencias de **aplicación de iPhone**

  ![Aplicación de inspección de referencias de aplicación de iPhone](project-references-images/catalog-reference1.png)

- La **aplicación de inspección** hace referencia a la extensión de la **aplicación Watch**

  ![Aplicación de inspección de referencias de aplicación de iPhone](project-references-images/catalog-reference2.png)

- La **extensión de la aplicación inspección** no hace referencia a ninguno de los otros proyectos

  ![La extensión de la aplicación de inspección no hace referencia a los otros proyectos](project-references-images/catalog-reference3.png)

## <a name="bundle-identifiers"></a>Identificadores de agrupación

También debe asegurarse de que los **identificadores de paquete** sean correctos.
Los tres proyectos deben tener el *mismo* prefijo de identificador, con los dos proyectos de inspección que tienen extensiones predefinidas de `watchkitextension` y `watchkitapp` , como se indica a continuación (en el ejemplo **WatchKitCatalog** ):

- Proyecto Unificado de Xamarin. iOS:`com.xamarin.WatchKitCatalog`

- Proyecto de extensión WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`

- Proyecto de la aplicación de inspección:`com.xamarin.WatchKitCatalog.watchkitapp`

Asegúrese también de que la configuración de **info. plist** sea correcta:

- El proyecto de la aplicación de inspección `WKCompanionAppBundleIdentifier` coincide con el identificador de lote de la aplicación principal/contenedor (es decir, el que se ejecuta en el iPhone).

- El **identificador de paquete de WKApp** del proyecto de extensión del kit de inspección coincide con el identificador de lote del proyecto de la aplicación de inspección.

Puede editar los identificadores haciendo doble clic en el archivo **info. plist** en cada proyecto.

Esta captura de pantalla es el archivo info. plist **de la extensión de inspección** y muestra también el identificador **de la aplicación Watch** :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Esta captura de pantalla es el archivo info. plist de la extensión de inspección.](project-references-images/infoplist-extension.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Esta captura de pantalla es el archivo info. plist de la extensión de inspección.](project-references-images/infoplist-extension-vs.png)

-----

Esta captura de pantalla es el archivo info. plist **de la aplicación de inspección** .
La versión del **so de inspección** actual es 8,2, por lo que el destino de **implementación** de la aplicación de inspección debe ser **8,2**. Tenga en cuenta que si tiene instalado Xcode 6,3, este valor se puede establecer en 8,3; debe cambiarlo 8,2.

![El archivo Watch info. plist](project-references-images/infoplist-watchapp.png)

El destino de implementación de la aplicación de inspección puede ser diferente de la extensión inspección y la aplicación iOS.
