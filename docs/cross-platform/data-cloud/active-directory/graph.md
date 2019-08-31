---
title: Acceso a Graph API
description: En este documento se describe cómo agregar la autenticación de Azure Active Directory a una aplicación móvil compilada con Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 74072a48e190478af79ec06ca8e5048d2cb61e36
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198568"
---
# <a name="accessing-the-graph-api"></a>Acceso a Graph API

Siga estos pasos para usar el Graph API desde una aplicación de Xamarin:

1. [Registro con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) en el portal de *windowsazure.com* ,
2. [Configurar servicios](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Paso 3. Agregar Active Directory la autenticación a una aplicación

En la aplicación, agregue una referencia a **Azure Active Directory biblioteca de autenticación (Azure Adal)** mediante el administrador de paquetes NuGet en Visual Studio o Visual Studio para Mac.
Asegúrese de seleccionar **Mostrar paquetes de versión preliminar** para incluir este paquete, ya que aún está en versión preliminar.

> [!IMPORTANT]
> Nota: Azure ADAL 3,0 es actualmente una versión preliminar y puede haber cambios importantes antes de que se publique la versión final. 


![](graph-images/06.-adal-nuget-package.jpg "Agregar una referencia a Azure Active Directory biblioteca de autenticación (Azure ADAL)")

En la aplicación, ahora necesitará agregar las siguientes variables de nivel de clase necesarias para el flujo de autenticación.

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

Lo que hay que tener en `commonAuthority`cuenta aquí es. Cuando el punto de conexión `common`de autenticación es, la aplicación se convierte en multiinquilino, lo que significa que cualquier usuario puede usar el inicio de sesión con sus credenciales de Active Directory. Después de la autenticación, el usuario trabajará en el contexto de su propio Active Directory, es decir, verá los detalles relacionados con su Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método Write para adquirir el token de acceso

El código siguiente (para Android) iniciará la autenticación y, tras la finalización, `authResult`asignará el resultado en. Las implementaciones de iOS y Windows Phone difieren ligeramente: el`Activity`segundo parámetro () es diferente en iOS y ausente en Windows Phone.

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

En el código anterior, el `AuthenticationContext` es responsable de la autenticación con commonAuthority. Tiene un `AcquireTokenAsync` método, que toma los parámetros como un recurso al que es necesario tener acceso, en este `clientId`caso `graphResourceUri`, y `returnUri`. La aplicación volverá a la cuando `returnUri` se complete la autenticación. Este código seguirá siendo el mismo para todas las plataformas; sin embargo, el último `AuthorizationParameters`parámetro,, será diferente en distintas plataformas y es responsable de controlar el flujo de autenticación.

En el caso de Android o iOS, `this` pasamos el parámetro a `AuthorizationParameters(this)` para compartir el contexto, mientras que en Windows se pasa sin ningún parámetro como nuevo. `AuthorizationParameters()`

### <a name="handle-continuation-for-android"></a>Controlar la continuación para Android

Una vez completada la autenticación, el flujo debe volver a la aplicación. En el caso de Android, se controla mediante el código siguiente, que se debe agregar a **MainActivity.CS**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>Controlar la continuación de Windows Phone

Por Windows Phone modifique el `OnActivated` método en el archivo **app.Xaml.CS** con el código siguiente:

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

Ahora, si ejecuta la aplicación, debería ver un cuadro de diálogo de autenticación.
Tras una autenticación correcta, le pedirá sus permisos para acceder a los recursos (en nuestro caso Graph API):

![](graph-images/08.-authentication-flow.jpg "Tras una autenticación correcta, le pedirá sus permisos para acceder a los recursos en nuestro caso Graph API")

Si la autenticación es correcta y ha autorizado a la aplicación para tener acceso a los recursos, debe `AccessToken` obtener `RefreshToken` un cuadro `authResult`combinado y en. Estos tokens son necesarios para otras llamadas API y para la autorización con Azure Active Directory en segundo plano.

![](graph-images/07.-access-token-for-authentication.jpg "Estos tokens son necesarios para otras llamadas API y para la autorización con Azure Active Directory en segundo plano.")

Por ejemplo, el código siguiente permite obtener una lista de usuarios de Active Directory. Puede reemplazar la dirección URL de la API Web por la API Web que está protegida por Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

