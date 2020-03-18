---
title: Características de KitKat
description: Android 4.4 (KitKat) incluye gran cantidad de características para usuarios y desarrolladores. En esta guía se destacan varias de estas características y se ofrecen ejemplos de código y detalles de implementación para ayudarle a sacar el máximo partido de KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027241"
---
# <a name="kitkat-features"></a>Características de KitKat

_Android 4.4 (KitKat) incluye gran cantidad de características para usuarios y desarrolladores. En esta guía se destacan varias de estas características y se ofrecen ejemplos de código y detalles de implementación para ayudarle a sacar el máximo partido de KitKat._

## <a name="overview"></a>Información general

Android 4.4 (nivel de API 19), también conocido como "KitKat", se publicó a finales de 2013. KitKat ofrece diversas nuevas características y mejoras, entre las que se incluyen:

- [Experiencia del usuario](#user_experience): animaciones sencillas con marco de transición, barras de navegación y estado translúcidas y modo envolvente de pantalla completa que contribuyen a crear una mejor experiencia para el usuario.

- [Contenido de usuario](#user_content): administración de archivos de usuario simplificada con el marco de acceso de almacenamiento; imprimir imágenes, sitios web y otro contenido es más fácil con las API de impresión mejoradas.

- [Hardware](#hardware): convierta cualquier aplicación en una tarjeta NFC con emulación de tarjeta basada en host de NFC; ejecute sensores de bajo consumo con el objeto `SensorManager`.

- [Herramientas de desarrollo](#developer_tools): aplicaciones de presentación en pantalla en acción con el cliente de Android Debug Bridge, disponible como parte de Android SDK.

En esta guía se ofrecen instrucciones para migrar una aplicación de Xamarin.Android existente a KitKat, así como una introducción de alto nivel de KitKat para desarrolladores de Xamarin.Android.

## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin.Android con KitKat es preciso instalar *Xamarin.Android 4.11.0* o posterior y Android 4.4 (nivel de API 19) a través del Administrador de SDK, tal y como se muestra en la siguiente captura de pantalla:

[![Selección de Android 4.4 en el Administrador de SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migración de la aplicación a KitKat

En esta sección se ofrecen algunos elementos de primera respuesta para ayudar a realizar la transición de aplicaciones existentes a Android 4.4.

### <a name="check-system-version"></a>Comprobación de la versión del sistema

Si una aplicación debe ser compatible con versiones anteriores de Android, asegúrese de encapsular cualquier código específico de KitKat en una comprobación de la versión del sistema, tal y como se muestra en el ejemplo de código siguiente:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Procesamiento por lotes de alarmas

Android usa servicios de alarma para reactivar una aplicación en segundo plano a una hora especificada. KitKat lleva esto más lejos con el procesamiento por lotes de las alarmas para conservar la energía. Esto significa que, en lugar de activar cada aplicación en un momento concreto, KitKat prefiere agrupar varias aplicaciones que están registradas para reactivarse durante el mismo intervalo de tiempo y reactivarlas al mismo tiempo.
Para indicar a Android que reactive una aplicación durante un intervalo de tiempo especificado, llame a `SetWindow` en el objeto [`AlarmManager`](xref:Android.App.AlarmManager), que pasa el tiempo mínimo y máximo (en milisegundos) que puede transcurrir antes de que se reactive la aplicación y la operación que se va a realizar en la reactivación.
El código siguiente ofrece un ejemplo de aplicación que tiene que reactivarse entre media hora y una hora después del momento en que se establece la ventana:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar con la reactivación de una aplicación en un momento concreto, use `SetExact`, que pasa la hora exacta en que se debe reactivar la aplicación y la operación que se va a realizar:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ya no permite establecer una alarma de repetición exacta. Las aplicaciones que utilizan [`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
y requieren alarmas exactas para funcionar ahora tendrán que desencadenar cada alarma manualmente.

### <a name="external-storage"></a>Almacenamiento externo

El almacenamiento externo ahora se divide en dos tipos: almacenamiento único para la aplicación y datos compartidos por varias aplicaciones. Leer y escribir en la ubicación específica de la aplicación en el almacenamiento externo no requiere ningún permiso especial. La interacción con los datos en el almacenamiento compartido requiere ahora el permiso `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE`. Los dos tipos se pueden clasificar como tales:

- Si se obtiene una ruta de acceso de archivo o directorio mediante una llamada a un método en `Context`, por ejemplo, [`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  o [`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs),
  - la aplicación no requiere permisos adicionales.

- Si se obtiene una ruta de acceso de archivo o directorio mediante una propiedad o una llamada a un método en `Environment`, por ejemplo, [`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  o [`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*),
  la aplicación requiere el permiso `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE`.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` implica el permiso `READ_EXTERNAL_STORAGE`, por lo que solo debería tener que establecer un permiso.

### <a name="sms-consolidation"></a>Consolidación de SMS

KitKat simplifica la mensajería para el usuario agregando todo el contenido de SMS en una aplicación predeterminada seleccionada por el usuario. El desarrollador es responsable de que la aplicación se seleccione como aplicación de mensajería predeterminada y se comporte de forma adecuada en el código y en la práctica si la aplicación no está seleccionada. Para obtener más información sobre cómo realizar la transición de la aplicación SMS a KitKat, consulte la guía de [preparación de las aplicaciones SMS para KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) de Google.

### <a name="webview-apps"></a>Aplicaciones WebView

[WebView](xref:Android.Webkit.WebView) se renovó radicalmente en KitKat. El cambio más importante consiste en mayor seguridad para cargar el contenido en un objeto `WebView`. Aunque la mayoría de las aplicaciones que tienen como destino versiones anteriores de API deberían funcionar según lo previsto, se recomienda muy especialmente probar las aplicaciones que usan la clase `WebView`. Para obtener más información sobre las API de WebView afectadas, consulte la documentación [Migración a WebView en Android 4.4](https://developer.android.com/guide/webapps/migrating.html) de Android.

<a name="user_experience" />

## <a name="user-experience"></a>Experiencia del usuario

KitKat incluye varias API nuevas para mejorar la experiencia del usuario, incluido el nuevo marco de transición para controlar las animaciones de propiedades y una opción de interfaz de usuario translúcida para los temas. Estos cambios se describen a continuación.

### <a name="transition-framework"></a>Marco de transición

El marco de transición facilita la implementación de animaciones. KitKat le permite realizar una sencilla animación de propiedades con una sola línea de código o personalizar las transiciones con *escenas*.

#### <a name="simple-property-animation"></a>Animación de propiedades simples

La nueva biblioteca de transiciones de Android simplifica el código subyacente a las animaciones de propiedad. El marco permite realizar animaciones simples con apenas código. Por ejemplo, el código siguiente usa [`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
para animar la visualización y ocultación de un objeto `TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

En el ejemplo anterior se usa el marco de transición para crear una transición predeterminada y automática entre los valores de propiedad cambiantes. Dado que la animación se controla mediante una sola línea de código, puede hacer que sea compatible fácilmente con versiones anteriores de Android encapsulando la llamada a `BeginDelayedTransition` en una comprobación de la versión del sistema. Para obtener más información, consulte la sección [Migración de la aplicación a KitKat](#Migrating_Your_App_to_KitKat).

En la captura de pantalla siguiente se muestra la aplicación antes de la animación:

[![Captura de pantalla de la aplicación antes de que empiece la animación](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

En la captura de pantalla siguiente se muestra la aplicación después de la animación:

[![Captura de pantalla de la aplicación después de completarse la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Puede obtener más control sobre la transición con escenas, que se describen en la sección siguiente.

#### <a name="android-scenes"></a>Escenas de Android

Las [escenas](xref:Android.Transitions.Scene) se presentaron como parte del marco de transición para ofrecer al desarrollador más control sobre las animaciones. Las escenas crean un área dinámica en la interfaz de usuario: se especifica un contenedor y varias versiones, o "escenas", para el contenido XML del contenedor y Android realiza el resto del trabajo para animar las transiciones entre escenas. Las escenas de Android permiten crear animaciones complejas con un apenas trabajo en el lado de desarrollo.

El elemento de la interfaz de usuario estática que aloja el contenido dinámico se denomina *contenedor* o *base de la escena*. En el ejemplo siguiente se usa Android Designer para crear un objeto `RelativeLayout` denominado `container`:

[![Uso de Android Designer para crear un contenedor RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

El diseño de ejemplo también define un botón denominado `sceneButton` debajo del objeto `container`. Este botón desencadenará la transición.

El contenido dinámico dentro del contenedor requiere dos nuevos diseños de Android. Estos diseños solo especifican el incluido código *dentro* del contenedor.
En el código de ejemplo siguiente se define un diseño llamado *Scene1* que contiene dos campos de texto que dicen "Kit" y "Kat" respectivamente, y un segundo diseño denominado *Scene2* que contiene los mismos campos de texto invertidos. El XML es el siguiente:

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

En el ejemplo anterior se usa `merge` para que el código de vista sea más corto y simplificar la jerarquía de vistas. [Aquí](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html) puede leer más información sobre diseños `merge`.

Una escena se crea llamando a [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*), que pasa el objeto de contenedor, el identificador de recurso del archivo de diseño de la escena y el objeto `Context` actual, tal y como se muestra en el ejemplo de código siguiente:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Al hacer clic en el botón se voltea entre las dos escenas, que Android anima con los valores de transición predeterminados:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

En la captura de pantalla siguiente se ilustra la escena antes de la animación:

[![Captura de pantalla de la aplicación antes de que se inicie la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

En la captura de pantalla siguiente se ilustra la escena después de la animación:

[![Captura de pantalla de la aplicación una vez completada la animación](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Hay un [error conocido](https://code.google.com/p/android/issues/detail?id=62450) en la biblioteca de transiciones de Android que hace que las escenas creadas con `GetSceneForLayout` se interrumpan la segunda vez que un usuario navega por una actividad. [Aquí](http://www.doubleencore.com/2013/11/new-transitions-framework/) se describe una solución alternativa de Java.

##### <a name="custom-transitions-in-scenes"></a>Transiciones personalizadas en escenas

Se puede definir una transición personalizada en un archivo de recursos XML en el directorio `transition` en `Resources`, tal y como se muestra en la siguiente captura de pantalla:

[![Ubicación del archivo transition.xml en el directorio Resources/transition](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

En el ejemplo de código siguiente se define una transición que se anima durante cinco segundos y usa el [interpolador de sobremodulación](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transición se crea en la actividad mediante el objeto [TransitionInflater](xref:Android.Transitions.TransitionInflater), tal y como se muestra en el código siguiente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nueva transición se agrega entonces a la llamada a `Go` que comienza la animación:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaz de usuario translúcida

KitKat le ofrece más control sobre la aplicación con barras opcionales de navegación y estado translúcidas. Puede cambiar la translucidez de los elementos de la interfaz de usuario del sistema en el mismo archivo XML que se usa para definir el tema de Android. KitKat presenta las siguientes propiedades:

- `windowTranslucentStatus`: cuando se establece en true, hace que la barra de estado superior sea translúcida.

- `windowTranslucentNavigation`: cuando se establece en true, hace que la barra de navegación inferior sea translúcida.

- `fitsSystemWindows`: si se establece la barra superior o inferior como translúcida, el contenido cambia el contenido en los elementos transparentes de la interfaz de usuario de forma predeterminada. Establecer esta propiedad en `true` es una forma sencilla de evitar que el contenido se superponga con los elementos de la interfaz de usuario del sistema translúcida.

En el código siguiente se define un tema con barras de navegación y estado translúcidas:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

En la captura de pantalla siguiente se nuestra el tema anterior con barras de navegación y estado translúcidas:

[![Captura de pantalla de ejemplo de la aplicación con barras de navegación y estado translúcidas](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenido del usuario

### <a name="storage-access-framework"></a>Marco de acceso al almacenamiento

El marco de acceso de almacenamiento (SAF) es una nueva forma de que los usuarios interactúen con contenido almacenado tales como imágenes, vídeos y documentos. En lugar de presentar a los usuarios un cuadro de diálogo para elegir una aplicación para controlar el contenido, KitKat abre una nueva interfaz de usuario que permite a los usuarios acceder a sus datos en una ubicación agregada. Una vez que se haya elegido el contenido, el usuario volverá a la aplicación que solicitó el contenido y la experiencia de la aplicación continuará como es habitual.

Este cambio requiere dos acciones en el lado del desarrollador: en primer lugar, las aplicaciones que requieren contenido de los proveedores deben actualizarse a una nueva forma de solicitar contenido. En segundo lugar, es necesario modificar las aplicaciones que escriben datos en un objeto `ContentProvider` para usar el nuevo marco. Ambos escenarios dependen de la nueva [`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

En KitKat, las interacciones con `ContentProviders` se abstraen con la clase `DocumentsProvider`. Esto significa que SAF no se preocupa de dónde están los datos físicamente, siempre y cuando sean accesibles a través de la API de `DocumentsProvider`. Los proveedores locales, los servicios en la nube y los dispositivos de almacenamiento externo usan la misma interfaz, y se tratan de la misma manera, lo que proporciona al usuario y al desarrollador un solo lugar para interactuar con el contenido del usuario.

En esta sección se explica cómo cargar y guardar contenido con el marco de acceso de almacenamiento.

#### <a name="request-content-from-a-provider"></a>Solicitud de contenido de un proveedor

Podemos indicar a KitKat que queremos elegir contenido mediante la interfaz de usuario de SAF con la intención `ActionOpenDocument`, lo que significa que queremos conectarnos a todos los proveedores de contenido disponibles en el dispositivo. Puede agregar algún filtro a esta intención especificando `CategoryOpenable`, lo que significa que solo se devolverá el contenido que se puede abrir (es decir, contenido accesible y utilizable). KitKat también permite filtrar contenido con el objeto `MimeType`. Por ejemplo, el código siguiente filtra los resultados de la imagen mediante la especificación de la imagen `MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La llamada a `StartActivityForResult` inicia la interfaz de usuario de SAF, que el usuario puede entonces examinar para elegir una imagen:

[![Captura de pantalla de ejemplo de una aplicación que usa el marco de acceso de almacenamiento para buscar una imagen](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Una vez que el usuario ha elegido una imagen, `OnActivityResult` devuelve el objeto `Android.Net.Uri` del archivo elegido. En el ejemplo de código siguiente se muestra la selección de imagen del usuario:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Escritura de contenido en un proveedor

Además de cargar contenido desde la interfaz de usuario de SAF, KitKat también le permite guardar contenido en cualquier `ContentProvider` que implemente la API de `DocumentProvider`. Al guardar el contenido, se usa un `Intent` con `ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

En el ejemplo de código anterior se carga la interfaz de usuario de SAF, lo que permite al usuario cambiar el nombre de archivo y seleccionar un directorio para hospedar el nuevo archivo:

[![Captura de pantalla del usuario que cambia el nombre de archivo a NewDoc en el directorio de descargas](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Cuando el usuario presiona **Guardar**, `OnActivityResult` pasa el objeto `Android.Net.Uri` del archivo recién creado, al que se puede acceder con `data.Data`. El URI se puede usar para transmitir datos al nuevo archivo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Tenga en cuenta que [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
devuelve un `System.IO.Stream`, por lo que se puede escribir en .NET todo el proceso de streaming.

Para obtener más información sobre la carga, creación y edición de contenido con el marco de acceso de almacenamiento, consulte la [documentación de Android sobre el marco de acceso de almacenamiento](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impresión

El contenido de impresión se simplifica en KitKat con la introducción de los [servicios de impresión](xref:Android.PrintServices) y `PrintManager`. KitKat es también la primera versión de la API que aprovecha por completo las [API del servicio Cloud Print de Google](https://developers.google.com/cloud-print/) con la [aplicación Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La mayoría de los dispositivos que se suministran con KitKat descargan automáticamente la aplicación Google Cloud Print y [HP Print Service Plugin](https://play.google.com/store/apps/details?id=com.hp.android.printservice) la primera vez que se conectan a la WiFi. Un usuario puede comprobar la configuración de impresión de su dispositivo navegando a **Configuración > Sistema > Impresión**:

[![Captura de pantalla de ejemplo de la pantalla de configuración de impresión](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Aunque las API de impresión están configuradas para que funcionen con Google Cloud Print de forma predeterminada, Android sigue permitiendo a los desarrolladores preparar el contenido de impresión con las nuevas API y enviarlo a otras aplicaciones para controlar la impresión.

#### <a name="printing-html-content"></a>Impresión de contenido HTML

KitKat crea automáticamente un objeto [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) para una vista web con `WebView.CreatePrintDocumentAdapter`. La impresión de contenido web es un esfuerzo coordinado entre un objeto [`WebViewClient`](xref:Android.Webkit.WebViewClient) que espera a que se cargue el contenido HTML y permite que la actividad sepa que la opción de impresión está disponible en el menú opciones y la actividad, que espera a que el usuario seleccione la opción de impresión y llama a `Print` en el `PrintManager`. En esta sección se describe la configuración básica necesaria para imprimir contenido HTML en pantalla.

Tenga en cuenta que la carga e impresión de contenido web requieren el permiso de Internet:

[![Configuración del permiso de Internet en las opciones de la aplicación](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Elemento del menú Imprimir

La opción Imprimir normalmente aparecerá en el [menú de opciones](https://developer.android.com/guide/topics/ui/menus.html#options-menu) de la actividad.
El menú opciones permite que los usuarios realicen acciones en una actividad. Se encuentra en la esquina superior derecha de la pantalla y tiene el siguiente aspecto:

[![Captura de pantalla de ejemplo del elemento de menú Imprimir mostrado en la esquina superior derecha de la pantalla](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

Se pueden definir elementos de menú adicionales en el directorio de *menús* en *Recursos*. El código siguiente define un elemento de menú de ejemplo denominado [Imprimir](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

La interacción con el menú de opciones de la actividad se produce a través de los métodos `OnCreateOptionsMenu` y `OnOptionsItemSelected`.
`OnCreateOptionsMenu` es el lugar donde se agregan nuevos elementos de menú, como la opción Imprimir, en el directorio de recursos de *menú*.
`OnOptionsItemSelected` escucha al usuario que selecciona la opción Imprimir en el menú y comienza la impresión:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

El código anterior también define una variable denominada `dataLoaded` para realizar un seguimiento del estado del contenido HTML. El objeto `WebViewClient` establecerá esta variable en true cuando se haya cargado todo el contenido, por lo que la actividad sabrá agregar el elemento de menú Imprimir al menú de opciones.

##### <a name="webviewclient"></a>WebViewClient

El trabajo del objeto `WebViewClient` es asegurarse de que los datos del objeto `WebView` se cargan por completo antes de que aparezca la opción Imprimir en el menú, que lo hace con el método `OnPageFinished`. `OnPageFinished` realiza escuchas para que el contenido web termine de cargarse e indica a la actividad que vuelva a crear su menú de opciones con `InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` también establece el valor `dataLoaded` en `true`, por lo que `OnCreateOptionsMenu` puede volver a crear el menú con la opción de impresión en contexto.

##### <a name="printmanager"></a>PrintManager

En el siguiente ejemplo de código se imprime el contenido de un objeto `WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` toma como argumentos: un nombre para el trabajo de impresión ("MyWebPage" en este ejemplo), un objeto [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
que genera el documento de impresión a partir del contenido y [`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null` en el ejemplo anterior). Puede especificar `PrintAttributes` para ayudar a diseñar el contenido en la página impresa, aunque los atributos predeterminados deben controlar la mayoría de los escenarios.

La llamada a `Print` carga la interfaz de usuario de impresión, que enumera las opciones del trabajo de impresión. La interfaz de usuario ofrece a los usuarios la opción de imprimir o guardar el contenido HTML en un archivo PDF, tal y como se muestra en las capturas de pantallas siguientes:

[![Captura de pantalla de PrintHtmlActivity que muestra el menú Imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de pantalla de PrintHtmlActivity que muestra el menú Guardar como PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat agrega varias API para acomodar las nuevas características del dispositivo. Las más importantes son la emulación de tarjeta basada en host y el nuevo objeto `SensorManager`.

### <a name="host-based-card-emulation-in-nfc"></a>Emulación de tarjeta basada en host de NFC

La emulación de tarjeta basada en host (HCE) permite que las aplicaciones se comporten como tarjetas NFC o lectores de tarjetas NFC sin depender del elemento seguro propio del operador. Antes de configurar HCE, asegúrese de que HCE esté disponible en el dispositivo con `PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requiere que la característica HCE y el permiso `Nfc` se registren con el `AndroidManifest.xml`de la aplicación:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Establecimiento del permiso NFC en las opciones de la aplicación](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabajar, HCE tiene que poder ejecutarse en segundo plano y debe iniciarse cuando el usuario realiza una transacción NFC, aunque la aplicación que usa HCE no se está ejecutando. Podemos lograr esto escribiendo el código de HCE como un objeto `Service`. Un servicio HCE implementa la interfaz `HostApduService`, que implementa los métodos siguientes:

- *ProcessCommandApdu*: una unidad de datos de protocolo de aplicación (APDU) es lo que se envía entre el lector NFC y el servicio HCE. Este método consume una ADPU del lector y devuelve una unidad de datos en respuesta.

- *OnDeactivated*: el objeto `HostAdpuService` se desactiva cuando el servicio HCE deja de comunicarse con el lector NFC.

Un servicio HCE también debe registrarse con el manifiesto de la aplicación y decorarse con los permisos, el filtro de intención y los metadatos adecuados. El código siguiente es un ejemplo de `HostApduService` registrado con el manifiesto de Android mediante el atributo `Service` (para más información sobre los atributos, consulte la guía sobre [trabajo con el manifiesto de Android](~/android/platform/android-manifest.md) de Xamarin):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

El servicio anterior ofrece una manera de que el lector NFC interactúe con la aplicación, pero el lector NFC todavía no tiene ninguna manera de saber si este servicio está emulando la tarjeta NFC que debe examinar. Para ayudar al lector NFC a identificar el servicio, podemos asignar al servicio un *identificador de aplicación (AID)* único. Especificamos un AID, junto con otros metadatos sobre el servicio HCE, en un archivo de recursos XML registrado con el atributo `MetaData` (consulte el ejemplo de código anterior). Este archivo de recursos especifica uno o varios filtros de AID: cadenas de identificador único en formato hexadecimal que corresponden a los AID de uno o más dispositivos de lectura NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Además de los filtros de ayuda, el archivo de recursos XML también proporciona una descripción orientada al usuario del servicio HCE, especifica un grupo de AID (aplicación de pago frente a "otros") y, en el caso de una aplicación de pago, un banner de 260x96 dp que se va a mostrar al usuario.

El programa de instalación descrito anteriormente proporciona los bloques de creación básicos para una aplicación que emula una tarjeta NFC. La propia NFC requiere varios pasos más y más pruebas para su configuración. Para obtener más información sobre la emulación de tarjeta basada en host, consulte el [portal de documentación de Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para obtener más información sobre el uso de NFC con Xamarin, consulte los [ejemplos de NFC de Xamarin](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat proporciona acceso a los sensores del dispositivo a través de un objeto [`SensorManager`](xref:Android.Hardware.SensorManager).
El objeto `SensorManager` permite que el sistema operativo programe la entrega de información de sensor a una aplicación por lotes, lo que preserva la duración de la batería.

KitKat también incluye dos nuevos tipos de sensor para realizar el seguimiento de los pasos del usuario. Se basan en el acelerómetro e incluyen:

- *StepDetector*: la aplicación se notifica/reactiva cuando el usuario realiza un paso y el detector proporciona un valor de tiempo para el momento en que se produjo el paso.

- *StepCounter*: realiza un seguimiento del número de pasos que ha seguido el usuario desde que se registró el sensor *hasta el siguiente reinicio del dispositivo*.

En la captura de pantalla siguiente se muestra el contador de pasos en acción:

[![Captura de pantalla de la aplicación SensorsActivity que muestra un contador de pasos](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Se puede crear un objeto `SensorManager` llamando a `GetSystemService(SensorService)` y convirtiendo el resultado en un objeto `SensorManager`. Para usar el contador de pasos, llame a `GetDefaultSensor` en el objeto `SensorManager`. Puede registrar el sensor y escuchar los cambios en el contador de pasos con la ayuda la interfaz [`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener),
tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

Se llama a `OnSensorChanged` si el contador de pasos se actualiza mientras la aplicación está en primer plano. Si la aplicación entra al segundo plano o el dispositivo está en suspensión, no se llamará a `OnSensorChanged`; sin embargo, los pasos se seguirán contando hasta que se llame a `UnregisterListener`.

Tenga en cuenta que *el valor de número de pasos es acumulativo en todas las aplicaciones que registran el sensor*. Esto significa que incluso si desinstala y vuelve a instalar la aplicación e inicializa la variable `count` en 0 al iniciarse la aplicación, el valor indicado por el sensor seguirá siendo el número total de pasos que se realizaron mientras se registraba el sensor, ya sea por su aplicación o por otra. Puede evitar que la aplicación se agregue al contador de pasos llamando a `UnregisterListener` en el objeto `SensorManager`, tal y como se muestra en el código siguiente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Al reiniciar el dispositivo, se restablece el número de pasos en 0. La aplicación necesitará código adicional para asegurarse de que notifica un recuento exacto a la aplicación, independientemente de que otras aplicaciones usen el sensor o el estado del dispositivo.

> [!NOTE]
> Mientras que la API para la detección y el recuento de pasos se incluye con KitKat, no todos los teléfonos están equipados con el sensor. Puede comprobar si el sensor está disponible ejecutando `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);` o comprobar que el valor devuelto de `GetDefaultSensor` no es `null`.

<a name="developer_tools" />

## <a name="developer-tools"></a>Herramientas de desarrollo

### <a name="screen-recording"></a>Grabación de pantalla

KitKat incluye nuevas funcionalidades de grabación de pantalla para que los desarrolladores puedan grabar aplicaciones en acción. La grabación de pantalla está disponible a través del cliente de [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html), que puede descargarse como parte de Android SDK.

Para grabar la pantalla, conecte el dispositivo; luego, busque la instalación de Android SDK, navegue hasta el directorio **platform-tools** y ejecute el cliente de **ADB**:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

El comando anterior grabará un vídeo de tres minutos a la resolución predeterminada de 4 Mbps. Para editar la duración, agregue la marca *--Time-Limit*.
Para cambiar la resolución, agregue la marca *--bit-rate*. El siguiente comando grabará un vídeo de un minuto de duración a 8 Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Puede encontrar el vídeo en el dispositivo: aparecerá en la Galería cuando se complete la grabación.

## <a name="other-kitkat-additions"></a>Otras incorporaciones de KitKat

Además de los cambios descritos anteriormente, KitKat le permite:

- *Usar la pantalla completa*: KitKat presenta un nuevo [modo envolvente](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para explorar contenido, jugar y ejecutar otras aplicaciones que podrían beneficiarse de una experiencia de pantalla completa.

- *Personalizar las notificaciones*: obtenga detalles adicionales acerca de las notificaciones del sistema con el objeto [`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . Esto le permite presentar la información de manera diferente dentro de la aplicación.

- *Reflejar recursos Drawable*: los recursos Drawable tienen un nuevo atributo [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  que indica al sistema que cree una versión reflejada para las imágenes que requieran el volteo de los diseños de izquierda a derecha.

- *Pausar animaciones*: pause y reanude animaciones creadas con la clase [`Animator`](xref:Android.Animation.Animator).
  clase.

- *Leer texto que cambia dinámicamente*: denota partes de la interfaz de usuario que se actualizan dinámicamente con nuevo texto como "regiones activas" con el nuevo atributo [`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  para que el nuevo texto se lea automáticamente en el modo de accesibilidad.

- *Mejorar la experiencia de audio*: haga pistas más fuertes con el objeto [`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  y busque los valores Peak y RMS de una secuencia de audio con la clase [`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  y obtenga información de una [marca de tiempo de audio](xref:Android.Media.AudioTimestamp) para ayudar con la sincronización de audio y vídeo.

- *Sincronizar ContentResolver en el intervalo personalizado*: KitKat agrega cierta variabilidad al momento en que se realiza una solicitud de sincronización. Sincronice un objeto `ContentResolver` en un momento o intervalo personalizado llamando a `ContentResolver.RequestSync` y pasando un objeto `SyncRequest`.

- *Distinguir entre los controladores* en KitKat, se asignan identificadores enteros únicos a los controladores a los que se puede acceder a través de la propiedad `ControllerNumber` del dispositivo. Así resulta más fácil distinguir a los jugadores de un juego.

- *Control remoto*: con pocos cambios tanto en el hardware como en el software, KitKat le permite convertir un dispositivo equipado con un transmisor de infrarrojos en un control remoto con el objeto `ConsumerIrService` e interactuar con los dispositivos periféricos con las nuevas API de [`RemoteController`](xref:Android.Media.RemoteController)
  .

Para más información sobre los anteriores cambios de la API, consulte la introducción a las [API de Google Android 4.4](https://developer.android.com/about/versions/android-4.4.html).

## <a name="summary"></a>Resumen

En este artículo se presentan algunas de las nuevas API disponibles en Android 4.4 (nivel de API 19) y se describen procedimientos recomendados para la transición de una aplicación a KitKat. Se describen los cambios en las API que afectan a la experiencia del usuario, que incluyen el *marco de transición* y las nuevas opciones para *temas*. A continuación, presenta el *marco de acceso de almacenamiento* y la clase `DocumentsProvider`, así como las nuevas *API de impresión*. Explora la *emulación de tarjeta basada en host de NFC* y cómo trabajar con *sensores de bajo consumo*, incluidos dos nuevos sensores para realizar un seguimiento de los pasos del usuario. Por último, se muestra la captura de demostraciones de aplicaciones en tiempo real con *grabación de pantalla* y se ofrece una lista detallada de los cambios y las incorporaciones de la API de KitKat.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [Interfaces API de Android 4.4](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
