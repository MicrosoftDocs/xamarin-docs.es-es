---
title: Xamarin.Formsmarcas experimentales
description: Xamarin.Formslas marcas experimentales permiten al equipo de ingeniería enviar nuevas características a los usuarios con más rapidez, al mismo tiempo que se pueden cambiar las API de características antes de que pasen a una versión estable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 17fcc996b4dc8013a23a598ece8e240caba3f775
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946122"
---
# <a name="xamarinforms-experimental-flags"></a>Xamarin.Formsmarcas experimentales

Cuando Xamarin.Forms se implementa una nueva característica, a veces se coloca detrás de una marca experimental. Esto permite al equipo de ingeniería proporcionar nuevas características con mayor rapidez, al tiempo que sigue pudiendo cambiar las API de características antes de que pasen a una versión estable. Después, se quita la marca experimental una vez que la característica se mueve a una versión estable.

Xamarin.Formsincluye las siguientes marcas experimentales:

- `AppTheme_Experimental`
- `CarouselView_Experimental`
- `Expander_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
- `Shapes_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

El uso de la funcionalidad que está detrás de una marca experimental requiere habilitar la marca, o marcas, en la aplicación. Hay dos métodos para habilitar las marcas experimentales:

- Habilite la marca experimental, o marcas, en los proyectos de la plataforma.
- Habilite la marca experimental, o marcas, en la `App` clase.

> [!WARNING]
> Si se utiliza la funcionalidad que está detrás de una marca experimental, sin habilitar la marca, la aplicación producirá una excepción que indica qué marca debe estar habilitada.

## <a name="enable-flags-in-platform-projects"></a>Habilitar marcas en proyectos de plataforma

El `Xamarin.Forms.Forms.SetFlags` método se puede usar para habilitar una marca experimental en los proyectos de la plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

El `SetFlags` método debe invocarse en la `AppDelegate` clase en iOS, en la `MainActivity` clase en Android y en la `App` clase en UWP.

> [!IMPORTANT]
> La habilitación de una marca experimental en los proyectos de la plataforma debe realizarse antes de que `Forms.Init` se invoque el método.

El `Xamarin.Forms.Forms.SetFlags` método acepta un `string` argumento de matriz, lo que hace posible habilitar varias marcas experimentales en una única llamada al método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca llame al `SetFlags` método más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar marcas en la clase de la aplicación

El `Device.SetFlags` método se puede usar para habilitar una marca experimental en la `App` clase en el proyecto de código compartido:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

El `Device.SetFlags` método acepta un `IReadOnlyList<string>` argumento, lo que permite habilitar varias marcas experimentales en una única llamada al método:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca llame al `SetFlags` método más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.
