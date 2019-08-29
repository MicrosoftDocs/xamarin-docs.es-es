---
title: Introducción a Android
description: En este documento se describe cómo empezar a usar la inserción de .NET con Android. Se describe la instalación de la inserción de .NET, la creación de un proyecto de biblioteca de Android, el uso de la salida generada en un proyecto de Android Studio y otras consideraciones.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: d1d05c75b8026112e8b81c91144361b65ad3a8e0
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120036"
---
# <a name="getting-started-with-android"></a>Introducción a Android

Además de los requisitos de la guía de [Introducción a Java](~/tools/dotnet-embedding/get-started/java/index.md) , también necesitará:

- [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o posterior
- [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1,8

Como información general, haremos lo siguiente:

- Crear un C# proyecto de biblioteca de Android
- Instalación de la inserción de .NET a través de NuGet
- Ejecución de la inserción de .NET en el ensamblado de la biblioteca de Android
- Usar el archivo AAR generado en un proyecto Java en Android Studio

## <a name="create-an-android-library-project"></a>Creación de un proyecto de biblioteca de Android

Abra Visual Studio para Windows o Mac, cree un nuevo proyecto de biblioteca de clases de Android, asígnele el nombre **Hello-from-CSharp**y guárdelo en **~/Projects/Hello-from-CSharp** o **%userprofile%\Projects\hello-from-CSharp**.

Agregue una nueva actividad de Android denominada **HelloActivity.CS**, seguida de un diseño de Android en **Resource/layout/Hello. axml**.

Agregue un nuevo `TextView` a su diseño y cambie el texto a algo más agradable.

El origen de diseño debería tener un aspecto similar al siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

En la actividad, asegúrese de que está llamando `SetContentView` a con el nuevo diseño:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> No olvide el `[Register]` atributo. Para obtener más información, consulte [limitaciones](#current-limitations-on-android).

Compile el proyecto. El ensamblado resultante se guardará `bin/Debug/hello-from-csharp.dll`en.

## <a name="installing-net-embedding-from-nuget"></a>Instalación de la inserción de .NET desde NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .net para su proyecto.

La invocación del comando que debe configurar tendrá el siguiente aspecto:

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Usar la salida generada en un proyecto de Android Studio

1. Abra Android Studio y cree un nuevo proyecto con una **actividad vacía**.
2. Haga clic con el botón derecho en el módulo de la **aplicación** y elija **nuevo > módulo**.
3. Seleccione **Importar. JAR/. Paquete AAR**.
4. Use el explorador de directorios para buscar **~/Projects/Hello-from-CSharp/Output/hello_from_csharp.AAR** y haga clic en **Finalizar**.

![Importar AAR en Android Studio](android-images/androidstudioimport.png)

Se copiará el archivo AAR en un nuevo módulo denominado **hello_from_csharp**.

![Proyecto de Android Studio](android-images/androidstudioproject.png)

Para usar el nuevo módulo desde la **aplicación**, haga clic con el botón derecho y elija **abrir configuración del módulo**. En la pestaña dependencias, agregue una nueva **dependencia de módulo** y elija **: hello_from_csharp**.

![Dependencias Android Studio](android-images/androidstudiodependencies.png)

En la actividad, agregue un nuevo `onResume` método y use el siguiente código para iniciar la C# actividad:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>Compresión de ensamblado (*importante*)

Se requiere un cambio adicional para la inserción de .NET en el proyecto de Android Studio.

Abra el archivo **Build. Gradle** de la aplicación y agregue el siguiente cambio:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin. Android actualmente carga los ensamblados .NET directamente desde APK, pero requiere que no se compriman los ensamblados.

Si no tiene esta configuración, la aplicación se bloqueará al iniciarse e imprimirá algo parecido a esto en la consola:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Ejecución de la aplicación

Al iniciar la aplicación:

![Hola desde C# el ejemplo que se ejecuta en el emulador](android-images/hello-from-csharp-android.png)

Tenga en cuenta lo que ha ocurrido aquí:

- Tenemos una C# clase, `HelloActivity`, que crea subclases de Java
- Tenemos archivos de recursos de Android
- Los usamos desde Java en Android Studio

Para que este ejemplo funcione, se configuran todos los elementos siguientes en el APK final:

- Xamarin. Android está configurado en el inicio de la aplicación
- Ensamblados .NET incluidos en **recursos o ensamblados**
- Modificaciones de **archivo AndroidManifest. XML** para C# sus actividades, etc.
- Recursos y activos de Android desde bibliotecas .NET
- [Contenedores de Android](~/android/platform/java-integration/android-callable-wrappers.md) a los que `Java.Lang.Object` se puede llamar para cualquier subclase

Si busca un tutorial adicional, consulte el vídeo siguiente, en el que se muestra la inserción de la [demostración de pintura](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) de Charles Petzold en un proyecto de Android Studio:

[![Embeddinator-4000 para Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>Uso de Java 1,8

En cuanto a la escritura, la mejor opción es usar Android Studio 3,0 ([Descargue aquí](https://developer.android.com/studio/index.html)).

Para habilitar Java 1,8 en el archivo **Build. Gradle** del módulo de la aplicación:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

También puede echar un vistazo a un [proyecto de prueba de Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) para obtener más detalles. 

Si desea usar Android Studio 2.3. x Stable, tendrá que habilitar la cadena de herramientas de Jack en desuso:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Limitaciones actuales en Android

En este momento, si crea subclases `Java.Lang.Object`, Xamarin. Android generará el código auxiliar de Java (contenedor de Android Callable) en lugar de la inserción de .net. Por esta razón, debe seguir las mismas reglas para exportar C# a Java como Xamarin. Android. Por ejemplo:

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

- `[Register]`se requiere para asignar a un nombre de paquete de Java deseado
- `[Export]`es necesario para hacer que un método sea visible en Java

Podemos usar `ViewSubclass` en Java como se puede:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Obtenga más información sobre la [integración de Java con Xamarin. Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Varios ensamblados

Insertar un solo ensamblado es sencillo; sin embargo, es mucho más probable que tenga más de un C# ensamblado. Muchas veces tendrá dependencias en paquetes NuGet, como las bibliotecas de compatibilidad de Android o Google Play Services que complican aún más las cosas.

Esto provoca un dilema, ya que la incrustación de .NET debe incluir muchos tipos de archivos en el AAR final, como:

- Activos de Android
- Recursos de Android
- Bibliotecas nativas de Android
- Código fuente de Java para Android

Lo más probable es que no quiera incluir estos archivos de la biblioteca de compatibilidad de Android ni Google Play Services en el AAR, sino que usaría la versión oficial de Google en Android Studio.

Este es el enfoque recomendado:

- Pasar la incrustación .NET de cualquier ensamblado de su propiedad (tener el origen para) y desea llamar desde Java
- Pasar la incrustación .NET a cualquier ensamblado del que necesite activos Android, bibliotecas nativas o recursos
- Agregue dependencias de Java como la biblioteca de compatibilidad de Android o Google Play Services en Android Studio

Por lo tanto, el comando podría ser:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Debe excluir cualquier elemento de NuGet, a menos que vea que contiene activos, recursos, etc. de Android que necesitará en el proyecto de Android Studio. También puede omitir las dependencias que no necesita llamar desde Java, y el vinculador _debe_ incluir las partes de la biblioteca que se necesitan.

Para agregar las dependencias Java necesarias en Android Studio, el archivo **Build. Gradle** podría ser similar al siguiente:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Información adicional

- [Devoluciones de llamada en Android](~/tools/dotnet-embedding/android/callbacks.md)
- [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
- [Limitaciones de la inserción de .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuir al proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo meteorológico (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
