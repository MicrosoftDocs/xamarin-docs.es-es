---
title: Crear un fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 1948c700827f1cc235de5857cde9a2a149af8412
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524368"
---
# <a name="creating-a-fragment"></a>Crear un fragmento

Para crear un fragmento, una clase debe heredar `Android.App.Fragment` de y, a `OnCreateView` continuación, reemplazar el método. `OnCreateView`la actividad de hospedaje lo llamará cuando sea el momento de poner el fragmento en la pantalla y devolverá un `View`. Un típico `OnCreateView` creará esto `View` al inflar un archivo de diseño y, a continuación, adjuntarlo a un contenedor primario. Las características del contenedor son importantes, ya que Android aplicará los parámetros de diseño del elemento primario a la interfaz de usuario del fragmento. Esto se ilustra en el siguiente ejemplo:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

El código anterior impondrá la vista `Resource.Layout.Example_Fragment`y la agregará como una vista secundaria `ViewGroup` al contenedor.


> [!NOTE]
> Las subclases de fragmento deben tener un constructor público predeterminado sin argumentos.

## <a name="adding-a-fragment-to-an-activity"></a>Agregar un fragmento a una actividad

Hay dos maneras de hospedar un fragmento dentro de una actividad:

- **Mediante declaración** Los fragmentos se pueden usar mediante declaración `.axml` en los archivos de diseño `<Fragment>` mediante la etiqueta. &ndash;

- **Mediante programación** También se pueden crear instancias de los fragmentos dinámicamente mediante `FragmentManager` la API de la clase. &ndash;

El uso mediante programación `FragmentManager` a través de la clase se explicará más adelante en esta guía.

### <a name="using-a-fragment-declaratively"></a>Usar un fragmento mediante declaración

Agregar un fragmento dentro del diseño requiere el uso `<fragment>` de la etiqueta y, a continuación, la identificación `class` del fragmento proporcionando `android:name` el atributo o el atributo. En el fragmento de código siguiente se muestra `class` cómo usar el atributo `fragment`para declarar un:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

En el siguiente fragmento de código se muestra `fragment` cómo declarar un `android:name` mediante el atributo para identificar la clase Fragment:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Cuando se crea la actividad, Android creará una instancia de cada fragmento especificado en el archivo de diseño e insertará la vista que `OnCreateView` se crea a partir `Fragment` de en lugar del elemento.
Los fragmentos que se agregan mediante declaración a una actividad son estáticos y permanecerán en la actividad hasta que se destruyan. no es posible reemplazar o quitar dinámicamente este fragmento durante la duración de la actividad a la que está adjuntado.

Cada fragmento debe tener asignado un identificador único:

- **Android: ID.** &ndash; Como con otros elementos de la interfaz de usuario de un archivo de diseño, se trata de un identificador único.

- **Android: etiqueta** &ndash; Este atributo es una cadena única.

Si no se usa ninguno de los dos métodos anteriores, el fragmento asumirá el identificador de la vista del contenedor. En el siguiente ejemplo en el `android:id` que `android:tag` no se proporciona ni, Android asignará `fragment_container` el identificador al fragmento:

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

### <a name="package-name-case"></a>Caso de nombre del paquete

Android no permite caracteres en mayúsculas en los nombres de paquete; se producirá una excepción al intentar aumentar la vista si un nombre de paquete contiene un carácter en mayúsculas. Sin embargo, Xamarin. Android es más permisivo y tolerará caracteres en mayúsculas en el espacio de nombres.

Por ejemplo, los dos fragmentos de código siguientes funcionarán con Xamarin. Android. Sin embargo, el segundo fragmento de código `android.view.InflateException` hará que una aplicación Android basada en Java pura genere una.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo de vida de fragmentos

Los fragmentos tienen su propio ciclo de vida que es en cierto modo independiente del [ciclo de vida de la actividad de hospedaje](~/android/app-fundamentals/activity-lifecycle/index.md), pero que todavía se ve afectado por él.
Por ejemplo, cuando una actividad se pausa, todos sus fragmentos asociados están en pausa. En el diagrama siguiente se describe el ciclo de vida del fragmento.

[![Diagrama de flujo que ilustra el ciclo de vida de los fragmentos](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Métodos de ciclo de vida de creación de fragmentos

La lista siguiente muestra el flujo de las distintas devoluciones de llamada en el ciclo de vida de un fragmento mientras se crea:

- **`OnInflate()`** &ndash; Se llama cuando el fragmento se crea como parte de un diseño de vista. Se puede llamar a este método inmediatamente después de que el fragmento se cree mediante declaración a partir de un archivo de diseño XML. El fragmento no está asociado todavía a su actividad, pero la **actividad**, el **Grupo**y el objeto **AttributeSet** de la jerarquía de vistas se pasan como parámetros. Este método se utiliza mejor para analizar el **AttributeSet** y para guardar los atributos que se podrían usar posteriormente en el fragmento.

- **`OnAttach()`** &ndash; Se llama después de que el fragmento esté asociado a la actividad. Este es el primer método que se va a ejecutar cuando el fragmento esté listo para usarse. En general, los fragmentos no deben implementar un constructor ni reemplazar el constructor predeterminado. Los componentes necesarios para el fragmento se deben inicializar en este método.

- **`OnCreate()`** &ndash; Lo llama la actividad para crear el fragmento. Cuando se llama a este método, es posible que no se pueda crear una instancia completa de la jerarquía de vista de la actividad de hospedaje, por lo que el fragmento no debe basarse en ninguna parte de la jerarquía de vistas de la actividad hasta más adelante en el ciclo de vida del fragmento. Por ejemplo, no utilice este método para realizar ajustes o ajustes en la interfaz de usuario de la aplicación. Esta es la primera vez que el fragmento puede comenzar a recopilar los datos que necesita. En este momento, el fragmento se está ejecutando en el subproceso de la interfaz de usuario, por lo que debe evitar un procesamiento largo o realizar ese procesamiento en un subproceso en segundo plano. Este método se puede omitir si se llama a **SetRetainInstance (true)** .
    Esta alternativa se describe con más detalle a continuación.

- **`OnCreateView()`** &ndash; Crea la vista para el fragmento.
    Se llama a este método una vez que se completa el método **alcrear ()** de la actividad. En este momento, es seguro interactuar con la jerarquía de vistas de la actividad. Este método debe devolver la vista que va a utilizar el fragmento.

- **`OnActivityCreated()`** Se llama después de que la actividad de hospedaje haya completado la **actividad. he creado.** &ndash;
    Los ajustes finales en la interfaz de usuario deben realizarse en este momento.

- **`OnStart()`** &ndash; Se llama después de que se reanude la actividad contenedora. Esto hace que el fragmento sea visible para el usuario. En muchos casos, el fragmento contendrá código que, de lo contrario, se encontraría en el método **OnStart ()** de una actividad.

- **`OnResume()`** &ndash; Este es el último método llamado antes de que el usuario pueda interactuar con el fragmento. Un ejemplo del tipo de código que se debe realizar en este método sería habilitar las características de un dispositivo con el que el usuario puede interactuar, como la cámara que los servicios de ubicación. Sin embargo, los servicios como estos pueden provocar un consumo excesivo de la batería, y una aplicación debe minimizar su uso para conservar la duración de la batería.


### <a name="fragment-destruction-lifecycle-methods"></a>Métodos de ciclo de vida de destrucción de fragmentos

En la lista siguiente se explican los métodos de ciclo de vida a los que se llama como un fragmento que se está destruyendo:

- **`OnPause()`** &ndash; El usuario ya no puede interactuar con el fragmento. Esta situación existe porque alguna otra operación de fragmento está modificando este fragmento o la actividad de hospedaje está en pausa. Es posible que la actividad que hospeda este fragmento todavía esté visible, es decir, que la actividad en el foco sea parcialmente transparente o no ocupe toda la pantalla. Cuando este método se activa, es la primera indicación de que el usuario abandona el fragmento. El fragmento debe guardar los cambios.

- **`OnStop()`** &ndash; El fragmento ya no está visible. Es posible que se detenga la actividad del host o que una operación de fragmento la esté modificando en la actividad. Esta devolución de llamada sirve para el mismo propósito que **Activity. OnStop**.

- **`OnDestroyView()`** &ndash; Se llama a este método para limpiar los recursos asociados a la vista. Se llama a este método cuando se ha destruido la vista asociada al fragmento.

- **`OnDestroy()`** &ndash; Se llama a este método cuando el fragmento ya no está en uso. Todavía está asociado a la actividad, pero el fragmento ya no funciona. Este método debe liberar cualquier recurso que esté usando el fragmento, como un [**SurfaceView**](xref:Android.Views.SurfaceView) que se pueda usar para una cámara. Este método se puede omitir si se llama a **SetRetainInstance (true)** . Esta alternativa se describe con más detalle a continuación.

- **`OnDetach()`** &ndash; Se llama a este método justo antes de que el fragmento deje de estar asociado a la actividad. La jerarquía de vistas del fragmento ya no existe y todos los recursos utilizados por el fragmento deben liberarse en este momento.


### <a name="using-setretaininstance"></a>Usar SetRetainInstance

Es posible que un fragmento especifique que no se debe destruir completamente si se vuelve a crear la actividad. La `Fragment` clase proporciona el método `SetRetainInstance` para este fin. Si `true` se pasa a este método, cuando se reinicie la actividad, se usará la misma instancia del fragmento. Si esto ocurre, todos los métodos de devolución de llamada se invocarán `OnCreate` excepto `OnDestroy` las devoluciones de llamada y del ciclo de vida. Este proceso se ilustra en el diagrama del ciclo de vida mostrado anteriormente (por las líneas de puntos verdes).


## <a name="fragment-state-management"></a>Administración de estado de fragmentos

Los fragmentos pueden guardar y restaurar su estado durante el ciclo de vida de los fragmentos mediante `Bundle`el uso de una instancia de. La agrupación permite que un fragmento guarde los datos como pares clave-valor y resulta útil para datos simples que no requieren mucha memoria. Un fragmento puede guardar su estado con una llamada a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Cuando se crea una nueva instancia de un fragmento, el estado guardado en el `Bundle` estará disponible para la nueva instancia a través de `OnCreate`los `OnCreateView`métodos, `OnActivityCreated` y de la nueva instancia.
En el ejemplo siguiente se muestra cómo recuperar el `current_choice` valor `Bundle`de:

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

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar datos transitorios en un fragmento a través de cambios de orientación, `current_choice` como el valor del ejemplo anterior. Sin embargo, la implementación predeterminada `OnSaveInstanceState` de se encarga de guardar los datos transitorios en la interfaz de usuario para cada vista que tenga un identificador asignado. Por ejemplo, examine una aplicación que tenga un `EditText` elemento definido en XML como se indica a continuación:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Puesto que `EditText` el control tiene `id` un asignado, el fragmento guarda automáticamente los datos en el widget `OnSaveInstanceState` cuando se llama a.


### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque el `OnSaveInstanceState` uso de facilita el almacenamiento de datos transitorios, el uso de este método tiene algunas limitaciones:

- Si el fragmento no se agrega a la pila de retroceso, su estado no se restaurará cuando el usuario presione el botón **atrás** .

- Cuando se utiliza la agrupación para guardar los datos, se serializan. Esto puede dar lugar a retrasos en el procesamiento.


## <a name="contributing-to-the-menu"></a>Contribución al menú

Los fragmentos pueden contribuir con elementos al menú de su actividad de hospedaje.
Una actividad trata primero los elementos de menú. Si la actividad no tiene un controlador, el evento se pasará al fragmento, que lo controlará.

Para agregar elementos al menú de la actividad, un fragmento debe hacer dos cosas.
En primer lugar, el fragmento debe implementar `OnCreateOptionsMenu` el método y colocar sus elementos en el menú, tal y como se muestra en el código siguiente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

El menú del fragmento de código anterior se infla a partir del siguiente XML, ubicado en el archivo `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

A continuación, el fragmento debe `SetHasOptionsMenu(true)`llamar a. La llamada a este método anuncia a Android que el fragmento tiene elementos de menú para contribuir al menú de opciones. A menos que se realice la llamada a este método, no se agregarán los elementos de menú para el fragmento al menú de opciones de la actividad. Esto se hace normalmente en el método `OnCreate()`de ciclo de vida, tal y como se muestra en el siguiente fragmento de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

En la pantalla siguiente se muestra el aspecto de este menú:

[![Captura de pantalla de ejemplo de la aplicación mis viajes que muestra los elementos de menú](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
