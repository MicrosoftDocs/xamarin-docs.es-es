---
title: Creación de recursos para diferentes pantallas
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 0a82c84b334cbfcf3ab978b5ebd0e256bcd64815
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755058"
---
# <a name="creating-resources-for-varying-screens"></a>Crear recursos para diferentes pantallas

Android se ejecuta en muchos dispositivos diferentes, cada uno con una amplia variedad de resoluciones, tamaños de pantalla y densidades de pantalla. Android realizará el escalado y el cambio de tamaño para que la aplicación funcione en estos dispositivos, pero esto puede dar lugar a una experiencia de usuario poco óptima. Por ejemplo, las imágenes podrían aparecer borrosas o pueden estar colocadas como se esperaba en una vista.

## <a name="concepts"></a>Conceptos

Es importante comprender algunos términos y conceptos para admitir varias pantallas.

- **Tamaño de pantalla** &ndash; La cantidad de espacio físico para mostrar la aplicación

- **Densidad de pantalla** &ndash; Número de píxeles de un área determinada de la pantalla. La unidad de medida típica es puntos por pulgada (PPP).

- **Solución** de &ndash; Número total de píxeles en la pantalla. Al desarrollar aplicaciones, la resolución no es tan importante como el tamaño y la densidad de la pantalla.

- **Píxel independiente de la densidad (DP)** &ndash; Unidad de medida virtual que permite diseñar diseños independientes de la densidad. Esta fórmula se usa para convertir el DP en píxeles de pantalla:

    PX &equals; DP &times; PPP 160&divide;

- **Orientación** &ndash; La orientación de la pantalla se considera horizontal cuando es más ancha que la alta. En cambio, la orientación vertical es cuando la pantalla es más alta que la ancha. La orientación puede cambiar durante la vigencia de una aplicación cuando el usuario gira el dispositivo.

Observe que los tres primeros de estos conceptos se relacionan entre &ndash; sí al aumentar la resolución sin aumentar la densidad, lo que aumentará el tamaño de la pantalla. Sin embargo, si aumentan la densidad y la resolución, el tamaño de la pantalla puede permanecer sin cambios. Esta relación entre el tamaño de la pantalla, la densidad y la resolución complica la compatibilidad con la pantalla rápidamente.

Para ayudar a abordar esta complejidad, el marco de trabajo de Android prefiere usar *píxeles independientes de la densidad (DP)* para los diseños de pantalla. Mediante el uso de píxeles independientes de densidad, los elementos de la interfaz de usuario aparecerán para que el usuario tenga el mismo tamaño físico en las pantallas con diferentes densidades.

## <a name="supporting-various-screen-sizes-and-densities"></a>Compatibilidad con varios tamaños de pantalla y densidades

Android controla la mayor parte del trabajo para presentar los diseños correctamente para cada configuración de pantalla. Sin embargo, se pueden realizar algunas acciones para ayudar al sistema a salir.

El uso de píxeles independientes de la densidad en lugar de píxeles reales en los diseños es suficiente en la mayoría de los casos para garantizar la independencia de la densidad.
Android escalará el drawables en tiempo de ejecución al tamaño adecuado.
Sin embargo, es posible que el escalado provoque que los mapas de bits parezcan borrosos. Para solucionar este problema, proporcione recursos alternativos para las diferentes densidades. Al diseñar dispositivos para varias resoluciones y densidades de pantalla, resultará más fácil empezar con las imágenes de mayor resolución o densidad y, a continuación, reducir verticalmente.

### <a name="declare-the-supported-screen-size"></a>Declarar el tamaño de pantalla compatible

La declaración del tamaño de la pantalla garantiza que solo los dispositivos compatibles puedan descargar la aplicación. Esto se logra estableciendo el elemento [Supports-Screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) en el archivo **archivo AndroidManifest. XML** . Este elemento se usa para especificar los tamaños de pantalla admitidos por la aplicación. Se considera que una pantalla determinada es compatible Si la aplicación puede colocar correctamente sus diseños en la pantalla de relleno. Al usar este elemento de manifiesto, la aplicación no se mostrará en [*Google Play*](https://play.google.com/) para los dispositivos que no cumplan las especificaciones de pantalla. Sin embargo, la aplicación todavía se ejecutará en dispositivos con pantallas no compatibles, pero los diseños pueden aparecer borrosos y con Pixelizar.

Los Sixes de pantalla admitidos se declaran en el archivo Configurations **/archivo AndroidManifest. XML** de la solución:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Edite **archivo AndroidManifest. XML** para incluir [Supports-Screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Proporcionar diseños alternativos para diferentes tamaños de pantalla

Los diseños alternativos permiten personalizar una vista para un tamaño de pantalla de específico, cambiando la posición o el tamaño de los elementos de la interfaz de usuario del componente.

A partir del nivel de API 13 (Android 3,2), los tamaños de pantalla están en desuso en favor de usar el calificador*N*DP de SW. Este nuevo calificador declara la cantidad de espacio que necesita un diseño determinado. Se recomienda que las aplicaciones que están diseñadas para ejecutarse en Android 3,2 o superior deben usar estos calificadores más recientes.

Por ejemplo, si un diseño requirió un mínimo de 700 DP de ancho de pantalla, el diseño alternativo se incluiría en una carpeta **layout-sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Carpeta de diseño para el ancho de pantalla de 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Carpeta de diseño para el ancho de pantalla de 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

Como directriz, estos son algunos números para varios dispositivos:

- **Teléfono típico** &ndash; 320 DP: un teléfono típico

- **Un dispositivo 5 "Tablet/" Tweener "** &ndash; 480 DP: por ejemplo, la nota de Samsung

- **Una tableta de 7 "** 600 DP: por ejemplo, el Nook de Barnes &amp;noble &ndash;

- **Una tableta de 10 "** &ndash; 720 DP: por ejemplo, el Xoom de Motorola

En el caso de las aplicaciones que tienen como destino niveles de API de hasta 12 (Android 3,1), los diseños deben ir en directorios que usan los calificadores **Small**/**Xlarge** **grandes**/**normales**/como generalizaciones de los distintos tamaños de pantalla que están disponibles en la mayoría de los dispositivos. Por ejemplo, en la imagen siguiente, existen recursos alternativos para los cuatro tamaños de pantalla diferentes:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos para cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos para cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

A continuación se explica cómo se comparan los calificadores de tamaño de pantalla de nivel 13 anteriores de la API anterior con los píxeles independientes de la densidad:

- 426 DP x 320 DP es **pequeño**

- 470 DP x 320 DP es **normal**

- 640 DP x 480 DP es **grande**

- 960 DP x 720 DP es **Xlarge**

Los calificadores de tamaño de pantalla más recientes en el nivel de API 13 y superior tienen una prioridad más alta que los calificadores de pantalla más antiguos de los niveles de API 12 y inferiores.
En el caso de las aplicaciones que abarcarán los niveles de API antiguos y nuevos, puede que sea necesario crear recursos alternativos con ambos conjuntos de calificadores, tal como se muestra en la siguiente captura de pantalla:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos con ambos calificadores](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos con ambos calificadores](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Proporcionar diferentes mapas de bits para diferentes densidades de pantalla

Aunque Android escalará los mapas de bits según sea necesario para un dispositivo, los propios mapas de bits no se pueden escalar o reducir verticalmente de forma elegante: pueden ser aproximados o borrosos. Si proporciona mapas de bits adecuados para la densidad de pantalla, se solucionará este problema.

Por ejemplo, la imagen siguiente es un ejemplo de problemas de diseño y apariencia que pueden producirse cuando no se proporcionan recursos de especificación de densidad.

![Capturas de pantallas sin recursos de densidad](resources-for-varying-screens-images/06-density-not-provided.png)

Compárelo con un diseño que esté diseñado con recursos específicos de la densidad:

![Capturas de pantallas con recursos específicos de la densidad](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Creación de recursos de densidad variable con Android Asset Studio

La creación de estos mapas de bits de diversas densidades puede ser un poco tediosa. Como tal, Google ha creado una utilidad en línea que puede reducir algunos de los tediosas tareas implicados en la creación de estos mapas de bits denominados [**Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Este sitio web le ayudará en la creación de mapas de bits que tienen como destino las cuatro densidades de pantalla comunes mediante el suministro de una imagen. Después, Android Asset Studio creará los mapas de bits con algunas personalizaciones y, a continuación, permitirá que se descarguen como un archivo zip.

## <a name="tips-for-multiple-screens"></a>Sugerencias para varias pantallas

Android se ejecuta en un número de dispositivos desconcertante, y la combinación de los tamaños de pantalla y las densidades de pantalla puede parecer abrumadora. Las siguientes sugerencias pueden ayudarle a minimizar el esfuerzo necesario para admitir varios dispositivos:

- **Solo diseño y desarrollo para lo que necesita** &ndash; Existen muchos dispositivos diferentes, pero algunos existen en factores de forma poco frecuentes que pueden suponer un esfuerzo importante para diseñar y desarrollar para. El panel de [**densidad y tamaño de pantalla**](https://developer.android.com/resources/dashboard/screens.html) es una página proporcionada por Google que proporciona datos sobre el desglose de la matriz de densidad de pantalla o el tamaño de la pantalla. Este desglose proporciona información sobre cómo desarrollar el esfuerzo en las pantallas de soporte técnico.

- **Usar DPS en lugar de píxeles** -píxeles se vuelve problemático a medida que cambia la densidad de la pantalla. No codificar valores de píxeles. Evite píxeles en favor de DP (píxeles independientes de la densidad).

- **Evitar** [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout) Siempre que **sea posible** &ndash; , está en desuso en el nivel de API 3 (Android 1,5) y dará como resultado diseños frágiles. 
   No debe usarse. En su lugar, intente usar widgets de diseño más flexibles como [**LinearLayout**](xref:Android.Widget.LinearLayout), [**RelativeLayout**](xref:Android.Widget.RelativeLayout)o el nuevo [**GridLayout**](xref:Android.Widget.GridLayout).

- **Elegir una orientación de diseño como predeterminada** Por ejemplo, en lugar de proporcionar los recursos alternativos y el puerto de diseño, coloque los recursos para el panorama en el diseño y los recursos de vertical en el puerto de diseño. &ndash;

- **Usar LayoutParams para el alto y el ancho** : al definir los elementos de la interfaz de usuario en un archivo de diseño XML, una aplicación Android que use los valores **wrap_content** y **fill_parent** tendrá más éxito y garantizará una apariencia adecuada en los distintos dispositivos que usar unidades independientes de píxeles o de densidad. Estos valores de dimensión hacen que Android escale los recursos de mapa de bits según corresponda. Por esta misma razón, las unidades independientes de la densidad se reservan mejor para cuando se especifican los márgenes y el relleno de los elementos de la interfaz de usuario.

## <a name="testing-multiple-screens"></a>Probar varias pantallas

Una aplicación Android debe probarse con todas las configuraciones que se admitirán. Idealmente, los dispositivos deben probarse en los propios dispositivos reales, pero en muchos casos esto no es posible o práctico.
En este caso, será útil el uso del emulador y el programa de instalación de dispositivos virtuales Android para cada configuración de dispositivo.

El Android SDK proporciona algunas máscaras del emulador que se pueden usar para crear AVD replicará el tamaño, la densidad y la resolución de muchos dispositivos.
Muchos de los proveedores de hardware proporcionan también máscaras para sus dispositivos.

Otra opción es usar los servicios de un servicio de prueba de terceros.
Estos servicios tomarán un APK, lo ejecutarán en muchos dispositivos diferentes y, a continuación, proporcionarán comentarios sobre cómo funcionó la aplicación.
