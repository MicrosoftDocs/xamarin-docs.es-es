---
title: Pantalla de presentación
description: Una aplicación Android tarda algún tiempo en iniciarse, sobre todo cuando la aplicación se inicia por primera vez en un dispositivo. Una pantalla de presentación puede mostrar el progreso de inicio al usuario o para indicar la personalización de marca.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/02/2019
ms.openlocfilehash: cc499902058e7b20b00e65e0c6541b8d137804a7
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425508"
---
# <a name="splash-screen"></a>Pantalla de presentación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)

_Una aplicación Android tarda algún tiempo en iniciarse, sobre todo cuando la aplicación se inicia por primera vez en un dispositivo. Una pantalla de presentación puede mostrar el progreso de inicio al usuario o para indicar la personalización de marca._

## <a name="overview"></a>Información general

Una aplicación Android tarda algún tiempo en iniciarse, especialmente durante la primera vez que la aplicación se ejecuta en un dispositivo (en ocasiones, esto se conoce como _Inicio en frío_). La pantalla de presentación puede mostrar el progreso de inicio del usuario, o puede mostrar información de personalización de marca para identificar y promover la aplicación.

En esta guía se describe una técnica para implementar una pantalla de presentación en una aplicación de Android. Se tratan los pasos siguientes:

1. Crear un recurso dibujable para la pantalla de presentación.

2. Definir un nuevo tema que mostrará el recurso que se va a dibujar.

3. Agregar una nueva actividad a la aplicación que se usará como la pantalla de presentación definida por el tema creado en el paso anterior.

[![pantalla de presentación del logotipo de Xamarin de ejemplo seguida de la pantalla de la aplicación](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>Requisitos

En esta guía se da por supuesto que la aplicación tiene como destino el nivel de API de Android 21 o superior. La aplicación también debe tener los paquetes de NuGet **Xamarin. Android. support. V4** y **Xamarin. Android. support. V7. AppCompat** agregados al proyecto.

Todo el código y XML de esta guía se pueden encontrar en el proyecto de ejemplo [SplashScreen](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen) de esta guía.

## <a name="implementing-a-splash-screen"></a>Implementar una pantalla de presentación

La forma más rápida de presentar y mostrar la pantalla de presentación es crear un tema personalizado y aplicarlo a una actividad que muestre la pantalla de presentación. Cuando se representa la actividad, carga el tema y aplica el recurso que se dibuja (al que hace referencia el tema) al fondo de la actividad. Este enfoque evita la necesidad de crear un archivo de diseño.

La pantalla de presentación se implementa como una actividad que muestra la marca dibujable, realiza las inicializaciones y inicia cualquier tarea. Una vez que la aplicación se ha arrancado, la actividad de la pantalla de presentación inicia la actividad principal y se quita de la pila de retroceso de la aplicación.

### <a name="creating-a-drawable-for-the-splash-screen"></a>Crear un dibujable para la pantalla de presentación

La pantalla de presentación mostrará un XML que se podrá dibujar en el fondo de la actividad de la pantalla de presentación. Es necesario usar una imagen de mapa de imágenes (como PNG o JPG) para que se muestre la imagen.

La aplicación de ejemplo define una dibujable denominada **splash_screen. XML**. Este dibujo usa una [lista de capas](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centrar la imagen de la pantalla de presentación en la aplicación, tal como se muestra en el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash_logo"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Esta `layer-list` centra la imagen de la pantalla de presentación en un color de fondo especificado por el recurso de `@color/splash_background`. La aplicación de ejemplo define este color en el archivo **Resources/Values/colors. XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  ...
  <color name="splash_background">#FFFFFF</color>
</resources>
```

Para obtener más información sobre los objetos de `Drawable`, consulte la [documentación de Google en Android drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable).

### <a name="implementing-a-theme"></a>Implementar un tema

Para crear un tema personalizado para la actividad de la pantalla de presentación, edite (o agregue) el archivo **Values/Styles. XML** y cree un nuevo elemento `style` para la pantalla de presentación. A continuación se muestra un archivo **Values/Style. XML** de ejemplo con un `style` denominado mis archivos **. Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

    <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>  
    <item name="android:windowFullscreen">true</item>  
    <item name="android:windowContentOverlay">@null</item>  
    <item name="android:windowActionBar">true</item>  
  </style>
</resources>
```

**. Splash** es muy Spartan &ndash; declara el fondo de la ventana, quita explícitamente la barra de título de la ventana y declara que está en pantalla completa. Si desea crear una pantalla de presentación que eMule la interfaz de usuario de la aplicación antes de que la actividad infla el primer diseño, puede usar `windowContentOverlay` en lugar de `windowBackground` en la definición de estilo. En este caso, también debe modificar **splash_screen. XML** drawable para que muestre una emulación de la interfaz de usuario.

### <a name="create-a-splash-activity"></a>Crear una actividad de presentación

Ahora necesitamos una nueva actividad para que Android inicie con la imagen de bienvenida y realice las tareas de inicio. El código siguiente es un ejemplo de una implementación completa de la pantalla de presentación:

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` usa explícitamente el tema que se creó en la sección anterior, invalidando el tema predeterminado de la aplicación.
No es necesario cargar un diseño en `OnCreate` como el tema declara una función dibujable como fondo.

Es importante establecer el atributo de `NoHistory=true` para que la actividad se quite de la pila de retroceso. Para evitar que el botón atrás cancele el proceso de inicio, también puede invalidar `OnBackPressed` y hacer que no haga nada:

```csharp
public override void OnBackPressed() { }
```

El trabajo de inicio se realiza de forma asincrónica en `OnResume`. Esto es necesario para que el trabajo de inicio no se ralentice o retrase la apariencia de la pantalla de inicio. Una vez completado el trabajo, `SplashActivity` iniciará `MainActivity` y el usuario puede empezar a interactuar con la aplicación.

Esta nueva `SplashActivity` se establece como la actividad del iniciador de la aplicación estableciendo el atributo `MainLauncher` en `true`. Dado que `SplashActivity` es ahora la actividad del iniciador, debe editar `MainActivity.cs`y quitar el atributo `MainLauncher` de `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modo horizontal

La pantalla de presentación implementada en los pasos anteriores se mostrará correctamente en el modo vertical y horizontal. Sin embargo, en algunos casos es necesario tener pantallas de presentación independientes para los modos vertical y horizontal (por ejemplo, si la imagen de la pantalla de presentación está en pantalla completa).

Para agregar una pantalla de presentación para el modo horizontal, siga estos pasos:

1. En la carpeta **Resources/drawable** , agregue la versión horizontal de la imagen de la pantalla de presentación que desee usar. En este ejemplo, **splash_logo_land. png** es la versión horizontal del logotipo que se usó en los ejemplos anteriores (utiliza letras en blanco en lugar de azul).

2. En la carpeta **Resources/drawable** , cree una versión horizontal del `layer-list` dibujable que se definió anteriormente (por ejemplo, **splash_screen_land. XML**). En este archivo, establezca la ruta de acceso del mapa de bits en la versión horizontal de la imagen de la pantalla de presentación. En el ejemplo siguiente, **splash_screen_land. XML** usa **splash_logo_land. png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3. Cree la carpeta **Resources/Values-Land** si aún no existe.

4. Agregue los archivos **colors. XML** y **style. XML** a **valores-terrenos** (se pueden copiar y modificar desde los archivos **valores/colores. XML** y **valores/estilo. XML** existentes).

5. Modifique **Values-Land/Style. XML** para que use la versión horizontal de drawable para `windowBackground`. En este ejemplo, se usa **splash_screen_land. XML** :

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6. Modifique **Values-Land/colors. XML** para configurar los colores que quiere usar para la versión horizontal de la pantalla de presentación. En este ejemplo, el color de fondo de la pantalla de presentación cambia a azul para el modo horizontal:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7. Vuelva a compilar y ejecutar la aplicación. Gire el dispositivo al modo horizontal mientras se sigue mostrando la pantalla de presentación. La pantalla de presentación cambia a la versión horizontal:

    [![rotación de la pantalla de presentación en modo horizontal](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

Tenga en cuenta que el uso de una pantalla de presentación en modo horizontal no siempre proporciona una experiencia sin problemas. De forma predeterminada, Android inicia la aplicación en modo vertical y la cambia al modo horizontal, incluso si el dispositivo ya está en modo horizontal. Como resultado, si la aplicación se inicia mientras el dispositivo está en modo horizontal, el dispositivo presenta brevemente la pantalla de presentación vertical y, a continuación, anima el giro desde el vertical hasta la pantalla de presentación horizontal. Desafortunadamente, esta transición inicial vertical a horizontal tiene lugar incluso cuando se especifica `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` en las marcas de la actividad de presentación. La mejor manera de solucionar esta limitación es crear una única imagen de pantalla de presentación que se represente correctamente en los modos vertical y horizontal.

## <a name="summary"></a>Resumen

En esta guía se describe una manera de implementar una pantalla de presentación en una aplicación de Xamarin. Android; es decir, aplicar un tema personalizado a la actividad de inicio.

## <a name="related-links"></a>Vínculos relacionados

- [SplashScreen (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [lista de capas que se dibujan](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [Patrones de diseño de material: pantallas de inicio](https://material.io/design/communication/launch-screen.html#usage)
