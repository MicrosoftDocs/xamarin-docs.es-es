---
title: ¿Por qué no se puede conectar la compilación de versión de Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 5996cfa3c0a18fc186ea862a2b3d7910594e1281
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027006"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar la compilación de versión de Android a Internet?

## <a name="cause"></a>Motivo

El motivo más habitual de esta incidencia es que el permiso de **INTERNET** se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una compilación de versión. Esto se debe a que el permiso de Internet se usa para permitir que un depurador se asocie al proceso, como se describe para "DebugSymbols" [aquí](~/android/deploy-test/building-apps/build-process.md).

## <a name="fix"></a>Solución

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto se puede hacer a través del editor de manifiestos o el código fuente del manifiesto:

- Corregir en el editor: En su proyecto de Android, vaya a **Propiedades -> AndroidManifest.xml -> Permisos necesarios** y compruebe **Internet**

- Corrección en el código fuente: Abra el manifiesto de Android en un editor de código fuente y agregue la etiqueta de permiso dentro de las etiquetas `<Manifest>`:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
