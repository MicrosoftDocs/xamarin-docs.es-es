---
title: Enlace de plataformas nativas
description: En este documento se describe cómo usar la opción-Framework de Objective Sharpie para crear un enlace a una biblioteca distribuida como un marco de trabajo.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 0733e2b406f5032a2e2717df66c96dcb28301f09
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374133"
---
# <a name="binding-native-frameworks"></a>Enlace de plataformas nativas

A veces, una biblioteca nativa se distribuye como un [marco](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objective Sharpie proporciona una característica útil para enlazar marcos de trabajo definidos correctamente mediante la opción `-framework`.

Por ejemplo, enlazar el [marco de trabajo de Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) para iOS es sencillo:

```
$ sharpie bind \
    -framework ./AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

En algunos casos, un marco de trabajo especificará un archivo **info. plist** que indica en qué SDK se debe compilar el marco de trabajo. Si esta información existe y no se pasa ninguna opción de `-sdk` explícita, Objective Sharpie lo deducirá del archivo **info. plist** de Framework (ya sea la clave `DTSDKName` o una combinación de las claves `DTPlatformName` y `DTPlatformVersion`).

La opción `-framework` no permite que se pasen archivos de encabezado explícitos. El archivo de encabezado de paraguas se elige por Convención según el nombre del marco de trabajo. Si no se encuentra un encabezado paraguas, Objective Sharpie no intentará enlazar el marco de trabajo y debe realizar manualmente el enlace proporcionando los archivos de encabezado paraguas correctos que se van a analizar, junto con los argumentos del marco de trabajo para Clang (por ejemplo, la opción ruta de acceso de búsqueda de `-F` Framework).

En el capó, especificar `-framework` es simplemente un acceso directo. Los siguientes argumentos de enlace son idénticos a los `-framework` abreviaturas anteriores.
Una importancia especial es la ruta de acceso de búsqueda de `-F .` Framework que se proporciona a Clang (tenga en cuenta el espacio y el período, que son necesarios como parte del comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    ./AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
