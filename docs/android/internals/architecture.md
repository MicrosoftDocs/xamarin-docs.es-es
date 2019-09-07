---
title: Arquitectura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 06817c563f12425e5c339cb8f2560f37f9ace0b5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756690"
---
# <a name="architecture"></a>Arquitectura

Las aplicaciones de Xamarin. Android se ejecutan en el entorno de ejecución de mono.
Este entorno de ejecución se ejecuta en paralelo con la máquina virtual en tiempo de ejecución (ART) de Android. Ambos entornos en tiempo de ejecución se ejecutan sobre el kernel de Linux y exponen varias API al código de usuario que permite a los desarrolladores tener acceso al sistema subyacente. El entorno de ejecución mono se escribe en el lenguaje C.

Puede usar el [sistema](xref:System), [System.IO](xref:System.IO), [System.net](xref:System.Net) y el resto de las bibliotecas de clases .net para tener acceso a las instalaciones subyacentes del sistema operativo Linux.

En Android, la mayoría de los recursos del sistema como audio, gráficos, OpenGL y telefonía no están disponibles directamente en las aplicaciones nativas, solo se exponen a través de las API de Java en tiempo de ejecución de Android que residen en uno de los espacios de nombres [Java](xref:Java.Lang). * o [Android ](xref:Android). * espacios de nombres. La arquitectura es aproximadamente similar a la siguiente:

[![Diagrama de mono y arte sobre el kernel y por debajo de los enlaces de .NET/Java +](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Los desarrolladores de Xamarin. Android acceden a las distintas características del sistema operativo mediante una llamada a las API de .NET que conocen (para el acceso de bajo nivel) o mediante las clases expuestas en los espacios de nombres de Android, que proporcionan un puente a las API de Java que expone el tiempo de ejecución de Android.

Para obtener más información sobre cómo se comunican las clases de Android con las clases en tiempo de ejecución de Android, consulte el documento de diseño de la [API](~/android/internals/api-design.md) .

## <a name="application-packages"></a>Paquetes de aplicación

Los paquetes de aplicación de Android son contenedores ZIP con una extensión de archivo *. apk* . Los paquetes de aplicación de Xamarin. Android tienen la misma estructura y el mismo diseño que los paquetes de Android normales, con las siguientes adiciones:

- Los ensamblados de aplicación (que contienen IL) se *almacenan* sin comprimir en la carpeta de *ensamblados* . Durante el inicio del proceso en la versión, la *. apk* es *mmap ()* Ed en el proceso y los ensamblados se cargan desde la memoria. Esto permite el inicio de la aplicación más rápido, ya que no es necesario extraer los ensamblados antes de la ejecución.  
- *Nota:* *No se puede confiar* en la información de ubicación del ensamblado como [Assembly. Location](xref:System.Reflection.Assembly.Location) y [Assembly. codebase](xref:System.Reflection.Assembly.CodeBase) en compilaciones de versión. No existen como entradas de sistema de archivos distintas y no tienen ninguna ubicación utilizable.

- Las bibliotecas nativas que contienen el entorno de ejecución mono están presentes dentro de *. apk* . Una aplicación de Xamarin. Android debe contener bibliotecas nativas para las arquitecturas de Android deseadas o de destino, por ejemplo, *armeabi* , *armeabi-v7a* , *x86* . Las aplicaciones de Xamarin. Android no se pueden ejecutar en una plataforma a menos que contenga las bibliotecas en tiempo de ejecución adecuadas.

Las aplicaciones de Xamarin. Android también contienen *contenedores de Android* a los que se puede llamar para permitir que Android llame a código administrado.

## <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

- Los contenedores a los que se **puede llamar de Android** son un puente [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) que se usan cada vez que el entorno de tiempo de ejecución de Android necesita invocar código administrado. Los contenedores a los que se puede llamar de Android son cómo se pueden invalidar los métodos virtuales y se pueden implementar interfaces de Java. Vea el documento [información general sobre la integración de Java](~/android/platform/java-integration/index.md) para obtener más información.

<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Contenedores a los que se puede llamar administrados

Los contenedores a los que se puede llamar administrados son un puente de JNI que se usa siempre que el código administrado necesita invocar código de Android y proporcionar compatibilidad para invalidar los métodos virtuales e implementar interfaces de Java. Todos los espacios de nombres de [Android](xref:Android). * y relacionados son contenedores a los que se puede llamar y que se generan mediante el [enlace. jar](~/android/platform/binding-java-library/index.md).
Los contenedores a los que se puede llamar administrados son responsables de la conversión entre los tipos administrados y Android, e invocando los métodos de la plataforma Android subyacentes a través de JNI.

Cada contenedor administrado al que se puede llamar contiene una referencia global de Java, a la que se puede tener acceso a través de la propiedad [Android. Runtime. IJavaObject. Handle](xref:Android.Runtime.IJavaObject.Handle) . Las referencias globales se utilizan para proporcionar la asignación entre las instancias de Java y las instancias administradas. Las referencias globales son un recurso limitado: los emuladores permiten que solo existan referencias globales 2000 a la vez, mientras que la mayoría del hardware permite que existan más de 52.000 referencias globales a la vez.

Para hacer un seguimiento de Cuándo se crean y destruyen referencias globales, puede establecer la propiedad del sistema [Debug. mono. log](~/android/troubleshooting/index.md) en contenga [Gref](~/android/troubleshooting/index.md).

Las referencias globales se pueden liberar explícitamente mediante una llamada a [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) en el contenedor al que se puede llamar administrado. Esto eliminará la asignación entre la instancia de Java y la instancia administrada y permitirá la recopilación de la instancia de Java. Si se vuelve a tener acceso a la instancia de Java desde el código administrado, se creará un nuevo contenedor al que se puede llamar administrado.

Se debe tener cuidado al desechar los contenedores que se pueden llamar administrados si la instancia se puede compartir accidentalmente entre subprocesos, ya que si se elimina la instancia, afectará a las referencias de otros subprocesos. Para obtener la máxima seguridad `Dispose()` , solo las instancias que se han `new` asignado a través de los métodos *o* desde los métodos que *sabe* que siempre asignan nuevas instancias y no las instancias almacenadas en caché, lo que puede provocar un uso compartido accidental de instancias entre ThreadPool.

## <a name="managed-callable-wrapper-subclasses"></a>Subclases de contenedor con llamadas administradas

Las subclases de contenedor a las que se puede llamar administradas son el lugar en el que puede residir toda la lógica específica de la aplicación "interesante". Entre ellas se incluyen las subclases de [Android. app. Activity](xref:Android.App.Activity) personalizadas (por ejemplo, el tipo [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) en la plantilla de proyecto predeterminada). (Concretamente, se trata de subclases *java. lang. Object* que *no* contienen un atributo personalizado [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) o [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) es *false*, que es el valor predeterminado).

Como los contenedores a los que se puede llamar administrados, las subclases de contenedor a las que se puede llamar administradas también contienen una referencia global, accesible a través de la propiedad [java. lang. Object. Handle](xref:Java.Lang.Object.Handle) . Al igual que con los contenedores a los que se puede llamar administrados, las referencias globales se pueden liberar explícitamente llamando a [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose).
A diferencia de los contenedores a los que se puede llamar administrados, se debe tener *especial cuidado* antes de desechar dichas instancias, ya que la toma de *Dispose ()* de la instancia interrumpirá la asignación entre la instancia de Java (una instancia de un contenedor de Android al que se puede llamar) y la administrada repetición.

### <a name="java-activation"></a>Activación de Java

Cuando se crea un [contenedor de Android Callable](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) a partir de Java, el constructor de C# ACW hará que se invoque el constructor correspondiente. Por ejemplo, ACW para *MainActivity* contendrá un constructor predeterminado que invocará el constructor predeterminado de *MainActivity*. (Esto se hace a través de la llamada a *TypeManager. Activate ()* dentro de los constructores ACW).

Hay otra signatura de constructor de consecuencia: el constructor *(IntPtr, JniHandleOwnership)* . El constructor *(IntPtr, JniHandleOwnership)* se invoca cada vez que un objeto de Java se expone a código administrado y es necesario construir un contenedor administrado al que se puede llamar para administrar el identificador de JNI. Normalmente, esto se hace automáticamente.

Hay dos escenarios en los que el constructor *(IntPtr, JniHandleOwnership)* se debe proporcionar manualmente en una subclase de contenedor invocable administrada:

1. Se ha subclase de [Android. app. Application](xref:Android.App.Application) . La *aplicación* es especial; *nunca* se invocará el constructor de *la configuración predeterminada* y, [en su lugar, se debe proporcionar el constructor (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocación de método virtual de un constructor de clase base.

Tenga en cuenta que (2) es una abstracción de fugas. En Java, como en C#, las llamadas a métodos virtuales desde un constructor siempre invocan la implementación de método más derivada. Por ejemplo, el [constructor TextView (Context, AttributeSet, int)](xref:Android.Widget.TextView#ctor*) invoca el método virtual [TextView. getDefaultMovementMethod ()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que está enlazado como la [propiedad TextView. DefaultMovementMethod](xref:Android.Widget.TextView.DefaultMovementMethod).
Por lo tanto, si un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) se tratara de (1) TextView de la [subclase](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [invalida TextView. DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)y (3) [activa una instancia de esa clase a través de XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) la propiedad *DefaultMovementMethod* reemplazada sería se invoca antes de que el constructor de ACW tuviera la oportunidad de ejecutarse y se produciría C# antes de que el constructor tuviera la oportunidad de ejecutarse.

Esto se admite creando instancias de una instancia de LogTextBox a través del constructor [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) cuando la instancia de ACW LogTextBox escribe primero código administrado y, a continuación, invocando el [LogTextBox (Context, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) *en la misma instancia* cuando se ejecuta el constructor ACW.

Orden de eventos:

1. El XML de diseño se carga en un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2. Android crea instancias del gráfico de objetos de diseño y crea una instancia de *monodroid. apidemo. LogTextBox* , ACW para *LogTextBox* .

3. El constructor *monodroid. apidemo. LogTextBox* ejecuta el constructor [Android. widget. TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) .

4. El constructor *TextView* invoca *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* .

5. *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* invoca *LogTextBox. n_getDefaultMovementMethod ()* , que invoca *TextView. n_getDefaultMovementMethod ()* , que invoca [java. lang. Object. GetObject&lt; TextView&gt; (Handle, JniHandleOwnership. DoNotTransfer)](xref:Java.Lang.Object.GetObject*) .

6. *Java. lang. Object. GetObject&lt;TextView&gt;()* comprueba si ya existe una instancia correspondiente C# para el *identificador* . Si existe, se devuelve. En este escenario, no existe, por lo que *Object&lt;.&gt;GetObject t ()* debe crear uno.

7. *Object. GetObject&lt;T&gt;()* busca el constructor *LogTextBox (IntPtr, JniHandleOwneship)* , lo invoca, crea una asignación entre el *identificador* y la instancia creada, y devuelve la instancia creada.

8. *TextView. n_GetDefaultMovementMethod ()* invoca el captador de la propiedad *LogTextBox. DefaultMovementMethod* .

9. El control vuelve al constructor de *Android. widget. TextView* , que finaliza la ejecución.

10. Se ejecuta el constructor *monodroid. apidemo. LogTextBox* , invocando *TypeManager. Activate ()* .

11. El constructor *LogTextBox (Context, IAttributeSet, int)* se ejecuta *en la misma instancia creada en (7)* .

12. Si no se encuentra el constructor (IntPtr, JniHandleOwnership), se producirá una excepción System. MissingMethodException] (XREF: System. MissingMethodException).

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Llamadas a Dispose () prematuras

Hay una asignación entre un identificador de JNI y la instancia C# correspondiente. Java. lang. Object. Dispose () interrumpe esta asignación. Si un identificador de JNI entra en el código administrado una vez que se ha interrumpido la asignación, se parece a la activación de Java y el constructor *(IntPtr, JniHandleOwnership)* se comprobará y se invocará. Si el constructor no existe, se producirá una excepción.

Por ejemplo, dada la siguiente subclase de Ajustador invocable administrada:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Si creamos una instancia de, Dispose () y hace que se vuelva a crear el contenedor al que se puede llamar:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

El programa se determinará:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Si la subclase contiene un constructor *(IntPtr, JniHandleOwnership)* , se creará una *nueva* instancia del tipo. Como resultado, la instancia parecerá "perder" todos los datos de instancia, ya que es una nueva instancia. (Tenga en cuenta que el valor es null).

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose ()* de las subclases de contenedor a las que se puede llamar administradas cuando sepa que el objeto de Java ya no se va a usar o la subclase no contiene datos de instancia y se ha proporcionado un constructor *(IntPtr, JniHandleOwnership)* .

## <a name="application-startup"></a>Inicio de la aplicación

Cuando se inicia una actividad, servicio, etc., Android comprobará primero si ya existe un proceso en ejecución para hospedar la actividad/servicio, etc. Si no existe tal proceso, se creará un nuevo proceso, se leerá [archivo AndroidManifest. XML](https://developer.android.com/guide/topics/manifest/manifest-intro.html) y se cargará y se creará una [/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm) instancia del tipo especificado en el atributo. A continuación, se crea una instancia [/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) de todos los tipos especificados por los valores de atributo y se invoca el método [ContentProvider. attachInfo% 28)](xref:Android.Content.ContentProvider.AttachInfo*) . Xamarin. Android se enlaza en esto agregando un *mono. MonoRuntimeProvider* *ContentProvider* archivo AndroidManifest. XML durante el proceso de compilación. *Mono. El método MonoRuntimeProvider. attachInfo ()* es responsable de cargar el tiempo de ejecución de mono en el proceso.
Cualquier intento de usar mono antes de este punto producirá un error. ( *Nota*: Este es el motivo por el que los tipos de la subclase [Android. app. Application](xref:Android.App.Application) deben proporcionar un [constructor (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), ya que la instancia de la aplicación se crea antes de que se pueda inicializar mono.

Una vez completada la inicialización del proceso, se consulta para encontrar el nombre de clase de la actividad/servicio, `AndroidManifest.xml` etc. para iniciar. Por ejemplo, el [ /manifest/application/activity/@android:name atributo](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) se usa para determinar el nombre de una actividad que se va a cargar. En el caso de las actividades, este tipo debe heredar [Android. app. Activity](xref:Android.App.Activity).
El tipo especificado se carga a través de [Class. forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que requiere que el tipo sea un tipo de Java, por lo tanto, los contenedores a los que se puede llamar de Android) y, después, se cree una instancia de él. La creación de una instancia de contenedor de Android al que se puede llamar desencadenará C# la creación de una instancia del tipo correspondiente. Después, Android invocará [Activity. Create (bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , lo que provocará que se invoque la [actividad correspondiente. Create (bundle)](xref:Android.App.Activity.OnCreate*) y está fuera de las carreras.
