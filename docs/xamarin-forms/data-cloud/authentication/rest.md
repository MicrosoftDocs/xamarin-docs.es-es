---
title: Autenticar un servicio web RESTful
description: La autenticación básica proporciona acceso a los recursos solo a aquellos clientes que tienen las credenciales correctas. En este artículo se explica cómo usar la autenticación básica para proteger el acceso a los recursos de servicio web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 65606b72c3944809a09b8c70b9cdbb5524e0f856
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388595"
---
# <a name="authenticate-a-restful-web-service"></a>Autenticar un servicio web RESTful

_HTTP admite el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. La autenticación básica proporciona acceso a los recursos solo a aquellos clientes que tienen las credenciales correctas. En este artículo se muestra cómo usar la autenticación básica para proteger el acceso a los recursos de servicio web RESTful._

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) aplica conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones creadas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> ATS se puede excluir si no es posible `HTTPS` utilizar el protocolo y la comunicación segura para los recursos de Internet. Esto se puede lograr mediante la actualización del archivo **Info.plist** de la aplicación. Para obtener más información, consulte [Seguridad de transporte](~/ios/app-fundamentals/ats.md)de aplicaciones .

## <a name="authenticating-users-over-http"></a>Autenticación de usuarios a través de HTTP

La autenticación básica es el mecanismo de autenticación más simple admitido por HTTP e implica que el cliente envíe el nombre de usuario y la contraseña como texto codificado en base64 sin cifrar. Funciona como se indica a continuación:

- Si un servicio web recibe una solicitud de un recurso protegido, rechaza la solicitud con un código de estado HTTP 401 (acceso denegado) y establece el encabezado de respuesta WWW-Authenticate, como se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Si un servicio web recibe una solicitud de `Authorization` un recurso protegido, con el encabezado establecido correctamente, el servicio web responde con un código de estado HTTP 200, que indica que la solicitud se realizó correctamente y que la información solicitada está en la respuesta. Este escenario se muestra en el diagrama siguiente:

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> La autenticación básica solo se debe utilizar a través de una conexión HTTPS. Cuando se utiliza a `Authorization` través de una conexión HTTP, el encabezado se puede descodificar fácilmente si un atacante captura el tráfico HTTP.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Especificación de la autenticación básica en una solicitud web

El uso de la autenticación básica se especifica de la siguiente manera:

1. La cadena "Basic" se `Authorization` agrega al encabezado de la solicitud.
1. El nombre de usuario y la contraseña se combinan en una cadena con el formato `Authorization` "username:password", que luego se codifica en base64 y se agrega al encabezado de la solicitud.

Por lo tanto, con un nombre de usuario de 'XamarinUser' y una contraseña de 'XamarinPassword', el encabezado se convierte en:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La `HttpClient` clase puede `Authorization` establecer el `HttpClient.DefaultRequestHeaders.Authorization` valor de encabezado en la propiedad. Dado `HttpClient` que la instancia existe en `Authorization` varias solicitudes, el encabezado solo debe establecerse una vez, en lugar de al realizar cada solicitud, como se muestra en el ejemplo de código siguiente:

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

A continuación, cuando se realiza una solicitud a `Authorization` una operación de servicio web, la solicitud se firma con el encabezado, lo que indica si el usuario tiene permiso para invocar la operación.

> [!IMPORTANT]
> Aunque este código almacena las credenciales como constantes, no deben almacenarse en un formato inseguro en una aplicación publicada.

## <a name="processing-the-authorization-header-server-side"></a>Procesamiento del lado del servidor del encabezado de la autorización

El servicio REST debe decorar `[BasicAuthentication]` cada acción con el atributo. Este atributo se utiliza `Authorization` para analizar el encabezado y determinar si las credenciales codificadas en base64 son válidas comparándolas con los valores almacenados en *Web.config*. Aunque este enfoque es adecuado para un servicio de ejemplo, requiere la ampliación de un servicio web orientado al público.

En el módulo de autenticación básico utilizado por IIS, los usuarios se autentican con sus credenciales de Windows. Por lo tanto, los usuarios deben tener cuentas en el dominio del servidor. Sin embargo, el modelo de autenticación básica se puede configurar para permitir la autenticación personalizada, donde las cuentas de usuario se autentican en un origen externo, como una base de datos. Para obtener más información, consulte [Autenticación básica en ASP.NET WEB API](https://www.asp.net/web-api/overview/security/basic-authentication) en el sitio web de ASP.NET.

> [!NOTE]
> La autenticación básica no se diseñó para administrar el cierre de sesión. Por lo tanto, el enfoque de autenticación básica estándar para cerrar la sesión es finalizar la sesión.

## <a name="related-links"></a>Vínculos relacionados

- [Consumir un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
