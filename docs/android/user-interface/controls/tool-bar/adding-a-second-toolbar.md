---
title: Adición de una segunda barra de herramientas
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 5d2fec537f10ad3ef5300275c9851d4f57bc961d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645166"
---
# <a name="adding-a-second-toolbar"></a>Adición de una segunda barra de herramientas


## <a name="overview"></a>Información general 

Puede hacer más que reemplazar la barra &ndash; de acción que se puede usar varias veces dentro de una actividad, se puede personalizar para colocarla en cualquier parte de la pantalla y se puede configurar para que abarque solo un ancho parcial de la pantalla. `Toolbar` Los ejemplos siguientes muestran cómo crear un segundo `Toolbar` y colocarlo en la parte inferior de la pantalla. Esto `Toolbar` implementa los elementos de menú de **copiar**, **cortar**y **pegar** . 


## <a name="define-the-second-toolbar"></a>Definir la segunda barra de herramientas 

Edite el archivo de diseño **Main. axml** y reemplace su contenido con por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Este XML agrega una segunda `Toolbar` a la parte inferior de la pantalla con un `ImageView` vacío que llena el centro de la pantalla. El alto de este `Toolbar` valor se establece en el alto de una barra de acciones: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de `Toolbar` este valor se establece en un color de énfasis que se definirá a continuación:

```xml
android:background="?android:attr/colorAccent
```

Tenga en cuenta `Toolbar` que esto se basa en un tema diferente (**ThemeOverlay. material. Dark. barra**) que el utilizado `Toolbar` por el creado al [reemplazar el barra de acciones](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; no está enlazado a la ventana de la actividad Décor o a el tema que se usa en `Toolbar`el primer.

Edite Resources **/Values/Styles. XML** y agregue el color de énfasis siguiente a la definición de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Esto proporciona a la barra de herramientas inferior un color ámbar oscuro. Al compilar y ejecutar la aplicación, se muestra una segunda barra de herramientas en blanco en la parte inferior de la pantalla: 

[![Captura de pantalla de la aplicación con la segunda barra de herramientas amarilla en la parte inferior de la pantalla](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Agregar elementos del menú Edición 

En esta sección se explica cómo agregar elementos de menú de edición `Toolbar`a la parte inferior. 

Para agregar elementos de menú a un `Toolbar`elemento secundario: 

1.  Agregue iconos de menú a `mipmap-` las carpetas del proyecto de la aplicación (si es necesario).

2.  Defina el contenido de los elementos de menú agregando un archivo de recursos de menú adicional a **los recursos o el menú**. 

3.  En el método de `OnCreate` la actividad, `Toolbar` busque (llamando a `FindViewById`) e inflar los `Toolbar`menús.

4.  Implemente un controlador de `OnCreate` clics en para los nuevos elementos de menú. 

En las secciones siguientes se muestra este proceso en detalle: Los elementos de menú **cortar**, **copiar**y **pegar** se agregan a `Toolbar`la parte inferior. 



### <a name="define-the-edit-menu-resource"></a>Definir el recurso del menú Edición

En el subdirectorio Resources **/Menu** , cree un nuevo archivo XML denominado **edit_menus. XML** y reemplace el contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Este XML crea los elementos de menú **cortar**, **copiar**y **pegar** (mediante los iconos que se agregaron `mipmap-` a las carpetas al [reemplazar el barra de acciones](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Inflar los menús

Al final del `OnCreate` método de **MainActivity.CS**, agregue las siguientes líneas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Este `edit_toolbar` código localiza la vista definida en **Main. axml**, establece su título en **Editing**y aumenta sus elementos de menú (definidos en **edit_menus. XML**). Define un controlador de clic de menú que muestra una notificación del sistema para indicar el icono de edición que se ha punteado. 

Compile y ejecute la aplicación. Cuando se ejecute la aplicación, el texto y los iconos agregados anteriormente aparecerán como se muestra aquí: 

[![Diagrama de la barra de herramientas inferior con iconos de cortar, copiar y pegar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Al pulsar el icono del menú **cortar** , se muestra el siguiente aviso: 

[![Captura de pantalla de la notificación del sistema que indica que se ha punteado el icono del menú cortar](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Al puntear en los elementos de menú de cualquier barra de herramientas se muestra la notificación del sistema resultante: 

[![Capturas de pantallas de las notificaciones del sistema para guardar, copiar y pegar elementos de menú que se van a puntear](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Botón subir 

La mayoría de las aplicaciones de Android se basan en el botón **atrás** para navegar por la aplicación. al presionar el botón **atrás** , el usuario se ejecuta en la pantalla anterior.
Sin embargo, es posible que también quiera proporcionar un botón **arriba** que facilite a los usuarios la navegación por la pantalla principal de la aplicación. Cuando el usuario selecciona el botón **subir** , el usuario se mueve hasta un nivel superior en la jerarquía &ndash; de la aplicación, es decir, la aplicación vuelve a hacer que el usuario realice varias actividades en la pila de retroceso, en lugar de volver a la actividad visitada previamente. 

Para habilitar el **botón subir** en una segunda actividad `Toolbar` que usa como barra de acción, llame a los `SetDisplayHomeAsUpEnabled` métodos y `SetHomeButtonEnabled` en el método de `OnCreate` la segunda actividad:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

El ejemplo de código de la [barra de herramientas de support V7](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) muestra el botón **subir** en acción. Este ejemplo (que utiliza la biblioteca AppCompat que se describe a continuación) implementa una segunda actividad que usa el botón de la barra de herramientas **arriba** para la navegación jerárquica de vuelta a la actividad anterior. En este ejemplo, el `DetailActivity` botón Inicio habilita el botón **subir** realizando las siguientes `SupportActionBar` llamadas al método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Cuando el usuario navega `MainActivity` de a `DetailActivity`, `DetailActivity` muestra un botón **arriba** (flecha hacia la izquierda) como se muestra en la captura de pantalla:

[![Ejemplo de captura de pantalla de una flecha izquierda de botón arriba en la barra de herramientas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Al pulsar este botón **arriba** , la aplicación vuelve a `MainActivity`. En una aplicación más compleja con varios niveles de jerarquía, al pulsar este botón, se devolvería al usuario al siguiente nivel superior de la aplicación, en lugar de a la pantalla anterior. 



## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
