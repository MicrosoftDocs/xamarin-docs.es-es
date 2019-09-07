---
title: Trabajo con JNI y Xamarin. Android
description: Xamarin. Android permite escribir aplicaciones Android con C# en lugar de Java. Se proporcionan varios ensamblados con Xamarin. Android que proporcionan enlaces para las bibliotecas de Java, incluidos mono. Android. dll y mono. Android. GoogleMaps. dll. Sin embargo, no se proporcionan enlaces para cada posible biblioteca Java, y los enlaces que se proporcionan no pueden enlazar todos los tipos y miembros de Java. Para usar tipos y miembros de Java sin enlazar, se puede usar la interfaz nativa de Java (JNI). En este artículo se muestra cómo usar JNI para interactuar con tipos y miembros de Java desde aplicaciones de Xamarin. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 9b4ecae0ce37aeb9893a4cb5e55da951789be182
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761436"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Trabajo con JNI y Xamarin. Android

_Xamarin. Android permite escribir aplicaciones Android con C# en lugar de Java. Se proporcionan varios ensamblados con Xamarin. Android que proporcionan enlaces para las bibliotecas de Java, incluidos mono. Android. dll y mono. Android. GoogleMaps. dll. Sin embargo, no se proporcionan enlaces para cada posible biblioteca Java, y los enlaces que se proporcionan no pueden enlazar todos los tipos y miembros de Java. Para usar tipos y miembros de Java sin enlazar, se puede usar la interfaz nativa de Java (JNI). En este artículo se muestra cómo usar JNI para interactuar con tipos y miembros de Java desde aplicaciones de Xamarin. Android._

## <a name="overview"></a>Información general

No siempre es necesario o no es posible crear un contenedor al que se puede llamar (MCW) administrado para invocar código Java. En muchos casos, el JNI "inline" es absolutamente aceptable y útil para el uso único de miembros de Java sin enlazar. A menudo es más fácil usar JNI para invocar un solo método en una clase de Java que generar un enlace jar completo.

Xamarin. Android proporciona el `Mono.Android.dll` ensamblado, que proporciona un enlace para la `android.jar` biblioteca de Android. Los tipos y miembros que no `Mono.Android.dll` están presentes en los tipos `android.jar` y que no están presentes en se pueden utilizar para enlazarlos manualmente. Para enlazar tipos y miembros de Java, se usa la **interfaz nativa de Java** (**JNI**) para buscar tipos, leer y escribir campos e invocar métodos.

La API de JNI en Xamarin. Android es conceptualmente muy similar a `System.Reflection` la API de .net: permite buscar tipos y miembros por nombre, leer y escribir valores de campo, invocar métodos, etc. Puede usar JNI y el `Android.Runtime.RegisterAttribute` atributo personalizado para declarar métodos virtuales que se pueden enlazar para admitir el reemplazo. Puede enlazar interfaces para que se puedan implementar en C#.

En este documento se explica:

- Cómo JNI hace referencia a los tipos.
- Cómo buscar, leer y escribir campos.
- Cómo buscar e invocar métodos.
- Cómo exponer métodos virtuales para permitir el reemplazo desde código administrado.
- Cómo exponer interfaces.

## <a name="requirements"></a>Requisitos

JNI, tal y como se expone a través del [espacio de nombres Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv), está disponible en todas las versiones de Xamarin. Android.
Para enlazar tipos e interfaces de Java, debe usar Xamarin. Android 4,0 o posterior.

## <a name="managed-callable-wrappers"></a>Contenedores a los que se puede llamar administrados

Un contenedor al que se **puede llamar administrado** (**MCW**) es un *enlace* para una clase o interfaz de Java que incluye toda la maquinaria JNI para C# que el código de cliente no tenga que preocuparse por la complejidad subyacente de JNI. La mayoría `Mono.Android.dll` de consta de contenedores a los que se puede llamar administrados.

Los contenedores a los que se puede llamar tienen dos propósitos:

1. Encapsular el uso de JNI para que el código de cliente no tenga que conocer la complejidad subyacente.
1. Permite que los tipos de Java de subclases e implementen interfaces de Java.

El primer propósito es solo por comodidad y encapsulación de complejidad para que los consumidores tengan un conjunto sencillo y administrado de clases para usar. Esto requiere el uso de los distintos miembros de [JNIEnv](xref:Android.Runtime.JNIEnv) tal y como se describe más adelante en este artículo. Tenga en cuenta que los contenedores a los que se puede &ndash; llamar administrados no son estrictamente necesarios "en línea" el uso de JNI es absolutamente aceptable y es útil para el uso único de miembros de Java sin enlazar. La subclase y la implementación de la interfaz requieren el uso de contenedores a los que se puede llamar administrados.

## <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

Los contenedores a los que se puede llamar de Android (ACW) son necesarios siempre que el tiempo de ejecución de Android (ART) debe invocar código administrado. Estos contenedores son necesarios porque no hay ninguna manera de registrar clases con arte en tiempo de ejecución.
(En concreto, la función JNI de [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) no es compatible con el tiempo de ejecución de Android. Por lo tanto, los contenedores a los que se puede llamar de Android constituyen la falta de compatibilidad con el registro de tipos en tiempo de ejecución.

Cada vez que el código de Android necesita ejecutar un método virtual o de interfaz que se invalida o se implementa en código administrado, Xamarin. Android debe proporcionar un proxy de Java para que este método se envíe al tipo administrado adecuado. Estos tipos de proxy Java son código Java que tienen la misma clase base y la misma lista de interfaces de Java que el tipo administrado, implementando los mismos constructores y declarando cualquier método de interfaz y clase base invalidado.

Los contenedores a los que se puede llamar de Android se generan mediante el programa **monodroid. exe** durante el [proceso de compilación](~/android/deploy-test/building-apps/build-process.md), y se generan para todos los tipos que heredan (directa o indirectamente) [java. lang. Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implementar interfaces

Hay ocasiones en las que puede que necesite implementar una interfaz de Android (como [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks)).

Todas las clases e interfaces de Android amplían la interfaz de [Android. Runtime. IJavaObject.](xref:Android.Runtime.IJavaObject) por lo tanto, todos los tipos `IJavaObject`de Android deben implementar.
Xamarin. Android se aprovecha de este hecho &ndash; que usa `IJavaObject` para proporcionar Android con un proxy de Java (un contenedor de Android al que se puede llamar) para el tipo administrado dado. Dado que **monodroid. exe** solo busca `Java.Lang.Object` subclases (que deben implementar `IJavaObject`), la subclase `Java.Lang.Object` nos proporciona una manera de implementar interfaces en código administrado. Por ejemplo:

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

*En el resto de este artículo se proporcionan detalles de implementación sujetos a cambios sin previo aviso* . (y se presenta aquí solo porque los desarrolladores pueden tener curiosidad sobre lo que está ocurriendo en el capó).

Por ejemplo, dado el siguiente C# origen:

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

El programa **mandroid. exe** generará el siguiente contenedor de Android al que se puede llamar:

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

Observe que la clase base se conserva y se proporcionan las declaraciones de método nativo para cada método que se reemplaza dentro del código administrado.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute y ExportFieldAttribute

Normalmente, Xamarin. Android genera automáticamente el código Java que comprende el ACW; esta generación se basa en los nombres de clase y método cuando una clase se deriva de una clase de Java e invalida los métodos de Java existentes. Sin embargo, en algunos escenarios, la generación de código no es adecuada, como se describe a continuación:

- Android admite nombres de acción en los atributos XML de diseño, por ejemplo, el atributo XML [Android: OnClick](xref:Android.Views.View.IOnClickListener.OnClick*) . Cuando se especifica, la instancia de vista no plana intenta buscar el método Java.

- La interfaz [java. IO. serializable](https://developer.android.com/reference/java/io/Serializable.html) requiere `readObject` métodos `writeObject` y. Dado que no son miembros de esta interfaz, nuestra implementación administrada correspondiente no expone estos métodos a código Java.

- La interfaz de [Android. os.](xref:Android.OS.Parcelable) reutilizable espera que una clase de implementación deba tener un `CREATOR` campo estático `Parcelable.Creator`de tipo. El código Java generado requiere algún campo explícito. Con nuestro escenario estándar, no hay ninguna manera de enviar el campo en código Java desde código administrado.

Dado que la generación de código no proporciona una solución para generar métodos Java arbitrarios con nombres arbitrarios, a partir de Xamarin. Android 4,2, [ExportAttribute](xref:Java.Interop.ExportAttribute) y [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) se introdujeron para ofrecer una solución a lo anterior situación. Ambos atributos residen en el `Java.Interop` espacio de nombres:

- `ExportAttribute`&ndash; especifica un nombre de método y sus tipos de excepción esperados (para proporcionar "throws" explícitos en Java). Cuando se usa en un método, el método "exporta" un método Java que genera un código de envío a la invocación de JNI correspondiente al método administrado. Se puede usar con `android:onClick` y. `java.io.Serializable`

- `ExportFieldAttribute`&ndash; especifica un nombre de campo. Reside en un método que funciona como inicializador de campo. Se puede usar con `android.os.Parcelable`.

El proyecto de ejemplo [ExportAttribute](https://docs.microsoft.com/samples/xamarin/monodroid-samples/exportattribute) muestra cómo utilizar estos atributos.

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solución de problemas de ExportAttribute y ExportFieldAttribute

- Se produce un error de empaquetado debido a que falta **mono. Android. Export. dll** &ndash; si usa `ExportAttribute` o `ExportFieldAttribute` en algunos métodos del código o bibliotecas dependientes, tiene que agregar **mono. Android. Export. dll**. Este ensamblado está aislado para admitir el código de devolución de llamada de Java. Es independiente de **mono. Android. dll** a medida que agrega tamaño adicional a la aplicación.

- En la compilación de `MissingMethodException` versión, se produce &ndash; para los métodos de `MissingMethodException` exportación de la compilación de versión, se produce para los métodos de exportación. (Este problema se ha corregido en la versión más reciente de Xamarin. Android).

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute`y `ExportFieldAttribute` proporcionan funcionalidad que el código en tiempo de ejecución de Java puede usar. Este código en tiempo de ejecución tiene acceso al código administrado a través de los métodos de JNI generados por esos atributos. Como resultado, no hay ningún método Java existente enlazado por el método administrado; por lo tanto, el método de Java se genera a partir de una firma de método administrado.

Sin embargo, este caso no es totalmente determinante. En concreto, esto es cierto en algunas asignaciones avanzadas entre tipos administrados y tipos de Java como:

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

Cuando se necesitan tipos como estos para los métodos exportados `ExportParameterAttribute` , debe usarse para proporcionar explícitamente el parámetro o valor devuelto correspondiente a un tipo.

### <a name="annotation-attribute"></a>Atributo Annotation

En Xamarin. Android 4,2, se han `IAnnotation` convertido los tipos de implementación en atributos (System. Attribute) y se ha agregado compatibilidad con la generación de anotaciones en contenedores de Java.

Esto significa que los siguientes cambios direccionales:

- El generador de enlaces `Java.Lang.DeprecatedAttribute` genera `java.Lang.Deprecated` desde (mientras debe estar `[Obsolete]` en código administrado).

- Esto no significa que la clase `Java.Lang.Deprecated` existente desaparecerá. Estos objetos basados en Java podrían seguir utilizándose como objetos Java usuales (si existe dicho uso). Habrá clases `DeprecatedAttribute`y. `Deprecated`

- La `Java.Lang.DeprecatedAttribute` clase se marca como `[Annotation]` . Cuando hay un atributo personalizado que se hereda de este atributo `[Annotation]` , la tarea MSBuild generará una anotación Java para ese atributo personalizado (@Deprecated) en el contenedor de Android al que se puede llamar (ACW).

- Las anotaciones se pueden generar en clases, métodos y campos exportados (que es un método de código administrado).

Si la clase contenedora (la propia clase anotada o la clase que contiene los miembros anotados) no está registrada, no se genera ningún origen de la clase Java completa, incluidas las anotaciones. En el `ExportAttribute` caso de los métodos, puede especificar para obtener el método generado explícitamente y anotado. Además, no es una característica para "generar" una definición de clase de anotación de Java. En otras palabras, si define un atributo administrado personalizado para una anotación determinada, tendrá que agregar otra biblioteca. jar que contenga la clase de anotación de Java correspondiente. Agregar un archivo de código fuente de Java que define el tipo de anotación no es suficiente. El compilador de Java no funciona de la misma manera que **apt**.

Además, se aplican las siguientes limitaciones:

- Este proceso de conversión no tiene `@Target` en cuenta la anotación en el tipo de anotación hasta ahora.

- Los atributos en una propiedad no funcionan. En su lugar, use atributos para captador o establecedor de propiedad.

## <a name="class-binding"></a>Enlace de clases

Enlazar una clase significa escribir un contenedor al que se puede llamar administrado para simplificar la invocación del tipo Java subyacente.

El enlace de métodos virtuales y abstractos para permitir C# el reemplazo desde requiere Xamarin. Android 4,0. Sin embargo, cualquier versión de Xamarin. Android puede enlazar métodos no virtuales, métodos estáticos o métodos virtuales sin admitir invalidaciones.

Normalmente, un enlace contiene los siguientes elementos:

- [Identificador de JNI para el tipo de Java que se está enlazando](#_Looking_up_Java_Types).

- [Identificadores de campo JNI y propiedades para cada campo enlazado](#_Instance_Fields).

- [Identificadores y métodos de método JNI para cada método enlazado](#_Instance_Methods).

- Si se requiere la subclase, el tipo debe tener un atributo personalizado [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) en la declaración de tipos con [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) establecido en `true`.

### <a name="declaring-type-handle"></a>Declarar el identificador de tipo

Los métodos de búsqueda de campo y método requieren una referencia de objeto que haga referencia a su tipo declarativo. Por Convención, se mantiene en un `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Vea la sección [referencias de tipos de JNI](#_JNI_Type_References) para obtener `CLASS` más información sobre el token.

### <a name="binding-fields"></a>Campos de enlace

Los campos de Java se C# exponen como propiedades; por ejemplo, el campo [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) de Java se enlaza como la C# propiedad [java.lang.JavaSystem.in](xref:Java.Lang.JavaSystem.In).
Además, dado que JNI distingue entre los campos estáticos y los campos de instancia, se usan diferentes métodos al implementar las propiedades.

El enlace de campo implica tres conjuntos de métodos:

1. Método de *obtención de identificador de campo* . El método *Get Field ID* es responsable de devolver un identificador de campo que usarán los métodos *Get Field* Value y *set Field Value* . Para obtener el identificador de campo es necesario conocer el tipo declarativo, el nombre del campo y la [firma de tipo JNI](#JNI_Type_Signatures) del campo.

1. Métodos de *obtención de valor de campo* . Estos métodos requieren el identificador de campo y son responsables de leer el valor del campo desde Java.
    El método que se va a usar depende del tipo de campo.

1. Los métodos *set Field Value* . Estos métodos requieren el identificador de campo y son responsables de escribir el valor del campo en Java. El método que se va a usar depende del tipo de campo.

Los [campos estáticos](#_Static_Fields) usan los métodos [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field`y [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) .

 Los [campos de instancia](#_Instance_Fields) usan los métodos [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), `JNIEnv.Get*Field`y [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*) .

Por ejemplo, la propiedad `JavaSystem.In` estática se puede implementar como:

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

Nota: Estamos usando [InputStreamInvoker. FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) para convertir la referencia de JNI en una `System.IO.Stream` instancia de y estamos usando `JniHandleOwnership.TransferLocalRef` porque [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) devuelve una referencia local.

Muchos de los tipos de [Android. Runtime](xref:Android.Runtime) tienen `FromJniHandle` métodos que convertirán una referencia de JNI en el tipo deseado.

### <a name="method-binding"></a>Enlace de método

Los métodos de Java se C# exponen C# como métodos y como propiedades. Por ejemplo, el método Java [. lang. Runtime. runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) se enlaza como el método [java. lang. Runtime. runFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) y el método [java. lang. Object. getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) está enlazado como [java. lang. Object. class. ](xref:Java.Lang.Object.Class)propiedad.

La invocación de métodos es un proceso de dos pasos:

1. *Identificador del método get* para el método que se va a invocar. El método *Get Method ID* es responsable de devolver un identificador de método que usarán los métodos de invocación de método. Para obtener el identificador de método es necesario conocer el tipo declarativo, el nombre del método y la [firma de tipo JNI](#JNI_Type_Signatures) del método.

1. Invoque al método.

Al igual que con los campos, los métodos que se usan para obtener el identificador de método e invocar el método difieren entre los métodos estáticos y los métodos de instancia.

[Los métodos estáticos](#_Static_Methods_1) usan [JNIEnv. GetStaticMethodID ()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) para buscar el identificador de método y `JNIEnv.CallStatic*Method` usar la familia de métodos para la invocación.

[Los métodos de instancia](#_Instance_Methods) usan [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) para buscar el identificador de método y `JNIEnv.Call*Method` usar `JNIEnv.CallNonvirtual*Method` las familias de métodos y para la invocación.

El enlace de método es potencialmente más que simplemente invocación de método. El enlace de método también incluye permitir que un método se invalide (para los métodos abstractos y no finales) o implementado (para los métodos de interfaz). En la sección [herencia, interfaces,](#_Supporting_Inheritance,_Interfaces_1) se abordan las complejidades de la compatibilidad de métodos virtuales y métodos de interfaz.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

Enlazar un método estático implica `JNIEnv.GetStaticMethodID` usar para obtener un identificador de método y, a `JNIEnv.CallStatic*Method` continuación, utilizar el método adecuado, dependiendo del tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para el método [Runtime. getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) :

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

Tenga en cuenta que el identificador de método se almacena en un `id_getRuntime`campo estático,. Se trata de una optimización del rendimiento, de modo que no es necesario buscar el identificador del método en cada invocación. No es necesario almacenar en caché el identificador del método de esta manera. Una vez que se obtiene el identificador del método, se usa [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) para invocar el método. `JNIEnv.CallStaticObjectMethod`Devuelve un `IntPtr` que contiene el identificador de la instancia de Java devuelta.
[Java. lang. Object. GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) se usa para convertir el identificador de Java en una instancia de objeto fuertemente tipado.

#### <a name="non-virtual-instance-method-binding"></a>Enlace de método de instancia no virtual

Enlazar `final` un método de instancia o un método de instancia que no requiere invalidar, `JNIEnv.GetMethodID` implica el uso de para obtener un identificador de método `JNIEnv.Call*Method` y, a continuación, utilizar el método adecuado, dependiendo del tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para la `Object.Class` propiedad:

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

Tenga en cuenta que el identificador de método se almacena en un `id_getClass`campo estático,.
Se trata de una optimización del rendimiento, de modo que no es necesario buscar el identificador del método en cada invocación. No es necesario almacenar en caché el identificador del método de esta manera. Una vez que se obtiene el identificador del método, se usa [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) para invocar el método. `JNIEnv.CallStaticObjectMethod`Devuelve un `IntPtr` que contiene el identificador de la instancia de Java devuelta.
[Java. lang. Object. GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) se usa para convertir el identificador de Java en una instancia de objeto fuertemente tipado.

### <a name="binding-constructors"></a>Constructores de enlace

Los constructores son métodos de Java con `"<init>"`el nombre. Al igual que con los métodos de `JNIEnv.GetMethodID` instancia de Java, se usa para buscar el identificador del constructor. A diferencia de los métodos de Java, se usan los métodos [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) para invocar el identificador del método de constructor. El valor devuelto `JNIEnv.NewObject` de es una referencia local de JNI:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente, un enlace de clase creará subclases [java. lang. Object](xref:Java.Lang.Object).
Al subclases `Java.Lang.Object`, una semántica adicional entra en juego: una `Java.Lang.Object` instancia mantiene una referencia global a una instancia de Java a `Java.Lang.Object.Handle` través de la propiedad.

1. El `Java.Lang.Object` constructor predeterminado asignará una instancia de Java.

1. Si el tipo tiene un `RegisterAttribute` , y `RegisterAttribute.DoNotGenerateAcw` es `true` , se crea una instancia del `RegisterAttribute.Name` tipo a través de su constructor predeterminado.

1. De lo contrario, se crea una instancia del contenedor de `this.GetType` Android al que se [puede llamar](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondiente a a través de su constructor predeterminado. Los contenedores a los que se puede llamar de Android se `Java.Lang.Object` generan durante la creación `RegisterAttribute.DoNotGenerateAcw` del paquete para cada `true`subclase para la que no se establece en.

En el caso de los tipos que no son enlaces de clase, esta es la semántica esperada: la creación de instancias de una `Mono.Samples.HelloWorld.HelloAndroid` C# instancia debe construir una instancia de Java `mono.samples.helloworld.HelloAndroid` que sea un contenedor de Android al que se puede llamar.

En el caso de los enlaces de clase, puede ser el comportamiento correcto si el tipo de Java contiene un constructor predeterminado y/o no es necesario invocar ningún otro constructor. De lo contrario, se debe proporcionar un constructor que realice las siguientes acciones:

1. Invocar [java. lang. Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) en lugar del constructor predeterminado `Java.Lang.Object` . Esto es necesario para evitar la creación de una nueva instancia de Java.

1. Compruebe el valor de [java. lang. Object. Handle](xref:Java.Lang.Object.Handle) antes de crear cualquier instancia de Java. La `Object.Handle` propiedad tendrá un valor distinto de `IntPtr.Zero` si se construyó un contenedor de Android al que se puede llamar en código Java, y el enlace de clase se construye para que contenga la instancia de contenedor de Android al que se puede llamar. Por ejemplo, cuando Android crea una `mono.samples.helloworld.HelloAndroid` instancia, primero se creará el contenedor de Android Callable y el `HelloAndroid` constructor de Java creará una instancia del `Mono.Samples.HelloWorld.HelloAndroid` tipo correspondiente, con `Object.Handle` la propiedad establezca en la instancia de Java antes de la ejecución del constructor.

1. Si el tipo en tiempo de ejecución actual no es el mismo que el tipo declarativo, se debe crear una instancia del contenedor correspondiente de Android al que se puede llamar y usar [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) para almacenar el identificador devuelto por [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Si el tipo en tiempo de ejecución actual es el mismo que el tipo declarativo, invoque el constructor de Java y use [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) para `JNIEnv.NewInstance` almacenar el identificador devuelto por.

Por ejemplo, considere el constructor [java. lang. Integer (int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) . Está enlazado como:

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

Los [métodos JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) son `JNIEnv.FindClass`auxiliares para realizar, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`y `JNIEnv.DeleteGlobalReference` en el valor devuelto de `JNIEnv.FindClass`. Para obtener más información, vea la siguiente sección.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Compatibilidad de herencia, interfaces

La creación de subclases de un tipo de Java o la implementación de una interfaz de Java requiere la generación de [contenedores de Android Callable](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) que se generan para cada `Java.Lang.Object` subclase durante el proceso de empaquetado. La generación de ACW se controla mediante el atributo personalizado [Android. Runtime. RegisterAttribute](xref:Android.Runtime.RegisterAttribute) .

En C# el caso de `[Register]` los tipos, el constructor de atributos personalizados requiere un argumento: la [referencia de tipo simplificado de JNI](#_Simplified_Type_References_1) para el tipo de Java correspondiente. Esto permite proporcionar nombres diferentes entre Java y C#.

Antes de Xamarin. Android 4,0, el `[Register]` atributo personalizado no estaba disponible para "alias" tipos de Java existentes. Esto se debe a que el proceso de generación de ACW generaría ACWs para cada `Java.Lang.Object` subclase encontrada.

Xamarin. Android 4,0 presentó la propiedad [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) . Esta propiedad indica al proceso de generación de ACW que *omita* el tipo anotado, lo que permite la declaración de nuevos contenedores a los que se puede llamar administrados que no producirán la generación de ACWs en el momento de la creación del paquete. Esto permite enlazar tipos de Java existentes. Por ejemplo, considere la siguiente clase simple de Java `Adder`,, que contiene un método `add`,, que se agrega a los enteros y devuelve el resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

El `Adder` tipo se puede enlazar como:

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

Aquí, el `Adder` C# tipo escribe el alias `Adder` del tipo de Java. El `[Register]` atributo se usa para especificar el nombre `mono.android.test.Adder` de JNI del tipo Java y la `DoNotGenerateAcw` propiedad se usa para impedir la generación de ACW. Esto producirá la generación de un ACW para el `ManagedAdder` tipo, que crea correctamente subclases del `mono.android.test.Adder` tipo. Si no `RegisterAttribute.DoNotGenerateAcw` se hubiera usado la propiedad, el proceso de compilación de Xamarin. Android habría generado un `mono.android.test.Adder` nuevo tipo de Java. Esto produciría errores de compilación, ya que `mono.android.test.Adder` el tipo se presentaría dos veces, en dos archivos independientes.

### <a name="binding-virtual-methods"></a>Enlazar métodos virtuales

`ManagedAdder`subclases del tipo `Adder` de Java, pero no es especialmente interesante: C# `Adder` el tipo no define ningún método virtual, `ManagedAdder` por lo que no puede invalidar nada.

Los `virtual` métodos de enlace para permitir el reemplazo por subclases requieren varias cosas que deben realizarse y que se encuentran en las dos categorías siguientes:

1. **Enlace de método**

1. **Registro de métodos**

#### <a name="method-binding"></a>Enlace de método

Un enlace de método requiere la adición de dos miembros de soporte C# `Adder` a la `ThresholdType`definición: `ThresholdClass`, y.

##### <a name="thresholdtype"></a>ThresholdType

La `ThresholdType` propiedad devuelve el tipo actual del enlace:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`se utiliza en el enlace de método para determinar cuándo debe realizarse la distribución de métodos virtuales y no virtuales. Siempre debe devolver una `System.Type` instancia de que se corresponda con el C# tipo declarativo.

##### <a name="thresholdclass"></a>ThresholdClass

La `ThresholdClass` propiedad devuelve la referencia de clase JNI para el tipo enlazado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`se utiliza en el enlace de método al invocar métodos no virtuales.

#### <a name="binding-implementation"></a>Implementación de enlace

La implementación de enlace de método es responsable de la invocación en tiempo de ejecución del método Java. También contiene una `[Register]` declaración de atributo personalizado que forma parte del registro del método y se explicará en la sección registro del método:

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

El `id_add` campo contiene el identificador de método para el método Java que se va a invocar. El `id_add` valor se obtiene de `JNIEnv.GetMethodID`, que requiere la clase declarativa`class_ref`(), el nombre del método`"add"`Java () y la firma JNI del método (`"(II)I"`).

Una vez que se obtiene el identificador `GetType` de método, `ThresholdType` se compara con para determinar si se requiere un envío virtual o no virtual. El envío virtual es necesario `GetType` cuando `ThresholdType`coincide, `Handle` ya que puede hacer referencia a una subclase asignada por Java que invalida el método.

Cuando `GetType` no coincide `ThresholdType` `ManagedAdder`, `Adder` se ha subclase (por ejemplo, por) y la `Adder.Add` implementación solo se invocará si la subclase se invoca `base.Add`. Este es el caso de envío no virtual, que es donde `ThresholdClass` entra. `ThresholdClass`Especifica qué clase Java proporcionará la implementación del método que se va a invocar.

#### <a name="method-registration"></a>Registro de métodos

Supongamos que tenemos `ManagedAdder` una definición actualizada que invalida el `Adder.Add` método:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Recuerde que `Adder.Add` tenía un `[Register]` atributo personalizado:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

El `[Register]` constructor de atributos personalizados acepta tres valores:

1. Nombre del método Java, `"add"` en este caso.

1. Firma de tipo JNI del método, `"(II)I"` en este caso.

1. El *método* de conector `GetAddHandler` , en este caso.
    Los métodos de conector se tratarán más adelante.

Los dos primeros parámetros permiten que el proceso de generación de ACW genere una declaración de método para invalidar el método. El ACW resultante contendría parte del código siguiente:

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

Tenga en cuenta `@Override` que se declara un método, que delega `n_`en un método de prefijo con el mismo nombre. Esto garantiza que `ManagedAdder.add`cuando se invoque el código Java, `ManagedAdder.n_add` se invocará, lo que permitirá que se C# `ManagedAdder.Add` ejecute el método de reemplazo.

Por lo tanto, la pregunta más importante: `ManagedAdder.n_add` ¿cómo se enlaza `ManagedAdder.Add`?

Los `native` métodos de Java se registran con el tiempo de ejecución de Java (Android Runtime) a través de la [función RegisterNatives de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives`toma una matriz de estructuras que contienen el nombre del método Java, la firma del tipo JNI y un puntero de función que se va a invocar después de la [Convención de llamada de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
El puntero de función debe ser una función que toma dos argumentos de puntero seguidos de los parámetros de método. El método `ManagedAdder.n_add` Java debe implementarse a través de una función que tenga el siguiente prototipo de C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin. Android no expone un `RegisterNatives` método. En su lugar, ACW y MCW proporcionan la información necesaria para invocar `RegisterNatives`: ACW contiene el nombre del método y la firma del tipo JNI, lo único que falta es un puntero de función que se va a enlazar.

Aquí es donde entra en el *método del conector* . El tercer `[Register]` parámetro de atributo personalizado es el nombre de un método definido en el tipo registrado o una clase base del tipo registrado que no acepta parámetros y devuelve un `System.Delegate`. El devuelto `System.Delegate` a su vez hace referencia a un método que tiene la firma de la función JNI correcta. Por último, el delegado que devuelve el método del conector *debe* tener la raíz para que el GC no lo recopile, ya que el delegado se proporciona a Java.

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

El `GetAddHandler` método crea un `Func<IntPtr, IntPtr, int, int,
int>` `n_Add` delegado que hace referencia al método y, a continuación, invoca [JNINativeWrapper. CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate`ajusta el método proporcionado en un bloque try/catch, de modo que se controlen las excepciones no controladas y provocarán que se genere el evento [AndroidEvent. UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) . El delegado resultante se almacena en la variable `cb_add` estática para que el GC no libere el delegado.

Por último, `n_Add` el método es responsable de calcular las referencias de los parámetros de JNI en los tipos administrados correspondientes y, a continuación, delegar la llamada al método.

Nota: Use `JniHandleOwnership.DoNotTransfer` siempre al obtener un MCW en una instancia de Java. Si se tratan como una referencia local (y, `JNIEnv.DeleteLocalRef`por lo tanto, llamando&gt; a)&gt; , se interrumpirán las transiciones de pila administradas por Java.

### <a name="complete-adder-binding"></a>Completar enlace de agregador

El enlace administrado completo para el `mono.android.tests.Adder` tipo es:

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

1. Subclases`Java.Lang.Object`

1. Tiene un `[Register]` atributo personalizado

1. `RegisterAttribute.DoNotGenerateAcw` es `true`

A continuación, para la interacción con GC, el tipo *no debe* tener ningún campo `Java.Lang.Object` que `Java.Lang.Object` pueda hacer referencia a una subclase o en tiempo de ejecución. Por ejemplo, no se permiten `System.Object` los campos de tipo y cualquier tipo de interfaz. Se permiten los tipos que `Java.Lang.Object` no pueden hacer referencia a las `System.String` instancias `List<int>`de, como y. Esta restricción es impedir la recolección de objetos prematura por parte del GC.

Si el tipo debe contener un campo de instancia que pueda hacer referencia `Java.Lang.Object` a una instancia de, el tipo de `System.WeakReference` campo `GCHandle`debe ser o.

## <a name="binding-abstract-methods"></a>Enlazar métodos abstractos

Los `abstract` métodos de enlace son en gran medida idénticos a los métodos virtuales de enlace. Solo hay dos diferencias:

1. El método abstracto es abstracto. Todavía conserva el `[Register]` atributo y el registro del método asociado, el enlace de método simplemente se mueve `Invoker` al tipo.

1. Se crea un `abstract` sin `Invoker` tipo que subclase el tipo abstracto. El `Invoker` tipo debe reemplazar todos los métodos abstractos declarados en la clase base y la implementación reemplazada es la implementación de enlace de método, aunque el caso de envío no virtual se puede omitir.

Por ejemplo, supongamos que `mono.android.test.Adder.add` el método `abstract`anterior era. El C# enlace cambiaría para que `Adder.Add` fuese abstracto y se definiría un nuevo `AdderInvoker` tipo que implementaba `Adder.Add`:

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

El `Invoker` tipo solo es necesario cuando se obtienen referencias JNI a instancias creadas por Java.

## <a name="binding-interfaces"></a>Interfaces de enlace

Las interfaces de enlace son conceptualmente similares a las clases de enlace que contienen métodos virtuales, pero muchos de los aspectos específicos difieren en modo sutil (y no tan sutil). Considere la siguiente [declaración de interfaz de Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Los enlaces de interfaz tienen dos partes: C# la definición de interfaz y una definición de invocador para la interfaz.

### <a name="interface-definition"></a>Definición de interfaz

La C# definición de la interfaz debe cumplir los siguientes requisitos:

- La definición de interfaz debe tener `[Register]` un atributo personalizado.

- La definición de interfaz debe extender `IJavaObject interface`el.
    Si no lo hace, impedirá que ACWs herede de la interfaz java.

- Cada método de interfaz debe contener `[Register]` un atributo que especifique el nombre del método Java, la firma de JNI y el método de conector correspondientes.

- El método de conector también debe especificar el tipo en el que se puede ubicar el método de conector.

Al `abstract` enlazar `virtual` y métodos, se buscaría el método de conector dentro de la jerarquía de herencia del tipo que se va a registrar. Las interfaces no pueden tener ningún método que contenga cuerpos, por lo que esto no funciona, por lo que es necesario especificar un tipo que indique dónde se encuentra el método de conector. El tipo se especifica dentro de la cadena de método de conector, `':'`después de dos puntos, y debe ser el nombre de tipo calificado con el ensamblado del tipo que contiene el invocador.

Las declaraciones de método de interfaz son una traducción del método de Java correspondiente usando tipos *compatibles* . En el caso de los tipos integrados de Java, los tipos C# compatibles son los tipos correspondientes `int` , C# `int`por ejemplo, Java es. En el caso de los tipos de referencia, el tipo compatible es un tipo que puede proporcionar un identificador de JNI del tipo Java adecuado.

Los miembros de interfaz no se invocarán directamente mediante la &ndash; invocación de Java se medirán a través del &ndash; tipo de invocador, por lo que se permite cierto grado de flexibilidad.

La interfaz de progreso de Java se puede [declarar en de C# ](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83)la siguiente manera:

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe en lo anterior que asignamos el parámetro `int[]` de Java a [un&lt;JavaArray&gt;int](xref:Android.Runtime.JavaArray`1).
Esto no es necesario: podríamos haber enlazado a un C# `int[]`, o a `IList<int>`un, o a algo más completo. Sea cual sea el tipo elegido `Invoker` , el debe ser capaz de traducirlo a un `int[]` tipo de Java para la invocación.

### <a name="invoker-definition"></a>Definición de invocador

La `Invoker` definición de tipo debe `Java.Lang.Object`heredar, implementar la interfaz adecuada y proporcionar todos los métodos de conexión a los que se hace referencia en la definición de interfaz. Hay una sugerencia más que difiere de un enlace de clase: los `class_ref` identificadores de campo y de método deben ser miembros de instancia, no miembros estáticos.

La razón para la preferencia de miembros de instancia tiene `JNIEnv.GetMethodID` que hacer con el comportamiento en el tiempo de ejecución de Android. (Esto también puede ser un comportamiento de Java; no se ha probado). `JNIEnv.GetMethodID` devuelve null al buscar un método que proviene de una interfaz implementada y no de la interfaz declarada. Considere la interfaz Java [. util.&lt;SortedMap k,&gt; v](https://developer.android.com/reference/java/util/SortedMap.html) Java, que implementa la interfaz [java. util. map&lt;k, v&gt; ](https://developer.android.com/reference/java/util/Map.html) . Map proporciona un método [Clear](https://developer.android.com/reference/java/util/Map.html#clear()) , por lo que una definición `Invoker` aparentemente razonable para SortedMap sería:

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

Lo anterior producirá un `JNIEnv.GetMethodID` error porque `null` devolverá cuando `Map.clear` se busque el `SortedMap` método a través de la instancia de clase.

Existen dos soluciones: realizar un seguimiento de la interfaz de la que procede cada método y tener `class_ref` un para cada interfaz, o bien mantener todo como miembros de instancia y realizar la búsqueda de métodos en el tipo de clase más derivado, no en el tipo de interfaz. Esto último se hace en **mono. Android. dll**.

La definición del Invocador tiene seis secciones: el constructor `Dispose` , el método `ThresholdType` , `ThresholdClass` los miembros y `GetObject` , el método, la implementación del método de interfaz y la implementación del método de conector.

#### <a name="constructor"></a>Constructor

El constructor necesita buscar la clase en tiempo de ejecución de la instancia que se está invocando y almacenar la clase `class_ref` en tiempo de ejecución en el campo de instancia:

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

Nota: La `Handle` propiedad se debe usar en el cuerpo del constructor y no en `handle` el parámetro, como en Android v 4.0 `handle` , el parámetro puede no ser válido después de que el constructor base termine de ejecutarse.

#### <a name="dispose-method"></a>Dispose (Método)

El `Dispose` método debe liberar la referencia global asignada en el constructor:

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

Los `ThresholdType` miembros `ThresholdClass` y son idénticos a lo que se encuentra en un enlace de clase:

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

Se requiere `GetObject` un método estático para admitir [extensions.&lt;JavaCast&gt;T ()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Métodos de interfaz

Cada método de la interfaz debe tener una implementación de, que invoca el método Java correspondiente a través de JNI:

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

#### <a name="connector-methods"></a>Métodos del conector

Los métodos de conector y la infraestructura de apoyo son responsables de calcular las referencias de C# los parámetros de JNI en los tipos adecuados. El parámetro `int[]` de Java se pasará como JNI `jintArray`, que es un `IntPtr` dentro de C#. Se `IntPtr` deben calcular las referencias de `JavaArray<int>` para poder invocar la C# interfaz:

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

Si `int[]` fuera`JavaList<int>`preferible, se podría usar [JNIEnv. GetArray ()](xref:Android.Runtime.JNIEnv.GetArray*) :

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Sin embargo, tenga en `JNIEnv.GetArray` cuenta que copia toda la matriz entre las máquinas virtuales, por lo que en el caso de las matrices grandes, esto podría dar lugar a una gran cantidad de presión de GC agregada.

### <a name="complete-invoker-definition"></a>Definición completa del Invocador

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

## <a name="jni-object-references"></a>Referencias del objeto JNI

Muchos métodos JNIEnv devuelven *referencias a objetos* *JNI* , que `GCHandle`son similares a s. JNI proporciona tres tipos diferentes de referencias de objeto: referencias locales, referencias globales y referencias globales débiles. Los tres se representan como `System.IntPtr`, *pero* (según la sección tipos de función JNI) no todos `IntPtr`los que se `JNIEnv` devuelven desde los métodos son referencias. Por ejemplo, [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) devuelve `IntPtr`, pero no devuelve una referencia `jmethodID`de objeto. Consulte la [documentación de la función JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obtener más información.

*La mayoría* de los métodos de creación de referencias crean las referencias locales.
Android solo permite que exista un número limitado de referencias locales en un momento dado, normalmente 512. Las referencias locales se pueden eliminar a través de [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
A diferencia de JNI, no todos los métodos JNIEnv de referencia que devuelven referencias a objetos devuelven referencias locales. [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*) devuelve una referencia *global* . Se recomienda encarecidamente que elimine las referencias locales tan pronto como pueda, mediante la construcción de un [objeto Java. lang. Object](xref:Java.Lang.Object) alrededor del objeto y la `JniHandleOwnership.TransferLocalRef` especificación de [java. lang. Object (identificador IntPtr, transferencia JniHandleOwnership) ](xref:Java.Lang.Object#ctor*)constructor.

Las referencias globales se crean mediante [JNIEnv. NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) y [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Se pueden destruir con [JNIEnv. DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Los emuladores tienen un límite de 2.000 referencias globales pendientes, mientras que los dispositivos de hardware tienen un límite de aproximadamente 52.000 referencias globales.

Las referencias globales débiles solo están disponibles en Android v 2.2 (Froyo) y versiones posteriores. Las referencias globales débiles se pueden eliminar con [JNIEnv. DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Trabajar con referencias locales de JNI

Los métodos [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) y [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) devuelven un `IntPtr` valor contiene una referencia local de JNI a un objeto de Java `IntPtr.Zero` o si se `null`devuelve Java. Debido al número limitado de referencias locales que pueden estar pendientes a la vez (entradas 512), es conveniente asegurarse de que las referencias se eliminan a tiempo. Hay tres maneras de tratar las referencias locales: eliminarlas explícitamente, crear una `Java.Lang.Object` instancia de para contenerlos y usar `Java.Lang.Object.GetObject<T>()` para crear un contenedor administrado al que se puede llamar.

### <a name="explicitly-deleting-local-references"></a>Eliminar explícitamente las referencias locales

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) se usa para eliminar referencias locales. Una vez eliminada la referencia local, ya no se puede usar, por lo que se debe tener cuidado para `JNIEnv.DeleteLocalRef` asegurarse de que es lo último que se ha hecho con la referencia local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Ajuste con Java. lang. Object

`Java.Lang.Object`proporciona un constructor [java. lang. Object (identificador IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) que se puede usar para ajustar una referencia JNI de salida. El parámetro [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) determina cómo se `IntPtr` debe tratar el parámetro:

- [JniHandleOwnership. DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; la instancia `Java.Lang.Object` creada creará una nueva referencia global desde el `handle` parámetro y `handle` no se modificará.
    El autor de la llamada es responsable de `handle` liberar, si es necesario.

- [JniHandleOwnership. TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; la instancia `Java.Lang.Object` creada creará una nueva referencia global desde el `handle` parámetro y `handle` se eliminará con [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . El autor de la llamada no `handle` debe liberar y no debe `handle` usar una vez que el constructor termine de ejecutarse.

- [JniHandleOwnership. TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; `Java.Lang.Object` la`handle` instancia creada tomará la propiedad del parámetro. El autor de la llamada no `handle` debe liberar.

Dado que los métodos de invocación del método JNI devuelven referencias locales, `JniHandleOwnership.TransferLocalRef` se utilizaría normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La referencia global creada no se liberará hasta que la `Java.Lang.Object` instancia se recopile como elemento no utilizado. Si es posible, la eliminación de la instancia liberará la referencia global, con lo que se aceleran las recolecciones de elementos no utilizados:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Usar Java. lang. Object. GetObject&lt;T&gt;()

`Java.Lang.Object`proporciona un método [java. lang. Object.&lt;GetObject&gt;T (identificador IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object.GetObject*) que se puede usar para crear un contenedor invocable administrado del tipo especificado.

El tipo `T` debe cumplir los siguientes requisitos:

1. `T`debe ser un tipo de referencia.

1. `T`debe implementar la `IJavaObject` interfaz.

1. Si `T` no es una clase o interfaz abstracta `T` , debe proporcionar un constructor con los tipos `(IntPtr,
    JniHandleOwnership)` de parámetro.

1. Si `T` es una clase abstracta o una interfaz, *debe* haber un *invocador* disponible para `T` . Un invocador es un tipo no abstracto que hereda `T` o `T` implementa, y tiene el mismo nombre que `T` con un sufijo de invocador. Por ejemplo, si T es la interfaz `Java.Lang.IRunnable` , el tipo `Java.Lang.IRunnableInvoker` debe existir y debe contener el constructor necesario `(IntPtr,
    JniHandleOwnership)` .

Dado que los métodos de invocación del método JNI devuelven referencias locales, `JniHandleOwnership.TransferLocalRef` se utilizaría normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Buscar tipos de Java

Para buscar un campo o método en JNI, primero debe buscar el tipo declarativo del campo o método. El método [Android. Runtime. JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)) se usa para buscar tipos de Java. El parámetro String es la *referencia de tipo simplificado* o la *referencia de tipo completo* para el tipo Java. Vea la [sección referencias de tipos de JNI](#_JNI_Type_References) para obtener más información sobre las referencias de tipos completos y simplificados.

Nota: A diferencia de todos `JNIEnv` los demás métodos que devuelven instancias de objeto, `FindClass` devuelve una referencia global, no una referencia local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instancia

Los campos se manipulan a través de los *identificadores de campo*. Los identificadores de campo se obtienen a través de [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), que requiere la clase en la que está definido el campo, el nombre del campo y la [firma de tipo JNI](#JNI_Type_Signatures) del campo.

No es necesario liberar los identificadores de campo y son válidos siempre que se cargue el tipo de Java correspondiente. (Android no admite actualmente la descarga de clases).

Existen dos conjuntos de métodos para manipular campos de instancia: uno para leer campos de instancia y otro para escribir campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo para leer o escribir el valor del campo.

### <a name="reading-instance-field-values"></a>Leer valores de campo de instancia

El conjunto de métodos para leer valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

donde `*` es el tipo del campo:

- [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; lee el valor de cualquier campo de instancia que no sea un `java.lang.Object` tipo integrado, como, por ejemplo, matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

- [JNIEnv. GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; lee el valor de `bool` los campos de instancia.

- [JNIEnv. GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; lee el valor de `sbyte` los campos de instancia.

- [JNIEnv. GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; lee el valor de `char` los campos de instancia.

- [JNIEnv. GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; lee el valor de `short` los campos de instancia.

- [JNIEnv. GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; lee el valor de `int` los campos de instancia.

- [JNIEnv. GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; lee el valor de `long` los campos de instancia.

- [JNIEnv. GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; lee el valor de `float` los campos de instancia.

- [JNIEnv. GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; lee el valor de `double` los campos de instancia.

### <a name="writing-instance-field-values"></a>Escribir valores de campo de instancia

El conjunto de métodos para escribir valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

donde *Type* es el tipo del campo:

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de cualquier campo que no sea un `java.lang.Object` tipo integrado, como, por ejemplo, matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local de JNI, una referencia global de JNI, una referencia global `IntPtr.Zero` no segura `null` de JNI o (para).

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `bool` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `sbyte` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `char` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `short` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `int` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `long` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `float` los campos de instancia.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; escriba el valor de `double` los campos de instancia.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Los campos estáticos se manipulan a través de los *identificadores de campo*. Los identificadores de campo se obtienen a través de [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), que requiere la clase en la que está definido el campo, el nombre del campo y la [firma de tipo JNI](#JNI_Type_Signatures) del campo.

No es necesario liberar los identificadores de campo y son válidos siempre que se cargue el tipo de Java correspondiente. (Android no admite actualmente la descarga de clases).

Existen dos conjuntos de métodos para manipular campos estáticos: uno para leer campos de instancia y otro para escribir campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo para leer o escribir el valor del campo.

### <a name="reading-static-field-values"></a>Leer valores de campos estáticos

El conjunto de métodos para leer valores de campos estáticos sigue el patrón de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

donde `*` es el tipo del campo:

- [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; lee el valor de cualquier campo estático que no sea un `java.lang.Object` tipo integrado, como, por ejemplo, matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

- [JNIEnv. GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; lee el valor de `bool` los campos estáticos.

- [JNIEnv. GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; lee el valor de `sbyte` los campos estáticos.

- [JNIEnv. GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; lee el valor de `char` los campos estáticos.

- [JNIEnv. GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; lee el valor de `short` los campos estáticos.

- [JNIEnv. GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; lee el valor de `long` los campos estáticos.

- [JNIEnv. GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; lee el valor de `float` los campos estáticos.

- [JNIEnv. GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; lee el valor de `double` los campos estáticos.

### <a name="writing-static-field-values"></a>Escribir valores de campo estáticos

El conjunto de métodos para escribir valores de campo estático sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

donde *Type* es el tipo del campo:

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de cualquier campo estático que no sea un `java.lang.Object` tipo integrado, como, por ejemplo, matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local de JNI, una referencia global de JNI, una referencia global `IntPtr.Zero` no segura `null` de JNI o (para).

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `bool` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `sbyte` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `char` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `short` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `int` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `long` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `float` los campos estáticos.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; escriba el valor de `double` los campos estáticos.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de instancia

Los métodos de instancia se invocan a través de los *identificadores de método*. Los identificadores de método se obtienen a través de [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), que requiere el tipo en el que se define el método, el nombre del método y la [firma de tipo JNI](#JNI_Type_Signatures) del método.

No es necesario liberar los identificadores de método y son válidos siempre que se cargue el tipo de Java correspondiente. (Android no admite actualmente la descarga de clases).

Hay dos conjuntos de métodos para invocar métodos: uno para invocar métodos de manera virtual, y otro para invocar métodos de manera no virtual. Ambos conjuntos de métodos requieren un identificador de método para invocar el método, y la invocación no virtual también requiere que se especifique la implementación de clase que se debe invocar.

Los métodos de interfaz solo se pueden buscar dentro del tipo declarativo; no se pueden buscar métodos que proceden de interfaces extendidas o heredadas. Para más información, consulte la sección interfaces de enlace e invocadores posteriores.

Se puede buscar cualquier método declarado en la clase o en cualquier clase base o interfaz implementada.

### <a name="virtual-method-invocation"></a>Invocación de método virtual

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

- [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; invoca un método que devuelve un `java.lang.Object` tipo no integrado, como matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv. CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; invoca un método que devuelve un `bool` valor.

- [JNIEnv. CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; invoca un método que devuelve un `sbyte` valor.

- [JNIEnv. CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; invoca un método que devuelve un `char` valor.

- [JNIEnv. CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; invoca un método que devuelve un `short` valor.

- [JNIEnv. CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invoca un método que devuelve un `long` valor.

- [JNIEnv. CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; invoca un método que devuelve un `float` valor.

- [JNIEnv. CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; invoca un método que devuelve un `double` valor.

### <a name="non-virtual-method-invocation"></a>Invocación de método no virtual

El conjunto de métodos para invocar métodos no casi prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método. La invocación de métodos no virtuales normalmente se usa para invocar el método base de un método virtual.

- [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; invocó de forma no virtual un método que devuelve un `java.lang.Object` tipo no integrado, como matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv. CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; no virtualmente invoca un método que devuelve un `bool` valor.

- [JNIEnv. CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; no virtualmente invoca un método que devuelve un `sbyte` valor.

- [JNIEnv. CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; no virtualmente invoca un método que devuelve un `char` valor.

- [JNIEnv. CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; no virtualmente invoca un método que devuelve un `short` valor.

- [JNIEnv. CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; no virtualmente invoca un método que devuelve un `long` valor.

- [JNIEnv. CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; no virtualmente invoca un método que devuelve un `float` valor.

- [JNIEnv. CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; no virtualmente invoca un método que devuelve un `double` valor.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Los métodos estáticos se invocan a través de los *identificadores de método*. Los identificadores de método se obtienen a través de [JNIEnv. GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), que requiere el tipo en el que se define el método, el nombre del método y la [firma de tipo JNI](#JNI_Type_Signatures) del método.

No es necesario liberar los identificadores de método y son válidos siempre que se cargue el tipo de Java correspondiente. (Android no admite actualmente la descarga de clases).

### <a name="static-method-invocation"></a>Invocación de método estático

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

- [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; invoca un método estático que devuelve un `java.lang.Object` tipo no integrado, como matrices e interfaces. El valor devuelto es una referencia local de JNI.

- [JNIEnv. CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; invoca un método estático que devuelve un `bool` valor.

- [JNIEnv. CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; invoca un método estático que devuelve un `sbyte` valor.

- [JNIEnv. CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; invoca un método estático que devuelve un `char` valor.

- [JNIEnv. CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; invoca un método estático que devuelve un `short` valor.

- [JNIEnv. CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; invoca un método estático que devuelve un `long` valor.

- [JNIEnv. CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; invoca un método estático que devuelve un `float` valor.

- [JNIEnv. CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; invoca un método estático que devuelve un `double` valor.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Signaturas de tipo JNI

Las [signaturas de tipo JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) son referencias de tipo de [JNI](#_JNI_Type_References) (aunque no son referencias de tipo simplificadas), excepto los métodos. Con los métodos, la signatura de tipo JNI es un paréntesis `'('`de apertura, seguido de las referencias de tipo de todos los tipos de parámetro concatenados (sin comas separadoras ni nada más), seguido de un `')'`paréntesis de cierre. seguido de la referencia de tipo JNI del tipo de valor devuelto del método.

Por ejemplo, dado el método Java:

```java
long f(int n, String s, int[] array);
```

La signatura de tipo JNI sería:

```csharp
(ILjava/lang/String;[I)J
```

En general, se recomienda *encarecidamente* usar el comando `javap` para determinar las firmas de JNI. Por ejemplo, la firma de tipo JNI del método [java. lang. Thread. state. valueA (String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) es "(Ljava/lang/String;) Ljava/lang/Thread $ State;", mientras que la firma de tipo JNI del método [java. lang. Thread. state. Values](https://developer.android.com/reference/java/lang/Thread.State.html#values) es "() [Ljava/ lang/Thread $ State; ". Vea los signos de punto y coma finales; Estos *forman* parte de la signatura de tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referencias de tipo JNI

Las referencias de tipo JNI son diferentes de las referencias de tipo de Java. No puede usar nombres de tipo Java completos como `java.lang.String` con JNI; en su lugar, debe usar las variaciones `"java/lang/String"` de JNI `"Ljava/lang/String;"`o, en función del contexto, para obtener más información.
Hay cuatro tipos de referencias de tipo JNI:

- **built-in**
- **representaciones**
- **type**
- **array**

### <a name="built-in-type-references"></a>Referencias de tipos integrados

Las referencias a tipos integrados son un único carácter que se usa para hacer referencia a tipos de valor integrados. La asignación es la siguiente:

- `"B"`para `sbyte` .
- `"S"`para `short` .
- `"I"`para `int` .
- `"J"`para `long` .
- `"F"`para `float` .
- `"D"`para `double` .
- `"C"`para `char` .
- `"Z"`para `bool` .
- `"V"`para `void` los tipos de valor devueltos del método.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referencias de tipo simplificado

Las referencias de tipo simplificado solo se pueden usar en [JNIEnv. FindClass (cadena)](xref:Android.Runtime.JNIEnv.FindClass*)).
Hay dos maneras de derivar una referencia de tipo simplificado:

1. En un nombre de Java completo, reemplace cada uno `'.'` dentro del nombre del paquete y antes del nombre del `'/'` tipo con, `'.'` y cada uno dentro de `'$'` un nombre de tipo con.

1. Lea el resultado de `'unzip -l android.jar | grep JavaName'` .

Cualquiera de los dos dará lugar a que el tipo Java [. lang. Thread. State](https://developer.android.com/reference/java/lang/Thread.State.html) se asigne a la referencia `java/lang/Thread$State`de tipo simplificado.

### <a name="type-references"></a>Referencias de tipo

Una referencia de tipo es una referencia de tipo integrada o una referencia de tipo simplificado `'L'` con un prefijo y un `';'` sufijo. Para el tipo de Java [java. lang. String](https://developer.android.com/reference/java/lang/String.html), la referencia de tipo `"java/lang/String"`simplificado es, mientras que `"Ljava/lang/String;"`la referencia de tipo es.

Las referencias de tipo se utilizan con referencias de tipo de matriz y con firmas de JNI.

Una manera adicional de obtener una referencia de tipo es leer la salida de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependiendo del tipo implicado, puede usar una declaración de constructor o un tipo de valor devuelto de método para determinar el nombre de JNI. Por ejemplo:

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

`Thread.State`es un tipo de enumeración de Java, por lo que podemos usar `valueOf` la firma del método para determinar que la referencia de tipo es Ljava/lang/Thread $ State;.

### <a name="array-type-references"></a>Referencias de tipos de matriz

Las referencias a tipos `'['` de matriz se anteponen a una referencia de tipo JNI.
No se pueden usar referencias de tipo simplificado al especificar matrices.

Por ejemplo, `int[]` es `"[I"`, `int[][]` es `"[[I"`y es`java.lang.Object[]` . `"[Ljava/lang/Object;"`

## <a name="java-generics-and-type-erasure"></a>Genéricos de Java y eliminación de tipos

La *mayoría* de las veces, como se aprecia a través de JNI, *no existen*genéricos de Java.
Hay algunas "arrugas", pero esas arrugas están en cómo interactúa Java con los genéricos, no en cómo busca JNI y llama a los miembros genéricos.

No hay ninguna diferencia entre un tipo o miembro genérico y un tipo o miembro no genérico al interactuar a través de JNI. Por ejemplo, el tipo genérico [java. lang. Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) es también el tipo `java.lang.Class`genérico "RAW", ambos tienen la misma referencia de tipo simplificada, `"java/lang/Class"`.

## <a name="java-native-interface-support"></a>Compatibilidad con la interfaz nativa de Java

[Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv) es un contenedor administrado para Jave Native Interface (JNI). Las funciones JNI se declaran dentro de la [especificación de interfaz nativa de Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), aunque los métodos se `JNIEnv*` han cambiado para quitar el parámetro explícito `jobject`y `jclass` `IntPtr` se `jmethodID`usa en lugar de,,, otros. Por ejemplo, considere la [función de JNI NewObject](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Esto se expone como el método [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) :

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Trasladar entre las dos llamadas es razonablemente sencillo. En C tendría:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

El C# equivalente sería:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una vez que tenga una instancia de objeto de Java contenida en un IntPtr, es probable que desee hacer algo con ella. Puede usar métodos de JNIEnv como [JNIEnv. CallVoidMethod ()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) para hacerlo, pero si ya existe un contenedor analógico C# , querrá construir un contenedor a través de la referencia de JNI. Puede hacerlo a través del método de extensión [extensions. JavaCast\<T >](xref:Android.Runtime.Extensions.JavaCast*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

También puede usar el método de [> Java. lang. Object\<. GetObject T](xref:Java.Lang.Object.GetObject*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Además, todas las funciones JNI se han modificado quitando el `JNIEnv*` parámetro presente en cada función JNI.

## <a name="summary"></a>Resumen

Tratar directamente con JNI es una experiencia terrible que debe evitarse a todos los costos. Desafortunadamente, no siempre se evita. Espero que esta guía le proporcione asistencia cuando alcance los casos de Java sin enlazar con mono para Android.

## <a name="related-links"></a>Vínculos relacionados

- [Especificación de interfaz nativa de Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funciones de interfaz nativa de Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
