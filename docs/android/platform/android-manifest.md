---
title: Trabajar con el manifiesto de Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: ef1ccd07eb4807cd84094cad1200d8a1ed7cea8b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524695"
---
# <a name="working-with-the-android-manifest"></a>Trabajar con el manifiesto de Android

**Archivo AndroidManifest. XML** es un archivo eficaz de la plataforma Android que le permite describir la funcionalidad y los requisitos de la aplicación para Android. Sin embargo, no es fácil trabajar con él. Xamarin. Android ayuda a minimizar esta dificultad al permitirle agregar atributos personalizados a las clases, que se usarán para generar automáticamente el manifiesto. Nuestro objetivo es que el 99% de nuestros usuarios nunca deben modificar manualmente **archivo AndroidManifest. XML**. 

**Archivo AndroidManifest. XML** se genera como parte del proceso de compilación y el XML que se encuentra en **las propiedades/archivo AndroidManifest. XML** se combina con el XML que se genera a partir de los atributos personalizados. El **archivo AndroidManifest. XML** combinado resultante se encuentra en el subdirectorio **obj** ; por ejemplo, reside en **obj/Debug/Android/archivo AndroidManifest. XML** para las compilaciones de depuración. El proceso de combinación es trivial: usa atributos personalizados en el código para generar elementos XML e *inserta* esos elementos en **archivo AndroidManifest. XML**. 



## <a name="the-basics"></a>Conceptos básicos

En tiempo de compilación, los ensamblados se examinan`abstract` en busca de clases que no son de [`[Activity]`](xref:Android.App.ActivityAttribute) la [actividad](xref:Android.App.Activity) y tienen el atributo declarado en ellos. A continuación, usa estas clases y atributos para generar el manifiesto. Por ejemplo, considere el siguiente código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Esto hace que no se genere nada en **archivo AndroidManifest. XML**. Si desea que se `<activity/>` genere un elemento, debe utilizar el[`[Activity]`](xref:Android.App.Activity) 
atributo personalizado: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este ejemplo hace que se agregue el siguiente fragmento XML a **archivo AndroidManifest. XML**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

El `[Activity]` atributo no tiene ningún efecto `abstract` en los tipos; `abstract` los tipos se omiten.



### <a name="activity-name"></a>Nombre de actividad

A partir de Xamarin. Android 5,1, el nombre de tipo de una actividad se basa en el MD5SUM del nombre calificado con el ensamblado del tipo que se va a exportar. Esto permite proporcionar el mismo nombre completo a partir de dos ensamblados diferentes y no obtener un error de empaquetado. (Antes de Xamarin. Android 5,1, el nombre de tipo predeterminado de la actividad se creó a partir del espacio de nombres en minúsculas y el nombre de clase). 

Si desea invalidar este valor predeterminado y especificar explícitamente el nombre de la actividad, use [`Name`](xref:Android.App.ActivityAttribute.Name) la propiedad: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento XML:

```xml
<activity android:name="awesome.demo.activity" />
```

*Nota*: debe usar la `Name` propiedad solo por motivos de compatibilidad con versiones anteriores, ya que este cambio de nombre puede ralentizar la búsqueda de tipos en tiempo de ejecución. Si tiene código heredado que espera que el nombre de tipo predeterminado de la actividad se base en el espacio de nombres en minúsculas y el nombre de clase, consulte [nombres de contenedor de Android Callable](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) para obtener sugerencias sobre el mantenimiento de la compatibilidad. 


### <a name="activity-title-bar"></a>Barra de título de actividad

De forma predeterminada, Android proporciona una barra de título a la aplicación cuando se ejecuta. El valor utilizado para este es [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). En la mayoría de los casos, este valor será diferente del nombre de clase. Para especificar la etiqueta de la aplicación en la barra de título, [`Label`](xref:Android.App.ActivityAttribute.Label) use la propiedad.
Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento XML:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Iniciable desde Application selector

De forma predeterminada, la actividad no se mostrará en la pantalla del iniciador de aplicaciones de Android. Esto se debe a que es probable que haya muchas actividades en la aplicación y no desea un icono para cada una de ellas. Para especificar la que se debe iniciar desde el iniciador de la aplicación, [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) use la propiedad. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento XML:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>Icono de actividad

De forma predeterminada, se proporcionará a la actividad el icono del iniciador predeterminado proporcionado por el sistema. Para usar un icono personalizado, agregue primero el **. png** a **Resources/drawable**, establezca su acción de compilación en **AndroidResource**y [`Icon`](xref:Android.App.ActivityAttribute.Icon) , a continuación, use la propiedad para especificar el icono que se va a usar. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento XML:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Permisos

Al agregar permisos al manifiesto de Android (como se describe en [Agregar permisos para el manifiesto de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), estos permisos se registran en **Properties/archivo AndroidManifest. XML**. Por ejemplo, si establece el `INTERNET` permiso, se agrega el elemento siguiente a **Properties/archivo AndroidManifest. XML**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Las compilaciones de depuración establecen automáticamente algunos permisos para `INTERNET` facilitar la &ndash; depuración (como y `READ_EXTERNAL_STORAGE`). esta configuración solo se establece en el **archivo OBJ/Debug/Android/archivo AndroidManifest. XML** generado y no se muestra como habilitado en el Configuración de **permisos necesarios** . 

Por ejemplo, si examina el archivo de manifiesto generado en **obj/Debug/Android/archivo AndroidManifest. XML**, es posible que vea los siguientes elementos de permiso agregados: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

En la versión de compilación de lanzamiento del manifiesto (en **obj/Debug/Android/archivo AndroidManifest. XML**), estos permisos *no* se configuran automáticamente. Si observa que el cambio a una compilación de versión hace que la aplicación pierda un permiso que estaba disponible en la compilación de depuración, compruebe que ha establecido explícitamente este permiso en la configuración de **permisos necesarios** para la aplicación (consulte **compilación > Android Aplicación** en Visual Studio para Mac; consulte **propiedades > manifiesto de Android** en Visual Studio). 




## <a name="advanced-features"></a>Características avanzadas


### <a name="intent-actions-and-features"></a>Acciones y características de intención

El manifiesto de Android proporciona una manera de describir las funcionalidades de su actividad. Esto se hace a través de las [intenciones](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) y el[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
atributo personalizado. Puede especificar qué acciones son adecuadas para su actividad con el[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
constructor y qué categorías son adecuadas con el[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
propiedad. Se debe proporcionar al menos una actividad (que es el motivo por el que se proporcionan las actividades en el constructor). `[IntentFilter]`se puede proporcionar varias veces y cada una de ellas tiene como resultado `<intent-filter/>` un elemento independiente `<activity/>`dentro de. Por ejemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este ejemplo genera el siguiente fragmento XML:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Elemento Application

El manifiesto de Android también proporciona una manera de declarar las propiedades de toda la aplicación. Esto se hace a través `<application>` del elemento y su homólogo, el atributo personalizado de la [aplicación](xref:Android.App.ApplicationAttribute) . Tenga en cuenta que se trata de una configuración de toda la aplicación (en todo el ensamblado) en lugar de la configuración por actividad. Normalmente, se declaran `<application>` las propiedades de toda la aplicación y, a continuación, se invalidan estas opciones (según sea necesario) en función de cada actividad. 

Por ejemplo, se agrega `Application` el atributo siguiente a **AssemblyInfo.CS** para indicar que se puede depurar la aplicación, que su nombre legible para el usuario es **mi aplicación**y que usa el `Theme.Light` estilo como tema predeterminado para todos los operaciones 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaración hace que se genere el siguiente fragmento XML en **obj/Debug/Android/archivo AndroidManifest. XML**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
En este ejemplo, todas las actividades de la aplicación tendrán como valor `Theme.Light` predeterminado el estilo. Si establece el tema de una actividad en `Theme.Dialog`, solo esa actividad usará el `Theme.Dialog` estilo, mientras que todas las demás actividades de la aplicación tendrán como `Theme.Light` valor predeterminado el estilo establecido `<application>` en el elemento. 

El `Application` elemento no es la única manera de configurar `<application>` atributos. Como alternativa, puede insertar atributos directamente en el `<application>` elemento de **Properties/archivo AndroidManifest. XML**. Estos valores se combinan en el último `<application>` elemento que reside en **obj/Debug/Android/archivo AndroidManifest. XML**. Tenga en cuenta que el contenido de **las propiedades/archivo AndroidManifest. XML** siempre invalida los datos proporcionados por los atributos personalizados. 

Hay muchos atributos de toda la aplicación que se pueden configurar en el `<application>` elemento. para obtener más información sobre esta configuración, vea la sección [propiedades públicas](xref:Android.App.ApplicationAttribute) de [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 



## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

- [Android. app. ActivityAttribute](xref:Android.App.ActivityAttribute) : Genera un fragmento de XML de [/manifest/Application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android. app. ApplicationAttribute](xref:Android.App.ApplicationAttribute) : Genera un fragmento de XML de [/manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android. app. InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : Genera un fragmento de XML de [/manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : Genera un fragmento de XML de [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android. app. MetaDataAttribute](xref:Android.App.MetaDataAttribute) : Genera un fragmento de XML de [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android. app. PermissionAttribute](xref:Android.App.PermissionAttribute) : Genera un fragmento de XML de [//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android. app. PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : Genera un fragmento de XML de [//Permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android. app. PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : Genera un fragmento de XML de [//Permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute) : Genera un fragmento de XML de [/manifest/Application/Service](https://developer.android.com/guide/topics/manifest/service-element.html) 
- [Android.App.UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : Genera un fragmento de XML de [/manifest/Application/uses-Library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android.App.UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : Genera un fragmento de XML de [/manifest/uses-Permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android. Content. BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : Genera un fragmento de XML de [/manifest/Application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android. Content. ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : Genera un fragmento de XML de [/manifest/Application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android. Content. GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : Genera un fragmento de XML de [/manifest/Application/Provider/Grant-URI-Permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)

