---
title: Seguridad de la capa de transporte (TLS) 1.2
description: En este documento se describe cómo habilitar TLS 1,2 para proyectos de Xamarin. iOS, Xamarin. Android y Xamarin. Mac. Muestra cómo hacerlo tanto en Visual Studio 2019 como en Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: fda004fe32b8f7d047298608a500cf72c4e9f06c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453376"
---
# <a name="transport-layer-security-tls-12"></a>Seguridad de la capa de transporte (TLS) 1.2

Es importante usar la versión más reciente de seguridad de la [ _capa de transporte_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) para garantizar la seguridad de las comunicaciones de red de la aplicación.

> [!WARNING]
> **Abril de 2018** : debido a los mayores requisitos de seguridad, incluido el cumplimiento de PCI, los principales proveedores en la nube y los servidores Web se espera que dejen de admitir versiones de TLS anteriores a 1,2. Los proyectos de Xamarin creados en versiones anteriores de Visual Studio usan de forma predeterminada versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones continúan funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin para que usen los valores siguientes y, luego, volver a compilar y volver a implementar las aplicaciones** para los usuarios.

Los proyectos deben hacer referencia al ensamblado **System .net. http** y deben configurarse como se muestra a continuación.

## <a name="update-xamarinandroid-to-tls-12"></a>Actualización de Xamarin. Android a TLS 1,2

Actualice la **implementación de HttpClient** y las opciones de **implementación de SSL/TLS** para habilitar la seguridad TLS 1,2.

> [!NOTE]
> Requiere Android 5,0 o posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Esta configuración se puede encontrar en **las propiedades del proyecto > opciones de Android** y, a continuación, en el botón **Opciones avanzadas** :

[![Configuración de HttpClient y TLS en Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración puede encontrarse en **Opciones del proyecto > Compilar > pestaña compilación de Android** :

[![Configuración de HttpClient y TLS en Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Actualización de Xamarin. iOS a TLS 1,2

Actualice la opción de **implementación de HttpClient** para habilitar la seguridad de TSL 1,2.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Esta configuración se puede encontrar en **las propiedades del proyecto > compilación de iOS**:

[![Configuración de HttpClient y TLS en Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración se puede encontrar en **Opciones del proyecto > Compilar > pestaña compilación de iOS** :

[![Configuración de HttpClient en Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Actualizar Xamarin. Mac a TLS 1,2

En Visual Studio para Mac, para habilitar TLS 1,2 en una aplicación de Xamarin. Mac, actualice la opción de **implementación de HttpClient** en **opciones de proyecto > compilación > compilación de Mac**:

[![Configuración de HttpClient en Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> La próxima versión de Xamarin.Mac 4.8 solo será compatible con macOS 10.9 o versiones posteriores.
> Las versiones anteriores de Xamarin.Mac son compatibles con macOS 10.7 o versiones posteriores, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativas

En esta sección se describen las alternativas a las configuraciones admitidas por TLS 1,2 que se muestran anteriormente.
Los desarrolladores de aplicaciones solo deben tener en cuenta estas alternativas si entienden los riesgos de usar distintos niveles de compatibilidad con TLS.

### <a name="httpclient-implementation"></a>Implementación de HttpClient

Los desarrolladores de Xamarin siempre han podido usar las clases de redes nativas en su código, pero también hay una opción que determina qué pila de red usan las `HttpClient` clases. Esto proporciona una API de .NET conocida que tiene las ventajas de velocidad y seguridad de la plataforma nativa.

Las opciones son:

- **Pila administrada** : la funcionalidad de red proporcionada por mono, o
- **Pila nativa** : varias API de red proporcionadas por las plataformas subyacentes (Android, iOS o MacOS).

La pila administrada proporciona el mayor nivel de compatibilidad con el código .NET existente, pero puede ser más lento y producir un mayor tamaño de los archivos ejecutables.

Las opciones nativas pueden ser más rápidas y tener una mayor seguridad (incluido TLS 1,2), pero es posible que no proporcionen toda la funcionalidad y las opciones de la `HttpClient` clase.

### <a name="ssltls-implementation-android"></a>Implementación de SSL/TLS (Android)

Las opciones de proyecto de Android también permiten elegir la implementación de SSL/TLS que se va a admitir:

- **Mono/administrado** : TLS 1,1 en Android
- **Nativo** : TLS 1,2 en Android.

Los nuevos proyectos de Xamarin tienen como valor predeterminado la implementación nativa que admite TLS 1,2 (que se recomienda para todos los proyectos); sin embargo, puede volver al código administrado si es necesario por motivos de compatibilidad.

> [!IMPORTANT]
> La opción **mono/Managed** se ha [quitado de las opciones de proyecto de iOS y Mac](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md) .
>
> La opción Native siempre se usa en las plataformas iOS y Mac.

## <a name="platform-specific-details"></a>Detalles específicos de la plataforma

En el resumen anterior se explica la configuración de nivel de proyecto para la implementación de HttpClient y SSL/TLS en los proyectos de Xamarin. La implementación de HttpClient también puede establecerse dinámicamente en el código. Consulte estas guías específicas de la plataforma para obtener más información:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS y Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Resumen

Las aplicaciones deben usar seguridad de la capa de transporte (TLS) 1,2 siempre que sea posible.
Debe actualizar la configuración de las aplicaciones existentes según las instrucciones de este artículo y volver a compilar y volver a implementar a sus clientes.

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Entorno de Xamarin.Android](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (2017 de febrero)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de la versión de mono 4,8: compatibilidad con TLS 1,2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler y objeto webrequesthandler explicados](/archive/blogs/henrikn/httpclient-httpclienthandler-and-webrequesthandler-explained)
- [System .net. HttpClient](/previous-versions/visualstudio/hh193681(v=vs.118))
- [System .net. HttpClientHandler](/previous-versions/visualstudio/hh138157(v=vs.118))
- [System .net. HttpMessageHandler](/previous-versions/visualstudio/hh138091(v=vs.118))
- [System .net. HttpWebRequest](/dotnet/api/system.net.httpwebrequest)
- [System.Net.WebClient](/dotnet/api/system.net.webclient)
- [System .net. WebRequest](/dotnet/api/system.net.webrequest)
- [Java. net. URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation. CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation. NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System .net. WebRequest](/dotnet/api/system.net.webrequest)
- [Cliente HTTP (ejemplo)](/samples/xamarin/ios-samples/httpclient/)