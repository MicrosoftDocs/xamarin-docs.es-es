---
title: Introducción a los comportamientos
description: Los comportamientos permiten agregar funciones a los controles de la interfaz de usuario sin tener que incluirlos en subclases. En su lugar, la función se implementa en una clase de comportamiento y se asocia al control como si fuera parte de este. En este artículo, se proporciona una introducción a los comportamientos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37c76a5f325c363a92c2a2c1e597dab28f064cd9
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964615"
---
# <a name="introduction-to-behaviors"></a>Introducción a los comportamientos

_Los comportamientos permiten agregar funciones a los controles de la interfaz de usuario sin tener que incluirlos en subclases. En su lugar, la función se implementa en una clase de comportamiento y se asocia al control como si fuera parte de este. En este artículo, se proporciona una introducción a los comportamientos._

Los comportamientos permiten implementar código que normalmente tendría que escribirse como código subyacente, ya que interactúan directamente con la API del control, de manera que pueden asociarse al control de manera concisa y empaquetarse para reutilizarlos en más de una aplicación. Pueden usarse para proporcionar una amplia variedad de funciones para los controles, como:

- Agregar un validador de correo electrónico a un elemento [`Entry`](xref:Xamarin.Forms.Entry).
- Crear un control de calificación mediante un reconocedor de gestos de pulsar.
- Controlar una animación.
- Agregar un efecto a un control.

Los comportamientos también permiten escenarios más avanzados. En el contexto de los *comandos*, los comportamientos son un método útil para conectar un control a un comando. Además, también pueden usarse para asociar comandos a los controles que no se han diseñado para interactuar con los comandos. Por ejemplo, pueden usarse para invocar un comando en respuesta a la activación de un evento.

Xamarin.Forms admite dos estilos de comportamiento:

- **Comportamientos de Xamarin.Forms** : clases que derivan de la clase [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), donde `T` es el tipo del control en el que tiene que aplicarse el comportamiento. Para obtener más información sobre los comportamientos de Xamarin.Forms, vea [Comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md).
- **Comportamientos asociados**: clases de `static` con una o varias propiedades asociadas. Para obtener más información sobre los comportamientos asociados, vea [Comportamientos asociados](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Esta guía se centra en los comportamientos de Xamarin.Forms, ya que son el método preferido para la creación de comportamientos.

## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
