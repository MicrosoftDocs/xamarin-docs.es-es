---
title: Uso de ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5948d4b5db53db97c4e76cb7568c109b5faf90fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762197"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Uso de un ContentProvider con Xamarin. Android

CursorAdapters también se puede usar para mostrar los datos de un ContentProvider.
ContentProviders le permiten tener acceso a los datos expuestos por otras aplicaciones (incluidos los datos del sistema Android como contactos, información multimedia y calendario).

La mejor manera de tener acceso a un ContentProvider es con un CursorLoader mediante LoaderManager. LoaderManager se presentó en Android 3,0 (nivel de API 11, Honeycomb) para quitar las tareas de bloqueo del subproceso principal y el uso de CursorLoader permite cargar los datos en un subproceso antes de enlazarlos a un control ListView para su presentación.

Consulte [Introducción a ContentProviders](~/android/platform/content-providers/index.md) para obtener más información.
