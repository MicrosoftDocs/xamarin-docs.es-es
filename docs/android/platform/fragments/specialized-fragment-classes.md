---
title: Clases de fragmentos especializadas
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 8d4dcedae6298d9a56ba52d4da3d081d4d69afe1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757561"
---
# <a name="specialized-fragment-classes"></a>Clases de fragmentos especializadas

La API de fragmentos proporciona otras subclases que encapsulan algunas de las funciones más comunes que se encuentran en las aplicaciones. Estas subclases son:

- **ListFragment** &ndash; Este fragmento se usa para mostrar una lista de elementos enlazados a un origen de objetos, como una matriz o un cursor.

- **DialogFragment** &ndash; Este fragmento se utiliza como contenedor alrededor de un cuadro de diálogo. El fragmento mostrará el cuadro de diálogo encima de su actividad.

- **PreferenceFragment** &ndash; Este fragmento se usa para mostrar los objetos de preferencias como listas.

## <a name="the-listfragment"></a>ListFragment

Es muy similar en concepto y funcionalidad `ListActivity`a; es un contenedor que hospeda un `ListView` en un fragmento. `ListFragment` La imagen siguiente muestra un `ListFragment` que se ejecuta en una tableta y un teléfono:

[![Capturas de pantallas de ListFragment en tabletas y en un teléfono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Enlazar datos con ListAdapter

La `ListFragment` clase ya proporciona un diseño predeterminado, por lo que no es necesario invalidar `OnCreateView` para `ListFragment`mostrar el contenido de. Se `ListView` enlaza a los datos mediante una `ListAdapter` implementación de. En el ejemplo siguiente se muestra cómo se puede hacer mediante una matriz simple de cadenas:

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

Al establecer `ListAdapter`, es importante usar la `ListFragment.ListAdapter` propiedad, y no la `ListView.ListAdapter` propiedad. El `ListView.ListAdapter` uso de hará que se omita el código de inicialización importante.

### <a name="responding-to-user-selection"></a>Responder a la selección del usuario

Para responder a las selecciones de usuario, una aplicación debe `OnListItemClick` reemplazar el método. En el ejemplo siguiente se muestra una de estas posibilidades:

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

*DialogFragment* es un fragmento que se usa para mostrar un objeto de cuadro de diálogo dentro de un fragmento que flotará en la parte superior de la ventana de la actividad. Está pensado para reemplazar las API de diálogo administradas (a partir de Android 3,0). En la captura de pantalla siguiente se muestra `DialogFragment`un ejemplo de:

[![Captura de pantalla de DialogFragment que muestra Add New Vehicle EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Un `DialogFragment` garantiza que el estado entre el fragmento y el cuadro de diálogo sigue siendo coherente. Todas las interacciones y el control del objeto de cuadro de `DialogFragment` diálogo se deben producir a través de la API y no se pueden realizar con llamadas directas en el objeto de cuadro de diálogo. La `DialogFragment` API proporciona a cada instancia un `Show()` método que se utiliza para mostrar un fragmento. Hay dos maneras de deshacerse de un fragmento:

- Llame `DialogFragment.Dismiss()` a en `DialogFragment` la instancia de. 

- Mostrar otro `DialogFragment`.

Para crear `DialogFragment`, una clase hereda de `Android.App.DialogFragment,` y, a continuación, reemplaza uno de los dos métodos siguientes:

- **OnCreateView** &ndash; Esto crea y devuelve una vista.

- **OnCreateDialog** &ndash; Esto crea un cuadro de diálogo personalizado. Normalmente se usa para mostrar un *AlertDialog*. Al reemplazar este método, no es necesario invalidar `OnCreateView` .

### <a name="a-simple-dialogfragment"></a>Un DialogFragment simple

En la captura de pantalla siguiente `DialogFragment` se muestra un `TextView` simple que `Button`tiene un y dos s:

[![Ejemplo de DialogFragment con un TextView y dos botones](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Mostrará el número de veces que el usuario hizo clic `DialogFragment`en un botón del, mientras que al hacer clic en el otro botón, se cerrará el fragmento. `TextView` El código de `DialogFragment` es:

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

Al igual que todos los `DialogFragment` fragmentos, se muestra en el contexto `FragmentTransaction`de un.

El `Show()` método en un `DialogFragment` toma `FragmentTransaction` y `string` como entrada. El cuadro de diálogo se agregará a la actividad y `FragmentTransaction` se confirmará.

En el código siguiente se muestra una posible manera de que una `Show()` actividad pueda usar el `DialogFragment`método para mostrar un:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Descartar un fragmento

La `Dismiss()` llamada a en una instancia `DialogFragment` de un provoca que se quite un fragmento de la actividad y confirme esa transacción.
Se llamará a los métodos de ciclo de vida de fragmento estándar que intervienen en la destrucción de un fragmento.

### <a name="alert-dialog"></a>Cuadro de diálogo de alerta

En lugar de reemplazar `OnCreateView` `DialogFragment` , en su lugar se puede invalidar `OnCreateDialog`. Esto permite que una aplicación cree un [AlertDialog](xref:Android.App.AlertDialog) administrado por un fragmento. El código siguiente es un ejemplo que usa `AlertDialog.Builder` para crear un: `Dialog`

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

Para ayudar a administrar las preferencias, la API de `PreferenceFragment` fragmentos proporciona la subclase. Es similar a [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; , que muestra una jerarquía de preferencias para el usuario en un fragmento. `PreferenceFragment` A medida que el usuario interactúe con las preferencias, se guardarán automáticamente en [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
En aplicaciones Android 3,0 o versiones posteriores, use `PreferenceFragment` para tratar con las preferencias de las aplicaciones. En la siguiente imagen se muestra un ejemplo `PreferenceFragment`de un:

[![Ejemplo de PreferencesFragment con preferencias de inicio, diálogo y en línea](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Crear un fragmento de preferencias a partir de un recurso

El fragmento de preferencias se puede inflar a partir de un archivo de recursos XML mediante el método [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . Un lugar lógico para llamar a este método en el ciclo de vida del fragmento sería en `OnCreate` el método.

La `PreferenceFragment` imagen anterior se creó cargando un recurso desde XML. El archivo de recursos es:

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

Otra técnica para crear un `PreferenceFragment` implica consultar las actividades. Cada actividad puede usar el atributo de [preferencia de\_clave\_de metadatos](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) que apuntará a un archivo de recursos XML. En Xamarin. Android, esto se hace mediante el uso de una etiqueta de `MetaDataAttribute`actividad con el y, a continuación, la especificación del archivo de recursos que se va a usar. La `PreferenceFragment` clase proporciona el método [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) que se puede usar para consultar una actividad para encontrar este recurso XML y aumentar una jerarquía de preferencias para él.

En el siguiente fragmento de código se proporciona un ejemplo de este proceso, que `AddPreferencesFromIntent` usa para crear `PreferenceFragment`un:

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

Android examinará la clase `MyActivityWithPreference`. La clase se debe adornar con `MetaDataAttribute,` como se muestra en el siguiente fragmento de código:

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

Declara un archivo de recursos XML `PreferenceFragment` que utilizará para inflar la jerarquía de preferencias. `MetaDataAttribute` Si no `MetatDataAttribute` se proporciona, se producirá una excepción en tiempo de ejecución. Cuando se ejecuta este código, `PreferenceFragment` aparece como en la siguiente captura de pantalla:

[![Captura de pantalla de la aplicación de ejemplo con PreferenceFragment mostrado](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
