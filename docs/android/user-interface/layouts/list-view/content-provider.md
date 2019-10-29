---
title: Uso de ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c99b3d64f4f5033240c086af8677d31485a44f01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028933"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Uso de un ContentProvider con Xamarin. Android

CursorAdapters también se puede usar para mostrar los datos de un ContentProvider.
ContentProviders le permiten tener acceso a los datos expuestos por otras aplicaciones (incluidos los datos del sistema Android como contactos, información multimedia y calendario).

La mejor manera de tener acceso a un ContentProvider es con un CursorLoader mediante LoaderManager. LoaderManager se presentó en Android 3,0 (nivel de API 11, Honeycomb) para quitar las tareas de bloqueo del subproceso principal y el uso de CursorLoader permite cargar los datos en un subproceso antes de enlazarlos a un control ListView para su presentación.

Consulte [Introducción a ContentProviders](~/android/platform/content-providers/index.md) para obtener más información.
