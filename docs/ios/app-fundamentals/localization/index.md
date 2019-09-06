---
title: Localización en Xamarin. iOS
description: En este documento se describen las características de localización de iOS y cómo usar estas características en aplicaciones de Xamarin. iOS. Describe el idioma, la configuración regional, los archivos de cadenas, las imágenes de inicio y mucho más.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/28/2017
ms.openlocfilehash: e394b5487b240f98310ab223371466d62a3bdf23
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278882"
---
# <a name="localization-in-xamarinios"></a>Localización en Xamarin. iOS

_En este documento se describen las características de localización del SDK de iOS y cómo acceder a ellas con Xamarin._

Consulte las [codificaciones de internacionalización](encodings.md) para obtener instrucciones sobre cómo incluir conjuntos de caracteres o páginas de códigos en aplicaciones que deben procesar datos no Unicode.

## <a name="ios-platform-features"></a>características de la plataforma iOS

En esta sección se describen algunas de las características de localización de iOS. Vaya a la [sección siguiente](#localization-basics-in-ios) para ver código y ejemplos específicos.

### <a name="language"></a>Idioma

Los usuarios eligen su idioma en la aplicación de **configuración** . Esta configuración afecta a las cadenas de idioma y a las imágenes mostradas por el sistema operativo y en las aplicaciones.

Para determinar el idioma que se usa en una aplicación, obtenga el primer elemento `NSBundle.MainBundle.PreferredLocalizations`de:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Este valor será un código `en` de idioma, como para `es` inglés, español, `ja` japonés, etc. El valor devuelto está restringido a una de las localizaciones admitidas por la aplicación (mediante reglas de reserva para determinar la mejor coincidencia).

El código de aplicación no siempre necesita comprobar este valor: Xamarin e iOS proporcionan características que ayudan a proporcionar automáticamente la cadena o el recurso correctos para el idioma del usuario. Estas características se describen en el resto de este documento.

> [!NOTE]
> Use `NSLocale.PreferredLanguages` para determinar las preferencias de idioma del usuario, independientemente de las localizaciones admitidas por la aplicación. Los valores devueltos por este método han cambiado en iOS 9; Vea la [Nota técnica TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obtener más información.

### <a name="locale"></a>Configuración regional

Los usuarios eligen su configuración regional en la aplicación de **configuración** . Esta configuración afecta a la manera en que se da formato a las fechas, las horas, los números y la moneda.

Esto permite a los usuarios elegir si ven formatos de 12 horas o de 24 horas, si el separador decimal es una coma o un punto, y el orden del día, del mes y del año en la presentación de la fecha.

Con Xamarin, tiene acceso a las clases de iOS de Apple`NSNumberFormatter`(), así como a las clases de .net en System. Globalization. Los desarrolladores deben evaluar lo que mejor se adapte a sus necesidades, ya que hay diferentes características disponibles en cada una. En concreto, si va a recuperar y mostrar los precios de compra desde la aplicación mediante StoreKit, debe usar las clases de formato de Apple para la información de precios devuelta.

La configuración regional actual se puede consultar de dos maneras:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

El sistema operativo puede almacenar en caché el primer valor, por lo que es posible que no siempre refleje la configuración regional seleccionada actualmente del usuario. Use el segundo valor para obtener la configuración regional seleccionada actualmente.

> [!NOTE]
> Mono (el entorno de ejecución .NET en el que se basa Xamarin. iOS) y las API de iOS de Apple no admiten conjuntos idénticos de combinaciones de idioma o región.
> Por este motivo, es posible seleccionar una combinación de idioma o región en la aplicación de **configuración** de iOS que no se asigna a un valor válido en mono. Por ejemplo, establecer el idioma de un iPhone en inglés y su región en España hará que las siguientes API produzcan valores diferentes:
>
> - `CurrentThead.CurrentCulture`: en-US (API mono)
> - `CurrentThread.CurrentUICulture`: en-US (API mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API de Apple)
>
> Dado que mono `CurrentThread.CurrentUICulture` utiliza para seleccionar recursos `CurrentThread.CurrentCulture` y para dar formato a fechas y divisas, es posible que la localización basada en mono (por ejemplo, con archivos. resx) no produzca los resultados esperados para estas combinaciones de idioma o región. En estas situaciones, confíe en las API de Apple para adaptarlas según sea necesario.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS genera una `NSCurrentLocaleDidChangeNotification` cuando el usuario actualiza su configuración regional. Las aplicaciones pueden escuchar esta notificación mientras se ejecutan y pueden realizar los cambios adecuados en la interfaz de usuario.

## <a name="localization-basics-in-ios"></a>Aspectos básicos de la localización en iOS

Las siguientes características de iOS se aprovechan fácilmente en Xamarin para proporcionar recursos localizados que se muestran al usuario. Consulte el [ejemplo TaskyL10n](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver cómo implementar estas ideas.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Especificación de los idiomas predeterminados y admitidos en info. plist

En [la pregunta técnica Q & QA1828: La forma en que Ios determina el idioma](https://developer.apple.com/library/content/qa/qa1828/_index.html)de la aplicación, Apple describe el modo en que Ios selecciona un idioma para usarlo en una aplicación. Los siguientes factores afectan al idioma que se muestra:

- Los idiomas preferidos del usuario (que se encuentran en la aplicación de **configuración** )
- Las localizaciones incluidas en la aplicación (carpetas. lproj)
- `CFBundleDevelopmentRegion`(Valor de**info. plist** que especifica el idioma predeterminado de la aplicación)
- `CFBundleLocalizations`(Matriz**info. plist** que especifica todas las localizaciones admitidas)

Como se indica en el & técnico Q, `CFBundleDevelopmentRegion` representa la región y el idioma predeterminados de una aplicación. Si la aplicación no admite explícitamente ninguno de los idiomas preferidos de un usuario, usará el idioma especificado por este campo.

> [!IMPORTANT]
> iOS 11 aplica este mecanismo de selección de lenguaje más estrictamente que las versiones anteriores del sistema operativo. Por este motivo, cualquier aplicación de iOS 11 que no declare explícitamente sus localizaciones admitidas, ya sea mediante carpetas. lproj o estableciendo `CFBundleLocalizations` un valor para, puede mostrar un idioma diferente en iOS 11 que en iOS 10.

Si `CFBundleDevelopmentRegion` no se ha especificado en el archivo **info. plist** , las herramientas de compilación de Xamarin. iOS usan actualmente un valor predeterminado `en_US`de. Aunque esto puede cambiar en una versión futura, significa que el idioma predeterminado es el inglés.

Para asegurarse de que la aplicación selecciona un idioma esperado, realice los pasos siguientes:

- Especifique un idioma predeterminado. Abra **info. plist** y use la vista de **código fuente** para establecer un valor para `CFBundleDevelopmentRegion` la clave; en XML, debería ser similar al siguiente:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

En este ejemplo se usa "es" para especificar que cuando no se admite ninguno de los idiomas preferidos de un usuario, el valor predeterminado es español.

- Declare todas las localizaciones admitidas. En **info. plist**, use la vista de **código fuente** para establecer una matriz para `CFBundleLocalizations` la clave; en XML, debería ser similar a la siguiente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Las aplicaciones de Xamarin. iOS que se han localizado con mecanismos de .NET, como los archivos. resx, deben proporcionar también estos valores de **info. plist** .

Para obtener más información acerca de estas claves **info. plist** , eche un vistazo a la [referencia de clave](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)de la lista de propiedades de información de Apple.

### <a name="getlocalizedstring-method"></a>Método GetLocalizedString

El `NSBundle.MainBundle.GetLocalizedString` método busca el texto localizado que se ha almacenado en los archivos **. Strings** del proyecto. Estos archivos se organizan por lenguaje, en directorios con nombre especial con un sufijo **. lproj** (tenga en cuenta que la primera letra de la extensión es una "L" minúscula).

#### <a name="strings-file-locations"></a>ubicaciones de archivos. Strings

- **Base. lproj** es el directorio que contiene los recursos para el idioma predeterminado.
  A menudo se encuentra en la raíz del proyecto (pero también se puede colocar en la carpeta **recursos** ).
- **&lt;los&gt;directorios de Language. lproj** se crean para cada idioma admitido, normalmente en la carpeta **recursos** .

Puede haber varios archivos **. Strings** diferentes en cada directorio de idioma:

- **Localizable. Strings** : la lista principal de texto localizado.
- **InfoPlist. Strings** : se permiten ciertas claves específicas en este archivo para traducir elementos como el nombre de la aplicación.
- **Storyboard: nombre >. Strings: archivo opcional que contiene traducciones para los elementos de la interfaz de usuario de un guion gráfico. \<**

La **acción de compilación** para estos archivos debe ser un **recurso de agrupación**.

#### <a name="strings-file-format"></a>. Strings (formato de archivo)

La sintaxis de los valores de cadena localizados es la siguiente:

```console
/* comment */
"key"="localized-value";
```

Debe aplicar un carácter de escape a los caracteres siguientes en las cadenas:

- `\"`cita
- `\\`barra diagonal inversa
- `\n`newline

Este es un ejemplo es **/localizable. Strings** (es decir, Spanish) del ejemplo:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Imágenes

Para localizar una imagen en iOS:

1. Haga referencia a la imagen en el código, por ejemplo:

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. Coloque el archivo de imagen predeterminado **Flag. png** en **base. lproj** (el directorio nativo del lenguaje de desarrollo).

3. Opcionalmente, coloque las versiones localizadas de la imagen en las carpetas **. lproj** para cada idioma (por ejemplo, **es.lproj**, **ja.lproj**). Use el mismo nombre de archivo **Flag. png** en cada directorio de idioma.

Si una imagen no está presente para un idioma determinado, iOS revertirá a la carpeta de idioma nativo predeterminado y cargará la imagen desde allí.

#### <a name="launch-images"></a>Imágenes de inicio

Use las convenciones de nomenclatura estándar para las imágenes de inicio (y XIB o Storyboard para los modelos de iPhone 6) al colocarlas en los directorios **. lproj** para cada idioma.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nombre de la aplicación

La colocación de un archivo **InfoPlist. Strings** en un directorio **. lproj** permite invalidar algunos valores de **info. plist**de la aplicación, incluido el nombre de la aplicación:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Otras claves que puede usar para [localizar cadenas específicas de la aplicación](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) son:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Fechas y horas

Aunque es posible usar las funciones integradas de fecha y hora de .net (junto con la actual `CultureInfo`) para dar formato a las fechas y horas de una configuración regional, esto omitiría la configuración de usuario específica de la configuración regional (que se puede establecer por separado del idioma).

Use iOS `NSDateFormatter` para producir una salida que coincida con la preferencia de la configuración regional del usuario. En el código de ejemplo siguiente se muestran las opciones de formato de fecha y hora básicas:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Resultados para inglés en el Estados Unidos:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados del español en España:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consulte la documentación de los [formateadores de fecha](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) de Apple para obtener más información. Al probar el formato de fecha y hora que depende de la configuración regional, Compruebe la configuración de idioma y **región** de **iPhone** .

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Diseño de derecha a izquierda (RTL)

iOS proporciona varias características que le ayudarán a crear aplicaciones compatibles con RTL:

- Use los atributos y `leading` `trailing` del diseño automático para la alineación de controles (que se corresponde a la izquierda y a la derecha para el inglés, pero se invierte para los lenguajes RTL).
  El [`UIStackView`](~/ios/user-interface/controls/uistackview.md) control es especialmente útil para diseñar controles que sean compatibles con RTL.
- Se `TextAlignment = UITextAlignment.Natural` usa para la alineación del texto (que se dejará para la mayoría de los lenguajes, pero derecha para RTL).
- `UINavigationController`voltea automáticamente el botón atrás y revierte la dirección de deslizamiento.

Las capturas de pantallas siguientes muestran el [ejemplo de tarea localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en árabe y hebreo (aunque se ha escrito en inglés en los campos):

[![](images/rtl-ar-sml.png "Localización en Árabe")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localización en hebreo")](images/rtl-he.png#lightbox "Hebrew")

iOS invierte automáticamente el `UINavigationController`y los demás controles se colocan `UIStackView` dentro o se alinean con el diseño automático.
El texto RTL se localiza mediante archivos **. Strings** de la misma manera que el texto LTR.

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizar la interfaz de usuario en el código

El ejemplo [tasky (localizado en código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) muestra cómo localizar una aplicación en la que la interfaz de usuario se compila en el código (en lugar de Xib o guiones gráficos).

### <a name="project-structure"></a>Estructura del proyecto

![](images/solution-code.png "Árbol de recursos")

### <a name="localizablestrings-file"></a>Archivo. Strings localizable

Tal y como se ha descrito anteriormente, el formato de archivo **localizable. Strings** consta de pares de clave y valor. La clave describe el intento de la cadena y el valor es el texto traducido que se va a usar en la aplicación.

A continuación semuestran las localizaciones en español para el ejemplo:

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Realización de la localización

En el código de la aplicación, siempre que se establezca el texto para mostrar de la interfaz de usuario (ya sea el texto de una etiqueta o el marcador de posición de una entrada `GetLocalizedString` , etc.), el código usa la función iOS para recuperar la traducción correcta que se va a mostrar:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizar ius de guion gráfico

La tarea de ejemplo [(guion gráfico localizado)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) muestra cómo localizar texto en los controles de un guion gráfico.

### <a name="project-structure"></a>Estructura del proyecto

El directorio **base. lproj** contiene el guión gráfico y también debe contener cualquier imagen que se use en la aplicación.

Los demás directorios de lenguaje contienen un archivo **. Strings localizable** para cualquier recurso de cadena al que se hace referencia en el código, así como un archivo **archivo mainstoryboard. Strings** que contiene traducciones para el texto del guión gráfico.

![](images/solution-storyboard.png "Árbol de recursos")

Los directorios de idioma deben contener una copia de las imágenes que se han localizado, para invalidar el presente en **base. lproj**.

### <a name="object-id--localization-id"></a>Identificador de objeto/ID. de localización

Al crear y editar controles en un guión gráfico, seleccione cada control y compruebe el identificador que se va a usar para la localización:

- En Visual Studio para Mac, se encuentra en el **Panel de propiedades** y se denomina **identificador de localización**.
- En Xcode, se denomina **identificador de objeto**.

A menudo, este valor de cadena tiene un formato como "NF3-H8-xmR", como se muestra en la siguiente captura de pantalla:

![](images/xs-designer-localization-id.png "Vista de Xcode de la localización de guiones gráficos")

Este valor se usa en el archivo **. Strings** para asignar el texto traducido automáticamente a cada control.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

El formato del archivo de traducción del guión gráfico es similar al archivo **. Strings localizable** , salvo que la clave (el valor de la izquierda) no puede estar definida por el usuario, pero en su lugar debe tener `ObjectID.property`un formato muy específico:.

En el ejemplo **archivo mainstoryboard. Strings** siguiente puede ver `UITextField`que tiene una `placeholder` propiedad Text que se puede localizar; s tienen una `text` propiedad; y `UIButton`s el texto predeterminado se establece `normalTitle`mediante: `UILabel`

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> El uso de un guion gráfico con clases de tamaño puede dar lugar a traducciones que no aparecen en la aplicación. Las [notas de la versión de Xcode de Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indican que un guión gráfico o Xib no se adaptarán correctamente si se cumplen tres cosas: usa clases de tamaño, la localización base y el destino de compilación se establecen en universal, y la compilación tiene como destino iOS 7,0. La solución consiste en duplicar el archivo de cadenas de guion gráfico en dos archivos idénticos: **Archivo mainstoryboard ~ iPhone. Strings** y **archivo mainstoryboard ~ iPad. Strings**, tal como se muestra en la siguiente captura de pantalla:
>
> ![](images/xs-dup-strings.png "Archivos de cadenas")

<a name="appstore" />

## <a name="app-store-listing"></a>Lista de App Store

Sigue las preguntas más frecuentes de Apple sobre la localización de la [tienda de aplicaciones](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) para especificar traducciones para cada país en el que la aplicación está en venta. Tenga en cuenta la advertencia de que las traducciones solo aparecerán si la aplicación también contiene un directorio **. lproj** adaptado para el idioma.

## <a name="summary"></a>Resumen

En este artículo se describen los aspectos básicos de la localización de aplicaciones de iOS con las características integradas de control de recursos y guiones gráficos.

Puede obtener más información sobre i18n y l10n para aplicaciones de iOS, Android y multiplataforma (incluido Xamarin. Forms) en [esta guía multiplataforma](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Vínculos relacionados

- [Tarea (localizada en código) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (guion gráfico localizado) (ejemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guía de localización de Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Información general sobre la localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localización de Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
