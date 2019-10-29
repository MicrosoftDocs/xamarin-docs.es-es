---
title: CardView
description: El widget CardView es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. En esta guía se explica cómo usar y personalizar CardView en aplicaciones de Xamarin. Android, a la vez que se mantiene la compatibilidad con versiones anteriores de Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 053847426d770408826297d9a80b6e38d7f6bc44
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029278"
---
# <a name="xamarinandroid-cardview"></a>CardView de Xamarin. Android

_El widget CardView es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. En esta guía se explica cómo usar y personalizar CardView en aplicaciones de Xamarin. Android, a la vez que se mantiene la compatibilidad con versiones anteriores de Android._

## <a name="overview"></a>Información general

El widget `Cardview`, introducido en Android 5,0 (Lollipop), es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. `CardView` se implementa como un widget `FrameLayout` con esquinas redondeadas y una sombra. Normalmente, un `CardView` se utiliza para presentar un elemento de una sola fila en un grupo de vistas de `ListView` o `GridView`. Por ejemplo, la siguiente captura de pantalla es un ejemplo de una aplicación de reserva de viajes que implementa tarjetas de destino de viaje basadas en `CardView`en un `ListView`desplazable:

![Aplicación de ejemplo que usa un CardView para cada destino de viaje](card-view-images/01-cardview-example.png)

En esta guía se explica cómo agregar el paquete de `CardView` al proyecto de Xamarin. Android, cómo agregar `CardView` al diseño y cómo personalizar la apariencia de `CardView` en la aplicación. Además, en esta guía se proporciona una lista detallada de los atributos de `CardView` que puede cambiar, incluidos los atributos para ayudarle a usar `CardView` en versiones de Android anteriores a la versión de Android 5,0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android 5,0 y versiones posteriores (incluido `CardView`) en aplicaciones basadas en Xamarin, es necesario lo siguiente:

- **Xamarin. android** &ndash; Xamarin. Android 4,20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

- **Android SDK** &ndash; Android 5,0 (API 21) o posterior debe instalarse a través del administrador de Android SDK.

- Se puede usar **Java jdk 1,8** &ndash; JDK 1,7 si es específicamente destino de nivel de API 23 y versiones anteriores. JDK 1,8 está disponible en [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

La aplicación también debe incluir el paquete de `Xamarin.Android.Support.v7.CardView`. Para agregar el paquete de `Xamarin.Android.Support.v7.CardView` en Visual Studio para Mac:

1. Abra el proyecto, haga clic con el botón derecho en **paquetes** y seleccione **agregar paquetes**.

2. En el cuadro de diálogo **agregar paquetes** , busque **CardView**.

3. Seleccione **biblioteca de soporte de Xamarin V7 CardView**y haga clic en **Agregar paquete**.

Para agregar el paquete de `Xamarin.Android.Support.v7.CardView` en Visual Studio:

1. Abra el proyecto, haga clic con el botón secundario en el nodo **referencias** (en el panel **Explorador de soluciones** ) y seleccione **administrar paquetes NuGet..** ..

2. Cuando se muestre el cuadro de diálogo **administrar paquetes NuGet** , escriba **CardView** en el cuadro de búsqueda.

3. Cuando aparezca la **biblioteca de compatibilidad de Xamarin V7 CardView** , haga clic en **instalar**.

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5,0, consulte [configuración de un proyecto de android 5,0](~/android/platform/lollipop.md).
Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Introducción a CardView

El `CardView` predeterminado se parece a una tarjeta blanca con esquinas redondeadas mínimas y una pequeña sombra. En el siguiente ejemplo de diseño **Main. axml** se muestra un único widget `CardView` que contiene un `TextView`:

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

En este ejemplo de diseño se crea un `CardView` predeterminado con una sola línea de texto, tal como se muestra en la siguiente captura de pantalla:

[![captura de pantalla de CardView con fondo blanco y línea de texto](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

En este ejemplo, el estilo de la aplicación se establece en el tema de material claro (`Theme.Material.Light`) para que las sombras y los bordes del `CardView` sean más fáciles de ver. Para obtener más información sobre cómo crear aplicaciones para Android 5,0, consulte el [tema material](~/android/user-interface/material-theme.md). En la siguiente sección, veremos cómo personalizar `CardView` para una aplicación.

## <a name="customizing-cardview"></a>Personalizar CardView

Puede modificar los atributos básicos del `CardView` para personalizar la apariencia del `CardView` en la aplicación. Por ejemplo, la elevación del `CardView` se puede aumentar para convertir una sombra más grande (lo que hace que la carta parezca flotar más arriba que el fondo). Además, el radio de esquina se puede aumentar para que las esquinas de la tarjeta se redondeen más.

En el siguiente ejemplo de diseño, se usa un `CardView` personalizado para crear una simulación de una fotografía de impresión (una "instantánea"). Se agrega un `ImageView` al `CardView` para mostrar la imagen y se coloca un `TextView` debajo del `ImageView` para mostrar el título de la imagen.
En este diseño de ejemplo, el `CardView` tiene las siguientes personalizaciones:

- El `cardElevation` se aumenta a 4DP para convertir una sombra mayor.
- El `cardCornerRadius` se aumenta a 5dp para que las esquinas aparezcan más redondeadas.

Dado que la biblioteca de compatibilidad de Android V7 proporciona `CardView`, sus atributos no están disponibles en el espacio de nombres `android:`. Por lo tanto, debe definir su propio espacio de nombres XML y usar dicho espacio de nombres como `CardView` prefijo del atributo. En el ejemplo de diseño siguiente, usaremos esta línea para definir un espacio de nombres denominado `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Puede llamar a este espacio de nombres `card_view` o incluso `myapp` si lo desea (solo es accesible dentro del ámbito de este archivo). Sea cual sea la opción que elija para llamar a este espacio de nombres, debe usarlo para prefijar el `CardView` atributo que desea modificar. En este ejemplo de diseño, el espacio de nombres `cardview` es el prefijo de `cardElevation` y `cardCornerRadius`:

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

Cuando este ejemplo de diseño se usa para mostrar una imagen en una aplicación de visualización fotográfica, el `CardView` tiene la apariencia de una instantánea de fotografía, tal como se muestra en la siguiente captura de pantalla:

[![CardView con una imagen y un título por debajo de la imagen](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Esta captura de pantalla se toma de la aplicación de ejemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , que usa un widget `RecyclerView` para presentar una lista de desplazamiento de `CardView` imágenes para ver las fotos. Para obtener más información sobre `RecyclerView`, consulte la guía de [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Tenga en cuenta que un `CardView` puede mostrar más de una vista secundaria en su área de contenido. Por ejemplo, en el ejemplo anterior de la aplicación de visualización de fotografías, el área de contenido se compone de una `ListView` que contiene un `ImageView` y un `TextView`. Aunque las instancias de `CardView` suelen estar organizadas verticalmente, también puede organizarlas horizontalmente (vea [crear un estilo de vista personalizado](~/android/user-interface/material-theme.md#customview) para obtener una captura de pantalla de ejemplo).

### <a name="cardview-layout-options"></a>Opciones de diseño CardView

`CardView` diseños se pueden personalizar estableciendo uno o más atributos que afecten a su relleno, elevación, radio de redondeo y color de fondo:

[![diagrama de atributos CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo también se puede cambiar dinámicamente llamando a un método homólogo `CardView` (para obtener más información sobre `CardView` métodos, vea la referencia de la [clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Tenga en cuenta que estos atributos (excepto el color de fondo) aceptan un valor de dimensión, que es un número decimal seguido de la unidad. Por ejemplo, `11.5dp` especifica píxeles de 11,5 independientes de la densidad.

#### <a name="padding"></a>Relleno

`CardView` ofrece cinco atributos de relleno para colocar el contenido dentro de la tarjeta. Puede establecerlos en el XML de diseño o puede llamar a métodos análogos en el código:

[![diagrama de atributos CardView padding](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Los atributos de relleno se explican de la siguiente manera:

- `contentPadding` &ndash; relleno interior entre las vistas secundarias de la `CardView` y todos los bordes de la tarjeta.

- `contentPaddingBottom` &ndash; relleno interior entre las vistas secundarias del `CardView` y el borde inferior de la tarjeta.

- `contentPaddingLeft` &ndash; relleno interior entre las vistas secundarias del `CardView` y el borde izquierdo de la tarjeta.

- `contentPaddingRight` &ndash; relleno interior entre las vistas secundarias del `CardView` y el borde derecho de la tarjeta.

- `contentPaddingTop` &ndash; relleno interior entre las vistas secundarias de la `CardView` y el borde superior de la tarjeta.

Los atributos de relleno de contenido son relativos al límite del área de contenido en lugar de a cualquier widget determinado ubicado en el área de contenido.
Por ejemplo, si `contentPadding` ha aumentado suficientemente en la aplicación de visualización fotográfica, el `CardView` recortaría tanto la imagen como el texto que se muestra en la tarjeta.

#### <a name="elevation"></a>Indicador

`CardView` ofrece dos atributos de elevación para controlar su elevación y, como resultado, el tamaño de su sombra:

[![diagrama de atributos de elevación CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Los atributos de elevación se explican de la siguiente manera:

- `cardElevation` &ndash; la elevación de la `CardView` (representa su eje Z).

- `cardMaxElevation` &ndash; el valor máximo de la elevación del `CardView`.

Los valores más grandes de `cardElevation` aumentan el tamaño de la sombra para que `CardView` parezca que flotan por encima del fondo. El atributo `cardElevation` también determina el orden de dibujo de las vistas superpuestas; es decir, el `CardView` se dibujará en otra vista superpuesta con una configuración de elevación superior y encima de las vistas superpuestas con una configuración de elevación inferior.
La configuración `cardMaxElevation` es útil cuando la aplicación cambia la elevación dinámicamente &ndash; impide que la sombra se extienda más allá del límite que defina con esta configuración.

#### <a name="corner-radius-and-background-color"></a>Color de fondo y radio de esquina

`CardView` ofrece atributos que se pueden usar para controlar el radio de esquina y el color de fondo. Estas dos propiedades permiten cambiar el estilo global del `CardView`:

[![diagrama de radio de esquina de CardView y atributos de color de fondo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Estos atributos se explican de la siguiente manera:

- `cardCornerRadius` &ndash; el radio de redondeo de todas las esquinas de la `CardView`.

- `cardBackgroundColor` &ndash; el color de fondo de la `CardView`.

En este diagrama, `cardCornerRadius` se establece en un 10dp redondeado y `cardBackgroundColor` se establece en `"#FFFFCC"` (amarillo claro).

## <a name="compatibility"></a>Compatibilidad

Puede usar `CardView` en versiones de Android anteriores a Android 5,0 Lollipop. Dado que `CardView` forma parte de la biblioteca de compatibilidad de Android V7, puede usar `CardView` con Android 2,1 (nivel de API 7) y versiones posteriores.
Sin embargo, debe instalar el paquete de `Xamarin.Android.Support.v7.CardView` como se describe en [requisitos](#requirements)anteriores.

`CardView` exhibe un comportamiento ligeramente diferente en los dispositivos antes del círculo (nivel de API 21):

- `CardView` usa una implementación de instantáneas mediante programación que agrega relleno adicional.

- `CardView` no recorta las vistas secundarias que se intersecan con las esquinas redondeadas del `CardView`.

Para ayudar a administrar estas diferencias de compatibilidad, `CardView` proporciona varios atributos adicionales que se pueden configurar en el diseño:

- `cardPreventCornerOverlap` &ndash; establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en versiones anteriores de Android (nivel de API 20 y versiones anteriores). Esta configuración evita que `CardView` contenido se interseque con las esquinas redondeadas del `CardView`.

- `cardUseCompatPadding` &ndash; establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en versiones de Android en o superior al nivel de API 21. Si desea usar `CardView` en dispositivos que tienen un círculo anterior y tienen el mismo aspecto en el círculo (o posterior), establezca este atributo en `true`. Cuando este atributo está habilitado, `CardView` agrega relleno adicional para dibujar sombras cuando se ejecuta en dispositivos con un círculo previo. Esto ayuda a superar las diferencias en el relleno que se introducen cuando se aplican las implementaciones de instantáneas de programación en el círculo previo.

Para obtener más información sobre cómo mantener la compatibilidad con versiones anteriores de Android, vea [mantener la compatibilidad](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Resumen

En esta guía se presentó el nuevo widget de `CardView` incluido en Android 5,0 (Lollipop). Se ha mostrado la apariencia de la `CardView` predeterminada y se ha explicado cómo personalizar `CardView` cambiando su elevación, redondez, relleno de contenido y color de fondo. En él se enumeran los atributos de diseño de `CardView` (con diagramas de referencia) y se explica cómo usar `CardView` en dispositivos Android anteriores a Android 5,0 Lollipop. Para obtener más información sobre `CardView`, vea la referencia de la [clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [Referencia de la clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
