---
title: recolección de elementos no utilizados
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: baabc5ccad26ca83241bfe49db40bbbd5df3ff77
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197535"
---
# <a name="garbage-collection"></a>recolección de elementos no utilizados

Xamarin. Android usa el recolector de [elementos no utilizados simple](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)de mono. Se trata de un recolector de elementos no utilizados de marca y barrido con dos generaciones y un *espacio de objetos grande*, con dos tipos de colecciones: 

- Colecciones secundarias (recopila el montón Gen0) 
- Colecciones principales (recopila GEN1 y montones de espacio de objetos grandes). 

> [!NOTE]
> En ausencia de una colección explícita a través de [GC. Las colecciones collect ()](xref:System.GC.Collect) están *a petición*, en función de las asignaciones del montón. *No se trata de un sistema de recuento de referencias*; los objetos *no se recopilarán en cuanto no haya referencias pendientes*o cuando se haya salido de un ámbito. El GC se ejecutará cuando el montón secundario se quede sin memoria para nuevas asignaciones. Si no hay ninguna asignación, no se ejecutará.


Las colecciones secundarias son baratas y frecuentes, y se usan para recopilar objetos inactivos y asignados recientemente. Las colecciones secundarias se realizan después de cada pocos MB de objetos asignados. Las colecciones secundarias se pueden realizar manualmente mediante una llamada a [GC. Collect (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Las colecciones principales son costosas y menos frecuentes, y se usan para recuperar todos los objetos inactivos. Las colecciones principales se realizan una vez que se ha agotado la memoria para el tamaño actual del montón (antes de cambiar el tamaño del montón). Las colecciones principales se pueden realizar manualmente mediante una llamada a [GC. Collect ()](xref:System.GC.Collect) o mediante una llamada a [GC. Collect (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con el argumento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Colecciones de objetos entre máquinas virtuales

Hay tres categorías de tipos de objeto.

- **Objetos administrados**: tipos que *no* heredan de [java. lang. Object](xref:Java.Lang.Object) , por ejemplo, [System. String](xref:System.String). 
    Estos se recopilan normalmente por el GC. 

- **Objetos de Java**: Tipos de Java que están presentes en la máquina virtual en tiempo de ejecución de Android, pero no se exponen a la máquina virtual mono. Estas son aburridas y no se tratarán más adelante. Estos se recopilan normalmente con la máquina virtual en tiempo de ejecución de Android. 

- **Objetos del mismo nivel**: tipos que implementan [IJavaObject](xref:Android.Runtime.IJavaObject) , por ejemplo, todas las subclases [java. lang. Object](xref:Java.Lang.Object) y [java. lang. Throwable](xref:Java.Lang.Throwable) . Las instancias de estos tipos tienen dos "mitades" de un *elemento administrado del mismo nivel* y un *elemento nativo del mismo nivel*. El elemento administrado del mismo nivel es una C# instancia de la clase. El elemento nativo del mismo nivel es una instancia de una clase de Java dentro de la máquina C# virtual en tiempo de ejecución de Android y la propiedad [IJavaObject. Handle](xref:Android.Runtime.IJavaObject.Handle) contiene una referencia global de JNI al elemento nativo del mismo nivel. 


Hay dos tipos de elementos del mismo nivel nativos:

- **Entornos del mismo nivel** : Tipos de Java "normales" que no saben nada de Xamarin. Android, por ejemplo,   [Android. Content. Context](xref:Android.Content.Context).

- **Elementos del mismo nivel de usuario** :   Los contenedores a los que se [puede llamar de Android](~/android/platform/java-integration/working-with-jni.md) , que se generan en tiempo de compilación para cada subclase Java. lang. Object presente dentro de la aplicación.


Como hay dos máquinas virtuales dentro de un proceso de Xamarin. Android, hay dos tipos de recolecciones de elementos no utilizados:

- Recopilaciones en tiempo de ejecución de Android 
- Colecciones mono 

Las recopilaciones en tiempo de ejecución de Android funcionan con normalidad, pero con una advertencia: una referencia global de JNI se trata como una raíz de GC. Por consiguiente, si hay una referencia global de JNI que contiene un objeto de máquina virtual en tiempo de ejecución de Android, *no se puede* recopilar el objeto, aunque de lo contrario se pueda seleccionar para la colección.

Las colecciones mono son donde ocurre la diversión. Los objetos administrados se recopilan normalmente. Los objetos del mismo nivel se recopilan realizando el proceso siguiente:

1. Todos los objetos del mismo nivel válidos para la colección mono tienen su referencia global de JNI reemplazada por una referencia global débil de JNI. 

2. Se invoca un GC de VM en tiempo de ejecución de Android. Se puede recopilar cualquier instancia nativa del mismo nivel. 

3. Se comprueban las referencias globales de JNI creadas en (1). Si se ha recopilado la referencia débil, se recopila el objeto del mismo nivel. Si *no* se ha recopilado la referencia débil, la referencia débil se reemplaza con una referencia global de JNI y el objeto del mismo nivel no se recopila. Nota: en la API 14 +, esto significa que el valor devuelto de `IJavaObject.Handle` puede cambiar después de un GC. 

El resultado final de todo esto es que una instancia de un objeto del mismo nivel se activará siempre que el código administrado (por ejemplo, almacenado en una `static` variable) haga referencia a él o que el código de Java haga referencia a él. Además, la duración de los elementos del mismo nivel nativos se ampliará más allá de lo que de otro modo activaría, ya que el elemento nativo del mismo nivel no se recopilará hasta que el elemento nativo del mismo nivel y el del mismo nivel administrados sean recopilables.


## <a name="object-cycles"></a>Ciclos de objeto

Los objetos del mismo nivel están lógicamente presentes en el tiempo de ejecución de Android y en las máquinas virtuales mono. Por ejemplo, una instancia administrada del mismo nivel de [Android. app. Activity](xref:Android.App.Activity) tendrá una instancia de Java de [Android. app. Activity](https://developer.android.com/reference/android/app/Activity.html) Framework correspondiente. Se espera que todos los objetos que heredan de [java. lang. Object](xref:Java.Lang.Object) tengan representaciones en ambas máquinas virtuales. 

Todos los objetos que tienen una representación en ambas máquinas virtuales tendrán duraciones extendidas en comparación con los objetos que solo están presentes en una sola máquina virtual [`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601)(por ejemplo, un). Llamando a [GC. Collect](xref:System.GC.Collect) no recopilará necesariamente estos objetos, ya que el GC de Xamarin. Android debe asegurarse de que ninguna máquina virtual haga referencia al objeto antes de recopilarlo. 

Para acortar la duración del objeto, se debe invocar [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) . Esto hará que la conexión se haga manualmente "servidor" en el objeto entre las dos máquinas virtuales liberando la referencia global, lo que permite recopilar los objetos más rápidamente. 


## <a name="automatic-collections"></a>Colecciones automáticas

A partir de la [versión 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md), Xamarin. Android realiza automáticamente un GC completo cuando se cruza un umbral de Gref. Este umbral es el 90% del Grefs máximo conocido de la plataforma: 1800 Grefs en el emulador (2000 máx.) y 46800 Grefs en hardware (máximo 52000). *Nota:* Xamarin. Android solo cuenta el Grefs creado por [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)y no conocerá ningún otro Grefs creado en el proceso. Esta es *solo*una heurística. 

Cuando se realiza una recopilación automática, se imprime un mensaje similar al siguiente en el registro de depuración:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

La aparición de esto no es determinista y puede ocurrir en momentos inoportunos (por ejemplo, en el medio de la representación de gráficos). Si ve este mensaje, es posible que desee realizar una colección explícita en otro lugar o que desee intentar [reducir la duración de los objetos del mismo nivel](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opciones del puente GC

Xamarin. Android ofrece administración de memoria transparente con Android y el tiempo de ejecución de Android. Se implementa como una extensión del recolector de elementos no utilizados mono denominado *puente GC*. 

El puente de GC funciona durante una recolección de elementos no utilizados mono y averigua qué objetos del mismo nivel necesitan su "vida" verificado con el montón de tiempo de ejecución de Android. El puente de GC realiza esta determinación realizando los pasos siguientes (en orden):

1. Inducir el gráfico de referencia mono de objetos del mismo nivel inalcanzables a los objetos de Java que representan. 

2. Realice un GC de Java.

3. Compruebe qué objetos están realmente inactivos. 

Este complejo proceso es lo que permite a las subclases de hacer referencia a cualquier objeto de forma gratuita; quita todas las restricciones a `Java.Lang.Object` C#las que se pueden enlazar los objetos de Java. Debido a esta complejidad, el proceso del puente puede resultar muy caro y puede causar pausas perceptibles en una aplicación. Si la aplicación experimenta pausas significativas, merece la pena investigar una de las tres implementaciones de puente de GC siguientes: 

- **Tarjan** : un diseño completamente nuevo del puente de GC basado en el [algoritmo de Robert Tarjan y la propagación de referencias hacia atrás](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Tiene el mejor rendimiento en nuestras cargas de trabajo simuladas, pero también tiene la mayor parte del código experimental. 

- **New** : una revisión importante del código original, corrigiendo dos instancias de comportamiento cuadrático pero manteniendo el algoritmo principal (basado en el [algoritmo de Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) para buscar componentes fuertemente conectados). 

- **Antigua** : la implementación original (considerada la más estable de los tres). Este es el puente que una aplicación debe usar si las `GC_BRIDGE` pausas son aceptables. 


La única manera de averiguar qué puente de GC funciona mejor es experimentar en una aplicación y analizar la salida. Hay dos maneras de recopilar los datos de pruebas comparativas: 

- **Habilitar registro** : habilite el registro (como se describe en la sección de [configuración](~/android/internals/garbage-collection.md) ) para cada opción de puente de GC y, a continuación, Capture y compare los resultados del registro de cada configuración. Inspeccione `GC` los mensajes para cada opción; en concreto, `GC_BRIDGE` los mensajes. Pausar hasta 150MS para las aplicaciones no interactivas son tolerable, pero las pausas anteriores a 60 ms para aplicaciones muy interactivas (como los juegos) son un problema. 

- **Habilitar cuentas de puente** : las cuentas de puente muestran el costo medio de los objetos a los que apunta cada objeto implicado en el proceso del puente. Al ordenar esta información por tamaño, se proporcionarán sugerencias sobre lo que contiene la mayor cantidad de objetos adicionales. 


Para especificar la `GC_BRIDGE` opción que una aplicación debe hacer, `bridge-implementation=old` `bridge-implementation=new` pasar o `bridge-implementation=tarjan` a la `MONO_GC_PARAMS` variable de entorno, por ejemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

La configuración predeterminada es **Tarjan**. Si encuentra una regresión, puede que sea necesario establecer esta opción en **Old**. Además, puede optar por usar la opción **antigua** más estable si **Tarjan** no produce una mejora en el rendimiento. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Ayuda al GC

Hay varias maneras de ayudar al GC a reducir el uso de memoria y los tiempos de recopilación.



### <a name="disposing-of-peer-instances"></a>Desechar instancias del mismo nivel

El GC tiene una vista incompleta del proceso y puede no ejecutarse cuando la memoria es baja, ya que el GC no sabe que la memoria es baja. 

Por ejemplo, una instancia de un tipo de [objeto Java. lang. Object](xref:Java.Lang.Object) o un tipo derivado tiene al menos 20 bytes de tamaño (sujeto a cambios sin previo aviso, etc., etc.). 
Los contenedores a los que se [puede llamar administrados](~/android/internals/architecture.md) no agregan miembros de instancia adicionales, por lo que, si tiene una instancia de [Android. Graphics. Bitmap](xref:Android.Graphics.Bitmap) que hace referencia a un BLOB &ndash; de memoria de 10 MB, el GC de Xamarin. Android no sabrá que el GC verá un objeto de 20 bytes y no podrá determinar si está vinculado a los objetos asignados en tiempo de ejecución de Android que mantienen 10 MB de memoria activo. 

A menudo es necesario ayudar al GC. Desafortunadamente, *GC. AddMemoryPressure ()* y *GC. No se admite RemoveMemoryPressure ()* , por lo que si *sabe* que acaba de liberar un gráfico de objetos asignados a Java de gran tamaño, es posible que tenga que llamar manualmente a [GC. Collect ()](xref:System.GC.Collect) para solicitar a un GC que libere la memoria del lado de la Java, o puede desechar explícitamente las subclases *java. lang. Object* , interrumpiendo la asignación entre el contenedor al que se puede llamar y la instancia de Java. Por ejemplo, vea el [error 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Debe ser *extremadamente* cuidadoso al desechar instancias `Java.Lang.Object` de subclases.

Para minimizar la posibilidad de daños en la memoria, observe las siguientes directrices `Dispose()`al llamar a.


#### <a name="sharing-between-multiple-threads"></a>Compartir entre varios subprocesos

Si el *Java o* la instancia administrada se pueden compartir entre varios subprocesos, *no debería `Dispose()`ser d*, **nunca**. Por ejemplo,[`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
puede devolver una *instancia almacenada en caché*. Si varios subprocesos proporcionan los mismos argumentos, obtendrán la *misma* instancia. Por consiguiente, `Dispose()`la `Typeface` operación de una instancia de un subproceso puede invalidar otros subprocesos `ArgumentException`, lo que `JNIEnv.CallVoidMethod()` puede dar lugar a s desde (entre otros) porque la instancia se eliminó de otro subproceso. 


#### <a name="disposing-bound-java-types"></a>Eliminación de tipos de Java enlazados

Si la instancia es de un tipo de Java enlazado, la instancia se puede eliminar siempre que la instancia no se vuelva a usar desde el código administrado *y* no se pueda compartir la instancia de Java entre los subprocesos (vea la explicación anterior `Typeface.Create()` ). (Tomar esta determinación puede ser difícil). La próxima vez que la instancia de Java Escriba código administrado, se creará un *nuevo* contenedor. 

Esto suele ser útil cuando se trata de Drawables y de otras instancias de recursos pesados:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

Lo anterior es seguro porque el elemento del mismo nivel que [drawable. CreateFromPath ()](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*) hará referencia a un elemento de marco de trabajo del mismo nivel, *no* a un usuario del mismo nivel. La llamada `Dispose()` al final del bloque `using` interrumpirá la relación entre las instancias [Dibujables](xref:Android.Graphics.Drawables.Drawable) administrables y de marco [Dibujables](https://developer.android.com/reference/android/graphics/drawable/Drawable.html), lo que permite que la instancia de Java se recopile en cuanto sea necesario el Runtime de Android. Esto *no* sería seguro si la instancia del mismo nivel hacía referencia a un usuario del mismo nivel; Aquí vamos a usar información "externa" para *saber* que el `Drawable` no puede hacer referencia a un usuario del mismo nivel `Dispose()` y, por lo tanto, la llamada es segura. 


#### <a name="disposing-other-types"></a>Desechar otros tipos 

Si la instancia hace referencia a un tipo que no es un enlace de un tipo de Java (como `Activity`un personalizado), **no** llame `Dispose()` a a menos que *sepa* que ningún código Java llamará a los métodos invalidados en esa instancia. Si no lo hace, se [ `NotSupportedException`](~/android/internals/architecture.md#Premature_Dispose_Calls)producirán. 

Por ejemplo, si tiene un agente de escucha de clic personalizado:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

*No debe* eliminar esta instancia, ya que Java intentará invocar métodos en ella en el futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usar comprobaciones explícitas para evitar excepciones

Si ha implementado un método de sobrecarga [java. lang. Object. Dispose](xref:Java.Lang.Object.Dispose*) , evite el toque de objetos que impliquen JNI. Si lo hace, puede crear una situación de *doble eliminación* que permita que el código intente (de manera irrecuperable) intentar tener acceso a un objeto Java subyacente que ya se ha recolectado como elemento no utilizado. Si lo hace, se produce una excepción similar a la siguiente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Esta situación se suele producir cuando el primer Dispose de un objeto hace que un miembro se convierta en NULL y, a continuación, un intento de acceso posterior en este miembro null provoca que se produzca una excepción. En concreto, el del `Handle` objeto (que vincula una instancia administrada a su instancia de Java subyacente) se invalida en el primer Dispose, pero el código administrado sigue intentando tener acceso a esta instancia subyacente de Java aunque ya no esté disponible (consulte [ Contenedores RCW administrados](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obtener más información sobre la asignación entre las instancias de Java y las instancias administradas). 

Una buena manera de evitar esta excepción es comprobar explícitamente en el `Dispose` método que la asignación entre la instancia administrada y la instancia de Java subyacente sigue siendo válida; es decir, comprobar si el valor del `Handle` objeto es null`IntPtr.Zero`() antes de tener acceso a sus miembros. Por ejemplo, el método `Dispose` siguiente tiene acceso a `childViews` un objeto: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

Si un paso de eliminación inicial `childViews` hace que tenga un `Handle`no válido `for` , el acceso al bucle `ArgumentException`producirá una excepción. Al agregar una comprobación `Handle` nula explícita antes del primer `childViews` acceso, el método `Dispose` siguiente evita que se produzca la excepción: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```


### <a name="reduce-referenced-instances"></a>Reducir las instancias a las que se hace referencia

Siempre que se examina una `Java.Lang.Object` instancia de un tipo o una subclase durante el GC, también se debe examinar todo el *gráfico de objetos* al que hace referencia la instancia. El gráfico de objetos es el conjunto de instancias de objeto al que hace referencia la "instancia raíz", *además* de todo lo al que hace referencia la instancia raíz, de forma recursiva. 

Considere la siguiente clase:

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

Cuando `BadActivity` se construye, el gráfico de objetos contendrá 10004 instancias ( `BadActivity`1x, `strings`1x, `string[]` 1x retenidas por `strings`, instancias de cadena 10000x), *todas* ellas deberán examinarse cada vez que el valorde`BadActivity` se analiza la instancia. 

Esto puede tener efectos perjudiciales en los tiempos de colección, lo que aumenta el tiempo de pausa de GC. 

Puede ayudar al GC reduciendo el tamaño de los gráficos de objetos que tienen la raíz de las instancias del mismo nivel de usuario. En el ejemplo anterior, esto se puede hacer pasando `BadActivity.strings` a una clase independiente que no se hereda de Java. lang. Object: 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```


## <a name="minor-collections"></a>Colecciones secundarias

Las colecciones secundarias se pueden realizar manualmente mediante una llamada a [GC. Collect (0)](xref:System.GC.Collect). Las colecciones secundarias son baratas (cuando se comparan con las colecciones principales), pero tienen un costo fijo significativo, por lo que no desea activarlas con demasiada frecuencia y deben tener un tiempo de pausa de unos milisegundos. 

Si la aplicación tiene un "ciclo de aranceles" en el que la misma cosa se realiza una y otra vez, puede ser aconsejable realizar manualmente una colección secundaria una vez que haya finalizado el ciclo de servicio. Los ciclos de servicio de ejemplo incluyen: 

- El ciclo de representación de una sola trama de juego.
- Toda la interacción con un cuadro de diálogo de aplicación determinado (abrir, rellenar, cerrar) 
- Un grupo de solicitudes de red para actualizar o sincronizar los datos de la aplicación.



## <a name="major-collections"></a>Colecciones principales

Las colecciones principales se pueden realizar manualmente mediante una llamada a [GC. Collect ()](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Deben realizarse con poca frecuencia y pueden tener un tiempo de pausa de un segundo en un dispositivo de estilo Android al recopilar un montón de 512 MB. 

Las colecciones principales solo se deben invocar manualmente, si alguna vez: 

- Al final de los ciclos de servicio largos y cuando una pausa larga no presente ningún problema al usuario. 

- Dentro de un método [Android. app. Activity. OnLowMemory ()](xref:Android.App.Activity.OnLowMemory) invalidado. 



## <a name="diagnostics"></a>Diagnóstico

Para realizar un seguimiento de Cuándo se crean y destruyen referencias globales, puede establecer la propiedad del sistema [Debug. mono. log](~/android/troubleshooting/index.md) para que contenga [*Gref*](~/android/troubleshooting/index.md) y/o [*GC*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuración

El recolector de elementos no utilizados de Xamarin. Android se `MONO_GC_PARAMS` puede configurar estableciendo la variable de entorno. Las variables de entorno se pueden establecer con una acción de compilación de [AndroidEnvironment](~/android/deploy-test/environment.md).

La `MONO_GC_PARAMS` variable de entorno es una lista separada por comas de los parámetros siguientes: 

- `nursery-size` = *tamaño* : Establece el tamaño de la enfermera. El tamaño se especifica en bytes y debe ser una potencia de dos. Los `k` sufijos `m` y `g` se pueden usar para especificar kilo-, mega-and gigabytes, respectivamente. La enfermera es la primera generación (de dos). Una enfermera más grande normalmente acelerará el programa, pero evidentemente usará más memoria. El tamaño de enfermeras predeterminado es de 512 KB. 

- `soft-heap-limit` = *tamaño* : El consumo máximo de memoria administrada de destino para la aplicación. Cuando el uso de memoria está por debajo del valor especificado, el GC está optimizado para el tiempo de ejecución (menos colecciones). 
    Por encima de este límite, el GC está optimizado para el uso de memoria (más colecciones). 

- `evacuation-threshold` = *umbral* : Establece el umbral de evacuación en porcentaje. El valor debe ser un entero comprendido en el intervalo comprendido entre 0 y 100. El valor predeterminado es 66. Si la fase de barrido de la colección encuentra que el grupo de un tipo de bloque de montón específico es inferior a este porcentaje, se realizará una recolección de copia para ese tipo de bloque en la siguiente colección principal, con lo que se restaurará la ocupación para que se acerque al 100 por ciento. Un valor de 0 desactiva la evacuación. 

- `bridge-implementation` = *implementación de puente* : Esto establecerá la opción puente de GC para solucionar los problemas de rendimiento de GC. Hay tres valores posibles: *Old* , *New* , *Tarjan*.

- `bridge-require-precise-merge`: El puente Tarjan contiene una optimización que, en raras ocasiones, puede hacer que se recopile un objeto en un GC después de que se convierta en un elemento no utilizado. Al incluir esta opción, se deshabilita la optimización, lo que permite que los GC sean más predecibles, pero potencialmente más lentos.

Por ejemplo, para configurar el GC para que tenga un límite de tamaño de montón de 128 MB, agregue un nuevo archivo al proyecto con una `AndroidEnvironment` **acción de compilación** de con el contenido: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
