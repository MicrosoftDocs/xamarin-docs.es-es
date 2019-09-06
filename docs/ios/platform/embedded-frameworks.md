---
title: Marcos de trabajo incrustados en Xamarin. iOS
description: En este documento se describe cómo compartir código con marcos de trabajo incrustados en una aplicación de Xamarin. iOS. Esto puede hacerse con la herramienta Mtouch o con las referencias nativas.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 6287dca8660c1147455beb22304b7f8637ac7fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292758"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Marcos de trabajo incrustados en Xamarin. iOS

_En este documento se describe cómo los desarrolladores de aplicaciones pueden insertar marcos de usuario en sus aplicaciones._

Con iOS 8,0 Apple hizo posible crear un marco de trabajo integrado para compartir código entre las extensiones de aplicación y la aplicación principal en Xcode.

Xamarin. iOS 9,0 agrega compatibilidad para consumir estos marcos de trabajo incrustados (creados con Xcode) en aplicaciones de Xamarin. iOS. ***No** es posible crear marcos de trabajo insertados a partir de cualquier tipo de proyectos de Xamarin. iOS, sino que solo se consumen los marcos nativos (Objective-C) existentes.*

Hay dos maneras de consumir marcos en Xamarin. iOS:

- Pase el marco de trabajo a la herramienta Mtouch agregando lo siguiente a los argumentos Mtouch adicionales en las opciones de **compilación de iOS** del proyecto:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Debe establecerse para cada configuración de proyecto.

- Agregar referencias nativas en el menú contextual

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Haga clic con el botón derecho en el proyecto y busque agregar referencias nativas.

![](embedded-frameworks-images/xam-native-refs.png "Seleccione Agregar referencias nativas en Visual Studio para Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Haga clic con el botón derecho en el proyecto y busque agregar referencias nativas.

![](embedded-frameworks-images/vs-native-refs.png "Seleccione Agregar referencias nativas en Visual Studio")

-----

  Esto funcionará para todas las configuraciones.

En versiones futuras de Visual Studio para Mac y las herramientas de Xamarin para Visual Studio, será posible consumir Marcos desde el IDE (sin editar archivos de proyecto manualmente).

Algunos proyectos de ejemplo se pueden encontrar en [GitHub](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitaciones

- Los marcos de trabajo incrustados solo se admiten en proyectos [unificados](~/cross-platform/macios/unified/index.md) .
- Los marcos de trabajo incrustados solo se admiten en proyectos con un destino de implementación de al menos iOS 8,0.
- Si una extensión requiere un marco de trabajo incrustado, la aplicación de contenedor también debe tener una referencia al marco; de lo contrario, el marco no se incluirá en el lote de aplicaciones.

## <a name="the-mono-runtime"></a>El entorno de ejecución mono

Xamarin. iOS internamente aprovecha esta característica para vincular con el tiempo de ejecución de mono como un marco, en lugar de vincular el tiempo de ejecución de mono estáticamente a cada extensión y la aplicación contenedora.

Esto se hace automáticamente si la aplicación de contenedor es una aplicación unificada, contiene extensiones y la implementación de destino es iOS 8,0 o superior.

Las aplicaciones sin extensiones seguirán vinculando el tiempo de ejecución de mono estáticamente, porque hay una penalización de tamaño para usar un marco de trabajo si solo hay una aplicación que haga referencia a ella.

El desarrollador de la aplicación puede invalidar este comportamiento; para ello, agregue lo siguiente como argumento Mtouch adicional en las opciones de compilación de iOS del proyecto:

- `--mono:static`: Vínculos con el tiempo de ejecución mono estáticamente.
- `--mono:framework`: Vínculos con el tiempo de ejecución mono como un marco.

Un escenario para la vinculación con el tiempo de ejecución de mono como un marco de trabajo para las aplicaciones sin extensiones es reducir el tamaño del archivo ejecutable, para superar las restricciones de tamaño que Apple aplica en el ejecutable. Como referencia, el entorno de ejecución mono agrega aproximadamente 1.7 MB por arquitectura (a partir de Xamarin. iOS 8,12, pero su variación varía entre versiones e incluso entre aplicaciones). El marco de trabajo mono agrega aproximadamente 2,3 MB por arquitectura, lo que significa que, en el caso de una aplicación de una sola arquitectura sin extensiones, el hecho de que el vínculo de la aplicación con el tiempo de ejecución de mono sea una plataforma, reducirá el archivo ejecutable de ~ 1.7 MB, pero agregará un marco de ~ 2,3 MB, lo que resultará en un ALLtogether de aplicación de ~ 0,6 MB de mayor tamaño.

