---
title: Introducción a los efectos
description: Con los efectos se pueden personalizar los controles nativos de cada plataforma y normalmente se usan para pequeños cambios de estilo. En este artículo se proporciona una introducción a los efectos, se describe el límite entre los efectos y los representadores personalizados, y se describe la clase PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a891ec70f6f83984ed463fe914442758bdf57a2e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139313"
---
# <a name="introduction-to-effects"></a>Introducción a los efectos

_Con los efectos se pueden personalizar los controles nativos de cada plataforma y normalmente se usan para pequeños cambios de estilo. En este artículo se proporciona una introducción a los efectos, se describe el límite entre los efectos y los representadores personalizados, y se describe la clase PlatformEffect._

En [Páginas, diseños y controles de Xamarin.Forms](~/xamarin-forms/user-interface/controls/index.md), se presenta una API común para describir interfaces de usuario móviles multiplataforma. Cada página, diseño y control se representa de forma distinta en cada plataforma mediante una clase `Renderer` que, a su vez, crea un control nativo (correspondiente a la representación de Xamarin.Forms), lo coloca en la pantalla y agrega el comportamiento especificado en el código compartido.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Pero la implementación de una clase de representador personalizado para llevar a cabo una personalización de controles simples suele ser una respuesta compleja. Los efectos simplifican este proceso y permiten que los controles nativos de cada plataforma se puedan personalizar más fácilmente.

Los efectos se crean en proyectos específicos de la plataforma mediante la creación de subclases del control `PlatformEffect` y, después, se consumen al asociarlos a un control adecuado en una biblioteca de .NET Standard de Xamarin.Forms o un proyecto de biblioteca compartida.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>¿Por qué usar un efecto en lugar de un representador personalizado?

Los efectos simplifican la personalización de un control, son reutilizables y se pueden parametrizar para aumentar todavía más la reutilización.

Todo lo que se puede lograr con un efecto también se puede lograr con un representador personalizado. Pero los representadores personalizados ofrecen más flexibilidad y personalización que los efectos. En las instrucciones siguientes se enumeran las circunstancias bajo las que elegir un efecto en lugar de un representador personalizado:

- Un efecto se recomienda cuando el cambio de las propiedades de un control específico de la plataforma proporcionará el resultado deseado.
- Un representador personalizado es necesario cuando hay que invalidar los métodos de un control específico de la plataforma.
- Un representador personalizado es necesario cuando hay que reemplazar el control específico de la plataforma que implementa un control de Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creación de subclases de la clase PlatformEffect

En la tabla siguiente se muestra el espacio de nombres para la clase `PlatformEffect` en cada plataforma y los tipos de sus propiedades:

|Plataforma|Espacio de nombres|Contenedor|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Ver|
|Plataforma universal de Windows (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Cada una de las clases `PlatformEffect` específicas de la plataforma expone las propiedades siguientes:

- `Container`: hace referencia al control específico de la plataforma que se usa para implementar el diseño.
- `Control`: hace referencia al control específico de la plataforma que se usa para implementar el control de Xamarin.Forms.
- `Element`: hace referencia al control de Xamarin.Forms que se va a representar.

Los efectos no tienen información de tipo sobre el contenedor, el control o el elemento al que se adjuntan, ya que se pueden adjuntar a cualquier elemento. Por tanto, cuando se adjunta un efecto a un elemento que no es compatible, se debe degradar correctamente o iniciar una excepción. Pero las propiedades `Container`, `Control` y `Element` se pueden convertir a su tipo de implementación. Para obtener más información sobre estos tipos, vea [Clases base y controles nativos del representador](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada clase `PlatformEffect` específica de la plataforma expone los métodos siguientes, que se deben invalidar para implementar un efecto:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached): se llama cuando se adjunta un efecto a un control de Xamarin.Forms. Una versión invalidada de este método, en cada clase de efecto específica de la plataforma, es el lugar para realizar la personalización del control, junto con el control de excepciones en caso de que no se pueda aplicar el efecto al control de Xamarin.Forms especificado.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached): se llama cuando se desasocia un efecto de un control de Xamarin.Forms. Una versión invalidada de este método, en cada clase de efecto específica de la plataforma, es el lugar para realizar cualquier limpieza de efectos, como anular el registro de un controlador de eventos.

Además, `PlatformEffect` expone el método [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)), que también se puede invalidar. Este método se llama cuando ha cambiado una propiedad del elemento. Una versión invalidada de este método, en cada clase de efecto específica de la plataforma, es el lugar para responder a los cambios de propiedad enlazable en el control de Xamarin.Forms. Siempre se debe realizar una comprobación de la propiedad que ha modificado, ya que esta invalidación se puede llamar varias veces.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
