---
title: Localización de Android
description: En este documento se presentan las características de localización del Android SDK y cómo obtener acceso a ellos con Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c76eb8c6553768af68687ab6a45447a16775940e
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119135"
---
# <a name="android-localization"></a>Localización de Android

_En este documento se presentan las características de localización del Android SDK y cómo obtener acceso a ellos con Xamarin._

## <a name="android-platform-features"></a>Características de la plataforma Android

En esta sección se describen las principales características de localización de Android. Vaya a la [sección siguiente](#basics) para ver código y ejemplos específicos.

### <a name="locale"></a>Configuración regional

Los usuarios eligen su idioma en **configuración > idioma & entrada**. Esta selección controla tanto el idioma mostrado como la configuración regional utilizada (por ejemplo, para el formato de fecha y número).

La configuración regional actual se puede consultar a través del del `Resources`contexto actual:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Este valor será un identificador de configuración regional que contiene un código de idioma y un código de configuración regional, separados por un carácter de subrayado. Como referencia, aquí se [muestra una lista de configuraciones regionales de Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) y [configuraciones regionales compatibles con Android a través de stackoverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Estos son algunos ejemplos comunes:

- `en_US`para inglés (Estados Unidos)
- `es_ES`para español (España)
- `ja_JP`para japonés (Japón)
- `zh_CN`para chino (China)
- `zh_TW`para chino (Taiwán)
- `pt_PT`para Portugués (Portugal)
- `pt_BR`para Portugués (Brasil)

### <a name="locale_changed"></a>LOCALE_CHANGED

Android genera `android.intent.action.LOCALE_CHANGED` cuando el usuario cambia la selección de idioma.

Las actividades pueden optar por controlar esto estableciendo `android:configChanges` el atributo en la actividad, de la siguiente manera:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Aspectos básicos de internacionalización en Android

La estrategia de localización de Android tiene las siguientes partes clave:

- Carpetas de recursos que contienen cadenas, imágenes y otros recursos localizados.

- `GetText`método, que se usa para recuperar cadenas localizadas en el código

- `@string/id`en archivos AXML, para colocar automáticamente las cadenas localizadas en los diseños.

### <a name="resource-folders"></a>Carpetas de recursos

Las aplicaciones de Android administran la mayor parte del contenido de las carpetas de recursos, como:

- **diseño** : contiene archivos de diseño AXML.
- **dibujable** : contiene imágenes y otros recursos Dibujables.
- **valores** : contiene cadenas.
- **raw** : contiene archivos de datos.

La mayoría de los desarrolladores ya están familiarizados con el uso de sufijos de **PPP** en el directorio **drawable** para proporcionar varias versiones de una imagen, lo que permite a Android elegir la versión correcta para cada dispositivo. El mismo mecanismo se usa para proporcionar traducciones de varios idiomas mediante el sufijo de directorios de recursos con identificadores de idioma y de referencia cultural.

![Captura de pantalla de recursos/carpetas Dibujables y de recursos/valores para varios identificadores culturales](localization-images/resources.png)

> [!NOTE]
> Cuando se especifica un lenguaje de nivel superior como `es` solo se necesitan dos caracteres; sin embargo, al especificar una configuración regional completa, el formato de nombre de directorio requiere un guión y **r** en minúsculas para separar las dos partes, por ejemplo, **PT-rBR** o  **ZH-rCN**. Compárelo con el valor devuelto en el código, que tiene un carácter de subrayado (por ejemplo, `pt_BR`). Ambos son diferentes del valor que usa la clase `CultureInfo` .net, que solo tiene un guión (por ejemplo, `pt-BR`). Tenga en cuenta estas diferencias al trabajar en plataformas de Xamarin.

#### <a name="stringsxml-file-format"></a>Strings. XML, formato de archivo

Un directorio de **valores** localizados (p. ej., **Values-es** o **Values-PT-rBR**) deben contener un archivo llamado **Strings. XML** que contendrá el texto traducido para esa configuración regional.

Cada cadena traducible es un elemento XML con el identificador de recurso especificado como el `name` atributo y la cadena traducida como el valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Debe usar un carácter de escape de acuerdo con las reglas XML `name` normales y el debe ser un identificador de recurso de Android válido (sin espacios ni guiones). Este es un ejemplo del archivo de cadenas (Inglés) predeterminado para el ejemplo:

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

Los **valores de** directorio Español contienen un archivo con el mismo nombre (**Strings. XML**) que contiene las traducciones:

**values-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Captura de pantalla de varias carpetas de valores, cada una con un archivo Strings. XML](localization-images/values.png)

Con la configuración de los archivos de cadenas, se puede hacer referencia a los valores traducidos en los diseños y en el código.

### <a name="axml-layout-files"></a>Archivos de diseño AXML

Para hacer referencia a cadenas localizadas en archivos de diseño `@string/id` , use la sintaxis. En este fragmento de código XML del `text` ejemplo se muestran las propiedades que se establecen con identificadores de recursos localizados (algunos otros atributos se han omitido):

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText (método)

Para recuperar las cadenas traducidas en el código `GetText` , use el método y pase el identificador de recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Cadenas de cantidad

Los recursos de cadena de Android también permiten crear *cadenas de cantidad* que permiten a los traductores proporcionar diferentes traducciones para distintas cantidades, como:

- "Hay una tarea restante".
- "Hay 2 tareas pendientes de realizar".

(en lugar de un genérico "hay n tareas a la izquierda").

En el **archivo Strings. XML**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Para representar la cadena completa, use `GetQuantityString` el método, pasando el identificador de recurso y el valor que se va a mostrar (que se pasa dos veces). Android usa el segundo parámetro para determinar `quantity` la cadena que se va a usar, el tercer parámetro es el valor que se sustituye realmente por la cadena (se requieren ambos).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Los `quantity` modificadores válidos son:

- cero
- one
- túnel
- suficiente
- many
- otras

Se describen con más detalle en los [documentos de Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Si un lenguaje determinado no requiere un control "especial", esas `quantity` cadenas se omitirán (por ejemplo, solo inglés utiliza `one` y `other`; si se especifica `zero` una cadena, no se usará).

### <a name="images"></a>Imágenes

Las imágenes localizadas siguen las mismas reglas que los archivos de cadenas: todas las imágenes a las que se hace referencia en la aplicación deben colocarse en directorios Dibujables, por lo que hay una reserva.

Las imágenes específicas de la configuración regional deben colocarse en carpetas Dibujables aptas como **drawable-es** o **drawable-ja** (también se pueden agregar especificadores de PPP).

En esta captura de pantalla, se guardan cuatro imágenes en el directorio drawable, pero solo una, **Flag. png**, tiene copias localizadas en otros directorios.

![Captura de pantalla de varias carpetas Dibujables, cada una de las cuales contiene uno o varios archivos. png localizados](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Otros tipos de recursos

También puede proporcionar otros tipos de recursos alternativos, específicos del lenguaje, incluidos los diseños, las animaciones y los archivos sin formato. Esto significa que puede proporcionar un diseño de pantalla específico para uno o varios de los idiomas de destino; por ejemplo, puede crear un diseño específicamente para alemán que permita etiquetas de texto muy largas.

Android 4,2 incorporó compatibilidad con [idiomas de derecha a izquierda (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) si establece la configuración `android:supportsRtl="true"`de la aplicación. El calificador `"ldrtl"` de recursos se puede incluir en un nombre de directorio para que contenga diseños personalizados que estén diseñados para la presentación RTL.

Para más información sobre la reserva y los nombres de directorios de recursos, consulte los documentos de Android para [proporcionar recursos alternativos](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nombre de la aplicación

El nombre de la aplicación es fácil de localizar mediante el `@string/id` uso de en `MainLauncher` para la actividad:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Android 4,2 y las versiones más recientes ofrecen compatibilidad total con los diseños RTL, que se describen en detalle en el [blog de soporte técnico de RTL nativo](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Cuando se usa Android 4,2 (nivel de API 17) y versiones más recientes, los valores `start` de `end` alineación se pueden `left` especificar con y en `android:paddingStart`lugar de y `right` (por ejemplo). También hay nuevas API como `LayoutDirection`, `TextDirection`y `TextAlignment` para ayudar a compilar pantallas que se adaptan a los lectores RTL.

En la captura de pantalla siguiente se muestra el [ejemplo de **tarea** localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe:

[![Captura de pantalla de la aplicación de tareas en Árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

En la siguiente captura de pantalla se muestra el [ejemplo de **tarea** localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en Hebreo:

[![Captura de pantalla de la aplicación de tareas en hebreo](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

El texto RTL se localiza mediante el uso de archivos **Strings. XML** de la misma manera que el texto LTR.

## <a name="testing"></a>Pruebas

Asegúrese de probar exhaustivamente la configuración regional predeterminada. La aplicación se bloqueará si no se pueden cargar los recursos predeterminados por algún motivo (es decir, faltan).

### <a name="emulator-testing"></a>Pruebas del emulador

Consulte las pruebas de Google [en una Android Emulator](https://developer.android.com/guide/topics/resources/localization.html#testing) sección para obtener instrucciones sobre cómo establecer un emulador en una configuración regional específica mediante el shell ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Pruebas de dispositivos

Para probar en un dispositivo, cambie el idioma en la aplicación de **configuración** .

> [!TIP]
> Tome nota de los iconos y la ubicación de los elementos de menú para que pueda revertir el idioma a la configuración original.


## <a name="summary"></a>Resumen

En este artículo se tratan los aspectos básicos de la localización de aplicaciones de Android mediante el control de recursos integrado. Puede obtener más información sobre i18n y l10n para aplicaciones de iOS, Android y multiplataforma (incluidas Xamarin. Forms) en [esta guía multiplataforma](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Vínculos relacionados

- [Tarea (localizada en código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Configuración de la localización de Android con recursos](https://developer.android.com/guide/topics/resources/localization.html)
- [Información general sobre la localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
