---
title: Xamarin. Android frente a Diferencias de escritorio en el tiempo de ejecución de mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 7f98f2f75a106ad3a9f62256a7145ac746c4b1c8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757786"
---
# <a name="limitations"></a>Limitaciones

Dado que las aplicaciones de Android requieren la generación de tipos de proxy de Java durante el proceso de compilación, no es posible generar todo el código en tiempo de ejecución.

Estas son las limitaciones de Xamarin. Android en comparación con el escritorio mono:

## <a name="limited-dynamic-language-support"></a>Compatibilidad limitada con los lenguajes dinámicos

 Los contenedores a los que se [puede llamar de Android](~/android/platform/java-integration/android-callable-wrappers.md) son necesarios cada vez que el tiempo de ejecución de Android necesita invocar código administrado. Los contenedores a los que se puede llamar de Android se generan en tiempo de compilación, en función del análisis estático de IL. El resultado neto de esto: *no puede* usar lenguajes dinámicos (IronPython, IronRuby, etc.) en cualquier escenario en el que se requiera la subclase de tipos de Java (incluida la subclase indirecta), ya que no hay forma de extraer estos tipos dinámicos en tiempo de compilación a genere los contenedores de Android a los que se puede llamar necesarios.

## <a name="limited-java-generation-support"></a>Compatibilidad con la generación limitada de Java

Los contenedores a los que se puede llamar de [Android](~/android/platform/java-integration/android-callable-wrappers.md) deben generarse para que el código Java llame a código administrado. *De forma predeterminada*, los contenedores a los que se puede llamar de Android solo contendrán (ciertos) métodos y constructores declarados que [`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)invalidan un método Java Virtual (es decir, `Attribute`tiene) o implementan un método de interfaz Java (la interfaz también tiene).
  
Antes de la versión 4,1, no se podía declarar ningún método adicional. Con la versión 4,1, [se `Export` pueden `ExportField` usar los atributos personalizados y para declarar métodos y campos de Java en el contenedor de Android al que se puede llamar](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Faltan constructores

Los constructores siguen siendo complicados [`ExportAttribute`](xref:Java.Interop.ExportAttribute) , a menos que se use. El algoritmo para generar constructores de contenedor a los que se puede llamar de Android es que se emitirá un constructor de Java si:

1. Hay una asignación de Java para todos los tipos de parámetro
2. La clase base declara el mismo constructor &ndash; esto es necesario porque el contenedor de Android al que se puede llamar *debe* invocar el constructor de clase base correspondiente; no se pueden usar argumentos predeterminados (ya que no hay ninguna manera fácil de determinar qué valores debe usarse en Java).

Por ejemplo, considere la siguiente clase:

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Aunque parece perfectamente lógico, el contenedor de Android al que se puede llamar resultante *en las compilaciones de versión* no contendrá un constructor predeterminado. Por lo tanto, si intenta iniciar este servicio (por ejemplo [`Context.StartService`](xref:Android.Content.Context.StartService*), se producirá un error:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

La solución consiste en declarar un constructor predeterminado, adornarlo con `ExportAttribute`y [`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString)establecer: 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```

### <a name="generic-c-classes"></a>Clases C# genéricas

Las C# clases genéricas solo se admiten parcialmente. Existen las siguientes limitaciones:

- Los tipos genéricos no `[Export]` pueden `[ExportField`usar o]. Si intenta hacerlo, se generará un `XA4207` error.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

- Los métodos genéricos no `[Export]` pueden `[ExportField]`usar ni:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

- `[ExportField]`no se puede usar en métodos que devuelven `void`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

- _No se deben_ crear instancias de tipos genéricos a partir del código de Java.
    Solo se pueden crear de forma segura a partir de código administrado:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```

## <a name="partial-java-generics-support"></a>Compatibilidad parcial con Java genéricos

La compatibilidad con enlaces de genéricos de Java es limitada. En particular, los miembros de una clase de instancia genérica que se deriva de otra clase genérica (sin instancias) se dejan expuestos como Java. lang. Object. Por ejemplo, el método [Android. Content. Intent. GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*) devuelve Java. lang. Object. Esto se debe a que se han borrado los genéricos de Java.
Tenemos algunas clases que no aplican esta limitación, pero se ajustan de forma manual.

## <a name="related-links"></a>Vínculos relacionados

- [Contenedores que se pueden llamar de Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [SuperString](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [RegisterAttribute](xref:Android.Runtime.RegisterAttribute)
