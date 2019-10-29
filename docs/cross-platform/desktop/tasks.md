---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparación de tareas comunes
description: En este documento se compara cómo realizar varias tareas comunes en WPF y Xamarin. Forms. Examina botones, temporizadores, tamaños de fuente, abre un URI y muestra una hoja de acción.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: d1f1430d8044f94c17a19d747334b5ed8a1441cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016418"
---
# <a name="common-tasks-comparison"></a>Comparación de tareas comunes

| Tarea | WPF | Xamarin.Forms |
|--- |--- |--- |
|Mostrar un mensaje en la pantalla con botones|`MessageBox`|`Page.DisplayAlert`|
|Crear un temporizador|Clase `DispatcherTimer`|`Device.StartTimer` método estático|
|Obtener un tamaño de fuente predeterminado|`SystemFonts` clase estática|`Device.GetNamedSize` método estático|
|Abrir un URI o una dirección URL|`Process.Start`|`Device.OpenUri`|
|Mostrar hoja de acciones (lista de botones)|no disponible|`Page.DisplayActionSheet`|
