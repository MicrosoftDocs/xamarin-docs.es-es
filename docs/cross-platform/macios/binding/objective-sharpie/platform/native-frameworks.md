---
title: Enlace de plataformas nativas
description: En este documento se describe cómo usar la opción-Framework de Objective Sharpie para crear un enlace a una biblioteca distribuida como un marco de trabajo.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cb6c39b2110161b3f839b8adc03701007f09cc4d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521883"
---
# <a name="binding-native-frameworks"></a>Enlace de plataformas nativas

A veces, una biblioteca nativa se distribuye como un [marco](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objective Sharpie proporciona una característica útil para enlazar marcos de trabajo definidos correctamente `-framework` mediante la opción.

Por ejemplo, enlazar el [marco de trabajo de Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) para iOS es sencillo:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

En algunos casos, un marco de trabajo especificará un archivo **info. plist** que indica en qué SDK se debe compilar el marco de trabajo. Si esta información existe y no se `-sdk` pasa ninguna opción explícita, Objective Sharpie lo deducirá del archivo **info. plist** de Framework (ya sea `DTSDKName` la clave o una combinación de `DTPlatformName` las `DTPlatformVersion` claves y).

La `-framework` opción no permite que se pasen archivos de encabezado explícitos. El archivo de encabezado de paraguas se elige por Convención según el nombre del marco de trabajo. Si no se encuentra un encabezado paraguas, Objective Sharpie no intentará enlazar el marco de trabajo y debe realizar manualmente el enlace proporcionando los archivos de encabezado paraguas correctos que se van a analizar, junto con los argumentos del marco de trabajo para Clang (como el `-F`opción de ruta de búsqueda de Framework).

En el capó, especificar `-framework` es simplemente un acceso directo. Los siguientes argumentos de enlace son idénticos `-framework` a los de la abreviatura anterior.
Una importancia especial es la `-F .` ruta de acceso de búsqueda de Framework proporcionada a Clang (tenga en cuenta el espacio y el punto, que son necesarios como parte del comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
