---
title: Contenedores de Android Callable para Xamarin. Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fb00bf4a817ad8188d5a127b23b4a910dd4f23a7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524133"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Contenedores de Android Callable para Xamarin. Android

Los contenedores a los que se puede llamar de Android (ACWs) son necesarios siempre que el tiempo de ejecución de Android invoca código administrado. Estos contenedores son necesarios porque no hay ninguna manera de registrar clases con arte (el tiempo de ejecución de Android) en tiempo de ejecución. (Concretamente, la [función DefineClass () de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) no es compatible con el tiempo de ejecución de Android.} Los contenedores a los que se puede llamar de Android componen la falta de compatibilidad con el registro de tipo en tiempo de ejecución. 

*Cada vez* El código de Android necesita ejecutar `virtual` un método de interfaz o `overridden` que se implementa en código administrado, Xamarin. Android debe proporcionar un proxy de Java para que este método se envíe al tipo administrado adecuado. Estos tipos de proxy Java son código Java que tiene la misma clase base y la misma lista de interfaces de Java que el tipo administrado, implementando los mismos constructores y declarando cualquier método de interfaz y clase base invalidado. 

Los contenedores a los que se puede llamar de Android se generan mediante el programa **monodroid. exe** durante el [proceso de compilación](~/android/deploy-test/building-apps/build-process.md): se generan para todos los tipos que heredan (directa o indirectamente) [java. lang. Object](xref:Java.Lang.Object). 



## <a name="android-callable-wrapper-naming"></a>Nombres de contenedor de Android Callable

Los nombres de paquete de los contenedores a los que se puede llamar de Android se basan en el MD5SUM del nombre calificado con el ensamblado del tipo que se va a exportar. Esta técnica de nomenclatura permite que el mismo nombre de tipo completo esté disponible en ensamblados diferentes sin introducir un error de empaquetado. 

Debido a este esquema de nomenclatura de MD5SUM, no puede acceder directamente a los tipos por nombre. Por ejemplo, el siguiente `adb` comando no funcionará porque el nombre `my.ActivityType` de tipo no se genera de forma predeterminada: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Además, es posible que vea errores como los siguientes si intenta hacer referencia a un tipo por nombre:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Si requiere acceso a los tipos por nombre, puede declarar un nombre para ese tipo en una declaración de atributo. Por ejemplo, este es el código que declara una actividad con el nombre `My.ActivityType`completo:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

La `ActivityAttribute.Name` propiedad se puede establecer para declarar explícitamente el nombre de esta actividad: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Una vez agregada la configuración de `my.ActivityType` esta propiedad, se puede tener acceso a ella por su `adb` nombre desde el código externo y desde los scripts. El `Name` atributo se puede establecer para muchos tipos diferentes, `Activity`entre los que `BroadcastReceiver`se incluyen `ContentProvider`, `Application`, `Service`, y: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

Los nombres de ACW basados en MD5SUM se introdujeron en Xamarin. Android 5,0. Para obtener más información sobre la nomenclatura de atributos, vea [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 



## <a name="implementing-interfaces"></a>Implementar interfaces

Hay ocasiones en las que puede que necesite implementar una interfaz de Android, como [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Dado que todas las clases e interfaces de Android amplían la interfaz de [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) , surge la pregunta: `IJavaObject`¿cómo se implementa? 

La pregunta se respondió anteriormente: el motivo por el que todos los tipos `IJavaObject` de Android deben implementarse es para que Xamarin. Android tenga un contenedor de Android al que se puede llamar para proporcionar a Android, es decir, un proxy de Java para el tipo especificado. Dado que **monodroid. exe** solo busca `Java.Lang.Object` subclases e `Java.Lang.Object` implementa `IJavaObject,` la respuesta es obvia: subclase `Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>Detalles de implementación

*El resto de esta página proporciona detalles de implementación sujetos a cambios sin previo aviso* (y se presenta aquí solo porque los desarrolladores tendrán curiosidad sobre lo que está ocurriendo). 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Observe que se conserva la clase base y `native` que se proporcionan declaraciones de método para cada método invalidado en el código administrado. 
