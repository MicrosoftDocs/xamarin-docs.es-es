---
title: Compatibilidad de barra de herramientas
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 8d5f0b06a804e9b9af7bb15111c70e4505c79fd0
ms.sourcegitcommit: 40a56bbc1e038a9181101580ad18a4584edb5ab0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2021
ms.locfileid: "98025518"
---
# <a name="toolbar-compatibility"></a>Compatibilidad de barra de herramientas

## <a name="overview"></a>Información general

En esta sección se explica cómo usar `Toolbar` en versiones de Android anteriores a android 5,0 Lollipop. Si la aplicación no admite versiones de Android anteriores a Android 5,0, puede omitir esta sección. 

Dado `Toolbar` que forma parte de la biblioteca de compatibilidad de Android V7, se puede usar en dispositivos que ejecutan android 2,1 (nivel de API 7) y versiones posteriores. Sin embargo, se debe instalar la [biblioteca de compatibilidad de Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) de NuGet y el código modificado para que use la `Toolbar` implementación proporcionada en esta biblioteca. En esta sección se explica cómo instalar este NuGet y modificar la aplicación **ToolbarFun** desde la [adición de una segunda barra de herramientas](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) para que se ejecute en versiones de Android anteriores al círculo 5,0.

Para modificar una aplicación para que use la versión AppCompat de la barra de herramientas: 

1. Establezca las versiones de Android mínima y de destino para la aplicación.

2. Instale el paquete de NuGet de AppCompat.

3. Use un tema AppCompat en lugar de un tema integrado de Android.

4. Modifique `MainActivity` para que sea subclases `AppCompatActivity` en lugar de `Activity` . 

Cada uno de estos pasos se explica en detalle en las secciones siguientes.

## <a name="set-the-minimum-and-target-android-version"></a>Establecimiento de la versión de Android mínima y de destino

La plataforma de destino de la aplicación debe establecerse en el nivel de API 21 o superior, o bien la aplicación no se implementará correctamente. Si se detecta un error, como **no se encontró ningún identificador de recursos para el atributo ' tileModeX ' en el paquete ' Android '** durante la implementación de la aplicación, esto se debe a que la plataforma de destino no está establecida en **Android 5,0 (nivel de API 21-Lollipop)** o superior. 

Establezca el nivel de plataforma de destino en el nivel de API 21 o superior y establezca la configuración de proyecto de nivel de API de Android en la versión de Android mínima que la aplicación admite. Para obtener más información sobre cómo establecer los niveles de la API de Android, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md). En el `ToolbarFun` ejemplo, la versión mínima de Android se establece en KitKat (nivel de API 4,4). 

## <a name="install-the-appcompat-nuget-package"></a>Instalación del paquete de NuGet de AppCompat

A continuación, agregue el paquete de la [biblioteca de compatibilidad de Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) en el proyecto. En Visual Studio, haga clic con el botón derecho en **referencias** y seleccione **administrar paquetes NuGet.**... Haga clic en **examinar** y busque **biblioteca de compatibilidad de Android V7 AppCompat**. Seleccione **Xamarin. Android. support. V7. AppCompat** y haga clic en **instalar**: 

[![Captura de pantalla del paquete de AppCompat de V7 seleccionado en administrar paquetes NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Cuando se instala este NuGet, también se instalan otros paquetes NuGet si aún no están presentes (como **Xamarin. Android. support. Animated. Vector. drawable**, **Xamarin. Android. support. V4** y **Xamarin. Android. support. Vector. drawable**). Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough). 

## <a name="use-an-appcompat-theme-and-toolbar"></a>Usar un tema y una barra de herramientas de AppCompat

La biblioteca AppCompat incluye varios `Theme.AppCompat` temas que se pueden usar en cualquier versión de Android compatible con la biblioteca AppCompat. El `ToolbarFun` tema de la aplicación de ejemplo se deriva de `Theme.Material.Light.DarkActionBar` , que no está disponible en las versiones de Android anteriores al círculo. Por lo tanto, `ToolbarFun` se debe adaptar para usar el homólogo de AppCompat para este tema, `Theme.AppCompat.Light.DarkActionBar` . Además, dado `Toolbar` que no está disponible en las versiones de Android anteriores a Lollipop, se debe usar la versión AppCompat de `Toolbar` . Por lo tanto, los diseños deben usar `android.support.v7.widget.Toolbar` en lugar de `Toolbar` . 

### <a name="update-layouts"></a>Actualizar diseños

Edite **Resources/layout/main. axml** y reemplace el `Toolbar` elemento por el siguiente código XML: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Edite **recursos/diseño/toolbar.xml** y reemplace su contenido por el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Tenga en cuenta que los valores ya no tienen el `?attr` prefijo `android:` (Recuerde que la `?` notación hace referencia a un recurso en el tema actual). Si `?android:attr` todavía se usaba aquí, Android haría referencia al valor de atributo desde la plataforma que se está ejecutando actualmente en lugar de desde la biblioteca AppCompat. Dado que en este ejemplo `actionBarSize` se usa el definido por la biblioteca AppCompat, `android:` se quita el prefijo. Del mismo modo, `@android:style` se cambia a para `@style` que el `android:theme` atributo se establezca en un tema de la biblioteca AppCompat, &ndash; el `ThemeOverlay.AppCompat.Dark.ActionBar` tema se usa aquí en lugar de `ThemeOverlay.Material.Dark.ActionBar` . 

### <a name="update-the-style"></a>Actualizar el estilo

Edite **recursos/valores/styles.xml** y reemplace su contenido por el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

Los nombres de elemento y el tema primario en este ejemplo ya no tienen el prefijo `android:` porque se usa la biblioteca AppCompat. Además, el tema primario cambia a la versión AppCompat de `Light.DarkActionBar` . 

### <a name="update-menus"></a>Actualizar menús

Para admitir versiones anteriores de Android, la biblioteca AppCompat usa atributos personalizados que reflejan los atributos del `android:` espacio de nombres. Sin embargo, algunos atributos (como el `showAsAction` atributo que se usa en la `<menu>` etiqueta) no existen en el marco de Android en dispositivos antiguos &ndash; `showAsAction` se presentó en Android API 11, pero no está disponible en Android API 7. Por esta razón, se debe usar un espacio de nombres personalizado para prefijar todos los atributos definidos por la biblioteca de compatibilidad. En los archivos de recursos de menú, se define un espacio de nombres denominado `local` para prefijar el `showAsAction` atributo. 

Edite **los recursos/menú/top_menus.xml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

El `local` espacio de nombres se agrega con esta línea:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

El `showAsAction` atributo está precedido por este `local:` espacio de nombres en lugar de `android:` 

```csharp
local:showAsAction="ifRoom"
```

Del mismo modo, edite **Resources/menu/edit_menus.xml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

¿Cómo ofrece este modificador de espacio de nombres compatibilidad con el `showAsAction` atributo en versiones de Android anteriores al nivel de API 11? El atributo personalizado `showAsAction` y todos sus valores posibles se incluyen en la aplicación cuando se instala el NuGet de AppCompat. 

## <a name="subclass-appcompatactivity"></a>Subclase AppCompatActivity

El último paso de la conversión es modificar para `MainActivity` que sea una subclase de `AppCompatActivity` . Edite **MainActivity.CS** y agregue las siguientes `using` instrucciones: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Declara `Toolbar` como la versión AppCompat de `Toolbar` . A continuación, cambie la definición de clase de `MainActivity` : 

```csharp
public class MainActivity : AppCompatActivity
```

Para establecer la barra de acciones en la versión AppCompat de `Toolbar` , sustituya la llamada a `SetActionBar` por `SetSupportActionBar` . En este ejemplo, el título también se cambia para indicar que se está usando la versión AppCompat de `Toolbar` :

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Por último, cambie el nivel mínimo de Android al valor anterior al círculo que se va a admitir (por ejemplo, API 19). 

Compilar la aplicación y ejecutarla en un dispositivo o emulador Android. En la captura de pantalla siguiente se muestra la versión AppCompat de **ToolbarFun** en un nexo 4 con KitKat (API 19): 

[![Captura de pantalla completa de la aplicación que se ejecuta en un dispositivo KitKat, se muestran las dos barras de herramientas](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Cuando se usa la biblioteca AppCompat, no es necesario cambiar los temas en función de la versión de Android &ndash; . la biblioteca AppCompat permite proporcionar una experiencia de usuario coherente en todas las versiones compatibles de Android. 

## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
