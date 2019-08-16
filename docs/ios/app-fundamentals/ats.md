---
title: Seguridad de transporte de aplicaciones en Xamarin. iOS
description: La seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 6890846a1cb5cd44429c9157b3aeb75c7a1f2f4b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521428"
---
# <a name="app-transport-security-in-xamarinios"></a>Seguridad de transporte de aplicaciones en Xamarin. iOS

_La seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación._

En este artículo se presentan los cambios de seguridad que la seguridad de transporte de aplicaciones aplica en una aplicación iOS 9 y [lo que esto significa para los proyectos de Xamarin. iOS](#xamarinsupport), se tratarán las [Opciones de configuración de ATS](#config) y se explicará cómo no participar en [ATS](#optout) . ATS si es necesario. Dado que ATS está habilitado de forma predeterminada, todas las conexiones a Internet no seguras producirán una excepción en las aplicaciones de iOS 9 (a menos que lo permita explícitamente).


## <a name="about-app-transport-security"></a>Acerca de la seguridad de transporte de aplicaciones

Como se indicó anteriormente, ATS garantiza que todas las comunicaciones de Internet en iOS 9 y OS X el capitan se ajusten a los procedimientos recomendados de conexión segura, lo que evita la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o de una biblioteca. consumiendo.

En el caso de las aplicaciones `HTTPS` existentes, implemente el protocolo siempre que sea posible. En el caso de las nuevas aplicaciones de Xamarin. `HTTPS` iOS, debe usar exclusivamente al comunicarse con recursos de Internet. Además, la comunicación de API de alto nivel debe cifrarse con la versión 1,2 de TLS con confidencialidad directa.

Cualquier conexión realizada con [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) usará ATS de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10,11 (el Capitan).

## <a name="default-ats-behavior"></a>Comportamiento de ATS predeterminado

Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10,11 (el Capitan), todas las conexiones que usan [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

### <a name="ats-connection-requirements"></a>Requisitos de conexión de ATS

ATS aplicará los siguientes requisitos para todas las conexiones a Internet:

- Todos los cifrados de conexión deben usar la confidencialidad directa. Consulte la lista de cifrados aceptados a continuación.
- El protocolo de seguridad de la capa de transporte (TLS) debe ser de la versión 1,2 o superior.
- Se debe usar al menos una huella digital SHA256 con una clave RSA de 2048 bits o superior, o una clave de curva elíptica (ECC) de 256 bits o superior para todos los certificados.

Una vez más, dado que ATS está habilitado de forma predeterminada en iOS 9, cualquier intento de establecer una conexión que no cumpla estos requisitos producirá una excepción. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Cifrado compatible con ATS

Las comunicaciones de Internet protegidas con ATS aceptan el siguiente tipo de cifrado de confidencialidad hacia delante:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Para obtener más información sobre cómo trabajar con clases de comunicación de Internet de iOS, consulte referencia de la [clase NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)de Apple, referencia de [CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o [referencia de clase NSURLSession](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Compatibilidad con ATS en Xamarin. iOS

Dado que ATS está habilitado de forma predeterminada en iOS 9 y OS X el Capitan, si la aplicación de Xamarin. iOS o cualquier biblioteca o servicio que usa realiza la conexión a Internet, deberá realizar alguna acción o las conexiones darán lugar a que se produzca una excepción.

En el caso de una aplicación existente, Apple sugiere `HTTPS` que se admita el protocolo lo antes posible. Si no puede porque se está conectando a un servicio Web de terceros que no es `HTTPS` compatible con o `HTTPS` si no lo es, puede no participar en ATS. Para más información, consulte la sección [de aceptación de ATS](#optout) a continuación.

En el caso de una nueva aplicación de Xamarin. iOS `HTTPS` , debe usar exclusivamente al comunicarse con recursos de Internet. De nuevo, puede haber situaciones (como el uso de un servicio Web de terceros) en las que esto no sea posible y deberá dejar de usar ATS.

Además, ATS exige que la comunicación de API de alto nivel se cifre mediante la versión 1,2 de TLS con confidencialidad directa. Consulte las secciones [requisitos de conexión de ATS](#ats-connection-requirements) y cifrados [compatibles con ATS](#ats-compatible-ciphers) anteriores para obtener más detalles.

Aunque es posible que no esté familiarizado con TLS (seguridad de la[capa de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security)), es el sucesor de SSL (capa de[sockets seguros](https://en.wikipedia.org/wiki/Transport_Layer_Security)) y proporciona una colección de protocolos criptográficos para aplicar la seguridad a través de las conexiones de red.

El nivel de TLS está controlado por el servicio Web que está consumiendo y, por lo tanto, está fuera del control de la aplicación. `HttpClient` Tanto`ModernHttpClient` como deben usar automáticamente el nivel máximo de cifrado TLS compatible con el servidor.

En función del servidor con el que esté hablando (especialmente si se trata de un servicio de terceros), es posible que tenga que deshabilitar la confidencialidad directa o seleccionar un nivel de TLS inferior. Consulte la sección [configuración de las opciones de ATS](#configuring-ats-options) más adelante para obtener más detalles.

> [!IMPORTANT]
> La seguridad de transporte de aplicaciones no se aplica a las aplicaciones de Xamarin mediante **implementaciones HTTPClient administradas**. Solo se aplica a las conexiones que usan implementaciones de CFNetwork **httpclient** o **NSURLSession httpclient** .

### <a name="setting-the-httpclient-implementation"></a>Establecimiento de la implementación de HTTPClient

Para establecer la implementación de HTTPClient usada por una aplicación de iOS, haga doble clic en el **proyecto** en el **Explorador de soluciones** para abrir las **Opciones del proyecto**. Vaya a **compilación de iOS** y seleccione el tipo de cliente que desee en la lista desplegable **implementación de HttpClient** :

![](ats-images/client01.png "Establecer las opciones de compilación de iOS")


#### <a name="managed-handler"></a>Controlador administrado

El controlador administrado es el controlador HttpClient totalmente administrado que se ha incluido con versiones anteriores de Xamarin. iOS y es el controlador predeterminado.

Profesionales ti

- Es la más compatible con Microsoft .NET y la versión anterior de Xamarin.

Iconos

- No está totalmente integrado con iOS (por ejemplo, está limitado a TLS 1,0).
- Normalmente es mucho más lento que las API nativas.
- Requiere más código administrado y crea aplicaciones de mayor tamaño.

#### <a name="cfnetwork-handler"></a>Controlador CFNetwork

El controlador basado en CFNetwork se basa en el `CFNetwork` marco nativo.

Profesionales ti

- Usa la API nativa para mejorar el rendimiento y los tamaños de los archivos ejecutables más pequeños.
- Agrega compatibilidad con estándares más recientes, como TLS 1,2.

Iconos

- Requiere iOS 6 o posterior.
- No está disponible en watchos.
- Algunas características y opciones de HttpClient no están disponibles.

#### <a name="nsurlsession-handler"></a>Controlador NSUrlSession

El controlador basado en NSUrlSession se basa en la `NSUrlSession` API nativa.

Profesionales ti

- Usa la API nativa para mejorar el rendimiento y los tamaños de los archivos ejecutables más pequeños.
- Agrega compatibilidad con estándares más recientes, como TLS 1,2.

Iconos

- Requiere iOS 7 o posterior.
- Algunas características y opciones de HttpClient no están disponibles. 

## <a name="diagnosing-ats-issues"></a>Diagnóstico de problemas de ATS

Al intentar conectarse a Internet, ya sea directamente o desde una vista Web en iOS 9, puede obtener un error con el formato:

> La seguridad de transporte de la aplicación ha bloqueado http://www.-the-blocked-domain.com) un http de texto no cifrado (carga de recursos, ya que no es seguro. Las excepciones temporales se pueden configurar mediante el archivo info. plist de la aplicación.

En iOS9, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación mediante el `HTTPS` protocolo y la comunicación de API de alto nivel que se va a cifrar con la versión 1,2 de TLS con confidencialidad directa.

Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10,11 (el `NSURLConnection`Capitan `CFURL` ) `NSURLSession` , todas las conexiones que usan o estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Apple también proporciona la [aplicación de ejemplo TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que se puede compilar (o, opcionalmente, transcodificar en Xamarin y C#) y usarse para diagnosticar problemas de ATS/TLS. Consulte la sección [de aceptación de ATS](#optout) a continuación para obtener información sobre cómo resolver este problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configuración de las opciones de ATS

Puede configurar varias de las características de ATS estableciendo valores para claves específicas en el archivo **info. plist** de la aplicación. Las siguientes claves están disponibles para controlar ATS (_con sangría para mostrar cómo se anidan_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Cada clave tiene el siguiente tipo y significado:

- **NSAppTransportSecurity** (`Dictionary`): Contiene todas las claves y valores de configuración de ATS.
- **NSAllowsArbitraryLoads** (`Boolean`): Si `YES` se va a deshabilitar ATS para cualquier dominio no `NSExceptionDomains`incluido en. En el caso de los dominios enumerados, se usará la configuración de seguridad especificada.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`): Si `YES` permite que las páginas web se carguen correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- **NSExceptionDomains** (`Dictionary`): Una colección de dominios que y la configuración de seguridad que ATS debe usar para un dominio determinado.
- `Dictionary`  **Domain-Name-for-Exception-As-String>():unacoleccióndeexcepcionesparaundominio\<** determinado (por ejemplo, `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`): La versión de TLS mínima `TLSv1.0`como `TLSv1.1` o `TLSv1.2` (que es el valor predeterminado).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`): Si `NO` el dominio no tiene que usar un cifrado con seguridad de reenvío. El valor predeterminado es `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`): Si `NO` (valor predeterminado) todas las comunicaciones con este dominio deben estar en `HTTPS` el protocolo.
- **NSRequiresCertificateTransparency** (`Boolean`): Si `YES` el capa de sockets seguros del dominio (SSL) debe incluir datos de transparencia válidos. El valor predeterminado es `NO`.
- **NSIncludesSubdomains** (`Boolean`): Si `YES` esta configuración invalida todos los subdominios de este dominio. El valor predeterminado es `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`): La versión de TLS que se usa cuando el dominio es un servicio de terceros fuera del control del desarrollador.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`): Si `YES` un dominio de terceros requiere confidencialidad directa.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`): Si `YES` el ATS va a permitir la comunicación no segura con dominios de terceros.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Retirada de ATS

Aunque Apple recomienda encarecidamente usar `HTTPS` el protocolo y proteger la comunicación con la información basada en Internet, puede haber ocasiones en las que no siempre es posible. Por ejemplo, si se comunica con un servicio Web de terceros o con anuncios entregados por Internet en la aplicación.

Si la aplicación de Xamarin. iOS debe realizar una solicitud a un dominio no seguro, los siguientes cambios en el archivo **info. plist** de la aplicación deshabilitarán los valores predeterminados de seguridad que se aplican con ATS para un dominio determinado:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

Dentro de Visual Studio para Mac, haga doble clic `Info.plist` en el archivo en el **Explorador de soluciones**, cambie a la vista de **código fuente** y agregue las claves anteriores:

[![](ats-images/ats01.png "La vista de origen del archivo info. plist")](ats-images/ats01.png#lightbox)


Si la aplicación necesita cargar y mostrar contenido web desde sitios no seguros, agregue lo siguiente al archivo **info. plist** de la aplicación para permitir que las páginas web se carguen correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Opcionalmente, puede realizar los siguientes cambios en el archivo **info. plist** de la aplicación para deshabilitar completamente ATS para todos los dominios y la comunicación de Internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro de Visual Studio para Mac, haga doble clic `Info.plist` en el archivo en el **Explorador de soluciones**, cambie a la vista de **código fuente** y agregue las claves anteriores:

[![](ats-images/ats02.png "La vista de origen del archivo info. plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Si la aplicación requiere una conexión a un sitio web inseguro, **siempre** debe escribir el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar completamente el ATS usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` solo debe usarse en situaciones de emergencias extremas.




De nuevo, la deshabilitación de ATS _solo_ debe usarse como último recurso, si el cambio a conexiones seguras no está disponible o no es práctico.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha introducido la seguridad de transporte de aplicaciones (ATS) y se describe la manera en que se aplican las comunicaciones seguras con Internet. En primer lugar, hemos tratado los cambios de ATS necesarios para una aplicación de Xamarin. iOS que se ejecuta en iOS 9. Después, hemos tratado el control de las características y opciones de ATS. Por último, hemos tratado la aceptación de ATS en la aplicación Xamarin. iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
