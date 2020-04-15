---
title: Trabajo con JNI y Xamarin.Android
description: Xamarin.Android permite escribir aplicaciones de Android con C# en lugar de Java. Se proporcionan varios ensamblados con Xamarin.Android que proporcionan enlaces para las bibliotecas Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, no se proporcionan enlaces para todas las bibliotecas Java posibles, y los que se proporcionan pueden no enlazar todos los tipos y miembros de Java. Para usar tipos y miembros de Java sin enlazar, puede utilizarse Java Native Interface (JNI). En este artículo se muestra cómo usar JNI para interactuar con los tipos y miembros de Java desde las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724349"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Trabajo con JNI y Xamarin.Android

_Xamarin.Android permite escribir aplicaciones de Android con C# en lugar de Java. Se proporcionan varios ensamblados con Xamarin.Android que proporcionan enlaces para las bibliotecas Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, no se proporcionan enlaces para todas las bibliotecas Java posibles, y los que se proporcionan pueden no enlazar todos los tipos y miembros de Java. Para usar tipos y miembros de Java sin enlazar, puede utilizarse Java Native Interface (JNI). En este artículo se muestra cómo usar JNI para interactuar con los tipos y miembros de Java desde las aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

No siempre es necesario o posible crear un contenedor que se puede llamar administrado (MCW) para invocar código Java. En muchos casos, JNI "insertada" es perfectamente aceptable y útil para el uso único de miembros de Java sin enlazar. Suele ser más sencillo usar JNI para invocar un solo método en una clase de Java que generar un enlace .jar entero.

Xamarin.Android proporciona el ensamblado `Mono.Android.dll`, que facilita un enlace para la biblioteca `android.jar` de Android. Los tipos y miembros no presentes en `Mono.Android.dll` y los tipos no presentes en `android.jar` pueden usarse enlazándolos manualmente. Para enlazar tipos y miembros de Java, use **Java Native Interface** (**JNI**) para buscar tipos, leer y escribir campos, e invocar métodos.

La API de JNI de Xamarin.Android es conceptualmente muy similar a la API `System.Reflection` de .NET: permite buscar tipos y miembros por nombre, leer y escribir valores de campo, invocar métodos, etc. Puede usar JNI y el atributo personalizado `Android.Runtime.RegisterAttribute` para declarar métodos virtuales que se puedan enlazar para admitir la invalidación. Puede enlazar interfaces para que puedan implementarse en C#.

En este documento se explica lo siguiente:

- Cómo hace referencia JNI a los tipos.
- Cómo buscar, leer y escribir campos.
- Cómo buscar e invocar métodos.
- Cómo exponer métodos virtuales para permitir la invalidación a partir de código administrado.
- Cómo exponer interfaces.

## <a name="requirements"></a>Requisitos

JNI, tal como se expone a través del [espacio de nombres Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv), está disponible en todas las versiones de Xamarin.Android.
Para enlazar tipos e interfaces de Java, debe usar Xamarin. Android 4.0 o posterior.

## <a name="managed-callable-wrappers"></a>Contenedores que se pueden llamar administrados

Un **contenedor que se puede llamar administrado** (**MCW**) es un *enlace* para una interfaz o clase de Java que contiene toda la maquinaria JNI para que el código de C# de cliente no tenga que preocuparse por la complejidad subyacente de JNI. La mayoría de `Mono.Android.dll` consta de contenedores que se pueden llamar administrados.

Los contenedores que se pueden llamar administrados tienen dos fines:

1. Encapsular el uso de JNI para que el código de cliente no tenga que conocer la complejidad subyacente.
1. Permitir subclasificar los tipos de Java e implementar interfaces de Java.

El primer fin es únicamente para su comodidad y la encapsulación de la complejidad para que los consumidores dispongan de un conjunto sencillo y administrado para su uso. Esto requiere el uso de los diversos miembros de [JNIEnv](xref:Android.Runtime.JNIEnv) como se describe más adelante en este artículo. Tenga en cuenta que los contenedores que se llaman administrados no son estrictamente necesarios &ndash; El empleo de JNI "insertada" es perfectamente aceptable y útil para el uso único de miembros de Java sin enlazar. La subclasificación y la implementación de interfaces requieren el uso de contenedores que se llaman administrados.

## <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

Los contenedores que se llaman de Android (ACW) son necesarios siempre que el entorno de ejecución de Android (ART) tenga que invocar código administrado; estos contenedores son necesarios porque no existe ninguna manera de registrar clases con ART en tiempo de ejecución
(en concreto, el entorno de ejecución de Android no admite la función de JNI [DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986). Por lo tanto, los contenedores que se llaman de Android compensan la falta de compatibilidad para el registro del tipo de entorno de ejecución).

Siempre que el código de Android tenga que ejecutar un método virtual o de interfaz que se invalide o implemente en código administrado, Xamarin.Android debe proporcionar un proxy Java para que este método se envíe al tipo administrado adecuado. Estos tipos de proxy Java son código Java que tienen la "misma" lista de interfaces Java y clases base que el tipo administrado, implementando los mismos constructores y declarando cualquier método de interfaz y clase base invalidado.

El programa **monodroid.exe** genera los contenedores que se llaman de Android durante el [proceso de compilación](~/android/deploy-test/building-apps/build-process.md) y los genera para todos los tipos que heredan (directa o indirectamente) [Java.Lang.Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementar interfaces

Hay ocasiones en las que puede que tenga que implementar una interfaz de Android (como [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks)).

Todas las clases e interfaces de Android amplían la interfaz [Android.Runtime.IJavaObject](xref:Android.Runtime.IJavaObject); por lo tanto, todos los tipos de Android deben implementar `IJavaObject`.
Xamarin.Android aprovecha este hecho &ndash; usa `IJavaObject` para proporcionar a Android un proxy Java (un contenedor que se llama de Android) para el tipo administrado especificado. Como **monodroid.exe** solo busca subclases `Java.Lang.Object` (que deben implementar `IJavaObject`), la subclasificación de `Java.Lang.Object` nos proporciona una manera de implementar interfaces en código administrado. Por ejemplo:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```

### <a name="implementation-details"></a>Detalles de implementación

*En el resto de este artículo se proporcionan detalles de implementación sujetos a cambio sin previo aviso* (y se presentan aquí solo porque los desarrolladores pueden sentir curiosidad por lo que ocurre en segundo plano).

Por ejemplo, dado el siguiente origen de C#:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

El programa **mandroid.exe** generará el siguiente contenedor que se llama de Android:

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Observe que se conserva la clase base y se proporcionan declaraciones de método nativo para cada método invalidado en código administrado.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute y ExportFieldAttribute

Normalmente, Xamarin.Android genera automáticamente el código Java que consta del ACW; esta generación se basa en los nombres de método y clase cuando una clase se deriva de una clase de Java e invalida los métodos de Java existentes. Sin embargo, en algunos escenarios, la generación de código no es adecuada, como se describe a continuación:

- Android admite nombres de acción en atributos XML de diseño como, por ejemplo, el atributo XML [android:onClick](xref:Android.Views.View.IOnClickListener.OnClick*). Al especificarse, la instancia de vista inflada intenta buscar el método de Java.

- La interfaz [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) requiere los métodos `readObject` y `writeObject`. Como no son miembros de esta interfaz, nuestra implementación administrada correspondiente no expone estos métodos a código Java.

- La interfaz [android.os.Parcelable](xref:Android.OS.Parcelable) espera que una clase de implementación deba tener un campo estático `CREATOR` de tipo `Parcelable.Creator`. El código Java generado requiere algún campo explícito. Con nuestro escenario estándar, no se puede generar el campo en código Java a partir de código administrado.

Como la generación de código no proporciona una solución para generar métodos de Java arbitrarios con nombres arbitrarios, a partir de Xamarin.Android 4.2, [ExportAttribute](xref:Java.Interop.ExportAttribute) y [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) se presentaron para ofrecer una solución a los escenarios anteriores. Ambos atributos residen en el espacio de nombres `Java.Interop`:

- `ExportAttribute` &ndash; especifica un nombre de método y sus tipos de excepción esperados (para proporcionar "throws" explícitos en Java). Al usarse en un método, este "exportará" un método de Java que genera un código de envío para la invocación de JNI correspondiente al método administrado. Esto se puede usar con `android:onClick` y `java.io.Serializable`.

- `ExportFieldAttribute` &ndash; especifica un nombre de campo. Reside en un método que funciona como inicializador de campo. Esto se puede usar con `android.os.Parcelable`.

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solución de problemas de ExportAttribute y ExportFieldAttribute

- El empaquetado no puede realizarse por la ausencia de **Mono.Android.Export.dll** &ndash; si usó `ExportAttribute` o `ExportFieldAttribute` en algunos métodos de su código o bibliotecas dependientes, debe agregar **Mono.Android.Export.dll**. Este ensamblado se aísla para admitir código de devolución de llamada de Java. Al agregar tamaño adicional a la aplicación, es independiente de **Mono.Android.dll**.

- En la compilación de versión, `MissingMethodException` se produce para los métodos de exportación &ndash; En la compilación de versión, `MissingMethodException` se produce para los métodos de exportación (este problema se corrigió en la versión más reciente de Xamarin.Android).

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` y `ExportFieldAttribute` proporcionan una funcionalidad que el código en tiempo de ejecución de Java puede usar. Este código en tiempo de ejecución tiene acceso al código administrado a través de los métodos de JNI generados controlados por esos atributos. Como resultado, no existe ningún método de Java enlazado por el método administrado; por lo tanto, el método de Java se genera a partir de una firma del método administrado.

Sin embargo, este caso no es totalmente determinante. Principalmente, esto es cierto en algunas asignaciones avanzadas entre tipos administrados y tipos de Java como, por ejemplo:

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

Cuando se necesitan tipos como estos para los métodos exportados, el atributo `ExportParameterAttribute` debe usarse para proporcionar explícitamente el parámetro correspondiente o valor devuelto a un tipo.

### <a name="annotation-attribute"></a>Atributo de anotación

En Xamarin.Android 4.2, convertimos los tipos de implementación `IAnnotation` en atributos (System.Attribute) y agregamos compatibilidad con la generación de anotaciones en los contenedores de Java.

Esto supone los siguientes cambios direccionales:

- El generador de enlaces genera `Java.Lang.DeprecatedAttribute` a partir de `java.Lang.Deprecated` (aunque debería ser `[Obsolete]` en código administrado).

- Esto no significa que la clase `Java.Lang.Deprecated` existente vaya a desaparecer. Estos objetos basados en Java podrían seguir usándose como objetos Java habituales (si existe dicho uso). Encontrará las clases `Deprecated` y `DeprecatedAttribute`.

- La clase `Java.Lang.DeprecatedAttribute` se marca como `[Annotation]`. Si hay un atributo personalizado heredado de este atributo `[Annotation]`, la tarea msbuild generará una anotación de Java para ese atributo personalizado (@Deprecated) en el contenedor que se llama de Android (ACW).

- Las anotaciones podrían generarse en clases, métodos y archivos exportados (que es un método en código administrado).

Si la clase contenedora (la propia clase anotada o la clase que contiene los miembros anotados) no está registrada, no se generará en absoluto el origen de la clase de Java en su totalidad, incluidas las anotaciones. En el caso de los métodos, puede especificar el atributo `ExportAttribute` para obtener el método generado y anotado explícitamente. Además, no es una característica para "generar" una definición de clase de anotación de Java. Es decir, si define un atributo administrado personalizado para una anotación determinada, tendrá que agregar otra biblioteca .jar que contenga la clase de anotación de Java correspondiente. Agregar un archivo de origen de Java que defina el tipo de anotación no es suficiente. El compilador de Java no funciona de la misma forma que **apt**.

Asimismo, se aplican las siguientes limitaciones:

- Este proceso de conversión no tiene en cuenta la anotación `@Target` del tipo de anotación hasta ahora.

- Los atributos de una propiedad no funcionan. En su lugar, use atributos para el captador o establecedor de propiedad.

## <a name="class-binding"></a>Enlace de clases

Enlazar una clase supone escribir un contenedor que se llama administrado para simplificar la invocación del tipo de Java subyacente.

Enlazar métodos virtuales y abstractos para permitir la invalidación a partir de C# requiere Xamarin.Android 4.0. Sin embargo, cualquier versión de Xamarin.Android puede enlazar métodos no virtuales, métodos estáticos o métodos virtuales sin admitir invalidaciones.

Normalmente, un enlace contiene los siguientes elementos:

- Un [identificador de JNI del tipo de Java que se enlaza](#_Looking_up_Java_Types).

- [Identificadores de campo de JNI y propiedades de cada campo enlazado](#_Instance_Fields).

- [Identificadores de método de JNI y métodos de cada método enlazado](#_Instance_Methods).

- Si se requiere la subclasificación, el tipo deberá tener un atributo personalizado [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) en la declaración de tipos con [RegisterAttribute.DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) establecido en `true`.

### <a name="declaring-type-handle"></a>Identificador de tipo declarativo

Los métodos de búsqueda de campo y método requieren una referencia al objeto que haga referencia a su tipo declarativo. Por convención, se mantiene en un campo `class_ref`:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte la sección [Referencias de tipo de JNI](#_JNI_Type_References) para obtener detalles sobre el token `CLASS`.

### <a name="binding-fields"></a>Enlace de campos

Los campos de Java se exponen como propiedades de C#, por ejemplo, el campo de Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) se enlaza como la propiedad de C# [Java.Lang.JavaSystem.In](xref:Java.Lang.JavaSystem.In).
Además, como JNI diferencia entre campos estáticos y campos de instancia, se usan diferentes métodos al implementar las propiedades.

El enlace de campos implica tres conjuntos de métodos:

1. El método *get field id*. El método *get field id* es responsable de devolver un identificador de campo que usarán los métodos *get field value* y *set field value*. La obtención del identificador de campo requiere que se conozcan el tipo declarativo, el nombre del campo y la [signatura de tipo de JNI](#JNI_Type_Signatures) del campo.

1. Los métodos *get field value*. Estos métodos requieren el identificador de campo y son responsables de leer el valor del campo de Java.
    El método que se va a usar depende del tipo del campo.

1. Los métodos *set field value*. Estos métodos requieren el identificador de campo y son responsables de escribir el valor del campo en Java. El método que se va a usar depende del tipo del campo.

Los [campos estáticos](#_Static_Fields) usan los métodos [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field` y [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*).

 Los [campos de instancia](#_Instance_Fields) usan los métodos [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), `JNIEnv.Get*Field` y [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*).

Por ejemplo, la propiedad estática `JavaSystem.In` puede implementarse como:

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Nota: Usamos [InputStreamInvoker.FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) para convertir la referencia de JNI en una instancia `System.IO.Stream` y utilizamos `JniHandleOwnership.TransferLocalRef` porque [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) devuelve una referencia local.

Muchos de los tipos [Android.Runtime](xref:Android.Runtime) disponen de métodos `FromJniHandle` que convertirán una referencia de JNI en el tipo deseado.

### <a name="method-binding"></a>Enlace de métodos

Los métodos de Java se exponen como métodos y propiedades de C#. Por ejemplo, el método de Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) se enlaza como el método [Java.Lang.Runtime.RunFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) y el método [java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) se enlaza como la propiedad [Java.Lang.Object.Class](xref:Java.Lang.Object.Class).

La invocación de método es un proceso que consta de dos pasos:

1. El método *get method id* del método que se va a invocar. El método *get method id* es responsable de devolver un identificador de método que usarán los métodos de invocación de método. La obtención del identificador de método requiere que se conozcan el tipo declarativo, el nombre del método y la [signatura de tipo de JNI](#JNI_Type_Signatures) del método.

1. Invoque al método.

Al igual que ocurre con los campos, los métodos que se van a usar para obtener el identificador de método e invocar este distinguen entre métodos estáticos y de instancia.

Los [métodos estáticos](#_Static_Methods_1) usan [JNIEnv.GetStaticMethodID()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) para buscar el identificador de método y utilizan la familia `JNIEnv.CallStatic*Method` de métodos para la invocación.

Los [métodos de instancia](#_Instance_Methods) usan [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) para buscar el identificador de método y utilizan las familias `JNIEnv.Call*Method` y `JNIEnv.CallNonvirtual*Method` de métodos para la invocación.

El enlace de métodos es posiblemente más que la invocación de método. El enlace de métodos también incluye permitir que se invalide (para métodos abstractos y no finales) o se implemente (para métodos de interfaz) un método. En la sección [Herencia e interfaces admitidas](#_Supporting_Inheritance,_Interfaces_1) se tratan las complejidades de admitir métodos virtuales y de interfaz.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

Enlazar un método estático implica el uso de `JNIEnv.GetStaticMethodID` para obtener un identificador de método y, a continuación, la utilización del método `JNIEnv.CallStatic*Method` adecuado, dependiendo del tipo de valor devuelto del método. A continuación se muestra un ejemplo de un enlace para el método [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()):

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Tenga en cuenta que almacenamos el identificador de método en un campo estático, `id_getRuntime`. Se trata de una optimización del rendimiento, de modo que no es necesario que se busque el identificador de método en cada invocación. No es necesario almacenar en caché el identificador de método de esta forma. Una vez que se obtiene el identificador de método, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) se usa para invocar el método. `JNIEnv.CallStaticObjectMethod` devuelve un elemento `IntPtr` que contiene el identificador de la instancia de Java devuelta.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) se usa para convertir el identificador de Java en una instancia de objeto fuertemente tipada.

#### <a name="non-virtual-instance-method-binding"></a>Enlace de métodos de instancia no virtuales

Enlazar un método de instancia `final` o un método de instancia que no requiere la invalidación implica el uso de `JNIEnv.GetMethodID` para obtener un identificador de método y, a continuación, la utilización del método `JNIEnv.Call*Method` adecuado, dependiendo del tipo de valor devuelto del método. A continuación se muestra un ejemplo de un enlace para la propiedad `Object.Class`:

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Tenga en cuenta que almacenamos el identificador de método en un campo estático, `id_getClass`.
Se trata de una optimización del rendimiento, de modo que no es necesario que se busque el identificador de método en cada invocación. No es necesario almacenar en caché el identificador de método de esta forma. Una vez que se obtiene el identificador de método, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) se usa para invocar el método. `JNIEnv.CallStaticObjectMethod` devuelve un elemento `IntPtr` que contiene el identificador de la instancia de Java devuelta.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) se usa para convertir el identificador de Java en una instancia de objeto fuertemente tipada.

### <a name="binding-constructors"></a>Enlace de constructores

Los constructores son métodos de Java con el nombre `"<init>"`. Al igual que ocurre con los métodos de instancia de Java, `JNIEnv.GetMethodID` se usa para buscar el identificador de constructor. A diferencia de los métodos de Java, los métodos [JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*) se usan para invocar el identificador de método de constructor. El valor devuelto de `JNIEnv.NewObject` es una referencia local de JNI:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente, un enlace de clases subclasificará [Java.Lang.Object](xref:Java.Lang.Object).
Al subclasificar `Java.Lang.Object`, una semántica adicional entra en juego: una instancia `Java.Lang.Object` mantiene una referencia global a una instancia de Java a través de la propiedad `Java.Lang.Object.Handle`.

1. El constructor predeterminado `Java.Lang.Object` asignará una instancia de Java.

1. Si el tipo tiene un atributo `RegisterAttribute` y `RegisterAttribute.DoNotGenerateAcw` es `true`, se creará una instancia del tipo `RegisterAttribute.Name` a través de su constructor predeterminado.

1. De lo contrario, se creará una instancia del [contenedor que se llama de Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondiente a `this.GetType` a través de su constructor predeterminado. Los contenedores que se llaman de Android se generan durante la creación de paquetes para todas las subclases `Java.Lang.Object` para las que `RegisterAttribute.DoNotGenerateAcw` no se establece en `true`.

En el caso de los tipos que no son enlaces de clases, esta es la semántica esperada: la creación de una instancia de una instancia C# `Mono.Samples.HelloWorld.HelloAndroid` debe dar lugar a la construcción de una instancia `mono.samples.helloworld.HelloAndroid` de Java que sea un contenedor que se llama de Android generado.

En el caso de los enlaces de clases, este puede ser el comportamiento correcto si el tipo de Java contiene un constructor predeterminado o no debe invocarse ningún otro constructor. De lo contrario, debe proporcionarse un constructor que realice las siguientes acciones:

1. Invocar [Java.Lang.Object(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) en lugar del constructor `Java.Lang.Object` predeterminado. Esto es necesario para evitar la creación de una nueva instancia de Java.

1. Compruebe el valor de [Java.Lang.Object.Handle](xref:Java.Lang.Object.Handle) antes de crear cualquier instancia de Java. La propiedad `Object.Handle` tendrá un valor distinto de `IntPtr.Zero` si un contenedor que se llama de Android se construyó en código Java, y el enlace de clases se construye para contener la instancia que se llama de Android creada. Por ejemplo, cuando Android crea una instancia `mono.samples.helloworld.HelloAndroid`, primero se creará el contenedor que se llama de Android y el constructor `HelloAndroid` de Java creará una instancia del tipo `Mono.Samples.HelloWorld.HelloAndroid` correspondiente, con el establecimiento de la propiedad `Object.Handle` en la instancia de Java antes de la ejecución del constructor.

1. Si el tipo actual en tiempo de ejecución no es igual que el tipo declarativo, debe crearse una instancia del contenedor que se llama de Android correspondiente, así como usarse [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) para almacenar el identificador que devuelve [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Si el tipo actual en tiempo de ejecución es igual que el tipo declarativo, invoque el constructor de Java y use [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) para almacenar el identificador que devuelve `JNIEnv.NewInstance`.

Por ejemplo, tenga en cuenta el constructor [java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)). Se enlaza como:

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

Los métodos [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) son asistentes para realizar un elemento `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject` y `JNIEnv.DeleteGlobalReference` en el valor devuelto desde `JNIEnv.FindClass`. Para obtener más información, vea la siguiente sección.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Herencia e interfaces admitidas

Subclasificar un tipo de Java o implementar una interfaz de Java requiere la generación de [contenedores que se llaman de Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) que se generan para todas las subclases `Java.Lang.Object` durante el proceso de empaquetado. La generación de ACW se controla a través del atributo personalizado [Android.Runtime.RegisterAttribute](xref:Android.Runtime.RegisterAttribute).

En el caso de los tipos de C#, el constructor de atributos personalizados `[Register]` requiere un argumento: la [referencia de tipo simplificada de JNI](#_Simplified_Type_References_1) para el tipo de Java correspondiente. Esto permite proporcionar diferentes nombres entre Java y C#.

Antes de Xamarin.Android 4.0, el atributo personalizado `[Register]` no estaba disponible para los tipos de Java existentes "alias". Esto se debe a que el proceso de generación de ACW generaría ACW para todas las subclases `Java.Lang.Object` encontradas.

Xamarin.Android 4.0 presentó la propiedad [RegisterAttribute.DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw). Esta propiedad indica al proceso de generación de ACW que *omita* el tipo anotado, permitiendo la declaración de nuevos contenedores que se llaman administrados que no dará como resultado la generación de ACW en el momento de creación de paquetes. Esto permite el enlace de los tipos de Java existentes. Por ejemplo, tenga en cuenta la siguiente clase de Java sencilla, `Adder`, que contiene un método, `add`, que se agrega a enteros y devuelve el resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

El tipo `Adder` podría enlazarse como:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

Aquí, el tipo de C# `Adder` *establece como alias* el tipo de Java `Adder`. El atributo `[Register]` se usa para especificar el nombre de JNI del tipo de Java `mono.android.test.Adder` y la propiedad `DoNotGenerateAcw` se usa para impedir la generación de ACW. Esto dará lugar a la generación de un ACW para el tipo `ManagedAdder`, que subclasifica correctamente el tipo `mono.android.test.Adder`. Si no se hubiese usado la propiedad `RegisterAttribute.DoNotGenerateAcw`, el proceso de compilación de Xamarin.Android habría generado un nuevo tipo de Java `mono.android.test.Adder`. Esto daría lugar a errores de compilación, ya que el tipo `mono.android.test.Adder` estaría presente dos veces, en dos archivos independientes.

### <a name="binding-virtual-methods"></a>Enlace de métodos virtuales

`ManagedAdder` subclasifica el tipo `Adder` de Java, pero no es especialmente interesante: el tipo `Adder` de C# no define ningún método virtual, por lo que `ManagedAdder` no puede invalidar nada.

El enlace de métodos `virtual` para permitir la invalidación por subclases requiere que se realicen varias acciones que se dividen en las siguientes dos categorías:

1. **Enlace de métodos**

1. **Registro de métodos**

#### <a name="method-binding"></a>Enlace de métodos

Un enlace de métodos requiere la incorporación de dos miembros de compatibilidad a la definición `Adder` de C#: `ThresholdType` y `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

La propiedad `ThresholdType` devuelve el tipo actual del enlace:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` se utiliza en el enlace de métodos para determinar cuándo debe enviar el método virtual frente al no virtual. Siempre debe devolver una instancia `System.Type` que corresponda al tipo de C# declarativo.

##### <a name="thresholdclass"></a>ThresholdClass

La propiedad `ThresholdClass` devuelve la referencia de clase de JNI para el tipo enlazado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` se usa en el enlace de métodos al invocar métodos no virtuales.

#### <a name="binding-implementation"></a>Implementación de enlace

La implementación de enlace de métodos es responsable de la invocación en tiempo de ejecución del método de Java. También contiene una declaración de atributo personalizado `[Register]` que forma parte del registro de métodos y se explicará en la sección sobre el registro de métodos:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

El campo `id_add` contiene el identificador de método del método de Java que se va a invocar. El valor `id_add` se obtiene de `JNIEnv.GetMethodID`, que requiere la clase declarativa (`class_ref`), el nombre del método de Java (`"add"`) y la firma de JNI del método (`"(II)I"`).

Una vez que se obtiene el identificador de método, `GetType` se compara con `ThresholdType` para determinar si se requiere el envío virtual o no virtual. El envío virtual se requiere cuando `GetType` coincide con `ThresholdType`, ya que `Handle` puede hacer referencia a una subclase asignada por Java que invalida el método.

Si `GetType` no coincide con `ThresholdType`, `Adder` se ha subclasificado (p. ej., por `ManagedAdder`) y la implementación `Adder.Add` solo se invocará si la subclase invocó `base.Add`. Este es el caso del envío no virtual, que es donde `ThresholdClass` entra en juego. `ThresholdClass` especifica qué clase de Java proporcionará la implementación del método que se va a invocar.

#### <a name="method-registration"></a>Registro de métodos

Supongamos que tenemos una definición `ManagedAdder` actualizada que invalida el método `Adder.Add`:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Recuerde que `Adder.Add` tenía un atributo personalizado `[Register]`:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

El constructor de atributos personalizados  `[Register]` acepta tres valores:

1. El nombre del método de Java, `"add"` en este caso.

1. La signatura de tipo de JNI del método, `"(II)I"` en este caso.

1. El *método de conector*, `GetAddHandler` en este caso.
    Los métodos de conector se tratarán más adelante.

Los dos primeros parámetros permiten al proceso de generación de ACW generar una declaración de método para invalidar el método. El ACW resultante contendría parte del siguiente código:

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Tenga en cuenta que se declara un método `@Override`, que delega en un método precedido de `n_` del mismo nombre. Esto garantiza que cuando el código Java invoque `ManagedAdder.add`, se invocará `ManagedAdder.n_add`, que permitirá la ejecución de la invalidación del método `ManagedAdder.Add` de C#.

Por lo tanto, la pregunta más importante: ¿cómo se enlaza `ManagedAdder.n_add` a `ManagedAdder.Add`?

Los métodos `native` de Java se registran con Java Runtime (el entorno de ejecución de Android) a través de la [función RegisterNatives de JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` toma una matriz de estructuras que contienen el nombre de método de Java, la signatura de tipo de JNI y un puntero de función para invocar que sigue la [convención de llamada de JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
El puntero de función debe ser una función que toma dos argumentos de puntero seguidos de los parámetros de métodos. El método `ManagedAdder.n_add` de Java debe implementarse a través de una función con el siguiente prototipo de C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android no expone un método `RegisterNatives`. En su lugar, el ACW y el MCW proporcionan conjuntamente la información necesaria para invocar `RegisterNatives`: el ACW contiene el nombre de método y la signatura de tipo de JNI, lo único que falta es un puntero de función que se va a enlazar.

Aquí es donde el *método de conector* entra en juego. El tercer parámetro del atributo personalizado `[Register]` es el nombre de un método definido en el tipo registrado o una clase base del tipo registrado que no acepta ningún parámetro y devuelve un `System.Delegate`. El `System.Delegate` devuelto hace referencia a su vez a un método que tiene la signatura de función de JNI correcta. Por último, el delegado que devuelve el método de conector *debe* estar en la raíz para que la GC no lo recopile, ya que el delegado se proporciona a Java.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

El método `GetAddHandler` crea un delegado `Func<IntPtr, IntPtr, int, int,
int>` que hace referencia al método `n_Add` y, a continuación, invoca [JNINativeWrapper.CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate` ajusta el método proporcionado en un bloque Try/Catch para que se controle cualquier excepción no controlada y dará lugar a la generación del evento [AndroidEvent.UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser). El delegado resultante se almacena en la variable `cb_add` estática para que la GC no libere el delegado.

Por último, el método `n_Add` es responsable de serializar los parámetros de JNI para los tipos administrados correspondientes y, a continuación, de delegar la llamada de método.

Nota: Use siempre `JniHandleOwnership.DoNotTransfer` al obtener un MCW a través de una instancia de Java. Su tratamiento como referencia local (y, por tanto, la llamada a `JNIEnv.DeleteLocalRef`) interrumpirá administrado -&gt; Java -&gt; transiciones de pila administradas.

### <a name="complete-adder-binding"></a>Enlace del agregador completo

El enlace administrado completo para el tipo `mono.android.tests.Adder` es:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```

### <a name="restrictions"></a>Restricciones

Al escribir un tipo que coincida con los criterios siguientes:

1. Subclases `Java.Lang.Object`

1. Tiene un atributo personalizado `[Register]`

1. `RegisterAttribute.DoNotGenerateAcw` es `true`

A continuación, en el caso de la interacción de la GC, el tipo *no debe* tener ningún campo que pueda hacer referencia a una subclase `Java.Lang.Object` o `Java.Lang.Object` en tiempo de ejecución. Por ejemplo, no se permiten los campos de tipo `System.Object` ni ningún tipo de interfaz. Se permiten los tipos que no pueden hacer referencia a las instancias `Java.Lang.Object`, como `System.String` y `List<int>`. Esta restricción es para evitar que la GC recopile objetos de forma prematura.

Si el tipo debe contener un campo de instancia que pueda hacer referencia a una instancia `Java.Lang.Object`, el tipo de campo debe ser `System.WeakReference` o `GCHandle`.

## <a name="binding-abstract-methods"></a>Enlace de métodos abstractos

Enlazar métodos `abstract` es, en gran medida, idéntico a enlazar métodos virtuales. Hay solo dos diferencias:

1. El método abstracto es abstracto. Aún conserva el atributo `[Register]` y el registro de método asociado. El enlace de métodos solo se traslada al tipo `Invoker`.

1. Se crea un tipo `Invoker` no `abstract` que subclasifica el tipo abstracto. El tipo `Invoker` debe invalidar todos los métodos abstractos declarados en la clase base y la implementación invalidada es la implementación de enlace de métodos, aunque puede omitirse el caso de envío no virtual.

Por ejemplo, suponga que el método `mono.android.test.Adder.add` anterior fuera `abstract`. El enlace de C# cambiaría de modo que `Adder.Add` fuera abstracto y se definiría un nuevo tipo `AdderInvoker` que implementó `Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

El tipo `Invoker` solo es necesario al obtener referencias de JNI a instancias creadas por Java.

## <a name="binding-interfaces"></a>Enlace de interfaces

El enlace de interfaces es conceptualmente similar al enlace de clases que contienen métodos virtuales, pero muchas de las características específicas difieren de formas sutiles (y no tan sutiles). Tenga en cuenta la siguiente [declaración de interfaz de Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Los enlaces de interfaz tienen dos partes: la definición de interfaz de C# y una definición de Invoker para la interfaz.

### <a name="interface-definition"></a>Definición de interfaz

La definición de interfaz de C# debe cumplir los requisitos siguientes:

- La definición de interfaz debe tener un atributo personalizado `[Register]`.

- La definición de interfaz debe ampliar la `IJavaObject interface`.
    Si no lo hace, se impedirá que los ACW hereden de la interfaz de Java.

- Cada método de interfaz debe contener un atributo `[Register]` que especifique el nombre de método de Java correspondiente, la firma de JNI y el método de conector.

- El método de conector también debe especificar el tipo en el que se puede ubicar el método de conector.

Al enlazar los métodos `abstract` y `virtual`, el método de conector se buscaría en la jerarquía de herencia del tipo que se registra. Es posible que las interfaces no tengan ningún método que contenga cuerpos, por lo que esto no funciona. Por lo tanto, el requisito de que se especifique un tipo indica dónde se encuentra el método de conector. Se especifica el tipo en la cadena de método de conector, después de un signo de dos puntos `':'`, y debe ser el nombre de tipo calificado de ensamblado del tipo que contiene el invocador.

Las declaraciones de método de interfaz son una traducción del método de Java correspondiente mediante tipos *compatibles*. En el caso de los tipos integrados de Java, los tipos compatibles son los tipos de C# correspondientes, p. ej., Java `int` es C# `int`. En el caso de los tipos de referencia, el tipo compatible es un tipo que puede proporcionar un identificador de JNI del tipo de Java adecuado.

Java no invocó directamente los miembros de la interfaz &ndash; la invocación se realizará a través del tipo Invoker &ndash; por lo que se permite cierta flexibilidad.

La interfaz de progreso de Java se puede [declarar en C# como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Tras lo explicado anteriormente, tenga en cuenta que asignamos el parámetro `int[]` de Java a un elemento [JavaArray&lt;int&gt;](xref:Android.Runtime.JavaArray`1).
Esto no es necesario: podríamos haberlo enlazado a un elemento `int[]` de C# o a un elemento `IList<int>`, o bien alguna otra cosa completamente. Sea cual sea el tipo elegido, el `Invoker` debe poder traducirlo a un tipo `int[]` de Java para la invocación.

### <a name="invoker-definition"></a>Definición de Invoker

La definición de tipo `Invoker` debe heredar `Java.Lang.Object`, implementar la interfaz adecuada y proporcionar todos los métodos de conexión a los que se hace referencia en la definición de interfaz. Hay una sugerencia más que difiere de un enlace de clases: los identificadores de método y campo `class_ref` deben ser miembros de instancia, no miembros estáticos.

El motivo por el que se prefieren los miembros de instancia está relacionado con el comportamiento `JNIEnv.GetMethodID` del entorno de ejecución de Android (también puede tratarse del comportamiento de Java; no se ha probado). `JNIEnv.GetMethodID` devuelve NULL al buscar un método procedente de una interfaz implementada y no de la interfaz declarada. Tenga en cuenta la interfaz de Java [java.util.SortedMap&lt;K, V&gt;](https://developer.android.com/reference/java/util/SortedMap.html), que implementa la interfaz [java.util.Map&lt;K, V&gt;](https://developer.android.com/reference/java/util/Map.html). El mapa proporciona un método [clear](https://developer.android.com/reference/java/util/Map.html#clear()). Por lo tanto, una definición `Invoker` aparentemente razonable para SortedMap sería:

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

Lo anterior producirá un error, ya que `JNIEnv.GetMethodID` devolverá `null` al buscar el método `Map.clear` a través de la instancia de clase `SortedMap`.

Existen dos soluciones a esto: realice un seguimiento de la interfaz de la que proceden todos los métodos y tenga un elemento `class_ref` para cada interfaz, o bien mantenga todo como miembros de instancia y realice la búsqueda del método en el tipo de clase más derivado, no en el tipo de interfaz. Esta última acción se ha completado en **Mono.Android.dll**.

La definición de Invoker tiene seis secciones: el constructor, el método `Dispose`, los miembros `ThresholdType` y `ThresholdClass`, el método `GetObject`, la implementación de métodos de interfaz y la implementación de métodos de conector.

#### <a name="constructor"></a>Constructor

El constructor debe buscar la clase en tiempo de ejecución de la instancia que se invoca y almacenarla en el campo `class_ref` de instancia:

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Nota: La propiedad `Handle` debe usarse en el cuerpo del constructor y no en el parámetro `handle`, ya que en Android v4.0 es posible que el parámetro `handle` no sea válido una vez que el constructor base termine de ejecutarse.

#### <a name="dispose-method"></a>Dispose (Método)

El método `Dispose` debe liberar la referencia global asignada en el constructor:

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```

#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType y ThresholdClass

Los miembros `ThresholdType` y `ThresholdClass` son idénticos a lo que se encuentra en un enlace de clases:

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

#### <a name="getobject-method"></a>GetObject (Método)

Un método `GetObject` estático es necesario para admitir [Extensions.JavaCast&lt;T&gt;()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Métodos de interfaz

Todos los métodos de la interfaz deben tener una implementación, que invoca el método de Java correspondiente a través de JNI:

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```

#### <a name="connector-methods"></a>Métodos de conector

Los métodos de conector y la compatibilidad de la infraestructura son responsables de serializar los parámetros de JNI para los tipos de C# adecuados. El parámetro `int[]` de Java se pasará como una `jintArray` de JNI, que es un elemento `IntPtr` dentro de C#. El elemento `IntPtr` debe serializarse para un elemento `JavaArray<int>` a fin de admitir la invocación de la interfaz de C#:

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Si `int[]` fuera preferible a `JavaList<int>`, [JNIEnv.GetArray()](xref:Android.Runtime.JNIEnv.GetArray*) podría usarse en su lugar:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Tenga en cuenta, sin embargo, que `JNIEnv.GetArray` copia toda la matriz entre las máquinas virtuales, por lo que en el caso de las matrices de gran tamaño, esto podría dar lugar a mucha de la presión de GC agregada.

### <a name="complete-invoker-definition"></a>Definición de Invoker completa

La [definición de IAdderProgressInvoker completa](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```

## <a name="jni-object-references"></a>Referencias a objetos de JNI

Muchos métodos JNIEnv devuelven *referencias a objetos* de *JNI*, que son similares a los identificadores `GCHandle`s. JNI proporciona tres tipos diferentes de referencias a objetos: referencias locales, referencias globales y referencias globales débiles. Los tres se representan como `System.IntPtr`, *pero* (según la sección Tipos de funciones de JNI) no todos los elementos `IntPtr`s devueltos desde los métodos `JNIEnv` son referencias. Por ejemplo, [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) devuelve un elemento `IntPtr`, pero no devuelve una referencia a objetos, devuelve un identificador `jmethodID`. Consulte la [documentación de la función de JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obtener detalles.

La *mayoría* de los métodos de creación de referencias crean las referencias locales.
Android solo permite la existencia de un número limitado de referencias locales en cualquier momento, normalmente 512. Las referencias locales se pueden eliminar a través de [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
A diferencia de JNI, no todos los métodos JNIEnv de referencia que devuelven referencias a objetos devuelven referencias locales; [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*) devuelve una referencia *global*. Se recomienda encarecidamente que elimine las referencias locales lo más rápido que pueda, posiblemente construyendo un objeto [Java.Lang.Object](xref:Java.Lang.Object) en torno al objeto y especificando `JniHandleOwnership.TransferLocalRef` al constructor [Java.Lang.Object(IntPtr handle, JniHandleOwnership transfer)](xref:Java.Lang.Object#ctor*).

[JNIEnv.NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) y [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*) crean las referencias globales.
Se pueden destruir con [JNIEnv.DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Los emuladores tienen un límite de 2000 referencias globales pendientes, mientras que los dispositivos de hardware tienen un límite de unas 52 000 referencias globales.

Las referencias globales débiles solo están disponibles en Android v2.2 (Froyo) y versiones posteriores. Las referencias globales débiles se pueden eliminar con [JNIEnv.DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Gestión de las referencias locales de JNI

Los métodos [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) y [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) devuelven un elemento `IntPtr` que contiene una referencia local de JNI a un objeto Java, o `IntPtr.Zero` si Java devolvió `null`. Debido al número limitado de referencias locales que pueden estar pendientes a la vez (512 entradas), es recomendable garantizar que las referencias se eliminen a tiempo. Existen tres formas en que pueden abordarse las referencias locales: eliminarlas explícitamente, crear una instancia `Java.Lang.Object` para almacenarlas y usar `Java.Lang.Object.GetObject<T>()` para crear un contenedor que se llama administrado en torno a ellas.

### <a name="explicitly-deleting-local-references"></a>Eliminación explícita de las referencias locales

[JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) se usa para eliminar las referencias locales. Una vez que se ha eliminado la referencia local, no se puede volver a usar, de modo que debe tenerse cuidado para garantizar que la referencia `JNIEnv.DeleteLocalRef` sea lo último que se haga con la referencia local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Ajuste con Java.Lang.Object

`Java.Lang.Object` proporciona un constructor [Java.Lang.Object(IntPtr handle, JniHandleOwnership transfer)](xref:Java.Lang.Object#ctor*) que se puede usar para ajustar una referencia de JNI existente. El parámetro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina cómo debe tratarse el parámetro `IntPtr`:

- [JniHandleOwnership.DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; la instancia `Java.Lang.Object` creará una nueva referencia global a partir del parámetro `handle` y `handle` permanece sin cambios.
    El autor de llamada es responsable de liberar `handle`, en caso necesario.

- [JniHandleOwnership.TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; la instancia `Java.Lang.Object` creada creará una nueva referencia global a partir del parámetro `handle` y `handle` se elimina con [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . El autor de llamada no debe liberar `handle` y no debe usar `handle` una vez finalizada la ejecución por parte del constructor.

- [JniHandleOwnership.TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; la instancia `Java.Lang.Object` creada tomará el control de la propiedad del parámetro `handle`. El autor de llamada no debe liberar `handle`.

Puesto que los métodos de invocación de método de JNI devuelven las referencias locales, `JniHandleOwnership.TransferLocalRef` se usaría normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La referencia global creada no se liberará hasta que se recolecte la instancia `Java.Lang.Object` no usada. Siempre que sea capaz, la eliminación de la instancia liberará la referencia global, acelerando las recolecciones de elementos no utilizados:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Uso de Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` proporciona un método [Java.Lang.Object.GetObject&lt;T&gt;(IntPtr handle, JniHandleOwnership transfer)](xref:Java.Lang.Object.GetObject*) que se puede usar para crear un contenedor que se llama administrado del tipo especificado.

El tipo `T` debe cumplir los requisitos siguientes:

1. `T` debe ser un tipo de referencia.

1. `T` debe implementar la interfaz `IJavaObject`.

1. Si `T` no es una clase o interfaz abstracta, `T` debe proporcionar un constructor con los tipos de parámetro `(IntPtr,
    JniHandleOwnership)`.

1. Si `T` es una clase abstracta o una interfaz, *debe* haber un *invocador* disponible para `T`. Un invocador es un tipo no abstracto que hereda `T` o implementa `T`, y tiene el mismo nombre que `T` con un sufijo Invoker. Por ejemplo, si T es la interfaz `Java.Lang.IRunnable`, el tipo `Java.Lang.IRunnableInvoker` debe existir y contener el constructor `(IntPtr,
    JniHandleOwnership)` requerido.

Puesto que los métodos de invocación de método de JNI devuelven las referencias locales, `JniHandleOwnership.TransferLocalRef` se usaría normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Búsqueda de tipos de Java

Para buscar un campo o método en JNI, el tipo declarativo del campo o método debe buscarse primero. El método [Android.Runtime.JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)) se usa para buscar tipos de Java. El parámetro de cadena es la *referencia de tipo simplificada* o la *referencia de tipo completa* para el tipo de Java. Consulte la [sección Referencias de tipo de JNI](#_JNI_Type_References) para obtener detalles sobre referencias de tipo simplificadas y completas.

Nota: A diferencia de todos los demás métodos `JNIEnv` que devuelven instancias de objeto `FindClass`, devuelve una referencia global, no una referencia local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instancia

Los campos se manipulan a través de *identificadores de campo*. Los identificadores de campo se obtienen a través del [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), que requiere la clase en que se define el campo, el nombre del campo y la [signatura de tipo de JNI](#JNI_Type_Signatures) del campo.

No es necesario liberar los identificadores de campo y son válidos siempre que se cargue el tipo de Java correspondiente (Android no admite actualmente la descarga de clases).

Existen dos conjuntos de métodos para manipular los campos de instancia: uno para leerlos y el otro para escribirlos. Todos los conjuntos de métodos requieren un identificador de campo para leer o escribir el valor de campo.

### <a name="reading-instance-field-values"></a>Lectura de valores del campo de instancia

El conjunto de métodos para leer valores del campo de instancia sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

donde `*` es el tipo del campo:

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; lectura del valor de cualquier campo de instancia que no sea un tipo integrado, como `java.lang.Object`, matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

- [JNIEnv.GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; lectura del valor de los campos de instancia `bool`.

- [JNIEnv.GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; lectura del valor de los campos de instancia `sbyte`.

- [JNIEnv.GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; lectura del valor de los campos de instancia `char`.

- [JNIEnv.GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; lectura del valor de los campos de instancia `short`.

- [JNIEnv.GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; lectura del valor de los campos de instancia `int`.

- [JNIEnv.GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; lectura del valor de los campos de instancia `long`.

- [JNIEnv.GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; lectura del valor de los campos de instancia `float`.

- [JNIEnv.GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; lectura del valor de los campos de instancia `double`.

### <a name="writing-instance-field-values"></a>Escritura de valores del campo de instancia

El conjunto de métodos para escribir valores del campo de instancia sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

donde *Tipo* es el tipo del campo:

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escritura del valor de cualquier campo que no sea un tipo integrado, como `java.lang.Object`, matrices y tipos de interfaz. El valor `IntPtr` puede ser una referencia local de JNI, una referencia global de JNI, una referencia global débil de JNI o `IntPtr.Zero` (para `null`).

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `bool`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `sbyte`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `char`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `short`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `int`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `long`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `float`.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*))   &ndash; escritura del valor de los campos de instancia `double`.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Los campos estáticos se manipulan a través de *identificadores de campo*. Los identificadores de campo se obtienen a través del [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), que requiere la clase en que se define el campo, el nombre del campo y la [signatura de tipo de JNI](#JNI_Type_Signatures) del campo.

No es necesario liberar los identificadores de campo y son válidos siempre que se cargue el tipo de Java correspondiente (Android no admite actualmente la descarga de clases).

Existen dos conjuntos de métodos para manipular los campos estáticos: uno para leer campos de instancia y el otro para escribirlos. Todos los conjuntos de métodos requieren un identificador de campo para leer o escribir el valor de campo.

### <a name="reading-static-field-values"></a>Lectura de valores del campo estático

El conjunto de métodos para leer valores del campo estático sigue el patrón de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

donde `*` es el tipo del campo:

- [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; lectura del valor de cualquier campo estático que no sea un tipo integrado, como `java.lang.Object`, matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

- [JNIEnv.GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; lectura del valor de los campos estáticos `bool`.

- [JNIEnv.GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; lectura del valor de los campos estáticos `sbyte`.

- [JNIEnv.GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; lectura del valor de los campos estáticos `char`.

- [JNIEnv.GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; lectura del valor de los campos estáticos `short`.

- [JNIEnv.GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; lectura del valor de los campos estáticos `long`.

- [JNIEnv.GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; lectura del valor de los campos estáticos `float`.

- [JNIEnv.GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; lectura del valor de los campos estáticos `double`.

### <a name="writing-static-field-values"></a>Escritura de valores del campo estático

El conjunto de métodos para escribir valores del campo estático sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

donde *Tipo* es el tipo del campo:

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escritura del valor de cualquier campo estático que no sea un tipo integrado, como `java.lang.Object`, matrices y tipos de interfaz. El valor `IntPtr` puede ser una referencia local de JNI, una referencia global de JNI, una referencia global débil de JNI o `IntPtr.Zero` (para `null`).

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `bool`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `sbyte`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `char`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `short`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `int`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `long`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `float`.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*))   &ndash; escritura del valor de los campos estáticos `double`.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de instancia

Los métodos de instancia se invocan a través de *identificadores de método*. Los identificadores de método se obtienen a través del [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), que requiere el tipo en el que se define el método, el nombre del método y la [signatura de tipo de JNI](#JNI_Type_Signatures) del método.

No es necesario liberar los identificadores de método y son válidos siempre que se cargue el tipo de Java correspondiente (Android no admite actualmente la descarga de clases).

Existen dos conjuntos de métodos para invocar los métodos: uno para invocarlos de forma virtual y el otro para hacerlo de forma no virtual. Ambos conjuntos de métodos requieren un identificador de método para invocar este, mientras que la invocación no virtual también requiere que especifique la implementación de clase que se debe invocar.

Los métodos de interfaz solo se pueden buscar en el tipo declarativo; los métodos que proceden de interfaces extendidas o heredadas no se pueden buscar. Consulte la sección Enlace de interfaces o Implementación de Invoker posterior para obtener más detalles.

Se puede buscar cualquier método declarado en la clase o cualquier clase base o interfaz implementada.

### <a name="virtual-method-invocation"></a>Invocación del método virtual

El conjunto de métodos para invocar métodos de forma virtual sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; invocación de un método que devuelve un tipo no integrado como `java.lang.Object`, matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv.CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; invocación de un método que devuelve un valor `bool`.

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; invocación de un método que devuelve un valor `sbyte`.

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; invocación de un método que devuelve un valor `char`.

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; invocación de un método que devuelve un valor `short`.

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invocación de un método que devuelve un valor `long`.

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; invocación de un método que devuelve un valor `float`.

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; invocación de un método que devuelve un valor `double`.

### <a name="non-virtual-method-invocation"></a>Invocación del método no virtual

El conjunto de métodos para invocar métodos de forma no virtual sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método. La invocación del método no virtual se suele usar para invocar el método base de un método virtual.

- [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; invocación no virtual de un método que devuelve un tipo no integrado como `java.lang.Object`, matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv.CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; invocación no virtual de un método que devuelve un valor `bool`.

- [JNIEnv.CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; invocación no virtual de un método que devuelve un valor `sbyte`.

- [JNIEnv.CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; invocación no virtual de un método que devuelve un valor `char`.

- [JNIEnv.CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; invocación no virtual de un método que devuelve un valor `short`.

- [JNIEnv.CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; invocación no virtual de un método que devuelve un valor `long`.

- [JNIEnv.CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; invocación no virtual de un método que devuelve un valor `float`.

- [JNIEnv.CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; invocación no virtual de un método que devuelve un valor `double`.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Los métodos estáticos se invocan a través de *identificadores de método*. Los identificadores de método se obtienen a través del [JNIEnv.GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), que requiere el tipo en el que se define el método, el nombre del método y la [signatura de tipo de JNI](#JNI_Type_Signatures) del método.

No es necesario liberar los identificadores de método y son válidos siempre que se cargue el tipo de Java correspondiente (Android no admite actualmente la descarga de clases).

### <a name="static-method-invocation"></a>Invocación de método estático

El conjunto de métodos para invocar métodos de forma virtual sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; invocación de un método estático que devuelve un tipo no integrado como `java.lang.Object`, matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv.CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; invocación de un método estático que devuelve un valor `bool`.

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; invocación de un método estático que devuelve un valor `sbyte`.

- [JNIEnv.CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; invocación de un método estático que devuelve un valor `char`.

- [JNIEnv.CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; invocación de un método estático que devuelve un valor `short`.

- [JNIEnv.CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invocación de un método estático que devuelve un valor `long`.

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; invocación de un método estático que devuelve un valor `float`.

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; invocación de un método estático que devuelve un valor `double`.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Signaturas de tipo de JNI

Las [signaturas de tipo de JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) son [referencias de tipo de JNI](#_JNI_Type_References) (aunque no referencias de tipo simplificadas), excepto para los métodos. Con los métodos, la signatura de tipo de JNI es un paréntesis de apertura `'('`, seguido de las referencias de tipo para todos los tipos de parámetro concatenados de forma conjunta (sin comas de separación ni nada más), seguidos de un paréntesis de cierre `')'`, seguido de la referencia de tipo de JNI del tipo de valor devuelto de método.

Por ejemplo, dado el método de Java:

```java
long f(int n, String s, int[] array);
```

La signatura de tipo de JNI sería:

```csharp
(ILjava/lang/String;[I)J
```

En general, se recomienda *encarecidamente* usar el comando `javap` para determinar las firmas de JNI. Por ejemplo, la signatura de tipo de JNI del método [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) es "(Ljava/lang/String;)Ljava/lang/Thread$State;", mientras que la signatura de tipo de JNI del método [java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) es "()[Ljava/lang/Thread$State;". Esté atento a los signos de punto y coma al final; estos *forman* parte de la signatura de tipo de JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referencias de tipo de JNI

Las referencias de tipo de JNI son distintas de las referencias de tipo de Java. No puede usar nombres de tipo de Java completos como `java.lang.String` con JNI. En su lugar, debe usar las variaciones de JNI `"java/lang/String"` o `"Ljava/lang/String;"`, dependiendo del contexto; consulte a continuación para obtener detalles.
Hay cuatro tipos de referencias de tipo de JNI:

- **integradas**
- **simplificadas**
- **type**
- **array**

### <a name="built-in-type-references"></a>Referencias de tipo integradas

Las referencias de tipo integradas son un solo carácter, usado para hacer referencia de los tipos de valor integrados. La asignación es la siguiente:

- `"B"` para  `sbyte` .
- `"S"` para  `short` .
- `"I"` para  `int` .
- `"J"` para  `long` .
- `"F"` para  `float` .
- `"D"` para  `double` .
- `"C"` para  `char` .
- `"Z"` para  `bool` .
- `"V"` para los tipos de valor devuelto del método `void`.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referencias de tipo simplificadas

Las referencias de tipo simplificadas solo se pueden usar en [JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)).
Existen dos formas de derivar una referencia de tipo simplificada:

1. En un nombre de Java completo, reemplazar cada `'.'` en el nombre del paquete y antes del nombre de tipo por `'/'` y cada `'.'` en un nombre de tipo por `'$'`.

1. Leer la salida de `'unzip -l android.jar | grep JavaName'`.

Cualquiera de las dos dará lugar a la asignación de [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) de tipo de Java a la referencia de tipo simplificada `java/lang/Thread$State`.

### <a name="type-references"></a>Referencias de tipo

Una referencia de tipo es una referencia de tipo integrada o una referencia de tipo simplificada con un prefijo `'L'` y un sufijo `';'`. En el caso de [java.lang.String](https://developer.android.com/reference/java/lang/String.html) de tipo de Java, la referencia de tipo simplificada es `"java/lang/String"`, mientras que la referencia de tipo es `"Ljava/lang/String;"`.

Las referencias de tipo se usan con referencias de tipo de matriz y con firmas de JNI.

Una forma adicional de obtener una referencia de tipo es leer la salida de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependiendo del tipo implicado, puede usar una declaración de constructor o un tipo de valor devuelto del método para determinar el nombre de JNI. Por ejemplo:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` es un tipo de enumeración de Java, por lo que podemos usar la signatura del método `valueOf` para determinar que la referencia de tipo es Ljava/lang/Thread$State;.

### <a name="array-type-references"></a>Referencias de tipo de matriz

Las referencias de tipo de matriz tienen el prefijo `'['`, que precede a una referencia de tipo de JNI.
No se pueden usar referencias de tipo simplificadas al especificar matrices.

Por ejemplo, `int[]` es `"[I"`, `int[][]` es `"[[I"` y `java.lang.Object[]` es `"[Ljava/lang/Object;"`.

## <a name="java-generics-and-type-erasure"></a>Genéricos de Java y supresión de tipos

La *mayoría* del tiempo, como se ve a través de JNI, los genéricos de Java *no existen*.
Hay algunos "aspectos a tener en cuenta", pero estos están relacionados con cómo interactúa Java con los genéricos, no con cómo JNI busca e invoca a los miembros genéricos.

No existe ninguna diferencia entre un tipo genérico o un miembro y un tipo no genérico o un miembro al interactuar a través de JNI. Por ejemplo, el tipo genérico [java.lang.Class&lt;T&gt;](https://developer.android.com/reference/java/lang/Class.html) también es el tipo genérico "sin formato" `java.lang.Class`. Ambos tienen la misma referencia de tipo simplificada, `"java/lang/Class"`.

## <a name="java-native-interface-support"></a>Compatibilidad con Java Native Interface

[Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv) es un contenedor administrado para Java Native Interface (JNI). Las funciones de JNI se declaran en la [especificación de Java Native Interface](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), aunque los métodos se han cambiado para quitar el parámetro `JNIEnv*` explícito y `IntPtr` se usa en lugar de `jobject`, `jclass`, `jmethodID`, etc. Por ejemplo, tenga en cuenta la [función NewObject de JNI](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Esto se expone como el método [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*):

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Traducir entre las dos llamadas es razonablemente sencillo. En C tendría:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

El equivalente de C# sería:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una vez que conserve una instancia de objeto Java en un elemento IntPtr, es probable que desee hacer algo con ella. Puede usar métodos JNIEnv como [JNIEnv.CallVoidMethod()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) para hacerlo, pero si ya hay un contenedor de C# analógico deseará construir un contenedor a través de la referencia de JNI. Puede hacerlo a través del método de extensión [Extensions.JavaCast\<T>](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

También puede usar el método [Java.Lang.Object.GetObject\<T>](xref:Java.Lang.Object.GetObject*):

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Además, todas las funciones de JNI se han modificado quitando el parámetro `JNIEnv*` presente en todas las funciones de JNI.

## <a name="summary"></a>Resumen

Lidiar directamente con JNI es una experiencia terrible que debe evitarse a toda costa. Desafortunadamente, no siempre puede evitarse; esperamos que esta guía proporcione algo de ayuda al obtener acceso a los casos de Java sin enlazar con Mono para Android.

## <a name="related-links"></a>Vínculos relacionados

- [Especificación de Java Native Interface](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funciones de Java Native Interface](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
