---
title: Conexión a servicios web locales desde simuladores de iOS y emuladores de Android
description: En este artículo se explica cómo una aplicación móvil de Xamarin que se ejecuta en el simulador de iOS o el emulador de Android puede consumir un servicio web de ASP.NET Core que se ejecuta localmente.
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 02/04/2021
ms.openlocfilehash: 6c9e91d8c434a0deea8c419def7dc3f1800b1d06
ms.sourcegitcommit: 3b6eec7841868f50827271105577ecdc6766c162
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/06/2021
ms.locfileid: "99606581"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>Conexión a servicios web locales desde simuladores de iOS y emuladores de Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

Muchas aplicaciones móviles consumen servicios web. Durante la fase de desarrollo, es habitual implementar un servicio web localmente y consumirlo desde una aplicación móvil que se ejecuta en el simulador de iOS o el emulador de Android. De esta forma, se evita tener que implementar el servicio web en un punto de conexión hospedado y permite una experiencia de depuración sencilla porque tanto la aplicación móvil como el servicio web se ejecutan localmente.

Las aplicaciones móviles que se ejecutan en el simulador de iOS o el emulador de Android pueden consumir servicios web de ASP.NET Core que se ejecutan localmente y se exponen a través de HTTP, de la manera siguiente:

- Las aplicaciones que se ejecutan en el simulador de iOS pueden conectarse a servicios web HTTP locales a través de la dirección IP de las máquinas o mediante el nombre de host `localhost`. Por ejemplo, dado un servicio web HTTP local que expone una operación GET mediante el identificador URI relativo `/api/todoitems/`, una aplicación que se ejecute en el simulador de iOS puede consumir la operación por medio del envío de una solicitud GET a `http://localhost:<port>/api/todoitems/`.
- Las aplicaciones que se ejecutan en el emulador de Android pueden conectarse a servicios web HTTP locales mediante la dirección `10.0.2.2`, que es un alias para la interfaz de bucle invertido de host (`127.0.0.1` en la máquina de desarrollo). Por ejemplo, dado un servicio web HTTP local que expone una operación GET mediante el identificador URI relativo `/api/todoitems/`, una aplicación que se ejecute en el emulador de Android puede consumir la operación por medio del envío de una solicitud GET a `http://10.0.2.2:<port>/api/todoitems/`.

Sin embargo, se requiere trabajo adicional para que una aplicación que se ejecuta en el simulador de iOS o el emulador de Android consuma un servicio web local que se expone a través de HTTPS. En este escenario, el proceso es el siguiente:

1. Cree un certificado de desarrollo autofirmado en la máquina. Para más información, consulte [Crear un certificado de desarrollo](#create-a-development-certificate).
1. Configure el proyecto para usar la pila de red `HttpClient` adecuada para su compilación de depuración. Para más información, consulte [Configurar el proyecto](#configure-your-project).
1. Especifique la dirección de la máquina local. Para más información, consulte [Especificar la dirección de la máquina local](#specify-the-local-machine-address).
1. Omita la comprobación de seguridad del certificado de desarrollo local. Para más información, consulte [Omitir la comprobación de seguridad del certificado](#bypass-the-certificate-security-check).

Cada elemento se explicará por turnos.

## <a name="create-a-development-certificate"></a>Crear un certificado de desarrollo

Al instalar el SDK de .NET Core se instala el certificado de desarrollo HTTPS de ASP.NET Core en el almacén de certificados local del usuario. Sin embargo, aunque el certificado se ha instalado, no es de confianza. Para confiar en el certificado, realice el siguiente paso puntual para ejecutar la herramienta `dev-certs` de dotnet:

```dotnetcli
dotnet dev-certs https --trust
```

El siguiente comando proporciona ayuda sobre la herramienta `dev-certs`:

```dotnetcli
dotnet dev-certs https --help
```

De forma alternativa, cuando se ejecuta un proyecto de ASP.NET Core 2.1 (o posterior) que usa HTTPS, Visual Studio detecta si falta el certificado de desarrollo y se ofrece a instalarlo, y confía en él.

> [!NOTE]
> El certificado de desarrollo HTTPS de ASP.NET Core es autofirmado.

Para más información sobre cómo habilitar HTTPS local en la máquina, consulte [Habilitar HTTPS local](/aspnet/core/getting-started#enable-local-https).

## <a name="configure-your-project"></a>Configurar el proyecto

Las aplicaciones de Xamarin que se ejecutan en iOS y Android pueden especificar qué pila de red usa la clase `HttpClient`, siendo las opciones una pila de red administrada o pilas de red nativas. La pila administrada proporciona un alto nivel de compatibilidad con código .NET ya existente, pero está limitada a TLS 1.0 y puede ser más lenta y dar lugar a un tamaño de archivo ejecutable mayor. Las pilas nativas pueden ser más rápidas y proporcionan mayor seguridad, pero no ofrecen toda la funcionalidad de la clase `HttpClient`.

### <a name="ios"></a>iOS

Las aplicaciones de Xamarin que se ejecutan en iOS pueden usar la pila de red administrada o las pilas de red `CFNetwork` o `NSUrlSession` nativas. De forma predeterminada, los nuevos proyectos de la plataforma iOS usan la pila de red `NSUrlSession` para admitir TLS 1.2 y emplean API para mejorar el rendimiento y reducir el tamaño del archivo ejecutable. Para más información, consulte [Selector de implementación de HttpClient y SSL/TLS para iOS y macOS](~/cross-platform/macios/http-stack.md).

### <a name="android"></a>Android

Las aplicaciones de Xamarin que se ejecutan en Android pueden usar la pila de red `HttpClient` administrada o la pila de red `AndroidClientHandler` nativa. De forma predeterminada, los nuevos proyectos de la plataforma Android usan la pila de red `AndroidClientHandler` para admitir TLS 1.2 y emplean API para mejorar el rendimiento y reducir el tamaño del archivo ejecutable. Para más información sobre las pilas de red de Android, consulte [Pila HttpClient y selector de implementación de SSL/TLS para Android](~/android/app-fundamentals/http-stack.md).

## <a name="specify-the-local-machine-address"></a>Especificar la dirección de la máquina local

El simulador de iOS y el emulador de Android proporcionan ambos acceso a servicios web seguros que se ejecutan en la máquina local. Sin embargo, la dirección de la máquina local es diferente en cada uno.

### <a name="ios"></a>iOS

El simulador de iOS usa la red de la máquina host. Por lo tanto, las aplicaciones que se ejecutan en el simulador pueden conectarse a servicios web que se ejecutan en la máquina local mediante la dirección IP de las máquinas o por medio del nombre de host `localhost`. Por ejemplo, dado un servicio web seguro local que expone una operación GET mediante el identificador URI relativo `/api/todoitems/`, una aplicación que se ejecute en el simulador de iOS puede consumir la operación por medio del envío de una solicitud GET a `https://localhost:<port>/api/todoitems/`.

> [!NOTE]
> Al ejecutar una aplicación móvil en el simulador de iOS desde Windows, la aplicación se muestra en el [simulador de iOS remoto para Windows](~/tools/ios-simulator/index.md). Sin embargo, la aplicación se ejecuta en el equipo Mac emparejado. Por lo tanto, no hay ningún acceso localhost a un servicio web que se ejecute en Windows en una aplicación de iOS que se ejecute en un equipo Mac.

### <a name="android"></a>Android

Cada instancia del emulador de Android está aislada de las interfaces de red de la máquina de desarrollo y se ejecuta detrás de un enrutador virtual. Por lo tanto, un dispositivo emulado no puede ver su máquina de desarrollo u otras instancias del emulador en la red.

Sin embargo, el enrutador virtual de cada emulador administra un espacio de red especial que incluye direcciones asignadas previamente, donde la dirección `10.0.2.2` es un alias a la interfaz de bucle invertido del host (127.0.0.1 en la máquina de desarrollo). Por lo tanto, dado un servicio web seguro local que expone una operación GET mediante el identificador URI relativo `/api/todoitems/`, una aplicación que se ejecute en el emulador de Android puede consumir la operación por medio del envío de una solicitud GET a `https://10.0.2.2:<port>/api/todoitems/`.

### <a name="detect-the-operating-system"></a>Detección del sistema operativo

La clase [`DeviceInfo`](xref:Xamarin.Essentials.DeviceInfo) se puede usar para detectar la plataforma en la que se ejecuta la aplicación. El nombre de host adecuado, que permite el acceso a servicios web seguros locales, se puede establecer de la manera siguiente:

```csharp
public static string BaseAddress =
    DeviceInfo.Platform == DevicePlatform.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

Para más información sobre la clase `DeviceInfo`, consulte [Xamarin.Essentials: Información del dispositivo](~/essentials/device-information.md).

## <a name="bypass-the-certificate-security-check"></a>Omitir la comprobación de seguridad de certificado

Al intentar invocar un servicio web seguro local desde una aplicación que se ejecuta en el simulador de iOS o el emulador de Android, se producirá una excepción `HttpRequestException`, incluso cuando se usa la pila de red administrada en cada plataforma. Esto se debe a que el certificado de desarrollo HTTPS local es autofirmado y este tipo de certificado no es de confianza en iOS o Android. Por lo tanto, cuando una aplicación consume un servicio web seguro local, es necesario omitir los errores de SSL. Esto puede llevarse a cabo cuando se usan las pilas de red tanto administradas como nativas en iOS y Android; para ello, hay que establecer la propiedad `ServerCertificateCustomValidationCallback` de un objeto `HttpClientHandler` en una devolución de llamada que omita el resultado de la comprobación de seguridad del certificado de desarrollo HTTPS local:

```csharp
// This method must be in a class in a platform project, even if
// the HttpClient object is constructed in a shared project.
public HttpClientHandler GetInsecureHandler()
{
    HttpClientHandler handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

En este ejemplo de código, el resultado de la validación del certificado de servidor se devuelve cuando el certificado sometido a validación no es el certificado `localhost`. Para este certificado, el resultado de la validación se omite y se devuelve `true`, que indica que el certificado es válido. El objeto `HttpClientHandler` resultante debe pasarse como argumento al constructor `HttpClient` para las compilaciones de depuración:

```csharp
#if DEBUG
    HttpClientHandler insecureHandler = GetInsecureHandler();
    HttpClient client = new HttpClient(insecureHandler);
#else
    HttpClient client = new HttpClient();
#endif
```

## <a name="enable-http-clear-text-traffic"></a>Habilitación del tráfico HTTP de texto no cifrado

También puede configurar los proyectos iOS y Android para permitir el tráfico HTTP de texto no cifrado. Si el servicio back-end está configurado para permitir el tráfico HTTP, puede especificar HTTP en las URL base y, a continuación, configurar los proyectos para que permitan el tráfico de texto no cifrado:

```csharp
public static string BaseAddress =
    DeviceInfo.Platform == DevicePlatform.Android ? "http://10.0.2.2:5000" : "http://localhost:5000";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

### <a name="ios-ats-opt-out"></a>Deshabilitación de ATS de iOS

Para habilitar el tráfico local de texto no cifrado en iOS, debe [deshabilitar ATS](~/ios/app-fundamentals/ats.md#optout). Para ello, agregue lo siguiente en el archivo **Info.plist**:

```xml
<key>NSAppTransportSecurity</key>    
<dict>
    <key>NSAllowsLocalNetworking</key>
    <true/>
</dict>
```

### <a name="android-network-security-configuration"></a>Configuración de seguridad de red de Android

Para habilitar el tráfico local de texto no cifrado en Android, debe crear una configuración de seguridad de red. Para ello, agregue un archivo XML nuevo denominado **network_security_config.xml** en la carpeta **Resources/xml**. El archivo XML debe especificar la configuración siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
  <domain-config cleartextTrafficPermitted="true">
    <domain includeSubdomains="true">10.0.2.2</domain>
  </domain-config>
</network-security-config>
```

Luego, configure la propiedad **networkSecurityConfig** en el nodo de **aplicación** del manifiesto de Android:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest>
    <application android:networkSecurityConfig="@xml/network_security_config">
        ...
    </application>
</manifest>
```

## <a name="related-links"></a>Vínculos relacionados

- [TodoREST (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-todorest/)
- [Habilitar HTTPS local](/aspnet/core/getting-started#enable-local-https)
- [Selector de implementación de HttpClient y SSL/TLS para iOS y macOS](~/cross-platform/macios/http-stack.md)
- [Selector de implementación de pila HttpClient y SSL/TLS para Android](~/android/app-fundamentals/http-stack.md)
- [Configuración de seguridad de red de Android](https://devblogs.microsoft.com/xamarin/cleartext-http-android-network-security/)
- [Seguridad de transporte de aplicación de iOS](~/ios/app-fundamentals/ats.md)
- [Xamarin.Essentials: Información del dispositivo](~/essentials/device-information.md)
