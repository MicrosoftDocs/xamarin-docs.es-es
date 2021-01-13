---
title: Xamarin.Forms marcas experimentales
description: Xamarin.Forms las marcas experimentales permiten al equipo de ingeniería enviar nuevas características a los usuarios con más rapidez, al mismo tiempo que se pueden cambiar las API de características antes de que pasen a una versión estable.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9f6f5e2d27f04dadfa2b830703bb36355072c44
ms.sourcegitcommit: c9f62ed4bab516be18606e87e89e01f57b4ad17e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163496"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Forms marcas experimentales

Cuando Xamarin.Forms se implementa una nueva característica, a veces se coloca detrás de una marca experimental. Esto permite al equipo de ingeniería proporcionar nuevas características con mayor rapidez, al tiempo que sigue pudiendo cambiar las API de características antes de que pasen a una versión estable. Después, se quita la marca experimental una vez que la característica se mueve a una versión estable.

Xamarin.Forms incluye las siguientes marcas experimentales:

- `Shell_UWP_Experimental`

El uso de la funcionalidad que está detrás de una marca experimental requiere habilitar la marca, o marcas, en la aplicación. Hay dos métodos para habilitar las marcas experimentales:

- Habilite la marca experimental en los proyectos de la plataforma.
- Habilite la marca experimental en la `App` clase.

> [!WARNING]
> Si se utiliza la funcionalidad que está detrás de una marca experimental, sin habilitar la marca, la aplicación producirá una excepción que indica qué marca debe estar habilitada.

## <a name="enable-flags-in-platform-projects"></a>Habilitar marcas en proyectos de plataforma

El `Xamarin.Forms.Forms.SetFlags` método se puede usar para habilitar una marca experimental en los proyectos de la plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

El `SetFlags` método debe invocarse en la `AppDelegate` clase en iOS, en la `MainActivity` clase en Android y en la `App` clase en UWP.

> [!IMPORTANT]
> La habilitación de una marca experimental en los proyectos de la plataforma debe realizarse antes de que `Forms.Init` se invoque el método.

El `Xamarin.Forms.Forms.SetFlags` método acepta un `string` argumento de matriz, lo que hace posible habilitar varias marcas experimentales en una única llamada al método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> Nunca llame al `SetFlags` método más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar marcas en la clase de la aplicación

El `Device.SetFlags` método se puede usar para habilitar una marca experimental en la `App` clase en el proyecto de código compartido:

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental" });
```

El `Device.SetFlags` método acepta un `IReadOnlyList<string>` argumento, lo que permite habilitar varias marcas experimentales en una única llamada al método:

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> Nunca llame al `SetFlags` método más de una vez, ya que las llamadas subsiguientes sobrescribirán el resultado de las llamadas anteriores.

## <a name="old-experimental-flags"></a>Marcas experimentales anteriores

En la tabla siguiente se enumeran las marcas experimentales de las características que ahora están disponibles con carácter general y la Xamarin.Forms versión en la que se quitó la marca experimental:

| Marca | Xamarin.Forms Emisión |
| ---- | --------------------- |
| `AppTheme_Experimental` | 4.8 |
| `Brush_Experimental` | 5.0 |
| `CarouselView_Experimental` | 5.0 |
| `CollectionView_Experimental` | 4.3 |
| `DragAndDrop_Experimental` | 5.0 |
| `FastRenderers_Experimental` | 4.0 |
| `IndicatorView_Experimental` | 4,7 |
| `Markup_Experimental` | 5,0 (se ha migrado al kit de herramientas de Xamarin Community) |
| `MediaElement_Experimental` | 5,0 (se ha migrado al kit de herramientas de Xamarin Community) |
| `RadioButton_Experimental` | 5.0 |
| `Shapes_Experimental` | 5.0 |
| `Shell_Experimental` | 4.0  |
| `StateTriggers_Experimental` | 4,7 |
| `SwipeView_Experimental` | 5.0 |
| `Visual_Experimental` | 3.6 |
