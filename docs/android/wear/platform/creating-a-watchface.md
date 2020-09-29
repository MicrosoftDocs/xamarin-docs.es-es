---
title: Creación de una esfera de inspección para el desgaste de Android 1,0
description: En esta guía se explica cómo implementar un servicio de reloj de observación personalizado para el desgaste de Android 1,0. Se proporcionan instrucciones paso a paso para crear un servicio de caras de supervisión digital de bajada, seguido de más código para crear una superficie de inspección de estilo analógico.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 79dcab73c379cecb5108a88cc8bbb2eab33af05c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457112"
---
# <a name="creating-a-watch-face"></a>Creación de una esfera del reloj

_En esta guía se explica cómo implementar un servicio de reloj de observación personalizado para el desgaste de Android 1,0. Se proporcionan instrucciones paso a paso para crear un servicio de caras de supervisión digital de bajada, seguido de más código para crear una superficie de inspección de estilo analógico._

## <a name="overview"></a>Información general

En este tutorial, se crea un servicio básico de la guía de observación para ilustrar los aspectos básicos de la creación de una superficie de inspección 1,0 personalizada de un desgaste de Android.
El servicio de la esfera de reloj inicial muestra una simple inspección digital que muestra la hora actual en horas y minutos:

[![Superficie de inspección digital](creating-a-watchface-images/01-initial-face.png "Captura de pantalla de ejemplo de la superficie de la inspección digital inicial")](creating-a-watchface-images/01-initial-face.png#lightbox)

Después de desarrollar y probar esta superficie de inspección digital, se agrega más código para actualizarla a una esfera de inspección analógica más sofisticada con tres manos:

[![Superficie de inspección analógica](creating-a-watchface-images/02-example-watchface.png "Captura de pantalla de ejemplo de la superficie de inspección analógica final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Ver los servicios faciales se agrupan e instalan como parte de una aplicación de desgaste de 1,0. En los siguientes ejemplos, no `MainActivity` contiene nada más que el código de la plantilla de aplicación de desgaste 1,0, de modo que el servicio de reloj de inspección se puede empaquetar e implementar en Smart Watch como parte de la aplicación. En efecto, esta aplicación servirá exclusivamente como vehículo para obtener el servicio de reloj de observación cargado en el dispositivo (o emulador) de desgaste 1,0 para la depuración y las pruebas.

## <a name="requirements"></a>Requisitos

Para implementar un servicio de reloj de inspección, se requiere lo siguiente:

- Android 5,0 (nivel de API 21) o superior en el dispositivo o emulador de desgaste.

- Las [bibliotecas de compatibilidad con el desgaste de Android de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Wear) se deben agregar al proyecto de Xamarin. Android.

Aunque Android 5,0 es el nivel de API mínimo para implementar un servicio de reloj, se recomienda Android 5,1 o posterior. Los dispositivos de uso de Android que ejecutan Android 5,1 (API 22) o una versión superior permiten el desgaste de las aplicaciones para controlar lo que se muestra en la pantalla mientras el dispositivo está en modo *ambiente* de baja energía. Cuando el dispositivo deja el modo *ambiente* de baja energía, está en modo *interactivo* . Para obtener más información sobre estos modos, consulte [mantener visible la aplicación](https://developer.android.com/training/wearables/apps/always-on.html).

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de desgaste de Android 1,0 denominado **WatchFace** (para obtener más información sobre cómo crear nuevos proyectos de Xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-vs-sml.png "Selección de la aplicación de desgaste en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Cuadro de diálogo nuevo proyecto](creating-a-watchface-images/03-wear-project-xs-sml.png "Selección de la aplicación de desgaste en el cuadro de diálogo nuevo proyecto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

Establezca el nombre del paquete en `com.xamarin.watchface` :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Configuración del nombre del paquete](creating-a-watchface-images/04-package-name-vs.png "Establezca el nombre del paquete en com. Xamarin. watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Configuración del nombre del paquete](creating-a-watchface-images/04-package-name-xs.png "Establezca el nombre del paquete en com. Xamarin. watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Además, desplácese hacia abajo y habilite los permisos de **Internet** y **WAKE_LOCK** :

[![Permisos necesarios](creating-a-watchface-images/05-required-permissions-vs.png "Habilitar permisos de INTERNET y WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Establezca la versión mínima de Android en **android 5,1 (nivel de API 22)**.
Además, habilite los permisos de **Internet** y **WakeLock** :

[![Permisos necesarios](creating-a-watchface-images/05-required-permissions-xs.png "Habilitación de permisos de Internet y WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

A continuación, descargue [preview.png](creating-a-watchface-images/preview.png) &ndash; este paso se agregará a la carpeta **drawables** más adelante en este tutorial.

## <a name="add-the-xamarinandroid-wear-package"></a>Adición del paquete de desgaste de Xamarin. Android

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Inicie el administrador de paquetes NuGet (en Visual Studio, haga clic con el botón derecho en **referencias** en el **Explorador de soluciones** y seleccione **administrar paquetes NuGet...**). Actualice el proyecto a la versión estable más reciente de **Xamarin. Android. desgaste**:

[![Agregar administrador de paquetes NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "Adición del paquete Xamarin. Android. desgaste")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Después, si **Xamarin. Android. support. v13** está instalado, desinstálelo:

[![Quitar el administrador de paquetes NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Quitar Xamarin. support. v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Inicie el administrador de paquetes NuGet (en Visual Studio para Mac, haga clic con el botón derecho en **paquetes** en el **Panel solución** y seleccione **agregar paquetes...**). Actualice el proyecto a la versión estable más reciente de **Xamarin. Android. desgaste**:

[![Agregar administrador de paquetes NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "Adición del paquete Xamarin. Android. desgaste")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

Compilar y ejecutar la aplicación en un dispositivo o emulador de desgaste (para obtener más información sobre cómo hacerlo, consulte la guía de [Introducción](~/android/wear/get-started/index.md) ). Debería ver la siguiente pantalla de la aplicación en el dispositivo de desgaste:

[![Captura de pantalla de la aplicación](creating-a-watchface-images/08-app-screen.png "Pantalla de la aplicación en el dispositivo de desgaste")](creating-a-watchface-images/08-app-screen.png#lightbox)

Llegados a este punto, la aplicación de desgaste básica no tiene la funcionalidad de observación, ya que aún no proporciona una implementación de Watch Point Service. Este servicio se agregará a continuación.

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

El desgaste de Android implementa caras de inspección a través de la `CanvasWatchFaceService` clase. `CanvasWatchFaceService` se deriva de `WatchFaceService` , que a su vez se deriva de `WallpaperService` tal y como se muestra en el diagrama siguiente:

[![Diagrama de herencia](creating-a-watchface-images/09-inheritance-diagram-sml.png "Diagrama de herencia de CanvasWatchFaceService")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` incluye un anidado `CanvasWatchFaceService.Engine` ; crea una instancia de un `CanvasWatchFaceService.Engine` objeto que realiza el trabajo real de dibujar la superficie de inspección. `CanvasWatchFaceService.Engine` se deriva de `WallpaperService.Engine` como se muestra en el diagrama anterior.

No se muestra en este diagrama `Canvas` el que `CanvasWatchFaceService` usa para dibujar la esfera del reloj que &ndash; `Canvas` se pasa a través del `OnDraw` método, tal y como se describe a continuación.

En las secciones siguientes, se creará un servicio de portada de inspección personalizado siguiendo estos pasos:

1. Defina una clase denominada `MyWatchFaceService` que se derive de  `CanvasWatchFaceService` .

2. Dentro de `MyWatchFaceService` , cree una clase anidada denominada  `MyWatchFaceEngine` que se derive de  `CanvasWatchFaceService.Engine` .

3. En `MyWatchFaceService` , implemente un `CreateEngine` método que cree instancias `MyWatchFaceEngine` de y lo devuelva.

4. En `MyWatchFaceEngine` , implemente el `OnCreate` método para crear el estilo de la esfera de inspección y realizar cualquier otra tarea de inicialización.

5. Implemente el `OnDraw` método de `MyWatchFaceEngine` . Se llama a este método cada vez que es necesario volver a dibujar la esfera de inspección (es decir, *invalidada*). `OnDraw` es el método que dibuja (y vuelve a dibujar) los elementos de la superficie de inspección, como la hora, el minuto y el segundo.

6. Implemente el `OnTimeTick` método de `MyWatchFaceEngine` .
    `OnTimeTick` se llama al menos una vez por minuto (en los modos de ambiente e interactivos) o cuando ha cambiado la fecha y hora.

Para obtener más información sobre `CanvasWatchFaceService` , consulte la documentación de la API de Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) .
Del mismo modo, [CanvasWatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica la implementación real de la superficie de inspección.

### <a name="add-the-canvaswatchfaceservice"></a>Agregar CanvasWatchFaceService

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Agregue un nuevo archivo denominado **MyWatchFaceService.CS** (en Visual Studio, haga clic con el botón derecho en **WatchFace** en el **Explorador de soluciones**, haga clic en **Agregar > nuevo elemento...** y seleccione **clase**).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Agregue un nuevo archivo denominado **MyWatchFaceService.CS** (en Visual Studio para Mac, haga clic con el botón derecho en el proyecto **WatchFace** , haga clic en **Agregar > nuevo archivo...** y seleccione **clase vacía**).

-----

Reemplace el contenido de este archivo por el código siguiente:

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (derivado de `CanvasWatchFaceService` ) es el "programa principal" de la superficie de inspección. `MyWatchFaceService` implementa solo un método, `OnCreateEngine` , que crea instancias y devuelve un `MyWatchFaceEngine` objeto ( `MyWatchFaceEngine` se deriva de `CanvasWatchFaceService.Engine` ). El `MyWatchFaceEngine` objeto con instancias debe devolverse como `WallpaperService.Engine` . El objeto de encapsulación `MyWatchFaceService` se pasa al constructor.

`MyWatchFaceEngine` es la implementación de la esfera de inspección real &ndash; que contiene el código que dibuja la esfera del reloj. También controla eventos del sistema, como cambios en la pantalla (modos de ambiente/interactivo, desactivación de la pantalla, etc.).

### <a name="implement-the-engine-oncreate-method"></a>Implementar el método de creación de motor

El `OnCreate` método inicializa la esfera de inspección. Agregue el siguiente campo a `MyWatchFaceEngine` :

```csharp
Paint hoursPaint;
```

Este `Paint` objeto se usará para dibujar la hora actual en la superficie de inspección. A continuación, agregue el método siguiente a `MyWatchFaceEngine` :

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` se llama poco después de `MyWatchFaceEngine` iniciarse. Configura `WatchFaceStyle` (que controla cómo interactúa el dispositivo con el usuario) y crea una instancia del `Paint` objeto que se usará para mostrar la hora.

La llamada a `SetWatchFaceStyle` hace lo siguiente:

1. Establece el *modo de inspección* en `PeekModeShort` , que hace que las notificaciones aparezcan como tarjetas de "lectura" pequeñas en la pantalla.

2. Establece la visibilidad del fondo en `Interruptive` , lo que hace que el fondo de una tarjeta PEEK solo se muestre brevemente si representa una notificación de interrupción.

3. Deshabilita la hora de la interfaz de usuario del sistema predeterminada para que no se dibuje en la superficie de inspección, de modo que la superficie de inspección personalizada pueda mostrar la hora en su lugar.

Para obtener más información sobre estas y otras opciones de estilo de la esfera de inspección, consulte la documentación de la API de Android [WatchFaceStyle. Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) .

Una vez `SetWatchFaceStyle` finalizada, `OnCreate` crea una instancia del `Paint` objeto ( `hoursPaint` ) y establece su color en blanco y su tamaño de texto en 48 píxeles ([TEXTSIZE](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) debe especificarse en píxeles).

### <a name="implement-the-engine-ondraw-method"></a>Implementar el método OnDraw del motor

El método `OnDraw` es quizás el método más `CanvasWatchFaceService.Engine` importante &ndash; es el método que realmente dibuja los elementos de la esfera de la inspección, como los dígitos y las manecillas del reloj.
En el ejemplo siguiente, dibuja una cadena de hora en la superficie de inspección.
Agregue el método siguiente a `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Cuando Android llama a `OnDraw` , pasa una `Canvas` instancia de y los límites en los que se puede dibujar la esfera. En el ejemplo de código anterior, `DateTime` se usa para calcular la hora actual en horas y minutos (en formato de 12 horas). La cadena de tiempo resultante se dibuja en el lienzo mediante el `Canvas.DrawText` método. La cadena mostrará 70 píxeles por encima del borde izquierdo y 80 píxeles hacia abajo desde el borde superior.

Para obtener más información sobre el `OnDraw` método, consulte la documentación de la API de Android [OnDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) .

### <a name="implement-the-engine-ontimetick-method"></a>Implementar el método OnTimeTick del motor

Android llama periódicamente al `OnTimeTick` método para actualizar la hora que se muestra en la superficie de inspección. Se llama al menos una vez por minuto (en los modos de ambiente e interactivos), o cuando la fecha y hora o la zona horaria han cambiado. Agregue el método siguiente a `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Esta implementación de `OnTimeTick` simplemente llama a `Invalidate` . El `Invalidate` método programa `OnDraw` para volver a dibujar la esfera de inspección.

Para obtener más información sobre el `OnTimeTick` método, consulte la documentación de la API de Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) .

## <a name="register-the-canvaswatchfaceservice"></a>Registrar CanvasWatchFaceService

`MyWatchFaceService` debe estar registrado en el **AndroidManifest.xml** de la aplicación de desgaste asociada. Para ello, agregue el siguiente código XML a la `<application>` sección:

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Este XML hace lo siguiente:

1. Establece el `android.permission.BIND_WALLPAPER` permiso. Este permiso concede al servicio de reloj ver el permiso para cambiar el papel tapiz del sistema en el dispositivo. Tenga en cuenta que este permiso se debe establecer en la `<service>` sección en lugar de en la `<application>` sección externa.

2. Define un `watch_face` recurso. Este recurso es un archivo XML corto que declara un `wallpaper` recurso (este archivo se creará en la sección siguiente).

3. Declara una imagen dibujable llamada `preview` que se mostrará en la pantalla de selección del selector de inspección.

4. Incluye un `intent-filter` para permitir que Android sepa que  `MyWatchFaceService` mostrará una esfera de inspección.

Esto completa el código para el ejemplo básico `WatchFace` . El siguiente paso consiste en agregar los recursos necesarios.

## <a name="add-resource-files"></a>Agregar archivos de recursos

Antes de poder ejecutar el servicio de inspección, debe agregar el recurso de **watch_face** y la imagen de vista previa. En primer lugar, cree un nuevo archivo XML en **Resources/XML/watch_face.xml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Establezca la acción de compilación de este archivo en **AndroidResource**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-vs.png "Establezca la acción de compilación en AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Acción de compilación](creating-a-watchface-images/10-android-resource-xs.png "Establezca la acción de compilación en AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Este archivo de recursos define un `wallpaper` elemento simple que se utilizará para la esfera de inspección.

Si todavía no lo ha hecho, descargue [preview.png](creating-a-watchface-images/preview.png).
Instálela en **Resources/drawable/preview.png**. Asegúrese de agregar este archivo al `WatchFace` proyecto. Esta imagen de vista previa se muestra al usuario en el selector de caras de inspección en el dispositivo de desgaste. Para crear una imagen de vista previa para su propia esfera de inspección, puede realizar una captura de pantalla de la superficie del reloj mientras se está ejecutando. (Para obtener más información sobre cómo obtener capturas de pantallas de dispositivos de desgaste, consulte [realizar capturas de pantallas](~/android/wear/deploy-test/debug-on-device.md#screenshots)).

## <a name="try-it"></a>¡Pruébelo!

Compile e implemente la aplicación en el dispositivo de desgaste. Debería ver que la pantalla de la aplicación de desgaste aparece como antes. Haga lo siguiente para habilitar la nueva esfera de inspección:

1. Deslice el dedo hacia la derecha hasta que vea el fondo de la pantalla de inspección.

2. Toque y mantenga presionado en cualquier lugar del fondo de la pantalla durante dos segundos.

3. Deslice el dedo de izquierda a derecha para desplazarse por los distintos rostros de inspección.

4. Seleccione la esfera de **ejemplo de Xamarin** (que se muestra a la derecha):

    [![Selector de watchface](creating-a-watchface-images/11-watchface-picker.png "Deslice el dedo para buscar la esfera de ejemplo de Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. Puntee en la esfera de **ejemplo de Xamarin** para seleccionarla.

Esto cambia la parte del reloj del dispositivo de desgaste para usar el servicio de la esfera de inspección personalizada implementada hasta el momento:

[![Superficie de inspección digital](creating-a-watchface-images/12-digital-watchface.png "Reloj digital personalizado en ejecución en el dispositivo de desgaste")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Se trata de una esfera de inspección relativamente crudo porque la implementación de la aplicación es tan mínima (por ejemplo, no incluye un fondo de la esfera de reloj y no llama `Paint` a los métodos de suavizado de contorno para mejorar la apariencia).
Sin embargo, implementa la funcionalidad básica necesaria para crear una superficie de inspección personalizada.

En la siguiente sección, esta superficie de inspección se actualizará a una implementación más sofisticada.

## <a name="upgrading-the-watch-face"></a>Actualización de la esfera de inspección

En el resto de este tutorial, `MyWatchFaceService` se actualiza para mostrar una superficie de inspección de estilo analógico y se amplía para admitir más características. Se agregarán las siguientes funcionalidades para crear la superficie de inspección actualizada:

1. Indica la hora con la hora analógica, el minuto y la segunda manecilla.

2. Reacciona a los cambios en la visibilidad.

3. Responde a los cambios entre el modo ambiente y el modo interactivo.

4. Lee las propiedades del dispositivo de desgaste subyacente.

5. Actualiza automáticamente la hora a la que tiene lugar un cambio de zona horaria.

Antes de implementar los cambios de código siguientes, descargue [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descomprima y mueva los archivos. png descomprimidos a **Resources/drawable** (sobrescriba el **preview.png**anterior). Agregue los nuevos archivos. png al `WatchFace` proyecto.

### <a name="update-engine-features"></a>Características del motor de actualización

El paso siguiente es actualizar **MyWatchFaceService.CS** a una implementación que dibuja una esfera de inspección analógica y admite nuevas características. Reemplace el contenido de **MyWatchFaceService.CS** por la versión analógica del código de la fuente de inspección en [MyWatchFaceService.CS](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (puede cortar y pegar este origen en el **MyWatchFaceService.CS**existente).

Esta versión de **MyWatchFaceService.CS** agrega más código a los métodos existentes e incluye métodos invalidados adicionales para agregar más funcionalidad. En las secciones siguientes se proporciona una visita guiada del código fuente.

#### <a name="oncreate"></a>OnCreate

El método **alcreate** actualizado configura el estilo de la esfera de inspección como antes, pero incluye algunos pasos adicionales:

1. Establece la imagen de fondo en el **xamarin_background** recurso que reside en **Resources/drawable-hdpi/xamarin_background.png**.

2. Inicializa `Paint` objetos para dibujar la manecilla de la hora, la manecilla del minuto y la segunda mano.

3. Inicializa un `Paint` objeto para dibujar los tics de hora alrededor del borde de la superficie de inspección.

4. Crea un temporizador que llama al `Invalidate` método (Redraw) para que la segunda mano se vuelva a dibujar cada segundo. Tenga en cuenta que este temporizador es necesario porque `OnTimeTick` llama `Invalidate` solo una vez cada minuto.

En este ejemplo solo se incluye una imagen **xamarin_background.png** ; sin embargo, puede que desee crear una imagen de fondo diferente para cada densidad de pantalla que admita la esfera de inspección personalizada.

#### <a name="ondraw"></a>OnDraw

El método **OnDraw** actualizado dibuja una superficie de inspección de estilo analógico mediante los pasos siguientes:

1. Obtiene la hora actual, que ahora se mantiene en un `time` objeto.

2. Determina los límites de la superficie de dibujo y su centro.

3. Dibuja el fondo, escalado para ajustarse al dispositivo cuando se dibuja el fondo.

4. Dibuja doce *TICs* alrededor de la superficie del reloj (correspondiente a las horas de la esfera del reloj).

5. Calcula el ángulo, la rotación y la longitud de cada mano de inspección.

6. Dibuja cada mano en la superficie de inspección. Tenga en cuenta que la segunda mano no se dibuja si el reloj está en modo ambiente.

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Se llama a este método para informar `MyWatchFaceEngine` sobre las propiedades del dispositivo de desgaste (por ejemplo, el modo ambiente de baja bits y la protección de la grabación). En `MyWatchFaceEngine` , este método solo comprueba el modo ambiente de baja velocidad (en el modo de ambiente de bajo nivel, la pantalla admite menos bits para cada color).

Para obtener más información sobre este método, consulte la documentación de la API de Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) .

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Se llama a este método cuando el dispositivo de desgaste entra o sale del modo ambiente. En la `MyWatchFaceEngine` implementación, la superficie de inspección deshabilita el suavizado de contorno cuando está en modo ambiente.

Para obtener más información sobre este método, consulte la documentación de la API de Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) .

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Se llama a este método siempre que el reloj esté visible u oculto. En `MyWatchFaceEngine` , este método registra o anula el registro del receptor de zona horaria (descrito a continuación) según el estado de visibilidad.

Para obtener más información sobre este método, consulte la documentación de la API de Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) .

### <a name="time-zone-feature"></a>Característica de zona horaria

El nuevo **MyWatchFaceService.CS** también incluye funcionalidad para actualizar la hora actual cada vez que cambia la zona horaria (por ejemplo, mientras viaja a través de zonas horarias). Casi al final de **MyWatchFaceService.CS**, se define un cambio de zona horaria `BroadcastReceiver` que controla los objetos de intención modificados por zona horaria:

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

El `RegisterTimezoneReceiver` `UnregisterTimezoneReceiver` método llama a los métodos y `OnVisibilityChanged` .
`UnregisterTimezoneReceiver` se llama a cuando el estado de visibilidad de la esfera de inspección se cambia a oculto. Cuando la superficie de inspección vuelve a estar visible, `RegisterTimezoneReceiver` se llama a (vea el `OnVisibilityChanged` método).

El `RegisterTimezoneReceiver` método Engine declara un controlador para este evento de receptor de zona horaria `Receive` ; este controlador actualiza el `time` objeto con la nueva hora cada vez que se cruza una zona horaria:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Se crea un filtro de intención y se registra para el receptor de zona horaria:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

El `UnregisterTimezoneReceiver` método anula el registro del receptor de zona horaria:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Ejecutar la superficie de inspección mejorada

Compile e implemente de nuevo la aplicación en el dispositivo de desgaste. Seleccione la esfera de reloj del selector de caras de inspección como antes. La vista previa en el selector de inspección se muestra a la izquierda y la nueva esfera de inspección se muestra a la derecha:

[![Superficie de inspección analógica](creating-a-watchface-images/13-analog-watchface.png "Aspecto analógico mejorado en el selector y en el dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

En esta captura de pantalla, la segunda mano se mueve una vez por segundo. Al ejecutar este código en un dispositivo de desgaste, la segunda mano desaparece cuando el reloj entra en el modo ambiente.

## <a name="summary"></a>Resumen

En este tutorial, se implementó y probó un desgaste de Android personalizado 1,0 watchface. `CanvasWatchFaceService` `CanvasWatchFaceService.Engine` Se introdujeron las clases y, y los métodos esenciales de la clase Engine se implementaron para crear una superficie simple de inspección digital. Esta implementación se actualizó con más funcionalidad para crear una superficie de inspección analógica y se implementaron métodos adicionales para controlar los cambios en la visibilidad, el modo ambiente y las diferencias en las propiedades del dispositivo. Por último, se implementó un receptor de difusión de zona horaria para que el reloj actualice automáticamente la hora a la que se cruza una zona horaria.

## <a name="related-links"></a>Vínculos relacionados

- [Crear caras de inspección](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Ejemplo de WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)