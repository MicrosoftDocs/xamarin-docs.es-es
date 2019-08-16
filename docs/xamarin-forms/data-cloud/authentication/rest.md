---
title: Autenticación de un servicio web RESTful
description: Autenticación básica proporciona acceso a recursos solo a los clientes que tienen las credenciales correctas. En este artículo se explica cómo usar la autenticación básica para proteger el acceso a recursos de servicio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 5a0e820c8a9f04b7ad9173893852285d53dbe7a6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529203"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticación de un servicio web RESTful

_HTTP es compatible con el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. Autenticación básica proporciona acceso a recursos solo a los clientes que tienen las credenciales correctas. En este artículo se muestra cómo usar la autenticación básica para proteger el acceso a recursos de servicio web RESTful._

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticar a los usuarios a través de HTTP

Autenticación básica es el mecanismo de autenticación más sencillo compatible con HTTP e implica al cliente que envía el nombre de usuario y la contraseña como texto no cifrado en base64 codificado. Funciona como se indica a continuación:

- Si un servicio web recibe una solicitud para un recurso protegido, se rechaza la solicitud con un código de estado HTTP 401 (acceso denegado) y establece el encabezado de respuesta WWW-Authenticate, tal como se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-fail.png "Error de autenticación básica")

- Si un servicio web recibe una solicitud para un recurso protegido, con el `Authorization` encabezado correctamente establecido, el web servicio responde con un código de estado HTTP 200, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Este escenario se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-success.png "Dirige la autenticación básica")

> [!NOTE]
> Sólo debe utilizarse la autenticación básica a través de una conexión HTTPS. Cuando se utiliza en una conexión HTTP, el `Authorization` encabezado puede descodificar fácilmente si un atacante captura el tráfico HTTP.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificar la autenticación básica en una solicitud Web

Uso de la autenticación básica se especifica como sigue:

1. La cadena "Basic" se agrega a la `Authorization` encabezado de la solicitud.
1. El nombre de usuario y la contraseña se combinan en una cadena con el formato "nombreDeUsuario: contraseña", que es, a continuación, codificado en base64 y agregado a la `Authorization` encabezado de la solicitud.

Por lo tanto, con un nombre de usuario de 'XamarinUser' y una contraseña de 'XamarinPassword', el encabezado se convierte en:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

El `HttpClient` clase puede establecer el `Authorization` valor de encabezado en el `HttpClient.DefaultRequestHeaders.Authorization` propiedad. Dado que el `HttpClient` instancia existe en varias solicitudes, el `Authorization` encabezado sólo es necesario establecer una vez, en lugar de cuando realizar cada solicitud, tal como se muestra en el ejemplo de código siguiente:

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

A continuación, cuando se realiza una solicitud a una operación de servicio web se firma la solicitud con el `Authorization` encabezado, que indica si el usuario tiene permiso para invocar la operación.

> [!NOTE]
> Aunque este código almacena las credenciales como constantes, no deben almacenarse en un formato no seguro en una aplicación publicada. El [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet proporciona funcionalidad para almacenar las credenciales de forma segura. Para obtener más información, consulte [almacenar y recuperar información de cuenta en los dispositivos](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Procesamiento en el lado del servidor de encabezado de autorización

El servicio Rest debe decorar cada acción con `[BasicAuthentication]` el atributo. Este atributo se usa para analizar el `Authorization` encabezado y determinar si las credenciales codificadas en Base64 son válidas comparándolos con los valores almacenados en *Web. config*. Aunque este enfoque es adecuado para un servicio de ejemplo, debe extenderse para un servicio Web de acceso público.

En el módulo de autenticación básica utilizadas por IIS, los usuarios se autentican con sus credenciales de Windows. Por lo tanto, los usuarios deben tener cuentas en el dominio del servidor. Sin embargo, el modelo de autenticación básica se puede configurar para permitir la autenticación personalizada, donde las cuentas de usuario se autenticarán un origen externo, como una base de datos. Para obtener más información, consulte [autenticación básica en ASP.NET Web API](http://www.asp.net/web-api/overview/security/basic-authentication) en el sitio Web ASP.NET.

> [!NOTE]
> Autenticación básica no se diseñó para administrar el cierre de sesión. Por lo tanto, el enfoque de autenticación básica estándar para el cierre de sesión es para finalizar la sesión.

## <a name="related-links"></a>Vínculos relacionados

- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
