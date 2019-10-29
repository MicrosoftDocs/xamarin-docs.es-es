---
title: Clases de fragmentos especializadas
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027311"
---
# <a name="specialized-fragment-classes"></a>Clases de fragmentos especializadas

La API de fragmentos proporciona otras subclases que encapsulan algunas de las funciones más comunes que se encuentran en las aplicaciones. Estas subclases son:

- **ListFragment** &ndash; este fragmento se usa para mostrar una lista de elementos enlazados a un origen de objetos como una matriz o un cursor.

- **DialogFragment** &ndash; este fragmento se usa como un contenedor alrededor de un cuadro de diálogo. El fragmento mostrará el cuadro de diálogo encima de su actividad.

- **PreferenceFragment** &ndash; este fragmento se usa para mostrar los objetos de preferencias como listas.

## <a name="the-listfragment"></a>ListFragment

La `ListFragment` es muy similar en concepto y funcionalidad al `ListActivity`; es un contenedor que hospeda una `ListView` en un fragmento. La imagen siguiente muestra un `ListFragment` que se ejecuta en una tableta y un teléfono:

[![capturas de pantallas de ListFragment en tabletas y en un teléfono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Enlazar datos con ListAdapter

La clase `ListFragment` ya proporciona un diseño predeterminado, por lo que no es necesario invalidar `OnCreateView` para mostrar el contenido de la `ListFragment`. El `ListView` se enlaza a los datos mediante una implementación de `ListAdapter`. En el ejemplo siguiente se muestra cómo se puede hacer mediante una matriz simple de cadenas:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

Al establecer el `ListAdapter`, es importante usar la propiedad `ListFragment.ListAdapter` y no la propiedad `ListView.ListAdapter`. El uso de `ListView.ListAdapter` hará que se omita el código de inicialización importante.

### <a name="responding-to-user-selection"></a>Responder a la selección del usuario

Para responder a las selecciones de usuario, una aplicación debe reemplazar el método `OnListItemClick`. En el ejemplo siguiente se muestra una de estas posibilidades:

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

En el código anterior, cuando el usuario selecciona un elemento en el `ListFragment`, se muestra un nuevo fragmento en la actividad de hospedaje, que muestra más detalles sobre el elemento seleccionado.

## <a name="dialogfragment"></a>DialogFragment

*DialogFragment* es un fragmento que se usa para mostrar un objeto de cuadro de diálogo dentro de un fragmento que flotará en la parte superior de la ventana de la actividad. Está pensado para reemplazar las API de diálogo administradas (a partir de Android 3,0). En la captura de pantalla siguiente se muestra un ejemplo de un `DialogFragment`:

[![captura de pantalla de DialogFragment que muestra Add New Vehicle EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Un `DialogFragment` garantiza que el estado entre el fragmento y el cuadro de diálogo sigue siendo coherente. Todas las interacciones y el control del objeto de cuadro de diálogo se deben producir a través de la API de `DialogFragment` y no se pueden realizar con llamadas directas en el objeto de cuadro de diálogo. La API de `DialogFragment` proporciona cada instancia de con un método `Show()` que se usa para mostrar un fragmento. Hay dos maneras de deshacerse de un fragmento:

- Llame a `DialogFragment.Dismiss()` en la instancia de `DialogFragment`. 

- Mostrar otro `DialogFragment`.

Para crear un `DialogFragment`, una clase hereda de `Android.App.DialogFragment,` y, a continuación, reemplaza uno de los dos métodos siguientes:

- **OnCreateView** &ndash; crea y devuelve una vista.

- **OnCreateDialog** &ndash; crea un cuadro de diálogo personalizado. Normalmente se usa para mostrar un *AlertDialog*. Al reemplazar este método, no es necesario invalidar `OnCreateView`.

### <a name="a-simple-dialogfragment"></a>Un DialogFragment simple

En la captura de pantalla siguiente se muestra un `DialogFragment` simple que tiene un `TextView` y dos `Button`s:

[![ejemplo DialogFragment con un TextView y dos botones](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

El `TextView` mostrará el número de veces que el usuario hizo clic en un botón del `DialogFragment`, mientras que al hacer clic en el otro botón se cerrará el fragmento. El código para `DialogFragment` es:

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```

### <a name="displaying-a-fragment"></a>Mostrar un fragmento

Al igual que todos los fragmentos, se muestra un `DialogFragment` en el contexto de un `FragmentTransaction`.

El método `Show()` de una `DialogFragment` toma un `FragmentTransaction` y un `string` como entrada. El cuadro de diálogo se agregará a la actividad y el `FragmentTransaction` confirmará.

En el código siguiente se muestra una posible manera de que una actividad pueda usar el método `Show()` para mostrar un `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Descartar un fragmento

La llamada a `Dismiss()` en una instancia de un `DialogFragment` hace que se quite un fragmento de la actividad y confirme esa transacción.
Se llamará a los métodos de ciclo de vida de fragmento estándar que intervienen en la destrucción de un fragmento.

### <a name="alert-dialog"></a>Cuadro de diálogo de alerta

En lugar de reemplazar `OnCreateView`, una `DialogFragment` puede invalidar `OnCreateDialog`. Esto permite que una aplicación cree un [AlertDialog](xref:Android.App.AlertDialog) administrado por un fragmento. El código siguiente es un ejemplo que usa la `AlertDialog.Builder` para crear un `Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```

## <a name="preferencefragment"></a>PreferenceFragment

Para ayudar a administrar las preferencias, la API de fragmentos proporciona la subclase `PreferenceFragment`. El `PreferenceFragment` es similar al [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; muestra una jerarquía de preferencias para el usuario en un fragmento. A medida que el usuario interactúe con las preferencias, se guardarán automáticamente en [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
En las aplicaciones Android 3,0 o superior, use la `PreferenceFragment` para tratar con las preferencias de las aplicaciones. En la siguiente imagen se muestra un ejemplo de un `PreferenceFragment`:

[![ejemplo de PreferencesFragment con las preferencias de inicio, diálogo y en línea](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Crear un fragmento de preferencias a partir de un recurso

El fragmento de preferencias se puede inflar a partir de un archivo de recursos XML mediante el método [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . Un lugar lógico para llamar a este método en el ciclo de vida del fragmento sería en el método `OnCreate`.

El `PreferenceFragment` imagen anterior se creó cargando un recurso desde XML. El archivo de recursos es:

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

El código para el fragmento de preferencias es el siguiente:

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```

### <a name="querying-activities-to-create-a-preference-fragment"></a>Consultar actividades para crear un fragmento de preferencias

Otra técnica para crear una `PreferenceFragment` implica la consulta de actividades. Cada actividad puede usar el atributo de [preferencia\_de metadatos\_clave](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) que apuntará a un archivo de recursos XML. En Xamarin. Android, esto se hace mediante la etiquetación de una actividad con el `MetaDataAttribute`y, a continuación, la especificación del archivo de recursos que se va a usar. La clase `PreferenceFragment` proporciona el método [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) que se puede usar para consultar una actividad con el fin de buscar este recurso XML y aumentar una jerarquía de preferencias.

En el siguiente fragmento de código se proporciona un ejemplo de este proceso, que usa `AddPreferencesFromIntent` para crear un `PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android examinará la `MyActivityWithPreference`de clase. La clase se debe adornar con el `MetaDataAttribute,` como se muestra en el siguiente fragmento de código:

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

El `MetaDataAttribute` declara un archivo de recursos XML que utilizará el `PreferenceFragment` para inflar la jerarquía de preferencias. Si no se proporciona el `MetatDataAttribute`, se producirá una excepción en tiempo de ejecución. Cuando se ejecuta este código, el `PreferenceFragment` aparece como en la siguiente captura de pantalla:

[![captura de pantalla de la aplicación de ejemplo con PreferenceFragment mostrado](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
