---
title: 'Guía detallada sobre los fragmentos: parte 2'
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 4d9ef88f39914f8fa5e578577ee9f6977c2bc88e
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020265"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Guía detallada sobre los fragmentos: horizontal

En la [Guía detallada sobre los fragmentos: parte 1](./walkthrough.md) se muestra cómo crear y usar fragmentos en una aplicación Android destinada a las pantallas más pequeñas de un teléfono. El siguiente paso de esta guía detallada es modificar la aplicación para aprovechar el espacio horizontal adicional de la tableta, habrá una actividad que siempre será la lista de reproducciones (`TitlesFragment`) y `PlayQuoteFragment` se agregará dinámicamente a la actividad en respuesta a una selección realizada por el usuario:

[![Aplicación que se ejecuta en una tableta](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Los teléfonos que se ejecutan en modo horizontal también se beneficiarán de esta mejora:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Actualización de la aplicación para controlar la orientación horizontal

Las siguientes modificaciones se basarán en el trabajo que se realizó en la [Guía detallada sobre los fragmentos: teléfono](./walkthrough.md)

1. Cree un diseño alternativo para mostrar los elementos `TitlesFragment` y `PlayQuoteFragment`.
1. Actualice `TitlesFragment` para detectar si el dispositivo muestra ambos fragmentos simultáneamente y cambie el comportamiento en consecuencia.
1. Actualice `PlayQuoteActivity` para que se cierre cuando el dispositivo esté en modo horizontal.

## <a name="1-create-an-alternate-layout"></a>1. Creación de un diseño alternativo

Cuando se crea la actividad principal en un dispositivo Android, Android decidirá qué diseño se debe cargar en función de la orientación del dispositivo. De forma predeterminada, Android proporcionará el archivo de diseño **Resources/layout/activity_main.axml**. En el caso de los dispositivos que se cargan en modo horizontal, Android proporcionará el archivo de diseño **Resources/layout-land/activity_main.axml**. La guía sobre los [recursos de Android](/xamarin/android/app-fundamentals/resources-in-android) contiene más detalles sobre cómo Android decide qué archivos de recursos se cargan para una aplicación.

Cree un diseño alternativo dirigido a la **orientación horizontal**, siguiendo los pasos descritos en la guía sobre los [diseños alternativos](/xamarin/android/user-interface/android-designer/alternative-layout-views). Con ello, se debería agregar un nuevo archivo de recursos de diseño al proyecto, **Resources/layout/activity_main.axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Diseño alternativo en el Explorador de soluciones](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Diseño alternativo en el Panel de solución](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Después de crear el diseño alternativo, edite el origen del archivo **Resources/layout-Land/activity_main.axml** para que coincida con este XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

A la vista raíz de la actividad se le asigna el id. de recurso `two_fragments_layout` y tiene dos subvistas, `fragment` y `FrameLayout`. Mientras que la vista `fragment` se carga estáticamente, `FrameLayout` actúa como un "marcador de posición" que se reemplazará en tiempo de ejecución por `PlayQuoteFragment`. Cada vez que se selecciona una nueva reproducción en `TitlesFragment`, `playquote_container` se actualizará con una nueva instancia de `PlayQuoteFragment`.

Cada una de las subvistas ocupará toda la altura de su vista primaria. Los atributos `android:layout_weight` y `android:layout_width` controlan el ancho de cada subvista. En este ejemplo, cada subvista ocupará el 50 % del ancho proporcionado por el elemento primario. Consulte el [documento de Google sobre el diseño lineal](https://developer.android.com/guide/topics/ui/layout/linear.html) para obtener más información sobre el _peso del diseño_.

## <a name="2-changes-to-titlesfragment"></a>2. Cambios en TitlesFragment

Una vez creado el diseño alternativo, es necesario actualizar `TitlesFragment`. Cuando la aplicación muestra los dos fragmentos en una actividad, `TitlesFragment` debe cargar `PlayQuoteFragment` en la actividad primaria. De lo contrario, `TitlesFragment` debe iniciar el elemento `PlayQuoteActivity` que hospeda el fragmento `PlayQuoteFragment`. Una marca booleana ayudará al fragmento `TitlesFragment` a determinar el comportamiento que debe utilizar. Esta marca se inicializará en el método `OnActivityCreated`.

En primer lugar, agregue una variable de instancia en la parte superior de la clase `TitlesFragment`:

```csharp
bool showingTwoFragments;
```

A continuación, agregue el siguiente fragmento de código a `OnActivityCreated` para inicializar la variable: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Si el dispositivo se está ejecutando en modo horizontal, el elemento `FrameLayout` con el id. de recurso `playquote_container` estará visible en la pantalla, por lo que `showingTwoFragments` se inicializará en `true`. Si el dispositivo se está ejecutando en modo vertical, el elemento `playquote_container` no estará en la pantalla, por lo que `showingTwoFragments` será `false`.

El método `ShowPlayQuote` deberá cambiar el modo en que se muestra una cita, en un fragmento o se inicia una nueva actividad.  Actualice el método `ShowPlayQuote` para cargar un fragmento cuando se muestren dos fragmentos; de lo contrario, debe iniciar una actividad:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Si el usuario ha seleccionado una reproducción diferente de la que se muestra actualmente en `PlayQuoteFragment`, se crea un nuevo fragmento `PlayQuoteFragment` que reemplazará el contenido de `playquote_container` en el contexto de un fragmento `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Código completo para TitlesFragment

Después de completar todos los cambios anteriores en `TitlesFragment`, la clase completa debe coincidir con este código:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. Cambios en PlayQuoteActivity

Hay un detalle final que se debe tener en cuenta: el elemento `PlayQuoteActivity` no es necesario cuando el dispositivo está en modo horizontal. Si el dispositivo está en modo horizontal, el elemento `PlayQuoteActivity` no estará visible. Actualice el método `OnCreate` de `PlayQuoteActivity` para que se cierre. Este código es la versión final de `PlayQuoteActivity.OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Esta modificación agrega una comprobación para la orientación del dispositivo. Si está en modo horizontal, el elemento `PlayQuoteActivity` se cerrará por sí mismo.

## <a name="4-run-the-application"></a>4. Ejecutar la aplicación

Una vez completados estos cambios, ejecute la aplicación, gire el dispositivo al modo horizontal (si es necesario) y, a continuación, seleccione una reproducción. La cita debe aparecer en la misma pantalla que la lista de reproducciones:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
