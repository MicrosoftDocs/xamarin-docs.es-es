---
title: Comportamientos de Xamarin.Forms
description: Los comportamientos permiten agregar funciones a los controles de interfaz de usuario sin tener que incluir subclases. Los comportamientos se escriben en código y se agregan a controles en XAML o código.
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d917d7d6421cfae7fc877c81023a835573fa99b1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964628"
---
# <a name="no-locxamarinforms-behaviors"></a>Comportamientos de Xamarin.Forms

_Los comportamientos permiten agregar funciones a los controles de interfaz de usuario sin tener que incluir subclases. Los comportamientos se escriben en código y se agregan a controles en XAML o código._

## <a name="introduction-to-behaviors"></a>[Introducción a comportamientos](introduction.md)

Los comportamientos permiten implementar el código que normalmente tendría que escribir como código subyacente, ya que interactúa directamente con la API del control de manera que puede asociarse al control de manera concisa. En este artículo, se proporciona una introducción a los comportamientos.

## <a name="attached-behaviors"></a>[Comportamientos asociados](attached.md)

Los comportamientos asociados son clases `static` con una o varias propiedades asociadas. En este artículo, se explica cómo crear y usar comportamientos asociados.

## <a name="no-locxamarinforms-behaviors"></a>[Comportamientos de Xamarin.Forms](creating.md)

Los comportamientos de Xamarin.Forms se crean mediante la derivación de la clase [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1). En este artículo se explica cómo crear y consumir comportamientos de Xamarin.Forms.

## <a name="reusable-effectbehavior"></a>[EffectBehavior reutilizable](effect-behavior.md)

Los comportamientos son una manera útil de agregar un efecto a un control, quitando el código de control de efecto reutilizable de los archivos de código subyacente. En este artículo se explica cómo crear y consumir un comportamiento de Xamarin.Forms para agregar un efecto a un control.
