---
title: 'Error de compilación de Android: error inesperado en la tarea LinkAssemblies'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 71305dd7287df56036d0298ebfcf8a8cb7c4d3b3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528367"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Error de compilación de Android: error inesperado en la tarea LinkAssemblies

Es posible que vea un mensaje `The "LinkAssemblies" task failed unexpectedly` de error al compilar un proyecto de Xamarin. Android que use formularios. Esto sucede cuando el enlazador está activo (normalmente en una compilación de *versión* para reducir el tamaño del paquete de la aplicación). y se produce porque los destinos de Android no se actualizan al marco de trabajo más reciente. (Más información: [Xamarin. Forms para los requisitos de Android](~/get-started/requirements.md#android))

La solución a este problema es asegurarse de que tiene las últimas versiones de Android SDK compatibles y establecer la versión de **.NET Framework de destino** en la plataforma instalada más reciente. También se recomienda establecer la **versión de Android de destino** en la plataforma instalada más reciente y la versión de **Android mínima** en la API 19 o superior. Esto se considera la configuración admitida.

## <a name="setting-in-visual-studio-for-mac"></a>Establecer en Visual Studio para Mac

1. Haga clic con el botón derecho en el proyecto de Android y seleccione **Opciones** en el menú.
2. En el cuadro de diálogo **Opciones del proyecto** , vaya a compilar **> General**.
3. Establezca compilar con la versión de **Android: (Plataforma de destino)**  a la plataforma instalada más reciente.
4. En el cuadro de diálogo **Opciones del proyecto** , vaya a compilar **> aplicación de Android**.
5. Establezca la **versión mínima de Android** en el nivel de API 19 o superior y la **versión de Android de destino** en la plataforma instalada más reciente que eligió en (3).

## <a name="setting-in-visual-studio"></a>Configuración en Visual Studio

1. Haga clic con el botón derecho en el proyecto de Android y seleccione **propiedades** en el menú.
2. En las propiedades del proyecto, vaya a **aplicación**.
3. Establezca compilar con la versión de **Android: (Plataforma de destino)**  a la plataforma instalada más reciente.
4. En las propiedades del proyecto, vaya a **manifiesto de Android**.
5. Establezca la **versión mínima de Android** en el nivel de API 19 o superior y la **versión de Android de destino** en la plataforma instalada más reciente que eligió en (3).

Una vez que haya actualizado la configuración, limpie y recompile el proyecto para asegurarse de que los cambios se recogen.
