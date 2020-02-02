---
title: Tema de materiales
description: Cómo aplicar un tema a la aplicación de Xamarin. Android con el tema material
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 809f6241b3a17f63fe3077f896095c303e1dfd2e
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940837"
---
# <a name="material-theme"></a>Tema de materiales

El *tema material* es un estilo de interfaz de usuario que determina la apariencia y el funcionamiento de las vistas y actividades a partir de Android 5,0 (Lollipop). El tema material está integrado en Android 5,0, por lo que la interfaz de usuario del sistema y las aplicaciones lo usan. El tema material no es un "tema" en el sentido de una opción de apariencia para todo el sistema que un usuario puede elegir de forma dinámica en un menú de configuración. En su lugar, el tema material puede considerarse como un conjunto de estilos base integrados relacionados que puede usar para personalizar la apariencia de la aplicación.

Android proporciona tres tipos de tema de material:

- `Theme.Material` &ndash; versión oscura del tema material; Este es el tipo predeterminado en Android 5,0.

- `Theme.Material.Light` &ndash; versión ligera del tema material.

- `Theme.Material.Light.DarkActionBar` &ndash; versión ligera del tema material, pero con una barra de acciones oscura.

Aquí se muestran ejemplos de estos tipos de tema de material:

[![capturas de pantallas de ejemplo del tema oscuro, el tema claro y el tema Barra de acciones oscuro](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

Puede derivar del tema material para crear su propio tema, invalidando algunos o todos los atributos de color. Por ejemplo, puede crear un tema que se derive de `Theme.Material.Light`, pero invalida el color de la barra de la aplicación para que coincida con el color de la marca. También puede aplicar un estilo a vistas individuales; por ejemplo, puede crear un estilo para [CardView](~/android/user-interface/controls/card-view.md) que tenga más esquinas redondeadas y use un color de fondo más oscuro.

Puede usar un solo tema para una aplicación completa o puede usar distintos temas para diferentes pantallas (actividades) en una aplicación. En las capturas de pantallas anteriores, por ejemplo, una sola aplicación usa un tema diferente para cada actividad con el fin de mostrar las combinaciones de colores integradas. Los botones de radio cambian la aplicación a diferentes actividades y, como resultado, muestran temas diferentes.

Dado que el tema material solo se admite en Android 5,0 y versiones posteriores, no se puede usar (o un tema personalizado derivado del tema material) para que la aplicación se ejecute en versiones anteriores de Android. Sin embargo, puede configurar la aplicación para que use el tema material en dispositivos Android 5,0 y revertir correctamente a un tema anterior cuando se ejecute en versiones anteriores de Android (consulte la sección de [compatibilidad](#compatibility) de este artículo para obtener más información).

## <a name="requirements"></a>Requisitos de

Lo siguiente es necesario para usar las nuevas características de tema de material de Android 5,0 en aplicaciones basadas en Xamarin:

- **Xamarin. android** &ndash; Xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) o posterior debe instalarse a través del administrador de Android SDK.

- Se puede usar **Java jdk 1,8** &ndash; JDK 1,7 Si tiene como destino específicamente el nivel de API 23 y versiones anteriores. JDK 1,8 está disponible en [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5,0, consulte [configuración de un proyecto de android 5,0](~/android/platform/lollipop.md).

## <a name="using-the-built-in-themes"></a>Usar los temas integrados

La forma más sencilla de usar el tema material es configurar la aplicación para que use un tema integrado sin personalización. Si no desea configurar explícitamente un tema, la aplicación se establecerá de forma predeterminada en `Theme.Material` (el tema oscuro). Si la aplicación tiene solo una actividad, puede configurar un tema en el nivel de actividad. Si la aplicación tiene varias actividades, puede configurar un tema en el nivel de aplicación para que use el mismo tema en todas las actividades, o puede asignar distintos temas a actividades diferentes. En las siguientes secciones se explica cómo configurar temas en el nivel de la aplicación y en el nivel de actividad.

### <a name="theming-an-application"></a>Crear una aplicación

Para configurar una aplicación completa para que use un tipo de tema material, establezca el atributo `android:theme` del nodo de aplicación en **archivo AndroidManifest. XML** en uno de los siguientes:

- `@android:style/Theme.Material` &ndash; tema oscuro.

- `@android:style/Theme.Material.Light` &ndash; tema claro.

- `@android:style/Theme.Material.Light.DarkActionBar` &ndash; tema claro con barra de acciones oscuras.

En el ejemplo siguiente se configura la aplicación *MyApp* para usar el tema claro:

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

Como alternativa, puede establecer el atributo de `Theme` de la aplicación en **AssemblyInfo.CS** (o **Properties.CS**). Por ejemplo:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

Cuando el tema de la aplicación se establece en `@android:style/Theme.Material.Light`, todas las actividades de *MyApp* se mostrarán con `Theme.Material.Light`.

### <a name="theming-an-activity"></a>Una actividad

Para aplicar un tema a una actividad, agregue un valor `Theme` al atributo `[Activity]` encima de la declaración de actividad y asigne `Theme` al tipo de tema material que desee usar. En el ejemplo siguiente se temas de una actividad con `Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

Otras actividades de esta aplicación usarán la combinación de colores predeterminados `Theme.Material` oscuro (o, si está configurado, la configuración del tema de la aplicación).

<a name="customtheme" />

## <a name="using-custom-themes"></a>Uso de temas personalizados

Puede mejorar su marca mediante la creación de un tema personalizado que le permite aplicar estilos a la aplicación con los colores de la marca&rsquo;s. Para crear un tema personalizado, se define un nuevo estilo que se deriva de un tipo de tema material integrado y se reemplazan los atributos de color que se desean cambiar. Por ejemplo, puede definir un tema personalizado que deriva de `Theme.Material.Light.DarkActionBar` y cambia el color de fondo de la pantalla a beige en lugar de a blanco.

El tema material expone los siguientes atributos de diseño para la personalización:

- `colorPrimary` &ndash; el color de la barra de la aplicación.

- `colorPrimaryDark` &ndash; el color de las barras de la barra de estado y la aplicación contextual; Normalmente, se trata de una versión oscura de `colorPrimary`.

- `colorAccent` &ndash; el color de los controles de interfaz de usuario, como las casillas, los botones de radio y los cuadros de texto de edición.

- `windowBackground` &ndash; el color del fondo de la pantalla.

- `textColorPrimary` &ndash; el color del texto de la interfaz de usuario en la barra de la aplicación.

- `statusBarColor` &ndash; el color de la barra de estado.

- `navigationBarColor` &ndash; el color de la barra de navegación.

Estas áreas de pantalla se etiquetan en el diagrama siguiente:

[![diagrama de atributos y sus áreas de pantalla asociadas](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

De forma predeterminada, `statusBarColor` se establece en el valor de `colorPrimaryDark`. Puede establecer `statusBarColor` en un color sólido, o bien puede establecerlo en `@android:color/transparent` para que la barra de estado sea transparente. También se puede hacer transparente la barra de navegación si se establece `navigationBarColor` en `@android:color/transparent`.

### <a name="creating-a-custom-app-theme"></a>Creación de un tema de aplicación personalizado

Puede crear un tema de aplicación personalizado mediante la creación y modificación de archivos en la carpeta **recursos** del proyecto de la aplicación. Para aplicar estilo a la aplicación con un tema personalizado, siga estos pasos:

- Cree un archivo **colors. XML** en **recursos/valores** &mdash; use este archivo para definir los colores del tema personalizado. Por ejemplo, puede pegar el código siguiente en el **archivo colors. XML** para ayudarle a comenzar:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

- Modifique este archivo de ejemplo para definir los nombres y códigos de color para los recursos de color que utilizará en el tema personalizado.

- Cree una carpeta **Resources/Values-V21** . En esta carpeta, cree un archivo **styles. XML** :

    [![ubicación de styles. XML en la carpeta Resources/Values-21. XML](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    Tenga en cuenta que **Resources/Values-V21** es específico de android 5,0 &ndash; versiones anteriores de Android no leerán los archivos de esta carpeta.

- Agregue un nodo de `resources` a **styles. XML** y defina un nodo de `style` con el nombre del tema personalizado. Por ejemplo, este es un archivo **styles. XML** que define *MyCustomTheme* (derivado del estilo de tema `Theme.Material.Light` integrado):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

- Llegados a este punto, una aplicación que usa *MyCustomTheme* mostrará el tema stock `Theme.Material.Light` sin las personalizaciones:

    [![apariencia del tema personalizado antes de las personalizaciones](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

- Agregue personalizaciones de color a **styles. XML** definiendo los colores de los atributos de diseño que desee cambiar. Por ejemplo, para cambiar el color de la barra de la aplicación a `my_blue` y cambiar el color de los controles de interfaz de usuario a `my_purple`, agregue invalidaciones de color a **styles. XML** que hagan referencia a recursos configurados en Colors **. XML**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

Una vez realizados estos cambios, una aplicación que use *MyCustomTheme* mostrará un color de barra de la aplicación en `my_blue` y controles de interfaz de usuario de `my_purple`, pero usará la combinación de colores `Theme.Material.Light` en cualquier otro lugar:

[![apariencia del tema personalizado después de las personalizaciones](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

En este ejemplo, *MyCustomTheme* toma los colores de `Theme.Material.Light` para el color de fondo, la barra de estado y los colores de texto, pero cambia el color de la barra de la aplicación a `my_blue` y establece el color del botón de radio en `my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>Crear un estilo de vista personalizado

Android 5,0 también permite aplicar estilo a una vista individual. Después de crear los **colores. XML** y **styles. XML** (como se describe en la sección anterior), puede Agregar un estilo de vista a **styles. XML**.
Para aplicar estilo a una vista individual, siga estos pasos:

- Edite **Resources/Values-V21/Styles. XML** y agregue un nodo `style` con el nombre del estilo de vista personalizado. Establezca los atributos de color personalizado para la vista en este `style` nodo. Por ejemplo, para crear un estilo [CardView](~/android/user-interface/controls/card-view.md) personalizado que tenga más esquinas redondeadas y use `my_blue` como color de fondo de la tarjeta, agregue un nodo `style` a **styles. XML** (dentro del nodo `resources`) y configure el color de fondo y el radio de esquina:

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

- En el diseño, establezca el atributo `style` de esa vista para que coincida con el nombre de estilo personalizado que eligió en el paso anterior. Por ejemplo:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

En la captura de pantalla siguiente se muestra un ejemplo de la `CardView` predeterminada (mostrada a la izquierda) en comparación con una `CardView` con el estilo de `CardView.MyBlue` personalizado (que se muestra a la derecha):

[![ejemplos de CardView y CardView personalizados predeterminados](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

En este ejemplo, el `CardView` personalizado se muestra con el color de fondo `my_blue` y un radio de esquina 18dp.

## <a name="compatibility"></a>Compatibilidad

Para aplicar estilo a la aplicación de forma que use el tema material en Android 5,0, pero se revierte automáticamente a un estilo compatible con las versiones anteriores de Android, siga estos pasos:

- Defina un tema personalizado en **Resources/Values-V21/Styles. XML** que se derive de un estilo material Theme. Por ejemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- Defina un tema personalizado en **Resources/Values/Styles. XML** que se derive de un tema anterior, pero que use el mismo nombre de tema que el anterior. Por ejemplo:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

- En **archivo AndroidManifest. XML**, configure la aplicación con el nombre del tema personalizado. 
    Por ejemplo:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

- Como alternativa, puede aplicar un estilo a una actividad específica mediante el tema personalizado:

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

Si el tema usa colores definidos en un archivo **colors. XML** , asegúrese de colocar este archivo en **recursos/valores** (en lugar de en **recursos/valores-V21**) para que ambas versiones del tema personalizado puedan tener acceso a las definiciones de color.

Cuando la aplicación se ejecuta en un dispositivo Android 5,0, usará la definición de tema especificada en **Resources/Values-V21/Styles. XML**. Cuando esta aplicación se ejecuta en dispositivos Android más antiguos, se revierte automáticamente a la definición de tema especificada en **Resources/Values/Styles. XML**.

Para obtener más información sobre la compatibilidad de temas con versiones anteriores de Android, consulte [recursos alternativos](~/android/app-fundamentals/resources-in-android/alternate-resources.md).

## <a name="summary"></a>Resumen

En este artículo se presentó el nuevo estilo de interfaz de usuario de tema de material incluido en Android 5,0 (Lollipop). Se han descrito los tres tipos de tema de material integrados que se pueden usar para aplicar estilo a la aplicación, se ha explicado cómo crear un tema personalizado para la personalización de marca de la aplicación y se ha proporcionado un ejemplo de cómo aplicar un tema a una vista individual. Por último, en este artículo se explica cómo usar el tema material en la aplicación manteniendo la compatibilidad con versiones anteriores de Android.

## <a name="related-links"></a>Vínculos relacionados

- [ThemeSwitcher (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-themeswitcher)
- [Introducción al círculo](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [Recursos alternativos](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Círculo de Android](https://developer.android.com/about/versions/lollipop)
- [Desarrollador de gráficos circulares de Android](https://developer.android.com/about/versions/pie/)
- [Diseño de materiales](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [Principios del diseño de materiales](https://material.io/design/)
- [Mantener la compatibilidad](https://developer.android.com/training/backward-compatible-ui/)
