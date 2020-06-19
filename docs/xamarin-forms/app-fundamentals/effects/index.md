---
title: Efectos de Xamarin.Forms
description: Con los efectos se pueden personalizar los controles nativos de cada plataforma sin tener que recurrir a la implementación de un representador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6206d2c561df74a01b7d7408e8d542f1e2189d3
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139339"
---
# <a name="xamarinforms-effects"></a>Efectos de Xamarin.Forms

_Las interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, lo que permite que las aplicaciones de Xamarin.Forms conserven la apariencia adecuada para cada plataforma. Con los efectos se pueden personalizar los controles nativos de cada plataforma sin tener que recurrir a la implementación de un representador personalizado._

## <a name="introduction-to-effects"></a>[Introducción a los efectos](introduction.md)

Con los efectos se pueden personalizar los controles nativos de cada plataforma y normalmente se usan para pequeños cambios de estilo. En este artículo se proporciona una introducción a los efectos, se describe el límite entre los efectos y los representadores personalizados y se describe la clase `PlatformEffect`.

## <a name="creating-an-effect"></a>[Crear un efecto](creating.md)

Los efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control [`Entry`](xref:Xamarin.Forms.Entry) cuando el control recibe el foco.

## <a name="passing-parameters-to-an-effect"></a>[Pasar parámetros a un efecto](passing-parameters/index.md)

La creación de un efecto que se configura a través de parámetros permite que el efecto se pueda volver a usar. En estos artículos se muestra cómo usar las propiedades para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución.

## <a name="invoking-events-from-an-effect"></a>[Invocación de eventos desde un efecto](touch-tracking.md)

Los efectos pueden invocar eventos. En este artículo se muestra cómo crear un evento que implementa el seguimiento de dedo multitoque de bajo nivel y señala una aplicación para presiones de toque, movimientos y liberaciones.

## <a name="reusable-roundeffect"></a>[RoundEffect reutilizable](reusable-roundeffect.md)

RoundEffect es un efecto reutilizable que se puede aplicar a cualquier control derivado de VisualElement para representar el control como un círculo. Este efecto se puede usar para crear imágenes circulares, botones circulares u otros controles circulares.
