---
title: Creación de un fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027501"
---
# <a name="creating-a-fragment"></a>Creación de un fragmento

Para crear un fragmento, una clase debe heredar de `Android.App.Fragment` y luego reemplazar el método `OnCreateView`. La actividad de hospedaje llamará a `OnCreateView` cuando sea el momento de poner el fragmento en la pantalla y devolverá `View`. Un método `OnCreateView` típico creará este `View` inflando un archivo de diseño y luego lo adjuntará a un contenedor principal. Las características del contenedor son importantes, ya que Android aplicará los parámetros de diseño del elemento primario a la interfaz de usuario del fragmento. Esto se ilustra en el siguiente ejemplo:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

El código anterior inflará la vista `Resource.Layout.Example_Fragment` y la agregará como una vista secundaria al contenedor `ViewGroup`.

> [!NOTE]
> Las subclases del fragmento deben tener un constructor público predeterminado que no tenga ningún parámetro.

## <a name="adding-a-fragment-to-an-activity"></a>Incorporación de un fragmento a una actividad

Hay dos maneras de hospedar un fragmento dentro de una actividad:

- **Mediante declaración**: los fragmentos se pueden usar mediante declaración en archivos de diseño `.axml` mediante la etiqueta `<Fragment>`.

- **Mediante programación**: también se pueden crear instancias de los fragmentos dinámicamente mediante la API de la clase `FragmentManager`.

El uso programático a través de la clase `FragmentManager` se tratará más adelante en esta guía.

### <a name="using-a-fragment-declaratively"></a>Uso de un fragmento mediante declaración

La incorporación de un fragmento dentro del diseño requiere el uso de la etiqueta `<fragment>` y luego la identificación del fragmento proporcionando el atributo `class` o el atributo `android:name`. En el siguiente fragmento de código se muestra cómo usar el atributo `class` para declarar `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

En el siguiente fragmento de código se muestra cómo declarar `fragment` mediante el atributo `android:name` para identificar la clase Fragment:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Cuando se crea la actividad, Android crea una instancia de cada fragmento especificado en el archivo de diseño e inserta la vista que se crea desde `OnCreateView` en el lugar del elemento `Fragment`.
Los fragmentos que se agregan mediante declaración a una actividad son estáticos y permanecerán en dicha actividad hasta que se destruya. No es posible reemplazar o quitar dinámicamente este fragmento mientras dura la actividad a la que está asociado.

Cada fragmento debe tener asignado un identificador único:

- **android:id**: como con otros elementos de la interfaz de usuario en un archivo de diseño, se trata de un identificador único.

- **android:tag**: este atributo es una cadena única.

Si no se usa ninguno de los dos métodos anteriores, el fragmento asumirá el identificador de la vista del contenedor. En el ejemplo siguiente, en el que no se proporciona ni `android:id` ni `android:tag`, Android asignará el identificador `fragment_container` al fragmento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Uso de mayúsculas y minúsculas en el nombre del paquete

Android no permite caracteres en mayúsculas en los nombres de paquete; se iniciará una excepción al intentar inflar la vista si un nombre de paquete contiene un carácter en mayúsculas. Sin embargo, Xamarin.Android es más permisivo y tolerará caracteres en mayúsculas en el espacio de nombres.

Por ejemplo, los dos fragmentos de código siguientes funcionarán con Xamarin.Android. Sin embargo, el segundo fragmento de código provocará que una aplicación Android pura basada en Java inicie `android.view.InflateException`.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

O

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Ciclo de vida de los fragmentos

Los fragmentos tienen su propio ciclo de vida que es en cierto modo independiente de, pero que todavía se ve afectado por, el [ciclo de vida de la actividad de hospedaje](~/android/app-fundamentals/activity-lifecycle/index.md).
Por ejemplo, cuando una actividad se pausa, todos sus fragmentos asociados se pausan. En el diagrama siguiente se describe el ciclo de vida del fragmento.

[![Diagrama de flujo que ilustra el ciclo de vida del fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de creación de fragmentos

La lista siguiente muestra el flujo de las distintas devoluciones de llamada en el ciclo de vida de un fragmento mientras se crea:

- **`OnInflate()`** &ndash; Se llama cuando el fragmento se crea como parte de un diseño de vista. Se puede llamar a este método inmediatamente después de que el fragmento se cree mediante declaración a partir de un archivo de diseño XML. El fragmento no está asociado todavía a su actividad, pero **Activity**, **Bundle** y **AttributeSet** de la jerarquía de la vista se pasan como parámetros. Este método se utiliza mejor para analizar **AttributeSet** y para guardar los atributos que el fragmento podría usar posteriormente.

- **`OnAttach()`** : se llama después de que el fragmento esté asociado a la actividad. Este es el primer método que se ejecutará cuando el fragmento esté listo para usarse. En general, los fragmentos no deben implementar un constructor ni reemplazar el constructor predeterminado. Los componentes necesarios para el fragmento se deben inicializar en este método.

- **`OnCreate()`** : la actividad lo llama para crear el fragmento. Cuando se llama a este método, es posible que no se pueda crear una instancia completa de la jerarquía de la vista de la actividad de hospedaje, por lo que el fragmento no debe basarse en ninguna parte de la jerarquía de la vista de la actividad hasta más adelante en el ciclo de vida del fragmento. Por ejemplo, no utilice este método para realizar cambios o ajustes en la interfaz de usuario de la aplicación. Esta es la primera vez que el fragmento puede comenzar a recopilar los datos que necesita. En este momento, el fragmento se está ejecutando en el subproceso de la interfaz de usuario, por lo que debe evitar un procesamiento largo o realizar ese procesamiento en un subproceso en segundo plano. Este método se puede omitir si se llama a **SetRetainInstance(true)** .
    Esta alternativa se describe con más detalle a continuación.

- **`OnCreateView()`** : crea la vista para el fragmento.
    Se llama a este método una vez que se ha completado el método **OnCreate()** de la actividad. En este punto, es seguro interactuar con la jerarquía de vistas de la actividad. Este método debe devolver la vista que el fragmento va a utilizar.

- **`OnActivityCreated()`** : se llama después de que la actividad de hospedaje haya completado **Activity.OnCreate**.
    Los ajustes finales en la interfaz de usuario deben realizarse en este momento.

- **`OnStart()`** : se llama después de que se reanude la actividad contenedora. Esto hace que el fragmento sea visible para el usuario. En muchos casos, el fragmento contendrá código que, de lo contrario, se encontraría en el método **OnStart()** de una actividad.

- **`OnResume()`** : este es el último método al que se llama antes de que el usuario pueda interactuar con el fragmento. Un ejemplo del tipo de código que se debe realizar en este método sería habilitar las características de un dispositivo con el que el usuario puede interactuar, como la cámara de los servicios de ubicación. Sin embargo, los servicios como estos pueden provocar un consumo excesivo de la batería, y una aplicación debe minimizar su uso para conservar la autonomía de la batería.

### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destrucción de fragmentos

En la siguiente lista se explican los métodos del ciclo de vida que se llaman cuando un Fragmento se destruye:

- **`OnPause()`** : el usuario ya no puede interactuar con el fragmento. Esta situación existe porque alguna otra operación de fragmento está modificando este fragmento o la actividad de hospedaje está en pausa. Es posible que la actividad que hospeda este fragmento todavía sea visible, es decir, que la actividad que tenga el foco sea parcialmente transparente o no ocupe toda la pantalla. Cuando este método se activa, es la primera indicación de que el usuario abandona el fragmento. El fragmento debe guardar los cambios.

- **`OnStop()`** : el fragmento ya no está visible. Es posible que la actividad de hospedaje esté detenida o que una operación de fragmento la esté modificando en la actividad. Esta devolución de llamada sirve para el mismo propósito que **Activity.OnStop**.

- **`OnDestroyView()`** : se llama a este método para limpiar los recursos asociados a la vista. Esto se llama cuando la vista asociada al fragmento se ha destruido.

- **`OnDestroy()`** : este método se llama cuando el fragmento ya no está en uso. Todavía está asociado a la actividad, pero el fragmento ya no funciona. Este método debería liberar todos los recursos que esté usando el fragmento, como [**SurfaceView**](xref:Android.Views.SurfaceView), que podría usarse para una cámara. Este método se puede omitir si se llama a **SetRetainInstance(true)** . Esta alternativa se describe con más detalle a continuación.

- **`OnDetach()`** : este método se llama justo antes de que el fragmento ya no esté asociado a la actividad. La jerarquía de la vista del fragmento ya no existe y todos los recursos que utiliza dicho fragmento deberían liberarse en este punto.

### <a name="using-setretaininstance"></a>Uso de SetRetainInstance

Es posible que un fragmento especifique que no debe ser completamente destruido si la actividad se está volviendo a crear. La clase `Fragment` proporciona el método `SetRetainInstance` para este fin. Si `true` se pasa a este método, cuando la Actividad se reinicie, se utilizará la misma instancia del fragmento. Si esto sucede, se invocarán todos los métodos de devolución de llamada, excepto las devoluciones de llamada del ciclo de vida `OnCreate` y `OnDestroy`. Este proceso se ilustra en el diagrama del ciclo de vida que se muestra arriba (por las líneas de puntos verdes).

## <a name="fragment-state-management"></a>Administración de estados de los fragmentos

Los fragmentos pueden guardar y restaurar su estado durante el ciclo de vida de los mismos mediante el uso de una instancia de `Bundle`. El elemento Bundle permite que un fragmento guarde datos como pares de clave/valor y es útil para datos simples que no requieren mucha memoria. Un fragmento puede guardar su estado con una llamada a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Cuando se crea una nueva instancia de un fragmento, el estado guardado en `Bundle` estará disponible para la nueva instancia a través de los métodos `OnCreate`, `OnCreateView` y `OnActivityCreated` de la nueva instancia.
En el ejemplo siguiente se muestra cómo recuperar el valor de `current_choice` a partir de `Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

Anular `OnSaveInstanceState` es un mecanismo apropiado para guardar datos transitorios en un fragmento a través de cambios de orientación, como el valor de `current_choice` en el ejemplo anterior. Sin embargo, la implementación predeterminada de `OnSaveInstanceState` se encarga de guardar los datos transitorios en la interfaz de usuario para cada vista que tiene un identificador asignado. Por ejemplo, observe una aplicación que tiene un elemento `EditText` definido en XML de la siguiente manera:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Dado que el control `EditText` tiene un `id` asignado, el fragmento guarda automáticamente los datos en el widget cuando se llama a `OnSaveInstanceState`.

### <a name="bundle-limitations"></a>Limitaciones de Bundle

Aunque el uso de `OnSaveInstanceState` facilita el almacenamiento de datos transitorios, el uso de este método tiene algunas limitaciones:

- Si el fragmento no se agrega a la pila posterior, entonces su estado no se restaurará cuando el usuario presione el botón **Atrás**.

- Cuando Bundle se usa para guardar datos, esos datos se serializan. Esto puede provocar demoras en el procesamiento.

## <a name="contributing-to-the-menu"></a>Contribuyendo al menú

Los fragmentos pueden aportar elementos al menú de su actividad de hospedaje.
Una actividad maneja los elementos del menú primero. Si la actividad no tiene un controlador, el evento se pasará al fragmento, que luego lo controlará.

Para agregar elementos al menú de la actividad, un fragmento debe hacer dos cosas.
En primer lugar, el fragmento debe implementar el método `OnCreateOptionsMenu` y colocar sus elementos en el menú, como se muestra en el siguiente código:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

El menú en el fragmento de código anterior se infla desde el siguiente código XML, ubicado en el archivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

A continuación, el fragmento debe llamar a `SetHasOptionsMenu(true)`. La llamada a este método anuncia a Android que el fragmento tiene elementos de menú para contribuir al menú de opciones. A menos que se realice la llamada a este método, los elementos de menú para el fragmento no se agregarán al menú de opciones de la actividad. Esto normalmente se realiza en el método de ciclo de vida `OnCreate()`, como se muestra en el siguiente fragmento de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

En la siguiente pantalla se muestra el aspecto que tendría este menú:

[![Captura de pantalla de ejemplo de la aplicación My Trips que muestra elementos de menú](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
