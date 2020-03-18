---
title: Trabajo con el manifiesto de Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027800"
---
# <a name="working-with-the-android-manifest"></a>Trabajo con el manifiesto de Android

El archivo **AndroidManifest.xml** es un archivo eficaz de la plataforma Android que le permite describir la funcionalidad y los requisitos de la aplicación para Android. Sin embargo, no es fácil trabajar con él. Xamarin.Android ayuda a minimizar esta dificultad al permitirle agregar atributos personalizados a las clases, que se usarán para generar automáticamente el manifiesto. Nuestro objetivo es que el 99 % de nuestros usuarios nunca tengan que modificar manualmente el archivo **AndroidManifest.xml**. 

El archivo **AndroidManifest.xml** se genera como parte del proceso de compilación y el XML que se encuentra en **Properties/AndroidManifest.xml** se combina con XML que se genera a partir de atributos personalizados. El archivo **AndroidManifest.xml** combinado resultante se encuentra en el subdirectorio **obj**; por ejemplo, reside en **obj/Debug/android/AndroidManifest.xml** para las compilaciones de depuración. El proceso de combinación es trivial: usa atributos personalizados en el código para generar elementos XML e *inserta* esos elementos en **AndroidManifest.xml**. 

## <a name="the-basics"></a>Conceptos básicos

En tiempo de compilación, los ensamblados se examinan para las clases que no son `abstract` que se derivan de la [actividad ](xref:Android.App.Activity) y tienen el atributo [`[Activity]`](xref:Android.App.ActivityAttribute) declarado en ellos. A continuación, usa estas clases y atributos para generar el manifiesto. Por ejemplo, considere el siguiente código: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Esto hace que no se genere nada en **AndroidManifest.xml**. Si desea que se genere un elemento `<activity/>`, debe usar el atributo personalizado [`[Activity]`](xref:Android.App.Activity) 
: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Este ejemplo hace que se agregue el siguiente fragmento xml al archivo **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

El atributo `[Activity]` no tiene ningún efecto en los tipos `abstract`; se omiten los tipos `abstract`.

### <a name="activity-name"></a>Nombre de actividad

A partir de Xamarin.Android 5.1, el nombre de tipo de una actividad se basa en el MD5SUM del nombre calificado con el ensamblado del tipo que se va a exportar. Esto permite proporcionar el mismo nombre completo a partir de dos ensamblados diferentes y no obtener un error de empaquetado. (Antes de Xamarin.Android 5.1, el nombre de tipo predeterminado de la actividad se creó a partir del espacio de nombres en minúsculas y el nombre de clase). 

Si desea invalidar este valor predeterminado y especificar explícitamente el nombre de la actividad, use la propiedad [`Name`](xref:Android.App.ActivityAttribute.Name): 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento xml:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> Debe utilizar la propiedad `Name` solo por motivos de compatibilidad con versiones anteriores, ya que este cambio de nombre puede ralentizar la búsqueda de tipos en tiempo de ejecución. Si tiene código heredado que espera que el nombre de tipo predeterminado de la actividad se base en el espacio de nombres en minúsculas y el nombre de clase, consulte [Nomenclatura de los contenedores de Android que se pueden llamar](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) para obtener sugerencias sobre cómo mantener la compatibilidad. 

### <a name="activity-title-bar"></a>Barra de título de actividad

De forma predeterminada, Android proporciona una barra de título a la aplicación cuando se ejecuta. El valor utilizado para esto es [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). En la mayoría de los casos, este valor será diferente del nombre de clase. Para especificar la etiqueta de la aplicación en la barra de título, use la propiedad [`Label`](xref:Android.App.ActivityAttribute.Label).
Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Disponible desde el selector de aplicaciones

De forma predeterminada, la actividad no se mostrará en la pantalla del iniciador de aplicaciones de Android. Esto se debe a que es probable que haya muchas actividades en la aplicación y no desea un icono para cada una de ellas. Para especificar la que se debe iniciar desde el iniciador de la aplicación, use la propiedad [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher). Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml.

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

De forma predeterminada, se proporcionará a la actividad el icono del iniciador predeterminado proporcionado por el sistema. Para usar un icono personalizado, agregue primero el archivo **.png** a **Resources/drawable**, establezca su acción de compilación en **AndroidResource** y, a continuación, use la propiedad [`Icon`](xref:Android.App.ActivityAttribute.Icon) para especificar el icono que se va a usar. Por ejemplo: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml.

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

Al agregar permisos al manifiesto de Android (como se describe en [Adición de permisos al manifiesto de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), estos permisos se registran en **Properties/AndroidManifest.xml**. Por ejemplo, si establece el permiso `INTERNET`, se agrega el siguiente elemento a **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Las compilaciones de depuración establecen automáticamente algunos permisos para facilitar la depuración (como `INTERNET` y `READ_EXTERNAL_STORAGE`) &ndash; estos valores solo se establecen en el archivo **obj/Debug/android/AndroidManifest.xml** generado y no se muestran como habilitados en la configuración de **Permisos necesarios**. 

Por ejemplo, si examina el archivo de manifiesto generado en **obj/Debug/android/AndroidManifest.xml**, puede ver los siguientes elementos de permiso agregados: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

En la versión de compilación de lanzamiento del manifiesto (en **obj/Debug/android/AndroidManifest.xml**), estos permisos *no* se configuran automáticamente. Si observa que el cambio a una compilación de lanzamiento hace que la aplicación pierda un permiso que estaba disponible en la compilación de depuración, compruebe que ha establecido explícitamente este permiso en la configuración de **Permisos necesarios** de la aplicación (consulte **Compilación > Aplicación de Android** en Visual Studio para Mac; consulte **Propiedades > Manifiesto de Android** en Visual Studio). 

## <a name="advanced-features"></a>Características avanzadas

### <a name="intent-actions-and-features"></a>Características y acciones de intención

El manifiesto de Android proporciona una manera de describir las funcionalidades de su actividad. Esto se hace mediante [intenciones](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) y el atributo personalizado [`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
. Puede especificar qué acciones son adecuadas para su actividad con el constructor [`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
y qué categorías son adecuadas con la propiedad [`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. Se debe proporcionar al menos una actividad (que es el motivo por el que se proporcionan las actividades en el constructor). Se puede proporcionar `[IntentFilter]` varias veces y cada una de ellas tiene como resultado un elemento `<intent-filter/>` independiente dentro del atributo `<activity/>`. Por ejemplo:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Este ejemplo produce el siguiente fragmento de xml.

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

### <a name="application-element"></a>Elemento de la aplicación

El manifiesto de Android también proporciona una manera de declarar las propiedades de toda la aplicación. Esto se hace a través del elemento `<application>` y su homólogo, el atributo personalizado [Application](xref:Android.App.ApplicationAttribute). Tenga en cuenta que se trata de una configuración de toda la aplicación (en todo el ensamblado) en lugar de la configuración por actividad. Normalmente, se declaran las propiedades `<application>` para toda la aplicación y, a continuación, se invalidan esta configuración (según sea necesario) en cada actividad. 

Por ejemplo, el atributo `Application` siguiente se agrega a **AssemblyInfo.cs** para indicar que se puede depurar la aplicación, que su nombre legible para el usuario sea **Mi aplicación** y que usa el estilo `Theme.Light` como tema predeterminado para todas las actividades: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Esta declaración hace que se genere el fragmento XML siguiente en **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

En este ejemplo, todas las actividades de la aplicación tendrán como valor predeterminado el estilo `Theme.Light`. Si establece el tema de una actividad en `Theme.Dialog`, solo esa actividad usará el estilo `Theme.Dialog`, mientras que todas las demás actividades de la aplicación tendrán como valor predeterminado el estilo `Theme.Light` establecido en el elemento `<application>`. 

El elemento `Application` no es la única manera de configurar los atributos `<application>`. Como alternativa, puede insertar atributos directamente en el elemento `<application>` de **Properties/AndroidManifest.xml**. Estos valores se combinan en el último elemento `<application>` que reside en **obj/Debug/android/AndroidManifest.xml**. Tenga en cuenta que el contenido de **Properties/AndroidManifest.xml** siempre invalida los datos proporcionados por los atributos personalizados. 

Hay muchos atributos de toda la aplicación que se pueden configurar en el elemento `<application>`; para más información acerca de esta configuración, consulte la sección [Propiedades públicas](xref:Android.App.ApplicationAttribute) de [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Lista de atributos personalizados

- [Android.App.ActivityAttribute](xref:Android.App.ActivityAttribute): Genera un fragmento XML de [/manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html). 
- [Android.App.ApplicationAttribute](xref:Android.App.ApplicationAttribute): Genera un fragmento XML de [/manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html). 
- [Android.App.InstrumentationAttribute](xref:Android.App.InstrumentationAttribute): Genera un fragmento XML de [/manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html). 
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute): Genera un fragmento XML de [//intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html). 
- [Android.App.MetaDataAttribute](xref:Android.App.MetaDataAttribute): Genera un fragmento XML de [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html). 
- [Android.App.PermissionAttribute](xref:Android.App.PermissionAttribute): Genera un fragmento XML de [//permission](https://developer.android.com/guide/topics/manifest/permission-element.html). 
- [Android.App.PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute): Genera un fragmento XML de [//permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html). 
- [Android.App.PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute): Genera un fragmento XML de [//permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html). 
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute) : Genera un fragmento XML de [/manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html). 
- [Android.App.UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute): Genera un fragmento XML de [/manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html). 
- [Android.App.UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute): Genera un fragmento XML de [/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html). 
- [Android.Content.BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute): Genera un fragmento XML de [/manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html). 
- [Android.Content.ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute): Genera un fragmento XML de [/manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html). 
- [Android.Content.GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute): Genera un fragmento XML de [/manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html).
