---
title: 'Guía detallada sobre los fragmentos: parte 2'
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 0363213d76d9a67b559614741edf37d296848075
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761654"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Panorama del &ndash; tutorial de fragmentos

En el [tutorial &ndash; de fragmentos, parte 1](./walkthrough.md) se muestra cómo crear y usar fragmentos en una aplicación Android destinada a pantallas más pequeñas en un teléfono. El siguiente paso de este tutorial es modificar la aplicación para aprovechar el espacio horizontal adicional en la tableta &ndash; , habrá una actividad que siempre será la lista de reproducciones (el `TitlesFragment`) y `PlayQuoteFragment` se agregará dinámicamente. a la actividad en respuesta a una selección realizada por el usuario:

[![Aplicación que se ejecuta en la tableta](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Los teléfonos que se ejecutan en modo horizontal también se beneficiarán de esta mejora:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Actualización de la aplicación para controlar la orientación horizontal

Las siguientes modificaciones se basarán en el trabajo que se realizó en los [fragmentos tutorial-teléfono](./walkthrough.md)

1. Cree un diseño alternativo para mostrar los `TitlesFragment` y. `PlayQuoteFragment`
1. Actualice `TitlesFragment` para detectar si el dispositivo muestra ambos fragmentos simultáneamente y cambiar el comportamiento en consecuencia.
1. Actualizar `PlayQuoteActivity` para cerrar cuando el dispositivo está en modo horizontal.

## <a name="1-create-an-alternate-layout"></a>1. Crear un diseño alternativo

Cuando se crea la actividad principal en un dispositivo Android, Android decidirá qué diseño se debe cargar en función de la orientación del dispositivo. De forma predeterminada, Android proporcionará el archivo de diseño **Resources/layout/activity_main. axml** . En el caso de los dispositivos que se cargan en modo horizontal, Android proporcionará el archivo de diseño **Resources/layout-Land/activity_main. axml** . La guía sobre [recursos de Android](/xamarin/android/app-fundamentals/resources-in-android) contiene más detalles sobre cómo Android decide qué archivos de recursos cargar para una aplicación.

Cree un diseño alternativo orientado a la orientación **horizontal** siguiendo los pasos descritos en la guía de [diseños alternativos](/xamarin/android/user-interface/android-designer/alternative-layout-views) . Esto debería agregar un nuevo archivo de recursos de diseño al proyecto, **Resources/layout/activity_main. axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Diseño alternativo en Explorador de soluciones](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Diseño alternativo en Panel de solución](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Después de crear el diseño alternativo, edite el origen del archivo **Resources/layout-Land/activity_main. axml** para que coincida con este XML:

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

La vista raíz de la actividad recibe el identificador `two_fragments_layout` de recurso y tiene dos subvistas `fragment` , y `FrameLayout`. Mientras se carga estáticamente `FrameLayout` , actúa como un "marcador de posición" que se reemplazará en tiempo de `PlayQuoteFragment`ejecución por. `fragment` Cada vez que se selecciona una nueva reproducción en `TitlesFragment`la `playquote_container` , se actualizará con `PlayQuoteFragment`una nueva instancia de.

Cada una de las subvistas ocupará el alto completo de su elemento primario. Los atributos `android:layout_weight` y `android:layout_width` controlan el ancho de cada subvista. En este ejemplo, cada subvista ocupará el 50% del ancho proporcionado por el elemento primario. Vea [el documento de Google en LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) para obtener más información sobre el _peso del diseño_.

## <a name="2-changes-to-titlesfragment"></a>2. Cambios en TitlesFragment

Una vez creado el diseño alternativo, es necesario actualizar `TitlesFragment`. Cuando la aplicación muestra los dos fragmentos en una actividad, `TitlesFragment` debe `PlayQuoteFragment` cargar en la actividad primaria. De lo `TitlesFragment` contrario, debe `PlayQuoteActivity` iniciar el que `PlayQuoteFragment`hospeda el. Una marca booleana le ayudará `TitlesFragment` a determinar el comportamiento que debe utilizar. Esta marca se inicializará en el `OnActivityCreated` método.

En primer lugar, agregue una variable de instancia en la `TitlesFragment` parte superior de la clase:

```csharp
bool showingTwoFragments;
```

A continuación, agregue el siguiente fragmento de `OnActivityCreated` código a para inicializar la variable: 

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

Si el dispositivo se está ejecutando en modo horizontal, el `FrameLayout` con el identificador `playquote_container` de recurso estará visible en la pantalla, por `showingTwoFragments` lo que se inicializará `true`en. Si el dispositivo se está ejecutando en modo vertical, `playquote_container` no aparecerá en la pantalla, por lo `showingTwoFragments` que `false`será.

El `ShowPlayQuote` método deberá cambiar cómo muestra una oferta &ndash; en un fragmento o iniciar una nueva actividad.  Actualice el `ShowPlayQuote` método para cargar un fragmento al mostrar dos fragmentos; de lo contrario, debe iniciar una actividad:

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

Si el usuario ha seleccionado una reproducción distinta de la que se `PlayQuoteFragment`muestra actualmente en, se crea un nuevo `PlayQuoteFragment` y `playquote_container` reemplazará el contenido de dentro del contexto de `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Código completo para TitlesFragment

Después de completar todos los cambios anteriores `TitlesFragment`en, la clase completa debe coincidir con este código:

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

Hay un detalle final que se debe tener en cuenta `PlayQuoteActivity` : no es necesario cuando el dispositivo está en modo horizontal. Si el dispositivo está en modo horizontal, `PlayQuoteActivity` el no debe estar visible. Actualice el `OnCreate` método de `PlayQuoteActivity` para que se cierre. Este código es la versión final de `PlayQuoteActivity.OnCreate`:

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

Esta modificación agrega una comprobación para la orientación del dispositivo. Si está en modo horizontal, `PlayQuoteActivity` se cerrará por sí mismo.

## <a name="4-run-the-application"></a>4. Ejecución de la aplicación

Una vez completados estos cambios, ejecute la aplicación, gire el dispositivo al modo horizontal (si es necesario) y, a continuación, seleccione una reproducción. La oferta debe aparecer en la misma pantalla que la lista de reproducciones:

[![Aplicación que se ejecuta en un teléfono Android en modo horizontal](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicación que se ejecuta en una tableta Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
