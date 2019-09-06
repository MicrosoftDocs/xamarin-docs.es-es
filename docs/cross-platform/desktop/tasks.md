---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de tareas comunes
description: En este documento se compara cómo realizar varias tareas comunes en WPF y Xamarin. Forms. Examina botones, temporizadores, tamaños de fuente, abre un URI y muestra una hoja de acción.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290327"
---
# <a name="common-tasks-comparison"></a>Comparación de tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Mostrar un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer`método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts`clase estática|`Device.GetNamedSize`método estático|
|Abrir un URI o una dirección URL|`Process.Start`|`Device.OpenUri`|
|Mostrar hoja de acciones (lista de botones)|N/D|`Page.DisplayActionSheet`|
