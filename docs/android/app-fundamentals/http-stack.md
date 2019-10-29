---
title: Selector de implementación de la pila HttpClient y SSL/TLS para Android
description: Los selectores de la pila HttpClient y de la implementación de SSL/TLS determinan la implementación de HttpClient y SSL/TLS que usarán las aplicaciones de Xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019232"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Selector de implementación de la pila HttpClient y SSL/TLS para Android

Los selectores de la pila HttpClient y de la implementación de SSL/TLS determinan la implementación de HttpClient y SSL/TLS que usarán las aplicaciones de Xamarin. Android.

Los proyectos deben hacer referencia al ensamblado **System .net. http** .

> [!WARNING]
> **Abril de 2018** : debido a los mayores requisitos de seguridad, incluido el cumplimiento de PCI, los principales proveedores en la nube y los servidores Web se espera que dejen de admitir versiones de TLS anteriores a 1,2. Los proyectos de Xamarin creados en versiones anteriores de Visual Studio usan de forma predeterminada versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones continúan funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin con la configuración de `Android HttpClient` y `Native TLS 1.2` que se muestra a continuación, y volver a compilar y volver a implementar las aplicaciones** para los usuarios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configuración de HttpClient. Android está en **Opciones de proyecto > opciones de Android**y, a continuación, haga clic en el botón **Opciones avanzadas** .

Esta es la configuración recomendada para la compatibilidad con TLS 1,2:

[![opciones de Android de Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

La configuración HttpClient de Xamarin. Android está en **Opciones del proyecto > Compilar >** configuración de compilación de Android y haga clic en la pestaña **General** .

Esta es la configuración recomendada para la compatibilidad con TLS 1,2:

[Opciones de![Visual Studio para Mac Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativas

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler es el nuevo controlador que delega en código nativo de Java/OS en lugar de implementar todo en código administrado.
**Esta es la opción recomendada.**

#### <a name="pros"></a>Profesionales ti

- Use la API nativa para mejorar el rendimiento y el tamaño de los archivos ejecutables más pequeños.
- Compatibilidad con los estándares más recientes, por ejemplo, TLS 1,2.

#### <a name="cons"></a>Iconos

- Requiere Android 4,1 o posterior.
- Algunas opciones y características de HttpClient no están disponibles.

### <a name="managed-httpclienthandler"></a>Administrado (HttpClientHandler)

El controlador administrado es el controlador HttpClient totalmente administrado que se ha enviado con versiones anteriores de Xamarin. Android.

#### <a name="pros"></a>Profesionales ti

- Es la versión más compatible (características) con MS .NET y versiones anteriores de Xamarin.

#### <a name="cons"></a>Iconos

- No está totalmente integrado con el sistema operativo (por ejemplo, limitado a TLS 1,0).
- Normalmente es mucho más lento (por ejemplo, cifrado) que la API nativa.
- Requiere más código administrado, lo que crea aplicaciones de mayor tamaño.

### <a name="choosing-a-handler"></a>Elegir un controlador

La elección entre `AndroidClientHandler` y `HttpClientHandler` depende de las necesidades de la aplicación. `AndroidClientHandler` se recomienda para la compatibilidad con la seguridad más actualizada, por ejemplo,

- Requiere compatibilidad con TLS 1.2 y.
- Su aplicación tiene como destino Android 4,1 (API 16) o posterior.
- Necesita compatibilidad con TLS 1.2 + para `HttpClient`.
- No necesita compatibilidad con TLS 1.2 + para `WebClient`.

`HttpClientHandler` es una buena opción si necesita compatibilidad con TLS 1.2 + pero debe admitir versiones de Android anteriores a Android 4,1. También es una buena opción si necesita compatibilidad con TLS 1.2 + para `WebClient`.

A partir de Xamarin. Android 8,3, `HttpClientHandler` toma como valor predeterminado el SSL de perforación (`btls`) como proveedor de TLS subyacente. El proveedor de SSL de perforación de aburrido ofrece las siguientes ventajas:

- Admite TLS 1.2 +.
- Es compatible con todas las versiones de Android.
- Proporciona compatibilidad con TLS 1.2 + para `HttpClient` y `WebClient`.

La desventaja de utilizar SSL aburrido como el proveedor de TLS de subordinado es que puede aumentar el tamaño del APK resultante (agrega aproximadamente 1 MB de tamaño de APK adicional por ABI compatible).

A partir de Xamarin. Android 8,3, el proveedor de TLS predeterminado es aburrido SSL (`btls`). Si no desea utilizar SSL aburrido, puede revertir a la implementación de SSL administrada histórica estableciendo la propiedad `$(AndroidTlsProvider)` en `legacy` (para obtener más información sobre cómo establecer las propiedades de compilación, vea [proceso de compilación](~/android/deploy-test/building-apps/build-process.md)).

### <a name="programatically-using-androidclienthandler"></a>Mediante programación con `AndroidClientHandler`

El `Xamarin.Android.Net.AndroidClientHandler` es una implementación de `HttpMessageHandler` específicamente para Xamarin. Android.
Las instancias de esta clase usarán la implementación de `java.net.URLConnection` nativa para todas las conexiones HTTP. En teoría, esto proporcionará un aumento en el rendimiento HTTP y tamaños de APK menores.

Este fragmento de código es un ejemplo de cómo explícitamente para una única instancia de la clase `HttpClient`:

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> El dispositivo Android subyacente debe admitir TLS 1,2 (es decir, Android 4,1 y versiones posteriores). Tenga en cuenta que el soporte técnico oficial de TLS 1,2 es Android 5.0 +. Sin embargo, algunos dispositivos admiten TLS 1,2 en Android 4.1 +.

## <a name="ssltls-implementation-build-option"></a>Opción de compilación de implementación de SSL/TLS

Esta opción de proyecto controla qué biblioteca TLS subyacente usarán todas las solicitudes Web, tanto `HttpClient` como `WebRequest`. De forma predeterminada, se selecciona TLS 1,2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[cuadro combinado de![de la implementación de TLS/SSL en Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![cuadro combinado de implementación de TLS/SSL en Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Por ejemplo:

```csharp
var client = new HttpClient();
```

Si la implementación de HttpClient se estableció en **administrado** y la implementación de TLS se estableció en **TLS 1.2 + nativo**, el objeto `client` usaría automáticamente el `HttpClientHandler` administrado y TLS 1,2 (proporcionado por la biblioteca BORINGSSL) para su http convocatorias.

Sin embargo, si la **implementación de HttpClient** está establecida en `AndroidHttpClient`, todos los objetos `HttpClient` usarán la clase Java subyacente `java.net.URLConnection` y no se verán afectados por el valor de implementación de **TLS/SSL** . `WebRequest` objetos usarían la biblioteca BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Otras formas de controlar la configuración de SSL/TLS

Hay tres maneras de que una aplicación de Xamarin. Android pueda controlar la configuración de TLS:

1. Seleccione la implementación de HttpClient y la biblioteca TLS predeterminada en las opciones del proyecto.
2. Mediante programación con `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare las variables de entorno (opcional).

De las tres opciones, el enfoque recomendado es usar las opciones de proyecto de Xamarin. Android para declarar los `HttpMessageHandler` predeterminados y TLS para toda la aplicación. A continuación, si es necesario, cree instancias de `Xamarin.Android.Net.AndroidClientHandler` objetos mediante programación. Estas opciones se han descrito anteriormente.

La tercera opción &ndash; usar variables de entorno &ndash; se explica a continuación.

### <a name="declare-environment-variables"></a>Declarar variables de entorno

Hay dos variables de entorno relacionadas con el uso de TLS en Xamarin. Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; esta variable de entorno declara la `HttpMessageHandler` predeterminada que utilizará la aplicación. Por ejemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; esta variable de entorno declarará qué biblioteca TLS se va a usar, ya sea `btls`, `legacy`o `default` (que equivale a omitir esta variable):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Esta variable de entorno se establece agregando un _archivo de entorno_ al proyecto. Un archivo de entorno es un archivo de texto sin formato de UNIX con una acción de compilación de **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de pantalla de la acción de compilación AndroidEnvironment en Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de pantalla de la acción de compilación AndroidEnvironment en Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Consulte la guía del [entorno de Xamarin. Android](~/android/deploy-test/environment.md) para obtener más detalles sobre las variables de entorno y Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
