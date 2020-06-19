---
title: Animación enXamarin.Forms
description: Xamarin.Formsincluye su propia infraestructura de animación que es sencilla para crear animaciones sencillas, a la vez que también es lo suficientemente versátil como para crear animaciones complejas.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 88a671c4d28d62a5f73e90a7b2fa9c45b7dbe8b1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84129004"
---
# <a name="animation-in-xamarinforms"></a>Animación enXamarin.Forms

_Xamarin. Forms incluye su propia infraestructura de animación que es sencilla para crear animaciones sencillas, a la vez que también es lo suficientemente versátil como para crear animaciones complejas._

Las Xamarin.Forms clases de animación tienen como destino distintas propiedades de los elementos visuales, con una animación típica que cambia progresivamente una propiedad de un valor a otro durante un período de tiempo. Tenga en cuenta que no hay ninguna interfaz XAML para las Xamarin.Forms clases de animación. Sin embargo, las animaciones se pueden encapsular en [comportamientos](~/xamarin-forms/app-fundamentals/behaviors/index.md) y después se hace referencia a ellas desde XAML.

## <a name="simple-animations"></a>[Animaciones simples](simple.md)

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase proporciona métodos de extensión que se pueden usar para construir animaciones simples que giran, escalan, traducen y atenúan [`VisualElement`](xref:Xamarin.Forms.VisualElement) instancias. En este artículo se muestra cómo crear y cancelar animaciones mediante la `ViewExtensions` clase.

## <a name="easing-functions"></a>[Funciones de aceleración](easing.md)

Xamarin.Formsincluye una [`Easing`](xref:Xamarin.Forms.Easing) clase que permite especificar una función de transferencia que controla el modo en que las animaciones se aceleran o reducen a medida que se ejecutan. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.

## <a name="custom-animations"></a>[Animaciones personalizadas](custom.md)

La [`Animation`](xref:Xamarin.Forms.Animation) clase es el bloque de creación de todas las Xamarin.Forms animaciones, con los métodos de extensión de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase que crea uno o más `Animation` objetos. En este artículo se muestra cómo usar la `Animation` clase para crear y cancelar animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan las propiedades que no animan los métodos de animación existentes.
