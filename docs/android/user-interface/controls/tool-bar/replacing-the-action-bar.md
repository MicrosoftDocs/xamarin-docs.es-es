---
title: Reemplazo de la barra de acciones
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: df6a479123dfc0fa2e5a47c9210a4bdf24d066e1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762458"
---
# <a name="replacing-the-action-bar"></a>Reemplazo de la barra de acciones

## <a name="overview"></a>Información general

Uno de los usos más comunes para `Toolbar` es reemplazar la barra de acciones predeterminada con un personalizado `Toolbar` (cuando se crea un nuevo proyecto de Android, usa la barra de acciones predeterminada). Dado que `Toolbar` proporciona la capacidad de agregar logotipos marcados, títulos, elementos de menú, botones de navegación e incluso vistas personalizadas a la sección de la barra de la aplicación de la interfaz de usuario de una actividad, ofrece una actualización importante sobre la barra de acción predeterminada.

Para reemplazar la barra de acciones predeterminada de una aplicación `Toolbar`por una: 

1. Cree un nuevo tema personalizado y modifique las propiedades de la aplicación para que use este nuevo tema. 

2. Deshabilite `windowActionBar` el atributo en el tema personalizado y habilite `windowNoTitle` el atributo.

3. Defina un diseño para `Toolbar`.

4. Incluya el `Toolbar` diseño en el archivo de diseño **Main. axml** de la actividad. 

5. Agregue `OnCreate` código al método de la actividad para `Toolbar` buscar `ToolBar` y llamar `SetActionBar` a para instalar como la barra de acciones.

En las secciones siguientes se explica en detalle este proceso. Se crea una aplicación sencilla y su barra de acción se reemplaza por una `Toolbar`personalizada. 

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **ToolbarFun** (vea [Hola, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información sobre la creación de un nuevo proyecto de Android). Una vez creado este proyecto, establezca los niveles de destino y mínimo de la API de Android en **android 5,0 (nivel de API 21-Lollipop)** o posterior. Para obtener más información sobre cómo establecer los niveles de versión de Android, consulte [Descripción de los niveles](~/android/app-fundamentals/android-api-levels.md)de la API de Android. Cuando la aplicación se compila y se ejecuta, muestra la barra de acciones predeterminada, tal como se muestra en esta captura de pantalla:

[![Captura de pantalla de la barra de acciones predeterminada](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

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

Este XML define un nuevo tema **personalizado denominado mis temas que** se basa en el tema **Theme. material. Light. DarkActionBar** en el círculo. El `windowNoTitle` atributo se establece en `true` para ocultar la barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para mostrar la barra de herramientas personalizada, `ActionBar` el valor predeterminado debe estar deshabilitado: 

```xml
<item name="android:windowActionBar">false</item>
```

Para el color de `colorPrimary` fondo de la barra de herramientas se usa un valor de verde oliva: 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Aplicar el tema personalizado

Edite **Properties/archivo AndroidManifest. XML** y agregue el `android:theme` atributo siguiente al `<application>` elemento para que la aplicación use el `MyTheme` tema personalizado: 

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

Este XML define el personalizado `Toolbar` que reemplaza la barra de acciones predeterminada. El alto mínimo de `Toolbar` se establece en el tamaño de la barra de acciones que reemplaza: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de `Toolbar` se establece en el color verde oliva definido anteriormente en **styles. XML**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partir de Lollipop, `android:theme` el atributo se puede usar para aplicar estilo a una vista individual. Los `ThemeOverlay.Material` temas introducidos en el círculo hacen posible la superposición `Theme.Material` de los temas predeterminados, sobrescribiendo los atributos relevantes para que sean claros o oscuros. En este ejemplo, `Toolbar` usa un tema oscuro para que su contenido sea ligero en color: 

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

Este diseño incluye el `Toolbar` definido en **Toolbar. XML** y utiliza `RelativeLayout` para especificar que `Toolbar` se va a colocar en la parte superior de la interfaz de usuario (encima del botón). 

## <a name="find-and-activate-the-toolbar"></a>Búsqueda y activación de la barra de herramientas

Edite **MainActivity.CS** y agregue la siguiente instrucción using:

```csharp
using Android.Views;
```

Además, agregue las siguientes líneas de código al final del `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Este código busca `Toolbar` y llama a `SetActionBar` para que el `Toolbar` asuma las características de la barra de acciones predeterminadas. El título de la barra de herramientas se cambia a **mi barra de herramientas**. Como se puede observar en este ejemplo de `ToolBar` código, se puede hacer referencia directamente a la misma como barra de acción. Compilar y ejecutar &ndash; esta aplicación `Toolbar` el personalizado se muestra en lugar de la barra de acciones predeterminada: 

[![Captura de pantalla de la barra de herramientas personalizada con la combinación de colores verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Tenga en cuenta `Toolbar` que tiene un estilo independiente `Theme.Material.Light.DarkActionBar` del tema que se aplica al resto de la aplicación. 

Si se produce una excepción durante la ejecución de la aplicación, consulte la sección de [solución de problemas](#troubleshooting) a continuación.

## <a name="add-menu-items"></a>Agregar elementos de menú 

En esta sección, los `Toolbar`menús se agregan a. El área superior derecha de `ToolBar` se reserva para los elementos &ndash; de menú cada elemento de menú (también denominado elemento de *acción*) puede realizar una acción dentro de la actividad actual o puede realizar una acción en nombre de toda la aplicación. 

Para agregar menús a `Toolbar`: 

1. Agregue iconos de menú (si es necesario) `mipmap-` a las carpetas del proyecto de la aplicación. Google proporciona un conjunto de iconos de menú gratuitos en la página [iconos de materiales](https://design.google.com/icons/) . 

2. Defina el contenido de los elementos de menú agregando un nuevo archivo de recursos de menú en **recursos/menú**. 

3. Implemente `OnCreateOptionsMenu` el método de la &ndash; actividad que este método infla los elementos de menú. 

4. Implemente `OnOptionsItemSelected` el método de la &ndash; actividad. este método realiza una acción cuando se puntea un elemento de menú. 

En las secciones siguientes se muestra este proceso en detalle mediante la adición de elementos de menú de `Toolbar` **edición** y guardado al personalizado. 

### <a name="install-menu-icons"></a>Iconos del menú instalar

Continuando con `ToolbarFun` la aplicación de ejemplo, agregue iconos de menú al proyecto de la aplicación. Descargue los iconos de la [barra de herramientas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), descomprima y copie el contenido de las carpetas *mipmap-* extraídas en el proyecto *MIP-* folders en **ToolbarFun/Resources** e incluya cada archivo de icono agregado en el proyecto.

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

- Un elemento de menú de **edición** que `ic_action_content_create.png` usa el icono (un lápiz). 

- Un elemento de menú **Guardar** que usa `ic_action_content_save.png` el icono (un disquete). 

- Un elemento de menú de **preferencias** que no tiene un icono.

Los `showAsAction` atributos de los elementos de menú **Editar** y **Guardar** se establecen `ifRoom` en &ndash; esta configuración hace que estos elementos de menú aparezcan en `Toolbar` si hay espacio suficiente para que se muestren. El elemento de menú Preferencias `showAsAction` establece `never` &ndash; en esto hace que el menú **preferencias** aparezca en el menú de *desbordamiento* (tres puntos verticales). 

### <a name="implement-oncreateoptionsmenu"></a>Implementación de OnCreateOptionsMenu

Agregue el método siguiente a **MainActivity.CS**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android llama al `OnCreateOptionsMenu` método para que la aplicación pueda especificar el recurso de menú para una actividad. En este método, el recurso **top_menus. XML** se infla en el pasado `menu`. Este código hace que los nuevos elementos de menú **Editar**, **Guardar**y **preferencias** aparezcan en `Toolbar`el. 

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

Cuando un usuario pulsa un elemento de menú, Android llama `OnOptionsItemSelected` al método y pasa el elemento de menú seleccionado. En este ejemplo, la implementación solo muestra una notificación del sistema para indicar qué elemento de menú se ha punteado. 

Compile y ejecute `ToolbarFun` para ver los nuevos elementos de menú en la barra de herramientas. `Toolbar` Ahora se muestran tres iconos de menú, tal como se muestra en esta captura de pantalla: 

[![Diagrama que ilustra las ubicaciones de los elementos de menú de edición, guardado y desbordamiento](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Cuando un usuario pulsa el elemento de menú **Editar** , se muestra una notificación del sistema para `OnOptionsItemSelected` indicar que se ha llamado al método: 

[![Captura de pantalla de la notificación del sistema mostrado cuando se puntea el elemento de edición](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Cuando un usuario pulsa el menú de desbordamiento, se muestra el elemento de menú **preferencias** . Normalmente, las acciones menos comunes se deben colocar &ndash; en el menú de desbordamiento. en este ejemplo se usa el menú de desbordamiento para las **preferencias** porque no se usa tan a menudo como **Editar** y **Guardar**: 

[![Captura de pantalla del elemento de menú de preferencias que aparece en el menú de desbordamiento](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obtener más información sobre los menús de Android, consulte el tema sobre los [menús](https://developer.android.com/guide/topics/ui/menus.html) del desarrollador de Android. 

## <a name="troubleshooting"></a>Solución de problemas

Las siguientes sugerencias pueden ayudarle a depurar problemas que pueden producirse al reemplazar la barra de acciones por una barra de herramientas.

### <a name="activity-already-has-an-action-bar"></a>La actividad ya tiene una Barra de acciones

Si la aplicación no está configurada correctamente para usar un tema personalizado, tal y como se explica en [aplicar el tema personalizado](#apply-the-custom-theme), se puede producir la siguiente excepción al ejecutar la aplicación:

![Error que puede producirse cuando no se usa el tema personalizado](replacing-the-action-bar-images/03-theme-not-defined.png)

Además, se puede producir un mensaje de error como el siguiente: _Java.Lang.IllegalStateException: Esta actividad ya tiene una barra de acciones proporcionada por la ventana Décor._ 

Para corregir este error, compruebe que se `android:theme` `<application>` ha agregado el atributo para el tema personalizado (en **Properties/archivo AndroidManifest. XML**) como se describió anteriormente en [aplicar el tema personalizado](#apply-the-custom-theme). Además, este error puede producirse si el diseño `Toolbar` o el tema personalizado no están configurados correctamente.

## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
