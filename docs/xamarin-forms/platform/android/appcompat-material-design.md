---
title: "incorporación de la descripción de AppCompat y el diseño de material": "en este artículo se explica cómo convertir Xamarin.Forms aplicaciones Android existentes para usar AppCompat y el diseño de material".
MS. Prod: Xamarin ms. AssetID: 045FBCDF-4D45-48BB-9911-BD3938C87D58 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/27/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="adding-appcompat-and-material-design"></a>Agregar AppCompat y el diseño de material

_Siga estos pasos para convertir Xamarin.Forms aplicaciones Android existentes para usar AppCompat y el diseño de material._

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Información general

En estas instrucciones se explica cómo actualizar las Xamarin.Forms aplicaciones Android existentes para usar la biblioteca AppCompat y habilitar el diseño de material en la versión de Android de las Xamarin.Forms aplicaciones.

### <a name="1-update-xamarinforms"></a>1. actualizarXamarin.Forms

Asegúrese de que la solución usa Xamarin.Forms 2,0 o posterior. Actualice elXamarin.Forms
  Paquete NuGet en 2,0 si es necesario.

### <a name="2-check-android-version"></a>2. comprobar la versión de Android

Asegúrese de que la plataforma de destino del proyecto de Android es Android 6,0 (Marshmallow). Compruebe las **Opciones del > del proyecto de Android > Compilar > configuración general** para asegurarse de que el marco de trabajo de corrent está seleccionado:

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. agregar nuevos temas para admitir el diseño de materiales

Cree los tres archivos siguientes en el proyecto de Android y péguelo en el contenido siguiente. Google proporciona una [Guía de estilo](https://www.google.com/design/spec/style/color.html#color-color-palette) y un [generador de paletas de colores](https://www.materialpalette.com/) para ayudarle a elegir una combinación de colores alternativa para la especificada.

**Recursos/valores/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Recursos/valores/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Se debe incluir un estilo adicional en la carpeta **Values-V21** para aplicar propiedades específicas cuando se ejecuta en un círculo de Android y versiones más recientes.

**Recursos/valores: V21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. actualizar AndroidManifest.xml

Para asegurarse de que se usa esta nueva información de tema, establezca tema en el archivo **archivo AndroidManifest** agregando `android:theme="@style/MyTheme"` (deje el resto del código XML tal como estaba).

**Propiedades/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. proporcionar diseños de pestaña y barra de herramientas

Cree archivos **. axml** y **Toolbar. axml** en el directorio **Resources/layout** y péguelo en el contenido siguiente:

**Resources/layout/Tab. axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Se han establecido algunas propiedades para las pestañas, incluidas la gravedad de la pestaña en `fill` y el modo en `fixed` .
Si tiene muchas pestañas que quiere cambiar a desplazable: Lea la documentación de Android [TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) para obtener más información.

**Resources/layout/Toolbar. axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

En estos archivos, vamos a crear un tema específico para la barra de herramientas que puede variar en la aplicación.
Para obtener más información, consulte la entrada de blog de la [barra de herramientas Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) .

### <a name="6-update-the-mainactivity"></a>6. actualizar el`MainActivity`

En Xamarin.Forms las aplicaciones existentes, la clase **MainActivity.CS** heredará de `FormsApplicationActivity` . Debe reemplazarse por `FormsAppCompatActivity` para habilitar la nueva funcionalidad.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Por último, "conectar" los nuevos diseños del paso 5 en el `OnCreate` método, como se muestra aquí:

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
