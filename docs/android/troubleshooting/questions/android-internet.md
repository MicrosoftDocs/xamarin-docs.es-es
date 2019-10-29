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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027006"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar la compilación de versión de Android a Internet?

## <a name="cause"></a>Motivo

La causa más común de este problema es que el permiso de **Internet** se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una compilación de versión. Esto se debe a que el permiso de Internet se usa para permitir que un depurador se asocie al proceso, tal como se describe [aquí](~/android/deploy-test/building-apps/build-process.md)"DebugSymbols".

## <a name="fix"></a>Solución

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto puede hacerse a través del editor de manifiestos o del SourceCode del manifiesto:

- Corrección en el editor: en el proyecto de Android, vaya a **propiedades-> archivo AndroidManifest. XML-> permisos necesarios** y Active **Internet**

- Corrección en SourceCode: Abra archivo AndroidManifest en un editor de código fuente y agregue la etiqueta Permission dentro de las etiquetas `<Manifest>`:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
