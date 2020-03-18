---
title: Contenedores de Android que se pueden llamar para Xamarin.Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 7278fd624bb3147c2e1a1a1a79adde68813a9888
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020153"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Contenedores de Android que se pueden llamar para Xamarin.Android

Los contenedores de Android que se pueden llamar (ACW) son necesarios siempre que el tiempo de ejecución de Android invoca código administrado. Estos contenedores son necesarios porque no hay ninguna manera de registrar clases con ART (tiempo de ejecución de Android) en tiempo de ejecución. En concreto, el tiempo de ejecución de Android no admite la [función JNI DefineClass()](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986). Por lo tanto, los contenedores de Android que se pueden llamar compensan la falta de compatibilidad con el registro del tipo de tiempo de ejecución. 

*Cada vez* que el código de Android tenga que ejecutar un método `virtual` o de interfaz que se `overridden` o implemente en código administrado, Xamarin.Android debe proporcionar un proxy Java para que este método se envíe al tipo administrado adecuado. Estos tipos de proxy Java son código Java que tiene la "misma" clase base y lista de interfaces Java que el tipo administrado, que implementa los mismos constructores y declara cualquier método de interfaz y clase base reemplazados. 

El programa **monodroid.exe** genera los contenedores de Android que se pueden llamar durante el [proceso de compilación](~/android/deploy-test/building-apps/build-process.md): se generan para todos los tipos que heredan (directa o indirectamente) [Java.Lang.Object](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>Contenedores de Android que se pueden llamar

Los nombres de paquete para los contenedores de Android que se pueden llamar se basan en MD5SUM del nombre de tipo calificado con el ensamblado del tipo que se va a exportar. Esta técnica de nomenclatura permite que el mismo nombre de tipo completo esté disponible en ensamblados diferentes sin introducir un error de empaquetado. 

Debido a este esquema de nomenclatura de MD5SUM, no puede acceder directamente a los tipos por nombre. Por ejemplo, el siguiente comando `adb` no funcionará porque el nombre de tipo `my.ActivityType` no se genera de forma predeterminada: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

Además, es posible que vea errores como los siguientes si intenta hacer referencia a un tipo por nombre:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Si *necesita* acceder a los tipos por nombre, puede declarar un nombre para ese tipo en una declaración de atributo. Por ejemplo, este es el código que declara una actividad con el nombre completo `My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Se puede establecer la propiedad `ActivityAttribute.Name` para declarar explícitamente el nombre de esta actividad: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Una vez agregada la configuración de esta propiedad, se puede acceder a `my.ActivityType` por nombre desde el código externo y desde los scripts de `adb`. Se puede establecer el atributo `Name` para muchos tipos diferentes, como `Activity`, `Application`, `Service`, `BroadcastReceiver` y `ContentProvider`: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

La nomenclatura ACW basada en MD5SUM se introdujo en Xamarin.Android 5.0. Para obtener más información sobre la nomenclatura de atributos, vea [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 

## <a name="implementing-interfaces"></a>Implementar interfaces

Hay ocasiones en las que puede que tenga que implementar una interfaz de Android, como [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Puesto que todas las clases e interfaces de Android amplían la interfaz [Android.Runtime.IJavaObject](xref:Android.Runtime.IJavaObject), la pregunta que surge es ¿cómo se implementa `IJavaObject`? 

La pregunta se respondió anteriormente: el motivo por el que todos los tipos de Android deben implementar `IJavaObject` es para que Xamarin.Android tenga un contenedor de Android que se puede llamar que pueda proporcionar a Android, es decir, un proxy Java para el tipo concreto. Como **monodroid.exe** solo busca subclases `Java.Lang.Object` y `Java.Lang.Object` implementa `IJavaObject,`, la respuesta es obvia: la subclase `Java.Lang.Object`: 

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

*En el resto de esta página se proporcionan detalles de implementación sujetos a cambio sin previo aviso* (y se presentan aquí solo porque los desarrolladores pueden sentir curiosidad por lo que ocurre). 

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

El programa **mandroid.exe** generará el siguiente contenedor de Android que se puede llamar: 

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

Observe que se conserva la clase base y se proporcionan declaraciones del método `native` para cada método que se reemplaza en el código administrado. 
