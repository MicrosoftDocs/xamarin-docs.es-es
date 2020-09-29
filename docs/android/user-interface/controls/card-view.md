---
title: CardView
description: El widget CardView es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. En esta guía se explica cómo usar y personalizar CardView en aplicaciones de Xamarin. Android, a la vez que se mantiene la compatibilidad con versiones anteriores de Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d378b48741b6640f342d51e6eaa16aaf1a2f74a5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457463"
---
# <a name="xamarinandroid-cardview"></a>CardView de Xamarin. Android

_El widget CardView es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. En esta guía se explica cómo usar y personalizar CardView en aplicaciones de Xamarin. Android, a la vez que se mantiene la compatibilidad con versiones anteriores de Android._

## <a name="overview"></a>Información general

El `Cardview` widget, introducido en Android 5,0 (Lollipop), es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. `CardView` se implementa como un `FrameLayout` widget con esquinas redondeadas y una sombra. Normalmente, `CardView` se usa para presentar un elemento de una sola fila en `ListView` un `GridView` grupo de vistas o. Por ejemplo, la siguiente captura de pantalla es un ejemplo de una aplicación de reserva de viajes que implementa `CardView` tarjetas de destino de viaje basadas en desplazable `ListView` :

![Aplicación de ejemplo que usa un CardView para cada destino de viaje](card-view-images/01-cardview-example.png)

En esta guía se explica cómo agregar el `CardView` paquete al proyecto de Xamarin. Android, cómo agregarlo `CardView` a su diseño y cómo personalizar la apariencia de `CardView` en la aplicación. Además, en esta guía se proporciona una lista detallada de `CardView` los atributos que puede cambiar, incluidos los atributos que le ayudarán a usar `CardView` en versiones de Android anteriores al círculo 5,0 de Android.

<a name="requirements"></a>

## <a name="requirements"></a>Requisitos

Lo siguiente es necesario para usar las nuevas características de Android 5,0 y versiones posteriores (incluido `CardView` ) en las aplicaciones basadas en Xamarin:

- **Xamarin. Android** &ndash; xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK** &ndash; Android 5,0 (API 21) o posterior debe instalarse a través del administrador de Android SDK.

- **Java JDK 1,8** &ndash; Se puede usar JDK 1,7 Si tiene como destino específicamente el nivel de API 23 y versiones anteriores. JDK 1,8 está disponible en [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

La aplicación también debe incluir el `Xamarin.Android.Support.v7.CardView` paquete. Para agregar el `Xamarin.Android.Support.v7.CardView` paquete en Visual Studio para Mac:

1. Abra el proyecto, haga clic con el botón derecho en **paquetes** y seleccione **agregar paquetes**.

2. En el cuadro de diálogo **agregar paquetes** , busque **CardView**.

3. Seleccione **biblioteca de soporte de Xamarin V7 CardView**y haga clic en **Agregar paquete**.

Para agregar el `Xamarin.Android.Support.v7.CardView` paquete en Visual Studio:

1. Abra el proyecto, haga clic con el botón secundario en el nodo **referencias** (en el panel **Explorador de soluciones** ) y seleccione **administrar paquetes NuGet..**..

2. Cuando se muestre el cuadro de diálogo **administrar paquetes NuGet** , escriba **CardView** en el cuadro de búsqueda.

3. Cuando aparezca la **biblioteca de compatibilidad de Xamarin V7 CardView** , haga clic en **instalar**.

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5,0, consulte [configuración de un proyecto de android 5,0](~/android/platform/lollipop.md).
Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Introducción a CardView

El valor predeterminado es `CardView` similar a una tarjeta blanca con esquinas redondeadas mínimas y una pequeña sombra. En el siguiente ejemplo de diseño **Main. axml** se muestra un único `CardView` widget que contiene `TextView` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Si usa este código XML para reemplazar el contenido existente de **Main. axml**, asegúrese de comentar cualquier código de **MainActivity.CS** que haga referencia a los recursos del XML anterior.

En este ejemplo de diseño se crea un valor predeterminado `CardView` con una sola línea de texto, tal como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de CardView con fondo blanco y línea de texto](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

En este ejemplo, el estilo de la aplicación se establece en el tema de material claro ( `Theme.Material.Light` ) para que las `CardView` sombras y los bordes sean más fáciles de ver. Para obtener más información sobre cómo crear aplicaciones para Android 5,0, consulte el [tema material](~/android/user-interface/material-theme.md). En la siguiente sección, veremos cómo personalizar `CardView` para una aplicación.

## <a name="customizing-cardview"></a>Personalizar CardView

Puede modificar los atributos básicos `CardView` para personalizar la apariencia de `CardView` en la aplicación. Por ejemplo, la elevación de `CardView` se puede aumentar para convertir una sombra más grande (lo que hace que la carta parezca flotar más arriba que el fondo). Además, el radio de esquina se puede aumentar para que las esquinas de la tarjeta se redondeen más.

En el siguiente ejemplo de diseño, `CardView` se utiliza un personalizado para crear una simulación de una fotografía de impresión (una "instantánea"). `ImageView`Se agrega un al `CardView` para mostrar la imagen y `TextView` se coloca debajo `ImageView` de para mostrar el título de la imagen.
En este diseño de ejemplo, el `CardView` tiene las siguientes personalizaciones:

- `cardElevation`Se aumenta a 4DP para convertir una sombra mayor.
- `cardCornerRadius`Se aumenta a 5dp para que las esquinas aparezcan más redondeadas.

Dado que `CardView` se proporciona mediante la biblioteca de compatibilidad de Android V7, sus atributos no están disponibles en el `android:` espacio de nombres. Por lo tanto, debe definir su propio espacio de nombres XML y usar dicho espacio de nombres como `CardView` prefijo del atributo. En el ejemplo de diseño siguiente, usaremos esta línea para definir un espacio de nombres denominado `cardview` :

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Puede llamar a este espacio `card_view` de nombres o incluso `myapp` si elige (solo es accesible en el ámbito de este archivo). Sea cual sea la opción que elija para llamar a este espacio de nombres, debe usarlo para prefijar el `CardView` atributo que desea modificar. En este ejemplo de diseño, el `cardview` espacio de nombres es el prefijo para `cardElevation` y `cardCornerRadius` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Cuando este ejemplo de diseño se usa para mostrar una imagen en una aplicación de visualización fotográfica, `CardView` tiene la apariencia de una instantánea de fotografía, como se muestra en la siguiente captura de pantalla:

[![CardView con una imagen y un título por debajo de la imagen](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Esta captura de pantalla se toma de la aplicación de ejemplo [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) , que usa un `RecyclerView` widget para presentar una lista desplazable de `CardView` imágenes para ver las fotos. Para obtener más información acerca de `RecyclerView` , consulte la guía de [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Tenga en cuenta que `CardView` puede mostrar más de una vista secundaria en su área de contenido. Por ejemplo, en el ejemplo anterior de la aplicación de visualización de fotografías, el área de contenido se compone de un `ListView` que contiene un `ImageView` y un `TextView` . Aunque `CardView` las instancias suelen estar organizadas verticalmente, también puede organizarlas horizontalmente (vea [crear un estilo de vista personalizado](~/android/user-interface/material-theme.md#customview) para obtener una captura de pantalla de ejemplo).

### <a name="cardview-layout-options"></a>Opciones de diseño CardView

`CardView` los diseños se pueden personalizar estableciendo uno o más atributos que afecten a su relleno, elevación, radio de redondeo y color de fondo:

[![Diagrama de atributos CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo también se puede cambiar dinámicamente llamando a un `CardView` método homólogo (para obtener más información sobre `CardView` los métodos, vea la referencia de la [clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Tenga en cuenta que estos atributos (excepto el color de fondo) aceptan un valor de dimensión, que es un número decimal seguido de la unidad. Por ejemplo, `11.5dp` especifica 11,5 píxeles independientes de la densidad.

#### <a name="padding"></a>Relleno

`CardView` ofrece cinco atributos de relleno para colocar el contenido dentro de la tarjeta. Puede establecerlos en el XML de diseño o puede llamar a métodos análogos en el código:

[![Diagrama de atributos de relleno CardView](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Los atributos de relleno se explican de la siguiente manera:

- `contentPadding`&ndash;Relleno interior entre las vistas secundarias de `CardView` y todos los bordes de la tarjeta.

- `contentPaddingBottom`&ndash;Relleno interior entre las vistas secundarias de `CardView` y el borde inferior de la tarjeta.

- `contentPaddingLeft`&ndash;Relleno interior entre las vistas secundarias de `CardView` y el borde izquierdo de la tarjeta.

- `contentPaddingRight`&ndash;Relleno interior entre las vistas secundarias de `CardView` y el borde derecho de la tarjeta.

- `contentPaddingTop`&ndash;Relleno interior entre las vistas secundarias de `CardView` y el borde superior de la tarjeta.

Los atributos de relleno de contenido son relativos al límite del área de contenido en lugar de a cualquier widget determinado ubicado en el área de contenido.
Por ejemplo, si `contentPadding` se ha aumentado suficientemente en la aplicación de visualización fotográfica, `CardView` recortaría tanto la imagen como el texto que se muestra en la tarjeta.

#### <a name="elevation"></a>Elevation

`CardView` ofrece dos atributos de elevación para controlar su elevación y, como resultado, el tamaño de su sombra:

[![Diagrama de atributos de elevación CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Los atributos de elevación se explican de la siguiente manera:

- `cardElevation`&ndash;Elevación de `CardView` (representa su eje Z).

- `cardMaxElevation`&ndash;Valor máximo de la `CardView` elevación de.

Los valores más grandes de `cardElevation` aumentan el tamaño de la sombra para que `CardView` parezca flotar más arriba encima del fondo. El `cardElevation` atributo también determina el orden de dibujo de las vistas superpuestas; es decir, se `CardView` dibujará en otra vista superpuesta con una configuración de elevación superior y encima de las vistas superpuestas con una configuración de elevación inferior.
La `cardMaxElevation` configuración es útil cuando la aplicación cambia la elevación dinámicamente &ndash; , lo que impide que la sombra se extienda más allá del límite que se define con esta configuración.

#### <a name="corner-radius-and-background-color"></a>Color de fondo y radio de esquina

`CardView` proporciona atributos que se pueden usar para controlar el radio de esquina y su color de fondo. Estas dos propiedades permiten cambiar el estilo general del `CardView` :

[![Diagrama de los atributos de color de fondo y radio de la esquina CardView](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Estos atributos se explican de la siguiente manera:

- `cardCornerRadius`&ndash;Radio de redondeo de todas las esquinas de `CardView` .

- `cardBackgroundColor`&ndash;Color de fondo del `CardView` .

En este diagrama, `cardCornerRadius` se establece en un 10dp redondeado y `cardBackgroundColor` se establece en `"#FFFFCC"` (amarillo claro).

## <a name="compatibility"></a>Compatibilidad

Puede usar `CardView` en versiones de Android anteriores a android 5,0 Lollipop. Dado `CardView` que forma parte de la biblioteca de compatibilidad de Android V7, puede usar `CardView` con Android 2,1 (nivel de API 7) y versiones posteriores.
Sin embargo, debe instalar el `Xamarin.Android.Support.v7.CardView` paquete tal y como se describe en [requisitos](#requirements)anteriores.

`CardView` exhibe un comportamiento ligeramente diferente en los dispositivos antes del círculo (nivel de API 21):

- `CardView` usa una implementación de instantáneas mediante programación que agrega relleno adicional.

- `CardView` no recorta las vistas secundarias que forman una intersección con las  `CardView` esquinas redondeadas de.

Para ayudar a administrar estas diferencias de compatibilidad, `CardView` proporciona varios atributos adicionales que puede configurar en el diseño:

- `cardPreventCornerOverlap`&ndash;Establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en versiones anteriores de Android (nivel de API 20 y anteriores). Esta configuración evita que `CardView` el contenido se interseque con las `CardView` esquinas redondeadas.

- `cardUseCompatPadding`&ndash;Establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en versiones de Android en o superior al nivel de API 21. Si desea usar `CardView` en dispositivos con un círculo anterior y hacer que tenga el mismo aspecto en el círculo (o en versiones posteriores), establezca este atributo en `true` . Cuando este atributo está habilitado, `CardView` agrega relleno adicional para dibujar sombras cuando se ejecuta en dispositivos con un círculo previo. Esto ayuda a superar las diferencias en el relleno que se introducen cuando se aplican las implementaciones de instantáneas de programación en el círculo previo.

Para obtener más información sobre cómo mantener la compatibilidad con versiones anteriores de Android, vea [mantener la compatibilidad](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Resumen

En esta guía se presentó el nuevo `CardView` Widget incluido en Android 5,0 (Lollipop). Se ha demostrado la `CardView` apariencia predeterminada y se ha explicado cómo personalizar cambiando `CardView` la elevación, el redondeo de la esquina, el relleno de contenido y el color de fondo. En él se enumeran los `CardView` atributos de diseño (con diagramas de referencia) y se explica cómo usar `CardView` en dispositivos Android anteriores a Android 5,0 Lollipop. Para obtener más información sobre `CardView` , vea la referencia de la [clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView (ejemplo)](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [Referencia de la clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)