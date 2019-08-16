---
title: Control de giro
description: En este tema se describe cómo controlar los cambios de orientación de dispositivos en Xamarin. Android. En él se explica cómo trabajar con el sistema de recursos de Android para cargar automáticamente recursos para una orientación de dispositivo determinada, así como cómo controlar mediante programación los cambios de orientación.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 198d667ea52fcad4758c2845e5f2e935d1f74a0b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521123"
---
# <a name="handling-rotation"></a>Control de giro

_En este tema se describe cómo controlar los cambios de orientación de dispositivos en Xamarin. Android. En él se explica cómo trabajar con el sistema de recursos de Android para cargar automáticamente recursos para una orientación de dispositivo determinada, así como cómo controlar mediante programación los cambios de orientación._


## <a name="overview"></a>Información general

Dado que los dispositivos móviles se giran con facilidad, la rotación integrada es una característica estándar de los sistemas operativos móviles. Android proporciona un marco de trabajo sofisticado para tratar el giro dentro de las aplicaciones, si la interfaz de usuario se crea mediante declaración en XML o mediante programación en código. Cuando se controlan automáticamente los cambios de diseño declarativos en un dispositivo girado, una aplicación puede beneficiarse de la estrecha integración con el sistema de recursos de Android. En el caso del diseño mediante programación, los cambios se deben controlar manualmente. Esto permite un mayor control en tiempo de ejecución, pero a costa de más trabajo para el desarrollador. Una aplicación también puede optar por no participar en el reinicio de la actividad y tomar el control manual de los cambios de orientación.

En esta guía se examinan los siguientes temas de orientación:

- **Rotación de diseño** declarativo &ndash; Cómo usar el sistema de recursos de Android para compilar aplicaciones que tienen en cuenta la orientación, incluida la forma de cargar los diseños y drawables para las orientaciones particulares.

- **Rotación de diseño mediante programación** &ndash; Cómo agregar controles mediante programación y cómo controlar manualmente los cambios de orientación.


## <a name="handling-rotation-declaratively-with-layouts"></a>Controlar la rotación mediante declaración con diseños

Al incluir archivos en carpetas que siguen las convenciones de nomenclatura, Android carga automáticamente los archivos correspondientes cuando cambia la orientación.
Esto incluye compatibilidad con:

- *Recursos de diseño* &ndash; Especificar los archivos de diseño que se inflan para cada orientación.

- *Recursos Dibujables* &ndash; Que especifica qué drawables se cargan para cada orientación.


### <a name="layout-resources"></a>Recursos de diseño

De forma predeterminada, los archivos de Android XML (AXML) incluidos en la carpeta **recursos/diseño** se usan para representar vistas para una actividad. Los recursos de esta carpeta se utilizan para la orientación vertical y horizontal si no se proporcionan recursos de diseño adicionales específicamente para el panorama. Considere la estructura del proyecto creada por la plantilla de proyecto predeterminada:

[![Estructura predeterminada de la plantilla de proyecto](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Este proyecto crea un único archivo **Main. axml** en la carpeta **recursos/diseño** . Cuando se llama al `OnCreate` método de la actividad, se infla la vista definida en **Main. axml,** que declara un botón como se muestra en el código XML siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

Si el dispositivo se gira a la orientación horizontal, se llama de `OnCreate` nuevo al método de la actividad y se infla el mismo archivo **Main. axml** , tal y como se muestra en la siguiente captura de pantalla:

[![Misma pantalla pero con orientación horizontal](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Diseños específicos de la orientación

Además de la carpeta de diseño (que de forma predeterminada es vertical y también se puede llamar explícitamente al *Puerto de diseño* mediante la `layout-land`inclusión de una carpeta denominada), una aplicación puede definir las vistas que necesita en el entorno sin ningún cambio de código.

Supongamos que el archivo **Main. axml** contiene el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Si se agrega al proyecto una carpeta denominada layout-Land que contiene un archivo **Main. axml** adicional, si se aumenta el diseño en horizontal, se cargará en Android el archivo **Main. axml** recién agregado. Considere la versión horizontal del archivo **Main. axml** que contiene el código siguiente (para simplificar, este XML es similar a la versión vertical predeterminada del código, pero usa una cadena diferente en `TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Al ejecutar este código y girar el dispositivo de vertical a horizontal, se muestra la nueva carga de XML, como se muestra a continuación:

[![Capturas de pantallas verticales y horizontales que imprimen el modo vertical](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Recursos Dibujables

Durante la rotación, Android trata los recursos que se dibujan de manera similar a los recursos de diseño. En este caso, el sistema obtiene el drawables de las carpetas Resources **/drawable** y Resources **-Land** , respectivamente.

Por ejemplo, suponga que el proyecto incluye una imagen con el nombre Monkey. png en la carpeta Resources **/drawable** , donde se hace referencia `ImageView` a drawable desde un en XML similar al siguiente:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Supongamos también que se incluye una versión diferente de **Monkey. png** en **recursos/tierras Dibujables**. Al igual que con los archivos de diseño, cuando se gira el dispositivo, los cambios Dibujables para la orientación determinada, como se muestra a continuación:

[![Una versión diferente de Monkey. png mostrada en los modos vertical y horizontal](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Controlar la rotación mediante programación

A veces, definimos diseños en el código. Esto puede ocurrir por diversos motivos, como limitaciones técnicas, preferencias del desarrollador, etc. Cuando se agregan controles mediante programación, una aplicación debe tener en cuenta manualmente la orientación del dispositivo, que se administra automáticamente al usar recursos XML.


### <a name="adding-controls-in-code"></a>Agregar controles en el código

Para agregar controles mediante programación, una aplicación debe realizar los siguientes pasos:

- Cree un diseño.
- Establezca los parámetros de diseño.
- Crear controles.
- Establecer los parámetros de diseño del control.
- Agregue controles al diseño.
- Establezca el diseño como la vista de contenido.

Por ejemplo, considere una interfaz de usuario que consta de un `TextView` único control agregado `RelativeLayout`a, tal como se muestra en el código siguiente.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Este código crea una instancia de una `RelativeLayout` clase y establece su `LayoutParameters` propiedad. La `LayoutParams` clase es la manera de Android de encapsular cómo se colocan los controles de forma reutilizable. Una vez creada una instancia de un diseño, se pueden crear y agregar controles. Los controles también `LayoutParameters`tienen, `TextView` como en este ejemplo. Una vez creado, se agrega `RelativeLayout` a y al establecer `RelativeLayout` como la vista de contenido da como resultado que la aplicación muestre `TextView` el, tal y como se muestra: `TextView`

[![Botón incrementar contador mostrado en los modos vertical y horizontal](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Detectar orientación en el código

Si una aplicación intenta cargar una interfaz de usuario diferente para cada orientación cuando `OnCreate` se llama a (esto ocurrirá cada vez que se gire un dispositivo), debe detectar la orientación y, a continuación, cargar el código de interfaz de usuario deseado. Android tiene una clase denominada `WindowManager`, que se puede usar para determinar la rotación del dispositivo actual a través de la `WindowManager.DefaultDisplay.Rotation` propiedad, como se muestra a continuación:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Este código establece el `TextView` que se va a colocar en 100 píxeles desde la parte superior izquierda de la pantalla, animando automáticamente al nuevo diseño, cuando se gira a horizontal, como se muestra aquí:

[![El estado de vista se conserva en los modos vertical y horizontal](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Impedir el reinicio de la actividad

Además de controlar todo en `OnCreate`, una aplicación también puede impedir que una actividad se reinicie cuando cambie la orientación estableciendo `ConfigurationChanges` en el de la `ActivityAttribute` manera siguiente:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Ahora, cuando se gira el dispositivo, la actividad no se reinicia. Para controlar manualmente el cambio de orientación en este caso, una actividad puede invalidar el `OnConfigurationChanged` método y determinar la orientación desde el `Configuration` objeto que se pasa, como en la nueva implementación de la actividad siguiente:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

Aquí los `TextView's` parámetros de diseño se inicializan tanto horizontal como vertical. Las variables de clase contienen los parámetros, junto `TextView` con el propio, ya que la actividad no se volverá a crear cuando cambie la orientación. El código todavía utiliza `surfaceOrientartion` en `OnCreate` para establecer `TextView`el diseño inicial de. Después, controla `OnConfigurationChanged` todos los cambios de diseño posteriores.

Cuando se ejecuta la aplicación, Android carga los cambios de la interfaz de usuario a medida que se produce la rotación del dispositivo y no reinicia la actividad.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Impedir el reinicio de la actividad para los diseños declarativos

Los reinicios de actividad causados por la rotación de dispositivos también se pueden evitar si se define el diseño en XML. Por ejemplo, se puede usar este enfoque si se desea evitar un reinicio de actividad (por razones de rendimiento, quizás) y no es necesario cargar nuevos recursos para distintas orientaciones.

Para ello, sigue el mismo procedimiento que usamos con un diseño mediante programación. Simplemente establezca `ConfigurationChanges` `ActivityAttribute`en, como hicimos en el `CodeLayoutActivity` anterior. Cualquier código que deba ejecutarse para el cambio de orientación se puede implementar de nuevo en `OnConfigurationChanged` el método.


## <a name="maintaining-state-during-orientation-changes"></a>Mantener el estado durante los cambios de orientación

Tanto si se controla la rotación de forma declarativa como mediante programación, todas las aplicaciones de Android deben implementar las mismas técnicas para administrar el estado cuando cambia la orientación del dispositivo. La administración del estado es importante porque el sistema reinicia una actividad en ejecución cuando se gira un dispositivo Android. Android hace esto para facilitar la carga de recursos alternativos, como diseños y drawables de elementos diseñados específicamente para una orientación determinada. Cuando se reinicia, la actividad pierde cualquier estado transitorio que pueda haber almacenado en variables de clase local. Por lo tanto, si una actividad depende del estado, debe conservar su estado en el nivel de la aplicación. Una aplicación necesita controlar el guardado y la restauración de cualquier estado de aplicación que desee conservar entre los cambios de orientación.

Para obtener más información sobre el estado persistente en Android, consulte la guía del [ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md) de la actividad.


## <a name="summary"></a>Resumen

En este artículo se explica cómo usar las capacidades integradas de Android para trabajar con la rotación. En primer lugar, se explicó cómo usar el sistema de recursos de Android para crear aplicaciones con reconocimiento de orientación. A continuación, se muestra cómo agregar controles en el código y cómo controlar los cambios de orientación manualmente.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de rotación (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [Ciclo de vida de la actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Control de los cambios en tiempo de ejecución](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Cambio de orientación de pantalla rápida](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
