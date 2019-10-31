---
title: ProGuard
description: ProGuard de Xamarin.Android es un reductor, optimizador y comprobador previo de archivos de clase Java. Permite detectar y quitar el código sin usar, así como analizar y optimizar el código de bytes. En esta guía se explica cómo funciona ProGuard, cómo se habilita en el proyecto y cómo se configura. También se proporcionan varios ejemplos de configuraciones de ProGuard.
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 854287ec22fb89ec2f0a55f8c3854dc0106fb11a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021164"
---
# <a name="proguard"></a>ProGuard

_ProGuard de Xamarin.Android es un reductor, optimizador, y comprobador previo de archivos de clase Java. Permite detectar y quitar el código sin usar, así como analizar y optimizar el código de bytes. En esta guía se explica cómo funciona ProGuard, cómo se habilita en el proyecto y cómo se configura. También se proporcionan varios ejemplos de configuraciones de ProGuard._

## <a name="overview"></a>Información general

ProGuard detecta y quita las clases, los campos, los métodos y los atributos sin usar de la aplicación empaquetada. Incluso puede hacer lo mismo en las bibliotecas a las que se hace referencia, lo que puede ayudar a evitar el límite de 64 000 referencias. La herramienta ProGuard de Android SDK también optimiza el código de bytes y quita las instrucciones de código sin usar. ProGuard lee los **archivos JAR de entrada**, los reduce, los optimiza y los comprueba previamente y, después, escribe los resultados en uno o varios **archivos JAR de salida**. 

ProGuard procesa los APK de entrada mediante los pasos siguientes: 

1. **Paso de reducción** &ndash; ProGuard determina de forma recursiva qué clases y miembros de clases se usan. Todas las demás clases y miembros de clases se descartan. 

2. **Paso de optimización** &ndash; ProGuard optimiza todavía más el código. 
    Entre otras optimizaciones, se pueden hacer privadas, estáticas o finales las clases y los métodos que no son puntos de entrada, se pueden quitar los parámetros sin usar y se pueden insertar algunos métodos. 

3. **Paso de ofuscación**: en el desarrollo nativo de Android, ProGuard cambia el nombre de las clases y los miembros de clases que no son puntos de entrada. La conservación de los puntos de entrada garantiza que todavía se pueda tener acceso a ellos mediante sus nombres originales. Sin embargo, este paso no es compatible con Xamarin.Android, dado que la aplicación se compila en lenguaje intermedio (IL).

4. **Paso de comprobación previa** &ndash; se realizan comprobaciones en los códigos de bytes de Java antes del tiempo de ejecución y se anotan los archivos de clase para que pueda aprovecharlo la máquina virtual Java. Este es el único paso que no tiene que conocer los puntos de entrada. 

Cada uno de estos pasos es *opcional*. Como se explicará en la sección siguiente, ProGuard Xamarin.Android solo usa un subconjunto de estos pasos. 

## <a name="proguard-in-xamarinandroid"></a>ProGuard en Xamarin.Android

La configuración de ProGuard de Xamarin.Android no ofusca el APK. De hecho, no es posible habilitar la ofuscación mediante ProGuard (ni siquiera mediante el uso de archivos de configuración personalizados). Por lo tanto, ProGuard de Xamarin.Android solo realiza los pasos de **reducción** y **optimización**: 

[![Pasos de reducción y optimización](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

Antes de usar ProGuard, es importante saber cómo funciona dentro del proceso de compilación de `Xamarin.Android`. Este proceso usa dos pasos independientes: 

1. Enlazador de Xamarin Android

2. ProGuard

Cada uno de estos pasos se describe a continuación.

### <a name="linker-step"></a>Paso de enlazador

El enlazador de Xamarin.Android emplea el análisis estático de la aplicación para determinar lo siguiente: 

- Qué ensamblados se usan realmente.

- Qué tipos se usan realmente.

- Qué miembros se usan realmente. 

El enlazador siempre se ejecutará antes del paso de ProGuard. Por este motivo, el enlazador puede eliminar un ensamblado, un tipo o un miembro en el que cabría esperar que se ejecutase ProGuard. (Para obtener más información sobre la vinculación en Xamarin.Android, consulte [Linking on Android](~/android/deploy-test/linker.md) (Vinculación en Android)).

### <a name="proguard-step"></a>Paso de ProGuard

Una vez que haya finalizado correctamente el paso de enlazador, ProGuard se ejecuta para quitar el código de bytes de Java sin usar. Este es el paso que optimiza el APK. 

## <a name="using-proguard"></a>Uso de ProGuard

Para usar ProGuard en el proyecto de aplicación, primero debe habilitar ProGuard. Después, puede dejar que el proceso de compilación de Xamarin.Android use un archivo de configuración de ProGuard predeterminado, o bien puede crear su propio archivo de configuración personalizado para que lo use ProGuard. 

### <a name="enabling-proguard"></a>Habilitación de ProGuard

Use los pasos siguientes para habilitar ProGuard en el proyecto de aplicación:

1. Asegúrese de que el proyecto está establecido en la configuración de **lanzamiento** (esto es importante porque el enlazador debe ejecutarse para que se pueda ejecutar ProGuard): 

    [![Configuración para seleccionar la versión](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. Habilite ProGuard. Para ello, active la opción **Habilitar ProGuard** en la pestaña **Empaquetado** de **Propiedades > Opciones de Android**: 

    [![Opción Habilitar ProGuard seleccionada](proguard-images/03-enable-proguard-sml.png)](proguard-images/03-enable-proguard.png#lightbox)

Para la mayoría de las aplicaciones de Xamarin.Android, el archivo de configuración de ProGuard predeterminado que Xamarin.Android proporciona será suficiente para quitar (exclusivamente) todo el código sin usar. Para ver la configuración de ProGuard predeterminada, abra el archivo que se encuentra en **obj\\Release\\proguard\\proguard_xamarin.cfg**.

En el ejemplo siguiente se muestra un archivo **proguard_xamarin.cfg** generado típico:

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

En la sección siguiente se describe cómo crear un archivo de configuración de ProGuard personalizado. 

### <a name="customizing-proguard"></a>Personalización de ProGuard

Opcionalmente, puede agregar un archivo de configuración de ProGuard personalizado para ejercer más control sobre las herramientas de ProGuard. Por ejemplo, tal vez le interese indicarle a ProGuard de manera explícita qué clases debe conservar. Para ello, cree un archivo **.cfg** y aplique la acción de compilación `ProGuardConfiguration` del panel de **Propiedades** del **Explorador de soluciones**: 

[![Acción de compilación ProguardConfiguration seleccionada](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

Tenga en cuenta que este archivo de configuración no reemplaza al archivo **proguard_xamarin.cfg** de Xamarin.Android, ya que ProGuard usa ambos. 

En algunos casos, ProGuard no puede analizar correctamente la aplicación y podría eliminar código que la aplicación realmente necesita. Si esto ocurre, puede agregar una línea `-keep` al archivo de configuración de ProGuard personalizado: 

```
-keep public class MyClass
```

En este ejemplo, se establece `MyClass` en el nombre real de la clase que se quiere que ProGuard omita.

También puede registrar sus propios nombres con anotaciones `[Register]` y usar estos nombres para personalizar las reglas de ProGuard. Puede registrar nombres para Adapters, Views, BroadcastReceivers, Services, ContentProviders, Activities y Fragments. Para más información sobre cómo usar el atributo personalizado `[Register]`, consulte [Working with JNI](~/android/platform/java-integration/working-with-jni.md) (Uso de JNI).

### <a name="proguard-options"></a>Opciones de ProGuard

ProGuard ofrece una serie de opciones que puede configurar para proporcionar un mayor control sobre su funcionamiento. En el [manual de ProGuard](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html) se proporciona documentación de referencia completa sobre el uso de ProGuard. 

Xamarin.Android admite las siguientes opciones de ProGuard: 

- [Opciones de entrada/salida](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [Opciones de conservación](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [Opciones de reducción](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [Opciones generales](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [Rutas de clase](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [Nombres de archivo](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [Filtros de archivo](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [Filtros](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [Información general sobre las opciones `Keep`](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [Modificadores de las opciones de conservación](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [Especificaciones de clase](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Xamarin.Android *omite* las opciones siguientes:

- [Opciones de optimización](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [Opciones de ofuscación](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [Opciones de comprobación previa](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard y Android Nougat

Si intenta usar ProGuard con Android 7.0 o versiones posteriores, debe descargar una versión más reciente de ProGuard, ya que Android SDK no incluye una versión nueva que sea compatible con JDK 1.8.

Puede usar este [paquete NuGet](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0) para instalar una versión más reciente de `proguard.jar`. Para obtener más información sobre cómo actualizar el archivo `proguard.jar` predeterminado de Android SDK, consulte esta explicación de [Stack Overflow](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706).

Encontrará todas las versiones de ProGuard en la [página de SourceForge](https://sourceforge.net/projects/proguard/files/). 

## <a name="example-proguard-configurations"></a>Ejemplo de configuraciones de ProGuard

A continuación se muestran dos archivos de configuración de ProGuard de ejemplo. Tenga en cuenta que, en estos casos, el proceso de compilación de Xamarin.Android proporcionará los archivos JAR de **entrada**, **salida** y **biblioteca**. Por lo tanto, puede centrarse en otras opciones como `-keep`. 

### <a name="a-simple-android-activity"></a>Actividad simple de Android

En el ejemplo siguiente se ilustra la configuración de una sencilla actividad de Android:

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>Aplicación completa de Android

En el ejemplo siguiente se ilustra la configuración de una aplicación completa de Android:

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard y el proceso de compilación de Xamarin.Android

En las secciones siguientes se explica cómo se ejecuta ProGuard durante una compilación de **lanzamiento** de Xamarin.Android.

### <a name="what-command-is-proguard-running"></a>¿Qué comando ejecuta ProGuard?

ProGuard es simplemente un archivo `.jar` que se proporciona con Android SDK. Por lo tanto, se invoca en un comando: 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>La tarea ProGuard

La tarea ProGuard se encuentra dentro del ensamblado **Xamarin.Android.Build.Tasks.dll**. Forma parte del destino `_CompileToDalvikWithDx`, que a su vez forma parte del destino `_CompileDex`. 

En la lista siguiente se proporciona un ejemplo de los parámetros predeterminados que se generan después de crear un proyecto mediante **Archivo > Nuevo proyecto**: 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

En el ejemplo siguiente se muestra un comando de ProGuard típico que se ejecuta desde el IDE:

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="file-issues"></a>Problemas de archivos

Cuando ProGuard lee su archivo de configuración, puede aparecer el mensaje de error siguiente: 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

Este problema suele producirse en Windows porque el archivo `.cfg` tiene una codificación incorrecta. ProGuard no puede controlar la _marca de orden de bytes_ (BOM) que puede estar presente en los archivos de texto. Si hay una marca BOM, ProGuard se cerrará con el error anterior. 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para evitar este problema, edite el archivo de configuración personalizado desde un editor de texto que permita guardar el archivo sin una marca BOM. Para solucionar este problema, asegúrese de que el editor de texto tenga la codificación establecida en `UTF-8`. Por ejemplo, el editor de texto [Notepad++](https://notepad-plus-plus.org/) puede guardar archivos sin la L. MAT seleccionando **Encoding (Codificación) &gt; Encode in UTF-8 Without BOM (Codificar en UTF-8 sin BOM)** al guardar el archivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para evitar este problema, guarde el archivo de configuración personalizado desde un editor de texto que permita omitir la marca BOM. 

-----

### <a name="other-issues"></a>Otros problemas

En la página de [solución de problemas](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html) de ProGuard se tratan los problemas comunes (junto con sus soluciones) que se pueden producir cuando se usa ProGuard.

## <a name="summary"></a>Resumen

En esta guía se explica cómo funciona ProGuard en Xamarin.Android, cómo se habilita en el proyecto de aplicación y cómo se configura. Se proporcionan configuraciones de ejemplo de ProGuard y se describen las soluciones de los problemas comunes. Para obtener más información sobre la herramienta ProGuard y Android, consulte [Reducir tu código y tus recursos](https://developer.android.com/tools/help/proguard.html). 

## <a name="related-links"></a>Vínculos relacionados

- [Preparar una aplicación para su lanzamiento](~/android/deploy-test/release-prep/index.md)
