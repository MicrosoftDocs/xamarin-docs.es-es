---
title: Autenticación de un servicio web RESTful
description: La autenticación básica solo proporciona acceso a los recursos a los clientes que tienen las credenciales correctas. En este artículo se explica cómo usar la autenticación básica para proteger el acceso a los recursos del servicio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a46d099520103ba6f1552d5bda3f619c07b743d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928528"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticación de un servicio web RESTful

_HTTP admite el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. La autenticación básica solo proporciona acceso a los recursos a los clientes que tienen las credenciales correctas. En este artículo se muestra cómo usar la autenticación básica para proteger el acceso a los recursos del servicio web RESTful._

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> ATS puede no participar en si no es posible usar el `HTTPS` Protocolo y proteger la comunicación de los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Autenticación de usuarios a través de HTTP

La autenticación básica es el mecanismo de autenticación más sencillo admitido por HTTP y implica que el cliente envíe el nombre de usuario y la contraseña como texto codificado en Base64 sin cifrar. Funciona como se indica a continuación:

- Si un servicio web recibe una solicitud de un recurso protegido, rechaza la solicitud con un código de Estado HTTP 401 (acceso denegado) y establece el encabezado de respuesta WWW-Authenticate, tal como se muestra en el diagrama siguiente:

![Error de autenticación básica](rest-images/basic-authentication-fail.png)

- Si un servicio web recibe una solicitud de un recurso protegido, con el `Authorization` encabezado establecido correctamente, el servicio web responde con un código de Estado HTTP 200, lo que indica que la solicitud se ha realizado correctamente y que la información solicitada se encuentra en la respuesta. Este escenario se muestra en el diagrama siguiente:

![Autenticación básica correcta](rest-images/basic-authentication-success.png)

> [!NOTE]
> La autenticación básica solo se debe usar a través de una conexión HTTPS. Cuando se usa a través de una conexión HTTP, el `Authorization` encabezado se puede descodificar fácilmente si un atacante captura el tráfico http.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificación de la autenticación básica en una solicitud Web

El uso de la autenticación básica se especifica de la siguiente manera:

1. La cadena "Basic" se agrega al `Authorization` encabezado de la solicitud.
1. El nombre de usuario y la contraseña se combinan en una cadena con el formato "nombre de usuario: contraseña", que se codifica en Base64 y se agrega al `Authorization` encabezado de la solicitud.

Por lo tanto, con un nombre de usuario de "XamarinUser" y una contraseña de "XamarinPassword", el encabezado se convierte en:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La `HttpClient` clase puede establecer el `Authorization` valor del encabezado en la `HttpClient.DefaultRequestHeaders.Authorization` propiedad. Dado que la `HttpClient` instancia existe en varias solicitudes, el `Authorization` encabezado solo tiene que establecerse una vez, en lugar de hacerlo al realizar cada solicitud, como se muestra en el ejemplo de código siguiente:

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

Después, cuando se realiza una solicitud a una operación de servicio Web, la solicitud se firma con el `Authorization` encabezado, que indica si el usuario tiene permiso para invocar la operación.

> [!IMPORTANT]
> Aunque este código almacena las credenciales como constantes, no deben almacenarse en un formato no seguro en una aplicación publicada.

## <a name="processing-the-authorization-header-server-side"></a>Procesar el lado servidor del encabezado de autorización

El servicio REST debe decorar cada acción con el `[BasicAuthentication]` atributo. Este atributo se usa para analizar el `Authorization` encabezado y determinar si las credenciales codificadas en Base64 son válidas comparándolos con los valores almacenados en *Web.config*. Aunque este enfoque es adecuado para un servicio de ejemplo, debe extenderse para un servicio Web de acceso público.

En el módulo de autenticación básica que usa IIS, los usuarios se autentican con sus credenciales de Windows. Por lo tanto, los usuarios deben tener cuentas en el dominio del servidor. Sin embargo, el modelo de autenticación básica puede configurarse para permitir la autenticación personalizada, donde las cuentas de usuario se autentican en un origen externo, como una base de datos de. Para obtener más información, vea [autenticación básica en ASP.net web API](https://www.asp.net/web-api/overview/security/basic-authentication) en el sitio web de ASP.net.

> [!NOTE]
> La autenticación básica no se diseñó para administrar el registro. Por lo tanto, el método de autenticación básica estándar para cerrar sesión es finalizar la sesión.

## <a name="related-links"></a>Vínculos relacionados

- [Consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
