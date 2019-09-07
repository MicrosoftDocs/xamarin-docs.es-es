---
title: ¿Por qué no se puede conectar la compilación de versión de Android a Internet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 162378c00f3e20574d04dc373fcc492a9407b88d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761030"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>¿Por qué no se puede conectar la compilación de versión de Android a Internet?

## <a name="cause"></a>Causa

La causa más común de este problema es que el permiso de **Internet** se incluye automáticamente en una compilación de depuración, pero debe establecerse manualmente para una compilación de versión. Esto se debe a que el permiso de Internet se usa para permitir que un depurador se asocie al proceso, tal como se describe [aquí](~/android/deploy-test/building-apps/build-process.md)"DebugSymbols".

## <a name="fix"></a>Solución

Para resolver el problema, puede requerir el permiso de Internet en el manifiesto de Android. Esto puede hacerse a través del editor de manifiestos o del SourceCode del manifiesto:

- Corrección en el editor: En el proyecto de Android, vaya a **propiedades-> archivo AndroidManifest. XML-> permisos necesarios** y compruebe **Internet** .

- Corrección en SourceCode: Abra archivo AndroidManifest en un editor de código fuente y agregue la etiqueta Permission dentro `<Manifest>` de las etiquetas:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
