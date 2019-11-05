---
title: Efectos de Xamarin.Forms
description: Con los efectos se pueden personalizar los controles nativos de cada plataforma sin tener que recurrir a la implementación de un representador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: 1242d32b7f072c837005cdee174abe6c0d1d0c68
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73083834"
---
# <a name="xamarinforms-effects"></a>Efectos de Xamarin.Forms

_Las interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, lo que permite que las aplicaciones de Xamarin.Forms conserven la apariencia adecuada para cada plataforma. Con los efectos se pueden personalizar los controles nativos de cada plataforma sin tener que recurrir a la implementación de un representador personalizado._

## <a name="introduction-to-effectsintroductionmd"></a>[Introducción a los efectos](introduction.md)

Con los efectos se pueden personalizar los controles nativos de cada plataforma y normalmente se usan para pequeños cambios de estilo. En este artículo se proporciona una introducción a los efectos, se describe el límite entre los efectos y los representadores personalizados y se describe la clase `PlatformEffect`.

## <a name="creating-an-effectcreatingmd"></a>[Crear un efecto](creating.md)

Los efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control [`Entry`](xref:Xamarin.Forms.Entry) cuando el control recibe el foco.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Pasar parámetros a un efecto](passing-parameters/index.md)

La creación de un efecto que se configura a través de parámetros permite que el efecto se pueda volver a usar. En estos artículos se muestra cómo usar las propiedades para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Invocación de eventos desde un efecto](touch-tracking.md)

Los efectos pueden invocar eventos. En este artículo se muestra cómo crear un evento que implementa el seguimiento de dedo multitoque de bajo nivel y señala una aplicación para presiones de toque, movimientos y liberaciones.

## <a name="reusable-roundeffectreusable-roundeffectmd"></a>[RoundEffect reutilizable](reusable-roundeffect.md)

RoundEffect es un efecto reutilizable que se puede aplicar a cualquier control derivado de VisualElement para representar el control como un círculo. Este efecto se puede usar para crear imágenes circulares, botones circulares u otros controles circulares.
