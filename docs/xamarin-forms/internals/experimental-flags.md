---
title: Marcas experimentales de Xamarin. Forms
description: Las marcas experimentales de Xamarin. Forms permiten al equipo de ingeniería enviar nuevas características a los usuarios con más rapidez, al tiempo que se pueden cambiar las API de características antes de que pasen a una versión estable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2020
ms.openlocfilehash: cebb996da992058616f9cf96ef3212c9ce27022a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112604"
---
# <a name="xamarinforms-experimental-flags"></a>Marcas experimentales de Xamarin. Forms

Cuando se implementa una nueva característica de Xamarin. Forms, a veces se coloca detrás de una marca experimental. Esto permite al equipo de ingeniería proporcionar nuevas características con mayor rapidez, al tiempo que sigue pudiendo cambiar las API de características antes de que pasen a una versión estable. Después, se quita la marca experimental una vez que la característica se mueve a una versión estable.

Xamarin. Forms incluye las siguientes marcas experimentales:

- `CarouselView_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

El uso de la funcionalidad que está detrás de una marca experimental requiere habilitar la marca, o marcas, en la aplicación. Hay dos métodos para habilitar las marcas experimentales:

- Habilite la marca experimental, o marcas, en los proyectos de la plataforma.
- Habilite la marca experimental, o marcas, en la clase `App`.

> [!WARNING]
> Si se utiliza la funcionalidad que está detrás de una marca experimental, sin habilitar la marca, la aplicación producirá una excepción que indica qué marca debe estar habilitada.

## <a name="enable-flags-in-platform-projects"></a>Habilitar marcas en proyectos de plataforma

El método `Xamarin.Forms.Forms.SetFlags` se puede usar para habilitar una marca experimental en los proyectos de la plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

El método `SetFlags` debe invocarse en la clase `AppDelegate` en iOS, en la clase `MainActivity` en Android y en la clase `App` en UWP.

> [!IMPORTANT]
> La habilitación de una marca experimental en los proyectos de la plataforma debe realizarse antes de que se invoque el método de `Forms.Init`.

El método `Xamarin.Forms.Forms.SetFlags` acepta un argumento de matriz `string`, lo que permite habilitar varias marcas experimentales en una única llamada al método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca llame al método `SetFlags` más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar marcas en la clase de la aplicación

El método `Device.SetFlags` se puede usar para habilitar una marca experimental en la clase `App` en el proyecto de código compartido:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

El método `Device.SetFlags` acepta un argumento `IReadOnlyList<string>`, lo que hace posible habilitar varias marcas experimentales en una única llamada al método:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca llame al método `SetFlags` más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.
