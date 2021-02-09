---
title: Ciclo de vida de la actividad
description: Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en varios Estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción, e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento del ciclo de vida adecuado, que notifica a la actividad el cambio de estado inminente y le permite ejecutar el código para adaptarse a ese cambio. En este artículo se examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para que formen parte de una aplicación con un comportamiento correcto y confiable.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: d01e32fcd710174aa458a749a9ede67e00301087
ms.sourcegitcommit: 877965466ae58a6821d9d16871de1013895fd083
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987530"
---
# <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

_Las actividades son un bloque de creación fundamental de las aplicaciones de Android y pueden existir en varios Estados diferentes. El ciclo de vida de la actividad comienza con la creación de instancias y termina con la destrucción, e incluye muchos Estados entre ellos. Cuando una actividad cambia de estado, se llama al método de evento del ciclo de vida adecuado, que notifica a la actividad el cambio de estado inminente y le permite ejecutar el código para adaptarse a ese cambio. En este artículo se examina el ciclo de vida de las actividades y se explica la responsabilidad que tiene una actividad durante cada uno de estos cambios de estado para que formen parte de una aplicación con un comportamiento correcto y confiable._

## <a name="activity-lifecycle-overview"></a>Información general del ciclo de vida de actividad

Las actividades son un concepto de programación inusual específico de Android. En el desarrollo de aplicaciones tradicional, normalmente hay un método Main estático, que se ejecuta para iniciar la aplicación. Con Android, sin embargo, las cosas son diferentes; Las aplicaciones de Android se pueden iniciar a través de cualquier actividad registrada en una aplicación. En la práctica, la mayoría de las aplicaciones solo tendrán una actividad específica que se especifica como el punto de entrada de la aplicación. Sin embargo, si una aplicación se bloquea, o el sistema operativo finaliza, puede intentar reiniciar la aplicación en la última actividad abierta o en cualquier otra parte de la pila de actividades anterior.
Además, el sistema operativo puede pausar actividades cuando no están activas y reclamarlas si tiene poca memoria. Se debe tener cuidado a la hora de permitir que la aplicación restaure correctamente su estado en caso de que se reinicie una actividad, especialmente si esa actividad depende de datos de actividades anteriores.

El ciclo de vida de la actividad se implementa como una colección de métodos que el sistema operativo llama a lo largo del ciclo de vida de una actividad. Estos métodos permiten a los desarrolladores implementar la funcionalidad necesaria para satisfacer los requisitos de administración de estado y recursos de sus aplicaciones.

Es extremadamente importante que el desarrollador de la aplicación analice los requisitos de cada actividad para determinar qué métodos expuestos por el ciclo de vida de la actividad deben implementarse. Si no lo hace, se puede producir inestabilidad en la aplicación, bloqueos, saturación de recursos y, posiblemente, la inestabilidad subyacente del sistema operativo.

En este capítulo se examina el ciclo de vida de la actividad en detalle, incluidos:

- Estados de actividad
- Métodos de ciclo de vida
- Conservar el estado de una aplicación

En esta sección también se incluye un [tutorial](~/android/app-fundamentals/activity-lifecycle/saving-state.md) que proporciona ejemplos prácticos sobre cómo guardar el estado de forma eficaz durante el ciclo de vida de la actividad. Al final de este capítulo, debe comprender el ciclo de vida de la actividad y cómo admitirlo en una aplicación Android.

## <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

El ciclo de vida de la actividad de Android comprende una colección de métodos que se exponen en la clase de actividad que proporcionan al desarrollador un marco de administración de recursos. Este marco de trabajo permite a los desarrolladores cumplir los requisitos de administración de estado únicos de cada actividad dentro de una aplicación y administrar correctamente la administración de recursos.

### <a name="activity-states"></a>Estados de actividad

El sistema operativo Android arbitra las actividades en función de su estado. Esto ayuda a Android a identificar las actividades que ya no se usan, lo que permite al sistema operativo reclamar la memoria y los recursos. En el diagrama siguiente se muestran los Estados en los que puede pasar una actividad durante su vigencia:

[![Diagrama de Estados de actividad](images/image1-sml.png)](images/image1.png#lightbox)

Estos Estados se pueden dividir en cuatro grupos principales como se indica a continuación:

1. *Activo o en ejecución* &ndash; Las actividades se consideran activas o en ejecución si están en primer plano, también conocidas como la parte superior de la pila de actividades. Esto se considera la actividad de prioridad más alta en Android y, como tal, solo la eliminará el sistema operativo en situaciones extremas, por ejemplo, si la actividad intenta usar más memoria que la disponible en el dispositivo, ya que esto podría hacer que la interfaz de usuario dejara de responder.

1. En *pausa* &ndash; Cuando el dispositivo entra en suspensión, o una actividad todavía está visible pero está parcialmente oculta por una actividad nueva, que no es de tamaño completo o transparente, se considera que la actividad está en pausa. Las actividades en pausa están todavía activas, es decir, mantienen toda la información sobre el estado y los miembros y permanecen adjuntas al administrador de ventanas. Se considera que es la segunda actividad de mayor prioridad en Android y, como tal, solo la eliminará el sistema operativo Si el sacrificio de esta actividad satisfará los requisitos de recursos necesarios para mantener la actividad activa/en ejecución estable y con capacidad de respuesta.

1. *Detenido o en segundo plano* &ndash; Las actividades ocultas completamente por otra actividad se consideran detenidas o en segundo plano.
    Las actividades detenidas siguen intentando conservar su estado y la información de los miembros siempre que sea posible, pero las actividades detenidas se consideran la prioridad más baja de los tres Estados y, como tal, el sistema operativo eliminará primero las actividades en este estado para satisfacer los requisitos de recursos de actividades de mayor prioridad.

1. *Reiniciado* &ndash; Es posible que Android Quite de la memoria una actividad que esté en cualquier lugar en pausa en el ciclo de vida. Si el usuario vuelve a la actividad, se debe reiniciar, restaurar a su estado guardado previamente y, a continuación, mostrar al usuario.

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Re-Creation de actividad en respuesta a los cambios de configuración

Para que las cuestiones sean más complicadas, Android lanza una llave más en la combinación llamada cambios de configuración. Los cambios de configuración son ciclos de destrucción y creación de una actividad rápida que se producen cuando cambia la configuración de una actividad. por ejemplo, cuando se [gira](~/android/app-fundamentals/handling-rotation.md) el dispositivo (y la actividad debe volver a generarse en modo horizontal o vertical), cuando se muestra el teclado (y la actividad se presenta con una oportunidad de cambiar el tamaño) o cuando el dispositivo se coloca en un Dock, entre otros.

Los cambios de configuración siguen provocando los mismos cambios de estado de actividad que se producirían durante la detención y el reinicio de una actividad. Sin embargo, para asegurarse de que una aplicación se comporta correctamente y funciona bien durante los cambios de configuración, es importante que se controlen lo más rápido posible. Por este motivo, Android tiene una API específica que se puede usar para conservar el estado durante los cambios de configuración.
Esto se tratará más adelante en el [Estado de administración a lo largo del ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) .

### <a name="activity-lifecycle-methods"></a>Métodos de ciclo de vida de actividad

Los Android SDK y, por extensión, el marco de Xamarin. Android proporcionan un modelo eficaz para administrar el estado de las actividades dentro de una aplicación. Cuando el estado de una actividad cambia, el sistema operativo notifica la actividad, que llama a métodos específicos en esa actividad. En el diagrama siguiente se muestran estos métodos en relación con el ciclo de vida de la actividad:

[![Diagrama de flujo del ciclo de vida](images/image2-sml.png)](images/image2.png#lightbox)

Como desarrollador, puede controlar los cambios de estado invalidando estos métodos dentro de una actividad. Sin embargo, es importante tener en cuenta que todos los métodos del ciclo de vida se llaman en el subproceso de la interfaz de usuario y impedirán que el sistema operativo realice la siguiente parte del trabajo de la interfaz de usuario, como ocultar la actividad actual, mostrar una nueva actividad, etc. Como tal, el código de estos métodos debe ser lo más breve posible para que una aplicación funcione correctamente. Las tareas de ejecución prolongada deben ejecutarse en un subproceso en segundo plano.

Vamos a examinar cada uno de estos métodos de ciclo de vida y su uso:

#### <a name="oncreate"></a>OnCreate

[Alcrear](xref:Android.App.Activity.OnCreate*) es el primer método al que se llama cuando se crea una actividad.
`OnCreate` siempre se invalida para realizar cualquier inicialización de inicio que puede ser necesaria para una actividad como la siguiente:

- Crear vistas
- Inicializar variables
- Enlazar datos estáticos a listas

`OnCreate` toma un parámetro de [agrupación](xref:Android.OS.Bundle) , que es un diccionario para almacenar y pasar información de estado y objetos entre actividades si la agrupación no es null, lo que indica que la actividad se está reiniciando y debe restaurar su estado desde la instancia anterior. En el código siguiente se muestra cómo recuperar valores de la agrupación:

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

Una vez `OnCreate` finalizado, Android llamará a `OnStart` .

#### <a name="onstart"></a>OnStart

El sistema siempre llama a [OnStart](xref:Android.App.Activity.OnStart) una vez `OnCreate` finalizada. Las actividades pueden invalidar este método si necesitan realizar cualquier tarea específica justo antes de que una actividad sea visible, como actualizar los valores actuales de las vistas dentro de la actividad. Android llamará `OnResume` inmediatamente después de este método.

#### <a name="onresume"></a>Alnude

El sistema llama a [Alreanudar](xref:Android.App.Activity.OnResume) cuando la actividad está lista para empezar a interactuar con el usuario.
Las actividades deben invalidar este método para realizar tareas como las siguientes:

- Rampa de velocidades de fotogramas (una tarea común en el desarrollo de juegos)
- Animaciones iniciales
- Escuchando actualizaciones de GPS
- Mostrar cualquier alerta o cuadro de diálogo relevante
- Conectar controladores de eventos externos

Por ejemplo, en el siguiente fragmento de código se muestra cómo inicializar la cámara:

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` es importante porque las operaciones que se realizan en `OnPause` deben deshacerse en `OnResume` , ya que es el único método de ciclo de vida que se garantiza que se ejecute después de que `OnPause` la actividad vuelva a la vida.

#### <a name="onpause"></a>Alpausar

Se llama a [OnPause](xref:Android.App.Activity.OnPause) cuando el sistema está a punto de colocar la actividad en segundo plano o cuando la actividad se oculta parcialmente. Las actividades deben invalidar este método si necesitan:

- Confirmar los cambios no guardados en los datos persistentes

- Destruir o limpiar otros objetos que consumen recursos

- Aumentar las velocidades de fotogramas y pausar animaciones

- Anular el registro de controladores de eventos externos o controladores de notificación (es decir, los que están vinculados a un servicio). Esto se debe hacer para evitar pérdidas de memoria de la actividad.

- Del mismo modo, si la actividad ha mostrado cuadros de diálogo o alertas, deben limpiarse con el `.Dismiss()` método.

Como ejemplo, el siguiente fragmento de código liberará la cámara, ya que la actividad no puede hacer uso de ella mientras está en pausa:

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

Hay dos métodos de ciclo de vida posibles a los que se llamará después de `OnPause` :

1. `OnResume` se llamará a si la actividad se va a devolver al primer plano.
1. `OnStop` se llamará a si la actividad se coloca en segundo plano.

#### <a name="onstop"></a>OnStop

Se llama a [OnStop](xref:Android.App.Activity.OnStop) cuando la actividad ya no es visible para el usuario. Esto sucede cuando se produce uno de los siguientes casos:

- Se está iniciando una nueva actividad y está cubriendo esta actividad.
- Una actividad existente se pone en primer plano.
- Se está destruyendo la actividad.

`OnStop` no siempre se puede llamar en situaciones de memoria insuficiente, como cuando Android se queda sin recursos y no puede almacenar en segundo plano la actividad. Por esta razón, es mejor no depender de la `OnStop` llamada a cuando se prepara una actividad para la destrucción. Los siguientes métodos de ciclo de vida a los que se puede llamar después de este se producirán `OnDestroy` si la actividad deja de estar disponible, o `OnRestart` si la actividad se va a devolver para interactuar con el usuario.

#### <a name="ondestroy"></a>Destruir

[Aldestroy](xref:Android.App.Activity.OnDestroy) es el método final al que se llama en una instancia de actividad antes de que se destruya y se quite por completo de la memoria. En situaciones extremas, Android puede terminar el proceso de aplicación que hospeda la actividad, lo que provocará que `OnDestroy` no se invoque. La mayoría de las actividades no implementarán este método, ya que la mayoría de las operaciones de limpieza y cierre se han realizado en los `OnPause` `OnStop` métodos y. `OnDestroy`Normalmente, el método se invalida para limpiar las tareas de ejecución prolongada que podrían perder recursos. Un ejemplo de esto podría ser subprocesos en segundo plano que se iniciaron en `OnCreate` .

No habrá ningún método de ciclo de vida llamado después de que se haya destruido la actividad.

#### <a name="onrestart"></a>Reiniciar

El [reinicio](xref:Android.App.Activity.OnRestart) se llama después de que se haya detenido la actividad antes de que se vuelva a iniciar. Un buen ejemplo sería cuando el usuario presiona el botón Inicio mientras está en una actividad de la aplicación. Cuando esto ocurre `OnPause` y se `OnStop` llama a los métodos, y la actividad se mueve al fondo, pero no se destruye. Si después el usuario restaurara la aplicación mediante el administrador de tareas o una aplicación similar, Android llamará al `OnRestart` método de la actividad.

No hay ninguna directriz general para el tipo de lógica que se debe implementar en `OnRestart` . Esto se debe `OnStart` a que siempre se invoca con independencia de si la actividad se crea o se reinicia, por lo que todos los recursos necesarios para la actividad se deben inicializar en `OnStart` , en lugar de `OnRestart` .

El siguiente método de ciclo de vida llamado después de `OnRestart` será `OnStart` .

### <a name="back-vs-home"></a>Atrás frente a Inicio

Muchos dispositivos Android tienen dos botones distintos: un botón "atrás" y un botón "Inicio". Un ejemplo de esto puede verse en la siguiente captura de pantalla de Android 4.0.3:

[![Botones atrás y Inicio](images/image4-sml.png)](images/image4.png#lightbox)

Hay una diferencia sutil entre los dos botones, aunque parezca que tienen el mismo efecto de colocar una aplicación en segundo plano. Cuando un usuario hace clic en el botón atrás, indica a Android que se ha hecho con la actividad. Android destruirá la actividad. Por el contrario, cuando el usuario hace clic en el botón Inicio, la actividad se coloca simplemente en el Android en segundo plano &ndash; no eliminará la actividad.

<a name="Managing_State_Throughout_the_Lifecycle"></a>

## <a name="managing-state-throughout-the-lifecycle"></a>Administración del estado a lo largo del ciclo de vida

Cuando una actividad se detiene o se destruye, el sistema ofrece la oportunidad de guardar el estado de la actividad para su posterior rehidratación.
Este estado guardado se conoce como estado de instancia. Android proporciona tres opciones para almacenar el estado de la instancia durante el ciclo de vida de la actividad:

1. Almacenar valores primitivos en un `Dictionary` conocido como [agrupación](xref:Android.OS.Bundle) que Android usará para guardar el estado.

1. Crear una clase personalizada que contenga valores complejos como mapas de bits. Android usará esta clase personalizada para guardar el estado.

1. Elusión del ciclo de vida de los cambios de configuración y suponiendo la responsabilidad completa de mantener el estado en la actividad.

En esta guía se describen las dos primeras opciones.

### <a name="bundle-state"></a>Estado del paquete

La opción principal para guardar el estado de la instancia es usar un objeto de Diccionario de clave/valor conocido como [agrupación](xref:Android.OS.Bundle).
Recuerde que cuando se crea una actividad que el `OnCreate` método pasa una agrupación como parámetro, este paquete se puede usar para restaurar el estado de la instancia. No se recomienda usar un paquete para datos más complejos que no se serialicen rápida o fácilmente en pares de clave y valor (como mapas de bits). en su lugar, debe usarse para valores simples como cadenas.

Una actividad proporciona métodos para ayudarle a guardar y recuperar el estado de la instancia en la agrupación:

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; Lo invoca Android cuando se destruye la actividad. Las actividades pueden implementar este método si necesitan conservar cualquier elemento de estado de clave/valor.

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash; Se llama a esta función una vez `OnCreate` finalizado el método y proporciona otra oportunidad para que una actividad restaure su estado una vez completada la inicialización.

En el diagrama siguiente se muestra cómo se usan estos métodos:

[![Diagrama de flujo de Estados de agrupación](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

Se llamará a [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) cuando se detenga la actividad. Recibirá un parámetro de agrupación en el que la actividad puede almacenar su estado. Cuando un dispositivo experimenta un cambio de configuración, una actividad puede usar el `Bundle` objeto que se pasa para conservar el estado de actividad invalidando `OnSaveInstanceState` . Por ejemplo, considere el siguiente código:

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

El código anterior incrementa un entero denominado `c` cuando `incrementCounter` se hace clic en un botón denominado y muestra el resultado en un `TextView` denominado `output` . Cuando se produce un cambio de configuración, por ejemplo, cuando se gira el dispositivo, el código anterior perdería el valor de `c` porque `bundle` sería `null` , tal y como se muestra en la ilustración siguiente:

[![Mostrar no muestra el valor anterior](images/07-sml.png)](images/07.png#lightbox)

Para conservar el valor de `c` en este ejemplo, la actividad puede invalidarse `OnSaveInstanceState` , guardando el valor en la agrupación como se muestra a continuación:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ahora, cuando el dispositivo se gira a una nueva orientación, el entero se guarda en la agrupación y se recupera con la línea:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> Es importante llamar siempre a la implementación base de `OnSaveInstanceState` para que también se pueda guardar el estado de la jerarquía de vistas.

##### <a name="view-state"></a>Estado de vista

Reemplazar `OnSaveInstanceState` es un mecanismo adecuado para guardar datos transitorios en una actividad a través de cambios de orientación, como el contador en el ejemplo anterior. Sin embargo, la implementación predeterminada de se encargará `OnSaveInstanceState` de guardar los datos transitorios en la interfaz de usuario para cada vista, siempre y cuando cada vista tenga un identificador asignado. Por ejemplo, suponga que una aplicación tiene un `EditText` elemento definido en XML como se indica a continuación:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Puesto que el `EditText` control tiene un `id` asignado, cuando el usuario escribe algunos datos y gira el dispositivo, los datos se siguen mostrando, como se muestra a continuación:

[![Los datos se conservan en modo horizontal](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

Se llamará a [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) después de `OnStart` . Proporciona una actividad con la oportunidad de restaurar cualquier Estado que se guardó anteriormente en un paquete durante el anterior `OnSaveInstanceState` . Sin embargo, es el mismo paquete que se proporciona `OnCreate` .

En el código siguiente se muestra cómo se puede restaurar el estado en `OnRestoreInstanceState` :

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Este método existe para proporcionar cierta flexibilidad a la hora de restaurar el estado. A veces es más adecuado esperar hasta que se realicen todas las inicializaciones antes de restaurar el estado de la instancia. Además, una subclase de una actividad existente solo puede querer restaurar determinados valores del estado de la instancia. En muchos casos, no es necesario invalidar `OnRestoreInstanceState` , ya que la mayoría de las actividades pueden restaurar el estado mediante el paquete proporcionado a `OnCreate` .

Para obtener un ejemplo de cómo guardar el estado con `Bundle` , consulte el [Tutorial: guardar el estado de la actividad](saving-state.md).

#### <a name="bundle-limitations"></a>Limitaciones de agrupación

Aunque `OnSaveInstanceState` facilita el almacenamiento de datos transitorios, tiene algunas limitaciones:

- No se llama en todos los casos. Por ejemplo, si presiona **Inicio** o **atrás** para salir de una actividad, no se `OnSaveInstanceState` llamará.

- La agrupación que `OnSaveInstanceState` se pasa a no está diseñada para objetos grandes, como imágenes. En el caso de objetos grandes, es preferible guardar el objeto de [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) , como se describe a continuación.

- Los datos guardados mediante el lote se serializan, lo que puede provocar retrasos.

El estado de agrupación es útil para datos simples que no usan mucha memoria, mientras que los *datos de instancia que no son de configuración* son útiles para datos más complejos, o datos que son caros de recuperar, como por ejemplo una llamada de servicio web o una consulta de base de datos complicada. Los datos de instancia que no son de configuración se guardan en un objeto según sea necesario. En la siguiente sección se explica `OnRetainNonConfigurationInstance` cómo conservar tipos de datos más complejos a través de los cambios de configuración.

### <a name="persisting-complex-data"></a>Persistencia de datos complejos

Además de conservar los datos en el paquete, Android también admite el almacenamiento de datos invalidando [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) y devolviendo una instancia de `Java.Lang.Object` que contiene los datos que se van a conservar. Usar para guardar el estado tiene dos ventajas principales `OnRetainNonConfigurationInstance` :

- El objeto devuelto de `OnRetainNonConfigurationInstance` funciona bien con tipos de datos más grandes y más complejos porque la memoria conserva este objeto.

- `OnRetainNonConfigurationInstance`Se llama al método a petición y solo cuando es necesario. Esto es más económico que usar una memoria caché manual.

`OnRetainNonConfigurationInstance`El uso de es adecuado para escenarios en los que resulta caro recuperar los datos varias veces, como las llamadas a servicios Web. Por ejemplo, considere el siguiente código que busca Twitter:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

Este código recupera los resultados del Web con formato JSON, los analiza y, a continuación, presenta los resultados en una lista, como se muestra en la siguiente captura de pantalla:

[![Resultados mostrados en la pantalla](images/06-sml.png)](images/06.png#lightbox)

Cuando se produce un cambio de configuración, por ejemplo, cuando se gira un dispositivo, el código repite el proceso. Para reutilizar los resultados recuperados originalmente y no ocasionar llamadas de red redundantes e innecesarias, podemos usar `OnRetainNonconfigurationInstance` para guardar los resultados, como se muestra a continuación:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

Ahora, cuando se gira el dispositivo, los resultados originales se recuperan de la `LastNonConfiguartionInstance` propiedad. En este ejemplo, los resultados se componen de un `string[]` que contiene tweets. Puesto que `OnRetainNonConfigurationInstance` requiere que `Java.Lang.Object` se devuelva, `string[]` se ajusta en una clase que subclases `Java.Lang.Object` , como se muestra a continuación:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Por ejemplo, si se intenta usar `TextView` como el objeto devuelto de `OnRetainNonConfigurationInstance` , se perderá la actividad, como se muestra en el código siguiente:

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

En esta sección, hemos aprendido a conservar los datos de estado sencillos con `Bundle` y a guardar tipos de datos más complejos con `OnRetainNonConfigurationInstance` .

## <a name="summary"></a>Resumen

El ciclo de vida de la actividad de Android proporciona un marco eficaz para la administración de Estados de actividades dentro de una aplicación, pero puede ser difícil de entender e implementar. En este capítulo se presentaron los distintos Estados en los que puede pasar una actividad durante su duración, así como los métodos de ciclo de vida asociados a esos Estados. A continuación, se proporciona una guía sobre el tipo de lógica que debe realizarse en cada uno de estos métodos.

## <a name="related-links"></a>Vínculos relacionados

- [Control de giro](~/android/app-fundamentals/handling-rotation.md)
- [Actividad de Android](xref:Android.App.Activity)
