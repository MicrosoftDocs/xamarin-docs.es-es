---
title: características de la plataforma watchos
description: Este documento contiene vínculos a varias guías que describen características de la plataforma watchos como Apple Pay, notificaciones, complicaciones, sugerencias proactivas, aplicaciones de entrenamiento, etc.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 2b987992bcb3dd4d2575a46e21a2302ed78d8d70
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282285"
---
# <a name="watchos-platform-features"></a>características de la plataforma watchos

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introducción a watchOS 5](introduction-to-watchos5/index.md)

En este documento se proporciona información general de alto nivel sobre las características nuevas y actualizadas de watchos 5 que están disponibles para su uso al compilar aplicaciones de watchos con Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introducción a watchOS 4](introduction-to-watchos4.md)

En este documento se proporciona información general de alto nivel sobre las características agregadas y actualizadas en watchos 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introducción a watchOS 3](introduction-to-watchos3/index.md)

En este artículo se describen las API nuevas y actualizadas en watchos 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay mejoras](~/ios/watchos/platform/apple-pay.md)

En watchos 3, el marco de trabajo de PassKit se ha ampliado para permitir la compatibilidad con pagos desde la aplicación y seguros (de bienes y servicios físicos) para las aplicaciones que se ejecutan en el Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tareas en segundo plano](~/ios/watchos/platform/background-tasks.md)

watchos 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información, lo que garantiza que tiene el contenido que necesita el usuario antes de abrirlo.

## <a name="notificationsnotificationsmd"></a>[Notificaciones](notifications.md)

Obtenga información sobre cómo proporcionar un control de notificaciones personalizado en la aplicación de inspección.

## <a name="complicationscomplicationsmd"></a>[Complicaciones](complications.md)

Agregue compatibilidad con complicaciones para Mostrar datos actualizados en la superficie de inspección.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugerencias proactivas](~/ios/watchos/platform/proactive-suggestions.md)

watchos 3 permite que la aplicación presente de forma proactiva información al usuario en contextos determinados. Para admitir esta característica, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) ahora incluye la `MapItem` propiedad que permite que la aplicación proporcione información de ubicación para su uso posterior por parte de otras aplicaciones.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interacción rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Proporcionar interacciones de usuario rápidas es esencial para crear atractivas aplicaciones de Apple Watch y complicaciones. Novedad de watchos 3, Apple ha agregado compatibilidad con los reconocedores de gestos, el acceso a la Digital Crown y nuevas técnicas de navegación y notificación de usuario. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permite al desarrollador crear fácilmente interfaces enriquecidas y de gran visión que son rápidas y con capacidad de respuesta.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Mejoras en las aplicaciones de entrenamiento](~/ios/watchos/platform/workout-apps.md)

Novedad de watchos 3, las aplicaciones relacionadas con el entrenamiento tienen la capacidad de ejecutarse en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a los datos de HealthKit), la aplicación debe `WKBackgroundModes` incluir la clave `Info.plist` en el archivo con `workout-processing`el valor.
