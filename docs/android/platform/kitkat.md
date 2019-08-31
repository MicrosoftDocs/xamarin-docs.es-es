---
title: Características de KitKat
description: Android 4,4 (KitKat) se carga con un Cornucopia de características para usuarios y desarrolladores. En esta guía se resaltan varias de estas características y se proporcionan ejemplos de código y detalles de implementación para ayudarle a sacar el máximo partido de KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: b61924c78f85c8a1a835cef87f357ec262926935
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197728"
---
# <a name="kitkat-features"></a>Características de KitKat

_Android 4,4 (KitKat) se carga con un Cornucopia de características para usuarios y desarrolladores. En esta guía se resaltan varias de estas características y se proporcionan ejemplos de código y detalles de implementación para ayudarle a sacar el máximo partido de KitKat._

## <a name="overview"></a>Información general

Android 4,4 (nivel de API 19), también conocido como "KitKat", se publicó a finales de 2013. KitKat ofrece una variedad de nuevas características y mejoras, entre las que se incluyen:

- [Experiencia del usuario](#user_experience) &ndash; Animaciones sencillas con el marco de transición, el estado translúcido y las barras de navegación y el modo inmersivo de pantalla completa ayudan a crear una mejor experiencia para el usuario.

- [Contenido del usuario](#user_content) &ndash; Administración de archivos de usuario simplificada con el marco de acceso de almacenamiento; la impresión de imágenes, sitios web y otro contenido es más fácil con las API de impresión mejoradas.

- [Hardware](#hardware) de Convierta cualquier aplicación en una tarjeta NFC con emulación de tarjeta basada en host NFC; ejecute sensores de baja energía con el `SensorManager`. &ndash;

- [Herramientas de desarrollo](#developer_tools) &ndash; Las aplicaciones de screencast en acción con el cliente Android Debug Bridge, disponibles como parte de la Android SDK.


En esta guía se proporcionan instrucciones para migrar una aplicación de Xamarin. Android existente a KitKat, así como una introducción de alto nivel de KitKat para desarrolladores de Xamarin. Android.

## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin. Android con KitKat, necesita *Xamarin. Android 4.11.0* o superior y Android 4,4 (nivel de API 19) instalado a través del administrador de Android SDK, como se muestra en la siguiente captura de pantalla:

[![Seleccionar Android 4,4 en el administrador de Android SDK](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migración de la aplicación a KitKat

En esta sección se proporcionan algunos elementos de primera respuesta para ayudar a realizar la transición de aplicaciones existentes a Android 4,4.

### <a name="check-system-version"></a>Comprobar la versión del sistema

Si una aplicación debe ser compatible con versiones anteriores de Android, asegúrese de ajustar cualquier código específico de KitKat en una comprobación de la versión del sistema, como se muestra en el ejemplo de código siguiente:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Procesamiento por lotes de alarmas

Android usa servicios de alarma para reactivar una aplicación en segundo plano a una hora especificada. KitKat realiza un paso más en el procesamiento por lotes de las alarmas para conservar la energía. Esto significa que, en lugar de activar cada aplicación en un momento concreto, KitKat prefiere agrupar varias aplicaciones que están registradas para reactivarse durante el mismo intervalo de tiempo y reactivarlas al mismo tiempo.
Para indicar a Android que reactive una aplicación durante un intervalo de tiempo `SetWindow` especificado, [`AlarmManager`](xref:Android.App.AlarmManager)llame a en el, pasando el tiempo mínimo y máximo, en milisegundos, que puede transcurrir antes de que se reactivarán la aplicación y la operación que se realiza en la reactivación.
El código siguiente proporciona un ejemplo de una aplicación que debe ser reactivarán entre una media hora y una hora desde el momento en que se establece la ventana:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar con la activación de una aplicación en un momento `SetExact`concreto, use, pasando el tiempo exacto en que se debe reactivarán la aplicación y la operación que se va a realizar:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat ya no permite establecer una alarma de repetición exacta. Aplicaciones que usan[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
y requerir que las alarmas exactas funcionen ahora deberán desencadenar cada alarma manualmente.

### <a name="external-storage"></a>Almacenamiento externo

El almacenamiento externo ahora se divide en dos tipos: almacenamiento único para la aplicación y datos compartidos por varias aplicaciones. Leer y escribir en la ubicación específica de la aplicación en el almacenamiento externo no requiere ningún permiso especial. La interacción con los datos en el almacenamiento compartido requiere `READ_EXTERNAL_STORAGE` ahora `WRITE_EXTERNAL_STORAGE` el permiso o. Los dos tipos se pueden clasificar como tales:

- Si va a obtener una ruta de acceso de archivo o directorio mediante una `Context` llamada a un método en, por ejemplo,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
   de[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
   - la aplicación no requiere permisos adicionales.

- Si va a obtener una ruta de acceso de archivo o directorio al obtener acceso a una propiedad o `Environment` llamar a un método en, como[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
   de[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
   , la aplicación requiere el `READ_EXTERNAL_STORAGE` permiso `WRITE_EXTERNAL_STORAGE` o.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implica el `READ_EXTERNAL_STORAGE` permiso, por lo que solo debería tener que establecer un permiso.

### <a name="sms-consolidation"></a>Consolidación de SMS

KitKat simplifica la mensajería para el usuario agregando todo el contenido de SMS en una aplicación predeterminada seleccionada por el usuario. El desarrollador es responsable de hacer que la aplicación se seleccione como la aplicación de mensajería predeterminada y se comporte de forma adecuada en el código y en la vida si la aplicación no está seleccionada. Para obtener más información sobre cómo realizar la transición de la aplicación SMS a KitKat, consulte la guía de preparación de las [aplicaciones SMS para KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) de Google.

### <a name="webview-apps"></a>Aplicaciones WebView

[WebView](xref:Android.Webkit.WebView) tiene un makeover en KitKat. El cambio más importante se agrega seguridad para cargar el contenido `WebView`en un. Aunque la mayoría de las aplicaciones que tienen como destino versiones anteriores de API deberían funcionar según lo `WebView` esperado, se recomienda encarecidamente probar las aplicaciones que usan la clase. Para obtener más información sobre las API de WebView afectadas, consulte la documentación sobre la migración de Android [a WebView en android 4,4](https://developer.android.com/guide/webapps/migrating.html) .

<a name="user_experience" />

## <a name="user-experience"></a>Experiencia del usuario

KitKat incluye varias API nuevas para mejorar la experiencia del usuario, incluido el nuevo marco de transición para controlar las animaciones de propiedades y una opción de interfaz de usuario translúcida para los mismos. Estos cambios se describen a continuación.

### <a name="transition-framework"></a>Marco de transición

El marco de transición facilita la implementación de animaciones. KitKat le permite realizar una sencilla animación de propiedades con una sola línea de código o personalizar las transiciones mediante *escenas*.

#### <a name="simple-property-animation"></a>Animación de propiedades simples

La nueva biblioteca de transiciones de Android simplifica el código subyacente a las animaciones de propiedad. El marco de trabajo permite realizar animaciones simples con código mínimo. Por ejemplo, en el ejemplo de código siguiente se usa[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
para animar la visualización y `TextView`ocultación de:

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

En el ejemplo anterior se usa el marco de transición para crear una transición automática y predeterminada entre los valores de propiedad variables. Dado que la animación se controla mediante una sola línea de código, puede hacerlo fácilmente con versiones anteriores de Android encapsulando la llamada en `BeginDelayedTransition` una comprobación de la versión del sistema. Para más información, consulte la sección [migración de la aplicación a KitKat](#Migrating_Your_App_to_KitKat) .

En la captura de pantalla siguiente se muestra la aplicación antes de la animación:

[![Captura de pantalla de la aplicación antes de que empiece la animación](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

En la captura de pantalla siguiente se muestra la aplicación después de la animación:

[![Captura de pantalla de la aplicación después de completarse la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Puede obtener más control sobre la transición con escenas, que se describen en la sección siguiente.

#### <a name="android-scenes"></a>Escenas de Android

Las [escenas](xref:Android.Transitions.Scene) se introdujeron como parte del marco de transición para ofrecer al desarrollador más control sobre las animaciones. Escenas crear un área dinámica en la interfaz de usuario: se especifica un contenedor y varias versiones, o "escenas", para el contenido XML dentro del contenedor y Android realiza el resto del trabajo para animar las transiciones entre las escenas. Las escenas de Android permiten crear animaciones complejas con un trabajo mínimo en el lado de desarrollo.

El elemento de la interfaz de usuario estática que aloja el contenido dinámico es un denominado *contenedor* o base de la *escena*. En el ejemplo siguiente se usa el Android Designer para `RelativeLayout` crear `container`un llamado:

[![Uso del Android Designer para crear un contenedor RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

El diseño de ejemplo también define un botón `sceneButton` denominado debajo `container`de. Este botón desencadenará la transición.

El contenido dinámico dentro del contenedor requiere dos nuevos diseños de Android. Estos diseños solo especifican el código *dentro* del contenedor.
En el código de ejemplo siguiente se define un diseño denominado *Scene1* que contiene dos campos de texto que leen "kit" y "Kat (", respectivamente, y un segundo diseño denominado *Scene2* que contiene los mismos campos de texto inversos. El XML es el siguiente:

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

En el ejemplo anterior `merge` se usa para que el código de vista sea más corto y para simplificar la jerarquía de vistas. Puede leer más sobre `merge` los diseños [aquí](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Una escena se crea llamando a [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*), pasando el objeto contenedor, el identificador de recurso del archivo de diseño de la escena y el actual `Context`, como se muestra en el ejemplo de código siguiente:

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

En la captura de pantalla siguiente se muestra la escena antes de la animación:

[![Captura de pantalla de la aplicación antes de que se inicie la animación](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

En la captura de pantalla siguiente se muestra la escena después de la animación:

[![Captura de pantalla de la aplicación una vez completada la animación](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Hay un [error conocido](https://code.google.com/p/android/issues/detail?id=62450) en la biblioteca de transiciones de Android que hace que las `GetSceneForLayout` escenas creadas con se interrumpan cuando un usuario navega por una actividad por segunda vez. [Aquí](http://www.doubleencore.com/2013/11/new-transitions-framework/)se describe una solución alternativa de Java.


##### <a name="custom-transitions-in-scenes"></a>Transiciones personalizadas en escenas

Una transición personalizada se puede definir en un archivo de recursos XML en `transition` el `Resources`directorio de, tal como se muestra en la siguiente captura de pantalla:

[![Ubicación del archivo Transition. XML en el directorio de recursos/transición](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

En el ejemplo de código siguiente se define una transición que anima cinco segundos y usa el [interpolador de sobremodulación](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

La transición se crea en la actividad mediante [TransitionInflater](xref:Android.Transitions.TransitionInflater), como se muestra en el código siguiente:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

La nueva transición se agrega a continuación a `Go` la llamada que comienza la animación:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>Interfaz de usuario translúcida

KitKat le ofrece más control sobre la aplicación con barras de navegación y de estado translúcido opcionales. Puede cambiar el translucidez de los elementos de la interfaz de usuario del sistema en el mismo archivo XML que se usa para definir el tema de Android. KitKat presenta las siguientes propiedades:

- `windowTranslucentStatus`-Cuando se establece en true, hace que la barra de estado superior sea translúcida.

- `windowTranslucentNavigation`-Cuando se establece en true, hace que la barra de navegación inferior sea translúcida.

- `fitsSystemWindows`-Si se establece la barra superior o inferior en transcluent, el contenido se desplaza bajo los elementos transparentes de la interfaz de usuario de forma predeterminada. Establecer esta propiedad en `true` es una forma sencilla de evitar que el contenido se superponga con los elementos de la interfaz de usuario del sistema translúcido.


En el código siguiente se define un tema con barras de navegación y de estado translúcido:

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

En la captura de pantalla siguiente se muestra el tema anterior con las barras de navegación y de estado translúcido:

[![Captura de pantalla de ejemplo de la aplicación con el estado translúcido y las barras de navegación](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Contenido del usuario

### <a name="storage-access-framework"></a>Marco de acceso al almacenamiento

El marco de acceso de almacenamiento (SAF) es una nueva forma de que los usuarios interactúen con el contenido almacenado, como imágenes, vídeos y documentos. En lugar de presentar a los usuarios un cuadro de diálogo para elegir una aplicación para controlar el contenido, KitKat abre una nueva interfaz de usuario que permite a los usuarios tener acceso a sus datos en una ubicación agregada. Una vez que se haya elegido el contenido, el usuario volverá a la aplicación que solicitó el contenido y la experiencia de la aplicación continuará como es habitual.

Este cambio requiere dos acciones en el lado del Desarrollador: en primer lugar, las aplicaciones que requieren contenido de los proveedores deben actualizarse a una nueva forma de solicitar contenido. En segundo lugar, las aplicaciones que escriben datos en un `ContentProvider` deben modificarse para usar el nuevo marco de trabajo. Ambos escenarios dependen del nuevo[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API.

#### <a name="documentsprovider"></a>DocumentsProvider

En KitKat, las interacciones con `ContentProviders` se abstraen con la `DocumentsProvider` clase. Esto significa que SAF no se preocupa de dónde están los datos físicamente, siempre y cuando sea accesible a `DocumentsProvider` través de la API. Los proveedores locales, los servicios en la nube y los dispositivos de almacenamiento externo usan la misma interfaz, y se tratan de la misma manera, lo que proporciona al usuario y al desarrollador un solo lugar para interactuar con el contenido del usuario.

En esta sección se explica cómo cargar y guardar contenido con el marco de acceso de almacenamiento.

#### <a name="request-content-from-a-provider"></a>Solicitar contenido de un proveedor

Podemos indicar a kitkat que queremos elegir contenido mediante la interfaz de usuario de SAF con `ActionOpenDocument` la intención, lo que significa que queremos conectarnos a todos los proveedores de contenido disponibles para el dispositivo. Puede Agregar algún filtro a esta intención especificando `CategoryOpenable`, lo que significa que solo se devolverá el contenido que se puede abrir (es decir, contenido accesible y utilizable). KitKat también permite filtrar el `MimeType`contenido con. Por ejemplo, el código siguiente filtra los resultados de la imagen mediante la especificación `MimeType`de la imagen:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

La `StartActivityForResult` llamada a inicia la interfaz de usuario de SAF, que el usuario puede examinar para elegir una imagen:

[![Captura de pantalla de ejemplo de una aplicación que usa el marco de acceso de almacenamiento para buscar una imagen](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Una vez que el usuario ha elegido una `OnActivityResult` imagen, `Android.Net.Uri` devuelve el del archivo elegido. En el ejemplo de código siguiente se muestra la selección de imagen del usuario:

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

#### <a name="write-content-to-a-provider"></a>Escribir contenido en un proveedor

Además de cargar contenido desde la interfaz de usuario de SAF, KitKat también le permite guardar contenido `ContentProvider` en cualquier que implemente la `DocumentProvider` API. Al guardar el contenido `Intent` , `ActionCreateDocument`se usa un con:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

En el ejemplo de código anterior se carga la interfaz de usuario de SAF, lo que permite al usuario cambiar el nombre de archivo y seleccionar un directorio para hospedar el nuevo archivo:

[![Captura de pantalla del usuario que cambia el nombre de archivo a NewDoc en el directorio downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Cuando el usuario presiona **Guardar**, `OnActivityResult` se pasa el `Android.Net.Uri` del archivo recién creado, al que se puede tener acceso con `data.Data`. El URI se puede usar para transmitir los datos en el nuevo archivo:

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

Tenga en cuenta que[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
Devuelve un `System.IO.Stream`objeto, por lo que se puede escribir en .net todo el proceso de streaming.

Para obtener más información sobre la carga, la creación y la edición de contenido con el marco de acceso de almacenamiento, consulte la [documentación de Android para el marco de acceso de almacenamiento](https://developer.android.com/guide/topics/providers/document-provider.html).

### <a name="printing"></a>Impresión

El contenido de impresión se simplifica en KitKat con la introducción de los servicios `PrintManager`de [impresión](xref:Android.PrintServices) y. KitKat también es la primera versión de la API para aprovechar completamente las [API del servicio de impresión en la nube de Google](https://developers.google.com/cloud-print/) con la [aplicación Google Cloud Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
La mayoría de los dispositivos que se incluyen con KitKat descargan automáticamente la aplicación de impresión en la nube de Google y el [complemento del servicio de impresión de HP](https://play.google.com/store/apps/details?id=com.hp.android.printservice)cuando se conectan por primera vez a WiFi Un usuario puede comprobar la configuración de impresión de su dispositivo navegando a **configuración > sistema > impresión**:

[![Captura de pantalla de ejemplo de la pantalla de configuración de impresión](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> Aunque las API de impresión están configuradas para que funcionen con Google Cloud Print de forma predeterminada, Android sigue permitiendo a los desarrolladores preparar el contenido de impresión con las nuevas API y enviarlo a otras aplicaciones para controlar la impresión.



#### <a name="printing-html-content"></a>Imprimir contenido HTML

KitKat crea automáticamente un [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) para una vista Web con `WebView.CreatePrintDocumentAdapter`. La impresión de contenido web es un esfuerzo coordinado entre un [`WebViewClient`](xref:Android.Webkit.WebViewClient) que espera a que se cargue el contenido HTML y permite que la actividad sepa que la opción de impresión está disponible en el menú opciones y la actividad, que espera a que el usuario seleccione la opción de impresión y llama a `Print`en el `PrintManager`. En esta sección se describe la configuración básica necesaria para imprimir contenido HTML en pantalla.

Tenga en cuenta que la carga y la impresión de contenido Web requieren el permiso de Internet:

[![Configuración del permiso de Internet en las opciones de la aplicación](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Elemento de menú de impresión

La opción Imprimir normalmente aparecerá en el [menú de opciones](https://developer.android.com/guide/topics/ui/menus.html#options-menu)de la actividad.
El menú opciones permite que los usuarios realicen acciones en una actividad. Está en la esquina superior derecha de la pantalla y tiene el siguiente aspecto:

[![Captura de pantalla de ejemplo del elemento de menú de impresión mostrado en la esquina superior derecha de la pantalla](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


Los elementos de menú adicionales pueden definirse en el directorio de *menús*en *recursos*. El código siguiente define un elemento de menú de ejemplo denominado [Print](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

La interacción con el menú de opciones de la actividad se `OnCreateOptionsMenu` produce `OnOptionsItemSelected` a través de los métodos y.
`OnCreateOptionsMenu`es el lugar donde se agregan nuevos elementos de menú, como la opción de impresión, en el directorio de recursos del *menú* .
`OnOptionsItemSelected`escucha al usuario seleccionando la opción de impresión en el menú y comienza la impresión:

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

El código anterior también define una variable denominada `dataLoaded` para realizar un seguimiento del estado del contenido HTML. El `WebViewClient` establecerá esta variable en true cuando se haya cargado todo el contenido, por lo que la actividad sabrá agregar el elemento de menú de impresión al menú opciones.

##### <a name="webviewclient"></a>WebViewClient

El trabajo de `WebViewClient` es asegurarse de que los datos `WebView` de se cargan por completo antes de que aparezca la opción Imprimir en el menú, que `OnPageFinished` lo hace con el método. `OnPageFinished`realiza escuchas para que el contenido web termine de cargarse e indica a la actividad que vuelva a `InvalidateOptionsMenu`crear su menú de opciones con:

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

`OnPageFinished`también establece el `dataLoaded` valor en `true`, por `OnCreateOptionsMenu` lo que puede volver a crear el menú con la opción de impresión en su lugar.

##### <a name="printmanager"></a>PrintManager

En el ejemplo de código siguiente se imprime el `WebView`contenido de un:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`toma como argumentos: un nombre para el trabajo de impresión ("mi página web" en este ejemplo), un[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
que genera el documento de impresión a partir del contenido y[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null` en el ejemplo anterior). Puede especificar `PrintAttributes` para ayudar a diseñar el contenido en la página impresa, aunque los atributos predeterminados deben controlar la mayoría de los escenarios.

La `Print` llamada a carga la interfaz de usuario de impresión, que enumera las opciones para el trabajo de impresión. La interfaz de usuario ofrece a los usuarios la opción de imprimir o guardar el contenido HTML en un archivo PDF, tal como se muestra en las capturas de pantallas siguientes:

[![Captura de pantalla de PrintHtmlActivity que muestra el menú de impresión](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de pantalla de PrintHtmlActivity que muestra el menú Guardar como PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat agrega varias API para acomodar las nuevas características del dispositivo. Los más importantes son la emulación de tarjetas basadas en host y el `SensorManager`nuevo.

### <a name="host-based-card-emulation-in-nfc"></a>Emulación de tarjeta basada en host en NFC

La emulación de tarjeta basada en host (HCE) permite que las aplicaciones se comporten como tarjetas NFC o lectores de tarjetas NFC sin depender del elemento seguro propio del operador. Antes de configurar HCE, asegúrese de que HCE está disponible en el `PackageManager.HasSystemFeature`dispositivo con:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE requiere que la característica HCE y el `Nfc` permiso se registren con el de `AndroidManifest.xml`la aplicación:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Establecer el permiso NFC en las opciones de la aplicación](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para trabajar, HCE tiene que poder ejecutarse en segundo plano y debe iniciarse cuando el usuario realiza una transacción NFC, incluso si la aplicación que usa HCE no se está ejecutando. Podemos lograr esto escribiendo el código de HCE como `Service`. Un servicio HCE implementa la `HostApduService` interfaz, que implementa los métodos siguientes:

- *ProcessCommandApdu* : una unidad de datos de protocolo de aplicación (APDU) es lo que se envía entre el lector NFC y el servicio HCE. Este método consume un ADPU del lector y devuelve una unidad de datos en respuesta.

- *OnDeactivated* : `HostAdpuService` se desactiva cuando el servicio HCE deja de comunicarse con el lector NFC.


Un servicio HCE también debe registrarse con el manifiesto de la aplicación y decorarse con los permisos, el filtro de intención y los metadatos adecuados. El código siguiente es un ejemplo de un `HostApduService` registrado con el manifiesto de Android mediante `Service` el atributo (para obtener más información sobre los atributos, consulte la guía Xamarin [Working with Android manifest](~/android/platform/android-manifest.md) ):

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

El servicio anterior proporciona una manera para que el lector NFC interactúe con la aplicación, pero el lector NFC todavía no tiene ninguna manera de saber si este servicio está emulando la tarjeta NFC que debe examinar. Para ayudar al lector NFC a identificar el servicio, podemos asignar el servicio a un *identificador de aplicación único (AID)* . Especificamos una ayuda, junto con otros metadatos sobre el servicio HCE, en un archivo de recursos XML registrado `MetaData` con el atributo (vea el ejemplo de código anterior). Este archivo de recursos especifica uno o varios filtros de AID: cadenas de identificador único en formato hexadecimal que corresponden a los Aid de uno o más dispositivos de Lector NFC:

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

Además de los filtros de ayuda, el archivo de recursos XML también proporciona una descripción orientada al usuario del servicio HCE, especifica un grupo de AID (aplicación de pago en lugar de "otros") y, en el caso de una aplicación de pago, un banner de 260x96 DP que se va a mostrar al usuario.

El programa de instalación descrito anteriormente proporciona los bloques de creación básicos para una aplicación que emula una tarjeta NFC. El propio NFC requiere varios pasos más y otras pruebas para configurar. Para obtener más información sobre la emulación de tarjetas basadas en host, consulte el [portal de documentación de Android](https://developer.android.com/guide/topics/connectivity/nfc/hce.html).
Para más información sobre el uso de NFC con Xamarin, consulte los [ejemplos de NFC de Xamarin](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat proporciona acceso a los sensores del dispositivo a través [`SensorManager`](xref:Android.Hardware.SensorManager)de un.
`SensorManager` Permite al sistema operativo programar la entrega de información de sensor a una aplicación por lotes, manteniendo la duración de la batería.

KitKat también incluye dos nuevos tipos de sensor para realizar el seguimiento de los pasos del usuario. Estos se basan en el acelerómetro e incluyen:

- *StepDetector* -APP se notifica/reactivarán cuando el usuario realiza un paso y el detector proporciona un valor de tiempo para el momento en que se produjo el paso.

- *StepCounter* : realiza un seguimiento del número de pasos que ha tomado el usuario desde que se registró el sensor *hasta el siguiente reinicio del dispositivo*.

En la captura de pantalla siguiente se muestra el contador de pasos en acción:

[![Captura de pantalla de la aplicación SensorsActivity que muestra un contador de pasos](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Puede crear un `SensorManager` `GetSystemService(SensorService)` llamando a y `SensorManager`convirtiendo el resultado como. Para usar el contador de pasos, `GetDefaultSensor` llame a `SensorManager`en. Puede registrar el sensor y escuchar los cambios en el recuento de pasos con la ayuda del[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
, como se muestra en el ejemplo de código siguiente:

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

`OnSensorChanged`se llama a si el recuento de pasos se actualiza mientras la aplicación está en primer plano. Si la aplicación entra en el fondo, o el dispositivo está en `OnSensorChanged` suspensión, no se llamará a; sin embargo, los pasos se seguirán contando hasta que `UnregisterListener` se llame a.

Tenga en cuenta que *el valor de recuento de pasos es acumulativo en todas las aplicaciones que registran el sensor*. Esto significa que incluso si desinstala y vuelve a instalar la aplicación e inicializa la `count` variable en 0 al iniciarse la aplicación, el valor indicado por el sensor seguirá siendo el número total de pasos que se realizaron mientras se registraba el sensor, ya sea por el aplicación u otra. Puede evitar que la aplicación se agregue al contador de pasos llamando `UnregisterListener` a `SensorManager`en, como se muestra en el código siguiente:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

Al reiniciar el dispositivo, se restablece el número de pasos en 0. La aplicación necesitará código adicional para asegurarse de que informa de un recuento exacto de la aplicación, independientemente de otras aplicaciones que usen el sensor o el estado del dispositivo.


> [!NOTE]
> Mientras que la API para la detección y el recuento de pasos se incluye con KitKat, no todos los teléfonos están desprovistos del sensor. Puede comprobar si el sensor está disponible ejecutando `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`o asegúrese de que el valor devuelto de `GetDefaultSensor` no `null`sea.


<a name="developer_tools" />

## <a name="developer-tools"></a>Herramientas de desarrollo

### <a name="screen-recording"></a>Grabación de pantalla

KitKat incluye nuevas capacidades de grabación de pantalla para que los desarrolladores puedan grabar aplicaciones en acción. La grabación de pantalla está disponible a través del cliente de [Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html) , que puede descargarse como parte de la Android SDK.

Para grabar la pantalla, conecte el dispositivo; a continuación, busque la instalación de Android SDK, navegue hasta el directorio **de herramientas de la plataforma** y ejecute el cliente **ADB** :

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

El comando anterior registrará un vídeo de 3 minutos predeterminado en la resolución predeterminada de 4Mbps. Para editar la longitud, agregue la marca *--Time-Limit* .
Para cambiar la resolución, agregue la marca *--bit-rate* . El siguiente comando registrará un vídeo de un minuto en 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Puede encontrar el vídeo en el dispositivo: aparecerá en la Galería cuando se complete la grabación.


## <a name="other-kitkat-additions"></a>Otras adiciones de KitKat

Además de los cambios descritos anteriormente, KitKat le permite:

- *Use la pantalla completa* : KitKat presenta un nuevo [modo inmersivo](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para examinar contenido, jugar a juegos y ejecutar otras aplicaciones que podrían beneficiarse de una experiencia de pantalla completa.

- *Personalización* de notificaciones: obtenga detalles adicionales acerca de las notificaciones del sistema con la[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
   . Esto le permite presentar la información de manera diferente dentro de la aplicación.

- *Recursos* que se dibujan en reflejo: los recursos que se van a dibujar tienen un nuevo[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   atributo que indica al sistema que cree una versión reflejada para las imágenes que requieran el volteo de los diseños de izquierda a derecha.

- *Pausar animaciones* : pausar y reanudar animaciones creadas con el[`Animator`](xref:Android.Animation.Animator)
   clase.

- *Leer texto dinámico cambiante* : denota partes de la interfaz de usuario que actualizan dinámicamente con nuevo texto como "regiones activas" con el nuevo[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   atributo para que el nuevo texto se lea automáticamente en el modo de accesibilidad.

- *Mejorar la experiencia de audio* : hacer pistas más fuertes con el[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
   , busque el pico y el RMS de una secuencia de audio con el[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
   y obtenga información de una marca de tiempo de [audio](xref:Android.Media.AudioTimestamp) para ayudarle con la sincronización de audio y vídeo.

- *Sincronizar ContentResolver devuelvan en el intervalo personalizado* : KitKat agrega cierta variabilidad al momento en que se realiza una solicitud de sincronización. Sincronizar `ContentResolver` a un momento o intervalo `ContentResolver.RequestSync` personalizado llamando a y pasando un `SyncRequest`.

- *Distinguir entre controladores* : en KitKat, a los controladores se les asignan identificadores enteros únicos a los que se puede tener `ControllerNumber` acceso a través de la propiedad del dispositivo. Esto hace que sea más fácil distinguir a los jugadores de un juego.

- *Control remoto* : con unos pocos cambios en el lado del hardware y el software, KitKat le permite convertir un dispositivo de un transmisor de infrarrojos en un control remoto mediante el `ConsumerIrService`y interactuar con dispositivos periféricos con el nuevo[`RemoteController`](xref:Android.Media.RemoteController)
   Enumera.

Para más información sobre los cambios anteriores de la API, consulte la introducción a las API de Google [Android 4,4](https://developer.android.com/about/versions/android-4.4.html) .


## <a name="summary"></a>Resumen

En este artículo se presentaron algunas de las nuevas API disponibles en Android 4,4 (nivel de API 19) y se han descrito prácticas recomendadas para la transición de una aplicación a KitKat. Se describen los cambios en las API que afectan a la experiencia del usuario, incluido el *marco de transición* ylas nuevas opciones para la misma. A continuación, se presentó el *marco* y `DocumentsProvider` la clase de acceso al almacenamiento, así como las nuevas API de *impresión*. Exploró la *emulación de la tarjeta basada en host NFC* y cómo trabajar con *sensores de baja energía*, incluidos dos nuevos sensores para realizar un seguimiento de los pasos del usuario. Por último, ha demostrado la captura de demostraciones en tiempo real de aplicaciones con *grabación de pantalla*y proporciona una lista detallada de los cambios y las adiciones de la API de kitkat.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de KitKat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [API de Android 4,4](https://developer.android.com/about/versions/android-4.4.html)
- [KitKat de Android](https://developer.android.com/about/versions/kitkat.html)
