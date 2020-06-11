---
title: " Xamarin.Forms marcas experimentales" Descripción: " Xamarin.Forms las marcas experimentales permiten al equipo de ingeniería enviar nuevas características a los usuarios con más rapidez, al tiempo que se pueden cambiar las API de características antes de que pasen a una versión estable".
MS. Prod: Xamarin ms. AssetID: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/14/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-experimental-flags"></a>Xamarin.Formsmarcas experimentales

Cuando Xamarin.Forms se implementa una nueva característica, a veces se coloca detrás de una marca experimental. Esto permite al equipo de ingeniería proporcionar nuevas características con mayor rapidez, al tiempo que sigue pudiendo cambiar las API de características antes de que pasen a una versión estable. Después, se quita la marca experimental una vez que la característica se mueve a una versión estable.

Xamarin.Formsincluye las siguientes marcas experimentales:

- `AppTheme_Experimental`
- `CarouselView_Experimental`
- `Expander_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
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
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
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
