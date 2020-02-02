---
title: Fuentes
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/09/2018
ms.openlocfilehash: 3bfa3bbde68fab95d729cc8a558d4eb3baf7b4fa
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940653"
---
# <a name="fonts"></a>Fuentes

## <a name="overview"></a>Información general del

A partir del nivel de API 26, el Android SDK permite que las fuentes se traten como recursos, al igual que los diseños o drawables. La [biblioteca de compatibilidad de Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) migrará las nuevas fuentes de la API a las aplicaciones que tengan como destino el nivel de API 14 o superior.

Después de establecer la API 26 como destino o instalar la biblioteca de compatibilidad de Android V26, hay dos maneras de usar fuentes en una aplicación de Android:

1. **Empaquete la fuente como un recurso de Android** &ndash; esto garantiza que la fuente esté siempre disponible para la aplicación, pero aumentará el tamaño de APK.
2. **Descargue las fuentes** &ndash; Android también admite la descarga de una fuente de un _proveedor de fuentes_. El proveedor de fuentes comprueba si la fuente ya está en el dispositivo. Si es necesario, la fuente se descargará y almacenará en caché en el dispositivo. Esta fuente se puede compartir entre varias aplicaciones.

Las fuentes similares (o una fuente que puede tener varios estilos distintos) se pueden agrupar en _familias de fuentes_. Esto permite a los desarrolladores especificar ciertos atributos de la fuente, como su peso, y Android seleccionará automáticamente la fuente adecuada de la familia de fuentes.

La biblioteca de compatibilidad de Android V26 descargará la compatibilidad de las fuentes con el nivel de API 26. Cuando el destino son los niveles de API más antiguos, es necesario declarar el espacio de nombres XML `app` y asignar un nombre a los distintos atributos de fuente mediante el espacio de nombres `android:` y el espacio de nombres `app:`. Si solo se usa el espacio de nombres `android:`, las fuentes no se mostrarán en los dispositivos que ejecuten el nivel de API 25 o menos. Por ejemplo, este fragmento de código XML declara un nuevo recurso de la [_familia de fuentes_](#font_families) que funcionará en el nivel de API 14 y versiones posteriores:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

Siempre que las fuentes se proporcionen a una aplicación de Android de manera adecuada, se pueden aplicar a un widget de interfaz de usuario estableciendo el [atributo de`fontFamily`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Por ejemplo, el siguiente fragmento de código muestra cómo mostrar una fuente en una TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

En esta guía se explica en primer lugar cómo usar fuentes como un recurso de Android y, a continuación, se explica cómo descargar fuentes en tiempo de ejecución.

## <a name="fonts-as-a-resource"></a>Fuentes como un recurso

Empaquetar una fuente en un APK de Android garantiza que siempre esté disponible para la aplicación. Un archivo de fuente (o. TTF o. El archivo OTF) se agrega a una aplicación de Xamarin. Android como cualquier otro recurso, mediante la copia de archivos en un subdirectorio de la carpeta **recursos** de un proyecto de Xamarin. Android. Los recursos de fuentes se mantienen en un subdirectorio de **fuente** de la carpeta **recursos** del proyecto.

> [!NOTE]
> Las fuentes deben tener una **acción de compilación** de **AndroidResource** o no se empaquetarán en el APK final. El IDE debe establecer automáticamente la acción de compilación.

Cuando hay muchos archivos de fuentes similares (por ejemplo, la misma fuente con diferentes pesos o estilos), es posible agruparlos en una familia de fuentes.

<a name="font_families" />

### <a name="font-families"></a>Familias de fuentes

Una familia de fuentes es un conjunto de fuentes que tienen diferentes pesos y estilos. Por ejemplo, puede haber archivos de fuentes independientes para las fuentes en negrita o cursiva. La familia de fuentes se define mediante `font` elementos de un archivo XML que se mantiene en el directorio **Resources/Font** . Cada familia de fuentes debe tener su propio archivo XML.

Para crear una familia de fuentes, primero agregue todas las fuentes a la carpeta **Resources/Font** . A continuación, cree un nuevo archivo XML en la carpeta fuente de la familia de fuentes. El nombre del archivo XML no tiene afinidad ni relación con las fuentes a las que se hace referencia; el archivo de recursos puede ser cualquier nombre de archivo de recursos válido de Android. Este archivo XML tendrá un elemento `font-family` raíz que contiene uno o varios elementos `font`. Cada elemento `font` declara los atributos de una fuente.

El siguiente XML es un ejemplo de una familia de fuentes para la fuente de _fuentes San de origen_ que define muchos pesos de fuente diferentes. Se guarda como archivo en la carpeta **Resources/Font** denominada **sourcesanspro. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

El `fontStyle` atributo tiene dos valores posibles:

- **normal** &ndash; una fuente normal
- **cursiva** &ndash; una fuente en cursiva

El atributo `fontWeight` corresponde al atributo `font-weight` de CSS y hace referencia al grosor de la fuente. Se trata de un valor en el intervalo de 100 a 900. En la lista siguiente se describen los valores de peso de fuente comunes y su nombre:

- &ndash; **fino** 100
- **Luz adicional** &ndash; 200
- &ndash; **claro** 300
- **Normal** &ndash; 400
- &ndash; **mediana** 500
- **Seminegrita** &ndash; 600
- **Negrita** &ndash; 700
- &ndash; en **negrita Extra** 800
- **Negro** &ndash; 900

Una vez definida una familia de fuentes, se puede usar mediante declaración estableciendo los atributos `fontFamily`, `textStyle`y `fontWeight` en el archivo de diseño.  Por ejemplo, el siguiente fragmento de código XML establece una fuente de peso 600 (normal) y un estilo de texto en cursiva:

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Asignar fuentes mediante programación

Las fuentes se pueden establecer mediante programación usando el método [`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) para recuperar un objeto [`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html) . Muchas vistas tienen una propiedad `TypeFace` que se puede usar para asignar la fuente al widget. Este fragmento de código muestra cómo establecer mediante programación la fuente en una TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

El método `GetFont` cargará automáticamente la primera fuente dentro de una familia de fuentes.  Para cargar una fuente que coincida con un estilo concreto, utilice el método `Typeface.Create`. Este método intentará cargar una fuente que coincida con el estilo especificado. Por ejemplo, este fragmento de código intentará cargar un objeto de `Typeface` en negrita de una familia de fuentes que se define en **Resources/Fonts**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Descargar fuentes

En lugar de empaquetar fuentes como un recurso de aplicación, Android puede descargar fuentes de un origen remoto. Esto tendrá el efecto deseado de reducir el tamaño de APK.

Las fuentes se descargan con la ayuda de un _proveedor de fuentes_. Se trata de un proveedor de contenido especializado que administra la descarga y el almacenamiento en caché de fuentes en todas las aplicaciones del dispositivo. Android 8,0 incluye un proveedor de fuentes para descargar fuentes del [repositorio de fuentes de Google](https://fonts.google.com). Este proveedor de fuentes predeterminado se ha trasladado al nivel de API 14 con la biblioteca de compatibilidad de Android V26.

Cuando una aplicación realiza una solicitud de una fuente, el proveedor de fuentes primero comprueba para ver si la fuente ya está en el dispositivo. En caso contrario, intentará descargar la fuente. Si no se puede descargar la fuente, Android usará la fuente predeterminada del sistema. Una vez descargada la fuente, está disponible para todas las aplicaciones del dispositivo, no solo para la aplicación que realizó la solicitud inicial.

Cuando se realiza una solicitud para descargar una fuente, la aplicación no consulta directamente al proveedor de fuentes. En su lugar, las aplicaciones usarán una instancia de la API de [`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) (o la [`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) si se usa la biblioteca de soporte 26).  

Android 8,0 admite la descarga de fuentes de dos maneras diferentes:

1. **Declarar fuentes descargables como un recurso** &ndash; una aplicación puede declarar fuentes descargables en Android a través de archivos de recursos XML. Estos archivos contendrán todos los metadatos que Android necesita para descargar las fuentes de forma asincrónica cuando la aplicación se inicia y almacenar en memoria caché en el dispositivo.
2. **Mediante programación** &ndash; API en el nivel de API de Android 26 permiten a una aplicación descargar las fuentes mediante programación, mientras se ejecuta la aplicación. Las aplicaciones crearán un `FontRequest` objeto para una fuente determinada y pasarán este objeto a la clase `FontsContract`. El `FontsContract` toma el `FontRequest` y recupera la fuente de un _proveedor de fuentes_. Android descargará la fuente de forma sincrónica. Más adelante en esta guía se muestra un ejemplo de la creación de un `FontRequest`.

Independientemente del método que se use, los archivos de recursos que se deben agregar a la aplicación de Xamarin. Android antes de que se puedan descargar las fuentes. En primer lugar, las fuentes deben declararse en un archivo XML en el directorio **Resources/Font** como parte de una familia de fuentes. Este fragmento de código es un ejemplo de cómo descargar fuentes de la [colección de código abierto de Google Fonts](https://fonts.google.com) mediante el proveedor de fuentes predeterminado que viene con Android 8,0 (o la biblioteca de compatibilidad V26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

El elemento `font-family` contiene los siguientes atributos, declarando la información que Android requiere para descargar las fuentes:

1. **fontProviderAuthority** &ndash; la autoridad del proveedor de fuentes que se va a utilizar para la solicitud.
2. **fontPackage** &ndash; el paquete del proveedor de fuentes que se va a utilizar para la solicitud. Se utiliza para comprobar la identidad del proveedor.
3. **fontQuery** &ndash; se trata de una cadena que ayudará al proveedor de fuentes a localizar la fuente solicitada. Los detalles de la consulta de fuente son específicos del proveedor de fuentes. La clase de [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) de la aplicación de ejemplo de [fuentes descargables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) proporciona cierta información sobre el formato de la consulta para las fuentes de la colección de código abierto Google Fonts.
4. **fontProviderCerts** &ndash; una matriz de recursos con la lista de conjuntos de hash para los certificados con los que se debe firmar el proveedor.

Una vez definidas las fuentes, puede que sea necesario proporcionar información sobre los _certificados de fuente_ implicados en la descarga.

### <a name="font-certificates"></a>Certificados de fuente

Si el proveedor de fuentes no está preinstalado en el dispositivo, o si la aplicación usa la biblioteca de `Xamarin.Android.Support.Compat`, Android requiere los certificados de seguridad del proveedor de fuentes. Estos certificados se enumerarán en un archivo de recursos de matriz que se guarda en el directorio **Resources/Values** .

Por ejemplo, el siguiente código XML se denomina **Resources/Values/fonts_cert. XML** y almacena los certificados para el proveedor de fuentes de Google:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Con estos archivos de recursos en su lugar, la aplicación es capaz de descargar las fuentes.

### <a name="declaring-downloadable-fonts-as-resources"></a>Declarar fuentes descargables como recursos

Al enumerar las fuentes que se pueden descargar en **archivo AndroidManifest. XML**, Android descargará las fuentes de forma asincrónica cuando se inicie la aplicación por primera vez. Las propias fuentes se enumeran en un archivo de recursos de matriz, similar a este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Para descargar estas fuentes, tienen que declararse en **archivo AndroidManifest. XML** agregando `meta-data` como un elemento secundario del elemento `application`. Por ejemplo, si las fuentes descargables se declaran en un archivo de recursos en **Resources/Values/downloadable_fonts. XML**, este fragmento de código tendría que agregarse al manifiesto:

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Descargar una fuente con las API de fuentes

Es posible descargar mediante programación una fuente mediante la creación de una instancia de un objeto [`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) y pasarlo al método `FontContractCompat.RequestFont`. El método `FontContractCompat.RequestFont` comprobará primero si la fuente existe en el dispositivo y, si es necesario, consultará de forma asincrónica el proveedor de fuentes e intentará descargar la fuente de la aplicación. Si `FontRequest` no puede descargar la fuente, Android usará la fuente predeterminada del sistema.

Un objeto `FontRequest` contiene información que utilizará el proveedor de fuentes para buscar y descargar una fuente. Un `FontRequest` requiere cuatro datos:

1. **Entidad del proveedor de fuentes** &ndash; la autoridad del proveedor de fuentes que se va a utilizar para la solicitud.
2. **Paquete de fuentes** &ndash; el paquete del proveedor de fuentes que se va a utilizar para la solicitud. Se utiliza para comprobar la identidad del proveedor.
3. **Consulta de fuente** &ndash; se trata de una cadena que ayudará al proveedor de fuentes a localizar la fuente solicitada. Los detalles de la consulta de fuente son específicos del proveedor de fuentes. Los detalles de la cadena son específicos del proveedor de fuentes. La clase de [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) de la aplicación de ejemplo de [fuentes descargables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) proporciona cierta información sobre el formato de la consulta para las fuentes de la colección de código abierto Google Fonts.
4. Los **certificados de proveedor de fuentes** &ndash; una matriz de recursos con la lista de conjuntos de hash para los certificados con los que se debe firmar el proveedor.

Este fragmento de código es un ejemplo de creación de instancias de un nuevo objeto `FontRequest`:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

En el fragmento de código anterior `FontToDownload` es una consulta que ayudará a la fuente de la colección de código abierto Google Fonts.

Antes de pasar el `FontRequest` al método `FontContractCompat.RequestFont`, hay dos objetos que se deben crear:

- **`FontsContractCompat.FontRequestCallback`** &ndash; es una clase abstracta que se debe extender. Es una devolución de llamada que se invocará cuando finalice la `RequestFont`. Una aplicación de Xamarin. Android debe subclase `FontsContractCompat.FontRequestCallback` e invalidar el `OnTypefaceRequestFailed` y el `OnTypefaceRetrieved`, lo que proporciona las acciones que deben realizarse cuando se produce un error en la descarga o se realiza correctamente, respectivamente.
- **`Handler`** &ndash; se trata de una `Handler` que usará `RequestFont` para descargar la fuente en un subproceso, si es necesario. Las fuentes **no** se deben descargar en el subproceso de la interfaz de usuario.

Este fragmento de código es un ejemplo C# de una clase que descargará asincrónicamente una fuente de la colección de código abierto de Google Fonts. Implementa la interfaz de `FontRequestCallback` y genera un C# evento cuando `FontRequest` ha finalizado.

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };

    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Para usar esta aplicación auxiliar, se crea un nuevo `FontDownloadHelper` y se asigna un controlador de eventos:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Resumen

En esta guía se describen las nuevas API de Android 8,0 para admitir fuentes y fuentes descargables como recursos. Se ha explicado cómo incrustar fuentes existentes en un APK y cómo usarlas en un diseño. También se explica cómo Android 8,0 admite la descarga de fuentes de un proveedor de fuentes, ya sea mediante programación o mediante la declaración de los metadatos de fuente en archivos de recursos.

## <a name="related-links"></a>Vínculos relacionados

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Tipográfico](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Biblioteca de compatibilidad de Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Usar fuentes en Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Especificación de espesor de fuente CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Colección de código abierto de Google Fonts](https://fonts.google.com/)
- [San de origen](https://fonts.google.com/specimen/Source+Sans+Pro)
