---
title: Trabajar con la localización de watchos en Xamarin
description: En este documento se describe cómo localizar aplicaciones de watchos compiladas con Xamarin. Se describen las aplicaciones de inspección, las extensiones de inspección, las cadenas en el código, el texto de guion gráfico, las pruebas, etc.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 82b739697705ac4c90390a36405d755a5f523159
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028405"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Trabajar con la localización de watchos en Xamarin

_Adaptación de las aplicaciones de watchos para varios idiomas_

![](localization-images/both-languages-sml.png "Apple Watch displaying localized content")

las aplicaciones de watchos se localizan mediante los métodos estándar de iOS:

- Usar el **identificador de localización** en elementos de guion gráfico
- archivos **. Strings** asociados al guion gráfico y
- Archivos **. Strings localizables** para el texto que se usa en el código.

Los guiones gráficos y los recursos predeterminados se encuentran en un directorio **base** , y las traducciones específicas del lenguaje y otros recursos se almacenan en los directorios **. lproj** .
iOS y Watch OS usarán automáticamente la selección de idioma del usuario para cargar las cadenas y los recursos correctos.

Dado que una aplicación Apple Watch tiene dos aplicaciones de inspección y visualización, se necesitan recursos de cadena localizada en dos lugares, en función de cómo se usen.

El texto y los recursos localizados serán *diferentes* en la aplicación inspección y en la extensión inspección.

## <a name="watch-app"></a>Aplicación de inspección

La aplicación de inspección contiene el guion gráfico que describe la interfaz de usuario de la aplicación. Los controles (como `Label` y `Image`) que admiten la localización tienen un **identificador de localización**.

Cada directorio **. lproj** específico del idioma debe contener archivos **. Strings** con las traducciones para cada elemento (mediante el **identificador de localización**), así como las imágenes a las que hace referencia el guión gráfico.

## <a name="watch-extension"></a>Ver extensión

La extensión de inspección es donde se ejecuta el código de la aplicación. Cualquier texto que se muestre al usuario desde el código debe localizarse en la extensión y no en la aplicación de inspección.

La extensión también debe contener directorios **. lproj** específicos del idioma, pero los archivos **. Strings** solo requieren traducciones para el texto que se usa en el código.

## <a name="globalizing-the-watch-solution"></a>Globalización de la solución Watch

La globalización es el proceso de convertir una aplicación en localizable.
En el caso de las aplicaciones de inspección, esto significa diseñar el guión gráfico con diferentes longitudes de texto en mente, asegurándose de que cada diseño de pantalla se ajuste correctamente según el texto que se muestre. También debe asegurarse de que las cadenas a las que se hace referencia en el código de la extensión de inspección se pueden traducir con el método `LocalizedString`.

### <a name="watch-app"></a>Aplicación de inspección

De forma predeterminada, la aplicación de inspección no está configurada para la localización. Debe trasladar el archivo de guion gráfico predeterminado y crear otros directorios para las traducciones:

1. Cree el directorio **base. lproj** y mueva la **interfaz. Storyboard** a él.

2. Cree **\<los directorios >. lproj de idioma** para cada idioma que desee admitir.

3. Los directorios **. lproj** deben contener un archivo de texto **interface. Strings** (el nombre de archivo debe coincidir con el nombre de storboard). También puede colocar cualquier imagen que requiera localización en estos directorios.

El proyecto de la aplicación de inspección tiene este aspecto una vez realizados estos cambios (solo se han agregado archivos de idioma en inglés y español):

  ![](localization-images/watchapp-solution.png "The watch app project with English and Spanish language files")

#### <a name="storyboard-text"></a>Texto de guion gráfico

Al editar el guion gráfico, seleccione cada elemento y observe el **identificador de localización** que aparece en el panel de **propiedades** :

  [![](localization-images/storyboard-sml.png "The Localization ID that appears in the Properties pad")](localization-images/storyboard.png#lightbox)

En la carpeta **base. lproj** , cree pares clave-valor, como se muestra a continuación, donde la clave está formada por el **identificador de localización** y un nombre de propiedad en el control, unida por un punto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Observe que en este ejemplo un **identificador de localización** puede ser una cadena de número simple (por ejemplo, "0", "1", etc.) o una cadena más compleja (por ejemplo, "AgC-el-HGC"). `Label` controles tienen una propiedad `Text` y `Button`s tienen una propiedad `Title`, que se refleja en la forma en que se establecen sus valores localizados. Asegúrese de usar el nombre de la propiedad en minúsculas tal como se muestra en el ejemplo anterior.

Cuando se representa el guión gráfico en el reloj, los valores correctos se extraen automáticamente y se muestran según el idioma seleccionado por el usuario.

#### <a name="storyboard-images"></a>Imágenes de guion gráfico

La solución de ejemplo también incluye una imagen **gradient@2x.png** en cada carpeta de idioma. Esta imagen puede ser diferente para cada idioma (por ejemplo, puede tener texto incrustado que necesita traducir o usar iconografía localizadas).

Solo tiene que establecer la propiedad **imagen** de la imagen en el guión gráfico y la imagen correcta se representará en el reloj según el idioma seleccionado por el usuario.

![](localization-images/storyboard-image.png "Set the images Image property in the storyboard")

Nota: dado que todos los relojes de Apple tienen pantallas de retina, solo se necesita la versión **@2x** de la imagen. No es necesario especificar **@2x** en el guión gráfico.

### <a name="watch-extension"></a>Ver extensión

La extensión Watch requiere una estructura de directorio similar para admitir la localización; sin embargo, no hay ningún guion gráfico. Las cadenas localizadas en la extensión son solo las a C# las que hace referencia el código.

![](localization-images/watchextension-solution.png "The watch extension directory structure to support localization")

#### <a name="strings-in-code"></a>Cadenas en el código

El archivo **localizable. Strings** tiene una estructura ligeramente diferente a si se asoció con un guion gráfico. En este caso, podemos elegir cualquier cadena "Key"; La recomendación de Apple es usar una clave que refleje el texto real que se mostraría en el idioma predeterminado:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

El método `NSBundle.MainBundle.LocalizedString` se usa para resolver cadenas en sus homólogos traducidos, tal y como se muestra en el código siguiente.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Imágenes en código

Las imágenes que se rellenan mediante código se pueden establecer de dos maneras.

1. Puede cambiar un control de `Image` estableciendo su valor en el nombre de cadena de una imagen que ya existe en la aplicación de inspección, por ejemplo,

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. Puede desplace una imagen de la extensión a la inspección mediante `FromBundle` y la aplicación elegirá automáticamente la imagen correcta para la selección de idioma del usuario. En la solución de ejemplo hay una imagen **language@2x.png** en cada carpeta de idioma y se muestra en `DetailController` mediante el código siguiente:

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Tenga en cuenta que no es necesario especificar el **@2x** al hacer referencia al nombre de archivo de la imagen.

El segundo método también es aplicable si se descarga una imagen de un servidor remoto para representarla en el reloj; sin embargo, en este caso, debe asegurarse de que la imagen que descarga está adaptada correctamente según las preferencias del usuario.

## <a name="localization"></a>Localización

Una vez configurada la solución, los traductores deberán procesar las imágenes y los archivos **. Strings** para cada idioma que desee admitir.

Puede crear tantos directorios **. lproj** como necesite (uno para cada idioma admitido). Se denominan mediante códigos de idioma, como **en**, **es**, **de**, **ja**, **pt-br**, etc. (para inglés, español, alemán, Japonés y Portugués (Brasil), respectivamente).

En el ejemplo adjunto se usan traducciones (generadas por el equipo) para mostrar cómo localizar una aplicación de watchos.

### <a name="watch-app"></a>Aplicación de inspección

Estos valores se usan para traducir la interfaz de usuario definida en el guión gráfico de la aplicación Watch. El valor de *clave* es una combinación de los **identificadores de localización** del control Storyboard y la propiedad que se está traduciendo.

Se recomienda agregar comentarios que contengan el texto original al archivo para que los traductores sepan cuál debe ser la traducción.

#### <a name="eslprojinterfacestrings"></a>es. lproj/interface. Strings

A continuación se muestran las cadenas de idioma español (traducidas en el equipo) para el guión gráfico. Resulta útil agregar comentarios a cada línea, ya que es difícil saber a qué hace referencia el **identificador de localización** :

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Ver extensión

Estos valores se usan en el código para traducir la información antes de mostrarla al usuario. El desarrollador selecciona la *clave* mientras escribe código y, normalmente, contiene la cadena real que se va a traducir.

#### <a name="eslprojlocalizablestrings-file"></a>archivo es. lproj/localizable. Strings

Las cadenas de idioma de Spansish (traducción de la máquina):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Pruebas

El método para cambiar las preferencias de idioma difiere entre el simulador y los dispositivos físicos.

### <a name="simulator"></a>Simulador

En el simulador, seleccione el idioma que se va a probar con la aplicación de **configuración** de iOS (el icono de engranajes grises en la pantalla principal del simulador).

  ![](localization-images/sim-settings-sml.png "The iOS Settings app Localization settings")

### <a name="watch-device"></a>Ver dispositivo

Al realizar pruebas con un reloj, cambie el idioma del reloj en la aplicación **Apple Watch** en el iPhone emparejado.

  ![](localization-images/phone-settings-sml.png "Change the watch's language in the Apple Watch app on the paired iPhone")

## <a name="related-links"></a>Vínculos relacionados

- [WatchLocalization (ejemplo)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
