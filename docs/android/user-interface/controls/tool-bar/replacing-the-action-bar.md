---
title: Reemplazo de la barra de acciones
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: f20568b5d76fcc1788d19497e372bcd0cecc61ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029086"
---
# <a name="replacing-the-action-bar"></a>Reemplazo de la barra de acciones

## <a name="overview"></a>Información general

Uno de los usos más comunes de la `Toolbar` es reemplazar la barra de acciones predeterminada por un `Toolbar` personalizado (cuando se crea un nuevo proyecto de Android, usa la barra de acciones predeterminada). Dado que el `Toolbar` proporciona la capacidad de agregar logotipos marcados, títulos, elementos de menú, botones de navegación e incluso vistas personalizadas a la sección de la barra de la aplicación de la interfaz de usuario de una actividad, ofrece una actualización significativa en la barra de acción predeterminada.

Para reemplazar la barra de acciones predeterminada de una aplicación por una `Toolbar`: 

1. Cree un nuevo tema personalizado y modifique las propiedades de la aplicación para que use este nuevo tema. 

2. Deshabilite el atributo `windowActionBar` en el tema personalizado y habilite el atributo `windowNoTitle`.

3. Defina un diseño para el `Toolbar`.

4. Incluya el diseño de `Toolbar` en el archivo de diseño **Main. axml** de la actividad. 

5. Agregue código al método `OnCreate` de la actividad para buscar el `Toolbar` y llamar a `SetActionBar` para instalar el `ToolBar` como la barra de acciones.

En las secciones siguientes se explica en detalle este proceso. Se crea una aplicación sencilla y se reemplaza su barra de acción por una `Toolbar`personalizada. 

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **ToolbarFun** (vea [Hola, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información sobre la creación de un nuevo proyecto de Android). Una vez creado este proyecto, establezca los niveles de destino y mínimo de la API de Android en **android 5,0 (nivel de API 21-Lollipop)** o posterior. Para obtener más información sobre cómo establecer los niveles de versión de Android, consulte [Descripción de los niveles](~/android/app-fundamentals/android-api-levels.md)de la API de Android. Cuando la aplicación se compila y se ejecuta, muestra la barra de acciones predeterminada, tal como se muestra en esta captura de pantalla:

[![captura de pantalla de la barra de acciones predeterminada](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

## <a name="create-a-custom-theme"></a>Crear un tema personalizado

Abra el directorio **Resources/Values** y cree un nuevo archivo denominado **styles. XML**. Reemplace su contenido por el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Este XML define un nuevo tema **personalizado denominado mis temas que** se basa en el tema **Theme. material. Light. DarkActionBar** en el círculo. El atributo `windowNoTitle` se establece en `true` para ocultar la barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para mostrar la barra de herramientas personalizada, la `ActionBar` predeterminada debe estar deshabilitada: 

```xml
<item name="android:windowActionBar">false</item>
```

Para el color de fondo de la barra de herramientas se usa un valor de `colorPrimary` oliva verde: 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Aplicar el tema personalizado

Edite **Properties/archivo AndroidManifest. XML** y agregue el siguiente atributo de `android:theme` al elemento `<application>` para que la aplicación use el `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obtener más información sobre cómo aplicar un tema personalizado a una aplicación, consulte [uso de temas personalizados](~/android/user-interface/material-theme.md#customtheme). 

## <a name="define-a-toolbar-layout"></a>Definir un diseño de barra de herramientas

En el directorio **Resources/layout** , cree un nuevo archivo denominado **Toolbar. XML**. Reemplace su contenido por el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Este XML define el `Toolbar` personalizado que reemplaza la barra de acción predeterminada. El alto mínimo de la `Toolbar` se establece en el tamaño de la barra de acciones que reemplaza: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo del `Toolbar` se establece en el color verde oliva definido anteriormente en **styles. XML**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partir de Lollipop, el atributo `android:theme` se puede usar para aplicar estilo a una vista individual. Los temas de `ThemeOverlay.Material` introducidos en el círculo hacen posible la superposición de los temas de `Theme.Material` predeterminados, sobrescribiendo los atributos pertinentes para que sean claros o oscuros. En este ejemplo, el `Toolbar` utiliza un tema oscuro para que su contenido sea ligero en color: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Esta configuración se usa para que los elementos de menú contrasten con el color de fondo más oscuro.

## <a name="include-the-toolbar-layout"></a>Incluir el diseño de la barra de herramientas

Edite el archivo de diseño **Resources/layout/main. axml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

Este diseño incluye el `Toolbar` definido en **Toolbar. XML** y utiliza un `RelativeLayout` para especificar que el `Toolbar` se va a colocar en la parte superior de la interfaz de usuario (encima del botón). 

## <a name="find-and-activate-the-toolbar"></a>Búsqueda y activación de la barra de herramientas

Edite **MainActivity.CS** y agregue la siguiente instrucción using:

```csharp
using Android.Views;
```

Además, agregue las siguientes líneas de código al final del método `OnCreate`:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Este código busca el `Toolbar` y llama a `SetActionBar` para que el `Toolbar` tome las características predeterminadas de la barra de acciones. El título de la barra de herramientas se cambia a **mi barra de herramientas**. Como se puede observar en este ejemplo de código, se puede hacer referencia directamente al `ToolBar` como una barra de acciones. Compile y ejecute esta aplicación &ndash; se muestra la `Toolbar` personalizada en lugar de la barra de acciones predeterminada: 

[![captura de pantalla de la barra de herramientas personalizada con la combinación de colores verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Observe que el `Toolbar` tiene un estilo independiente del tema `Theme.Material.Light.DarkActionBar` que se aplica al resto de la aplicación. 

Si se produce una excepción durante la ejecución de la aplicación, consulte la sección de [solución de problemas](#troubleshooting) a continuación.

## <a name="add-menu-items"></a>Agregar elementos de menú 

En esta sección, los menús se agregan a la `Toolbar`. La zona superior derecha de la `ToolBar` está reservada para los elementos de menú &ndash; cada elemento de menú (también denominado *elemento de acción*) puede realizar una acción dentro de la actividad actual o puede realizar una acción en nombre de toda la aplicación. 

Para agregar menús al `Toolbar`: 

1. Agregue iconos de menú (si es necesario) a las carpetas `mipmap-` del proyecto de la aplicación. Google proporciona un conjunto de iconos de menú gratuitos en la página [iconos de materiales](https://design.google.com/icons/) . 

2. Defina el contenido de los elementos de menú agregando un nuevo archivo de recursos de menú en **recursos/menú**. 

3. Implemente el método `OnCreateOptionsMenu` de la actividad &ndash; este método infla los elementos de menú. 

4. Implemente el método `OnOptionsItemSelected` de la actividad &ndash; este método realiza una acción cuando se puntea un elemento de menú. 

En las secciones siguientes se muestra este proceso en detalle mediante la adición de elementos de menú de **edición** y **guardado** a la `Toolbar`personalizada. 

### <a name="install-menu-icons"></a>Iconos del menú instalar

Continuando con la aplicación de ejemplo `ToolbarFun`, agregue iconos de menú al proyecto de la aplicación. Descargue los iconos de la [barra de herramientas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), descomprima y copie el contenido de las carpetas *mipmap-* extraídas en el proyecto *MIP-* folders en **ToolbarFun/Resources** e incluya cada archivo de icono agregado en el proyecto.

### <a name="define-a-menu-resource"></a>Definir un recurso de menú

Cree un nuevo subdirectorio de **menú** en **recursos**. En el subdirectorio **Menu** , cree un nuevo archivo de recursos de menú llamado **top_menus. XML** y reemplace su contenido por el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Este XML crea tres elementos de menú:

- Un elemento de menú **edición** que utiliza el icono de `ic_action_content_create.png` (un lápiz). 

- Un elemento de menú **Guardar** que usa el icono de `ic_action_content_save.png` (un disquete). 

- Un elemento de menú de **preferencias** que no tiene un icono.

Los atributos `showAsAction` de los elementos de menú **Editar** y **Guardar** se establecen en `ifRoom` &ndash; esta configuración hace que estos elementos de menú aparezcan en el `Toolbar` si hay espacio suficiente para que se muestren. El elemento de menú **preferencias** establece `showAsAction` en `never` &ndash; esto hace que el menú **preferencias** aparezca en el menú de *desbordamiento* (tres puntos verticales). 

### <a name="implement-oncreateoptionsmenu"></a>Implementación de OnCreateOptionsMenu

Agregue el método siguiente a **MainActivity.CS**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android llama al método `OnCreateOptionsMenu` para que la aplicación pueda especificar el recurso de menú para una actividad. En este método, el recurso **top_menus. XML** se infla en el `menu`pasado. Este código hace que los nuevos elementos de menú **Editar**, **Guardar**y **preferencias** aparezcan en el `Toolbar`. 

### <a name="implement-onoptionsitemselected"></a>Implementación de OnOptionsItemSelected

Agregue el método siguiente a **MainActivity.CS**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Cuando un usuario pulsa un elemento de menú, Android llama al método `OnOptionsItemSelected` y pasa el elemento de menú seleccionado. En este ejemplo, la implementación solo muestra una notificación del sistema para indicar qué elemento de menú se ha punteado. 

Compile y ejecute `ToolbarFun` para ver los nuevos elementos de menú en la barra de herramientas. En el `Toolbar` ahora se muestran tres iconos de menú, tal como se muestra en esta captura de pantalla: 

[Diagrama de![que ilustran las ubicaciones de los elementos de menú de edición, guardado y desbordamiento](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Cuando un usuario pulsa el elemento de menú **Editar** , se muestra una notificación del sistema para indicar que se ha llamado al método `OnOptionsItemSelected`: 

[![captura de pantalla del sistema de notificación que se muestra al puntear el elemento de edición](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Cuando un usuario pulsa el menú de desbordamiento, se muestra el elemento de menú **preferencias** . Normalmente, las acciones menos comunes se deben colocar en el menú de desbordamiento &ndash; este ejemplo usa el menú de desbordamiento para las **preferencias** porque no se usa tan a menudo como **Editar** y **Guardar**: 

[![captura de pantalla del elemento de menú Preferencias que aparece en el menú de desbordamiento](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obtener más información sobre los menús de Android, consulte el tema sobre los [menús](https://developer.android.com/guide/topics/ui/menus.html) del desarrollador de Android. 

## <a name="troubleshooting"></a>Solución de problemas

Las siguientes sugerencias pueden ayudarle a depurar problemas que pueden producirse al reemplazar la barra de acciones por una barra de herramientas.

### <a name="activity-already-has-an-action-bar"></a>La actividad ya tiene una Barra de acciones

Si la aplicación no está configurada correctamente para usar un tema personalizado, tal y como se explica en [aplicar el tema personalizado](#apply-the-custom-theme), se puede producir la siguiente excepción al ejecutar la aplicación:

![Error que puede producirse cuando no se usa el tema personalizado](replacing-the-action-bar-images/03-theme-not-defined.png)

Además, se puede producir un mensaje de error como el siguiente: _java. lang. IllegalStateException: esta actividad ya tiene una barra de acciones proporcionada por la ventana Décor._ 

Para corregir este error, compruebe que el atributo `android:theme` del tema personalizado se ha agregado a `<application>` (en **Properties/archivo AndroidManifest. XML**) tal y como se ha descrito anteriormente en [aplicar el tema personalizado](#apply-the-custom-theme). Además, este error puede producirse si el diseño de `Toolbar` o el tema personalizado no están configurados correctamente.

## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
