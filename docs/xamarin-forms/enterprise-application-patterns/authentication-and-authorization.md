---
title: Autenticación y autorización
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers realiza la autenticación y la autorización en los microservicios en contenedores.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 667de4d579f43558d9a811c386e355433f526077
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760455"
---
# <a name="authentication-and-authorization"></a>Autenticación y autorización

La autenticación es el proceso de obtención de credenciales de identificación, como el nombre y la contraseña de un usuario, y la validación de esas credenciales en una autoridad. Si las credenciales son válidas, la entidad que envía las credenciales se considera una identidad autenticada. Una vez que se ha autenticado una identidad, un proceso de autorización determina si esa identidad tiene acceso a un recurso determinado.

Hay muchos enfoques para integrar la autenticación y la autorización en una aplicación de Xamarin. Forms que se comunica con una aplicación web MVC de ASP.NET, incluido el uso de ASP.NET Core identidad, proveedores de autenticación externos como Microsoft, Google, Facebook, Twitter y middleware de autenticación. La aplicación móvil eShopOnContainers realiza la autenticación y la autorización con un microservicio de identidad en contenedor que usa IdentityServer 4. La aplicación móvil solicita tokens de seguridad de IdentityServer, ya sea para autenticar a un usuario o para tener acceso a un recurso. Para que IdentityServer emita tokens en nombre de un usuario, el usuario debe iniciar sesión en IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario ni una base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, se utiliza ASP.NET Core identidad para este propósito.

## <a name="authentication"></a>Authentication

La autenticación es necesaria cuando una aplicación necesita conocer la identidad del usuario actual. El mecanismo principal de ASP.NET Core para identificar usuarios es el sistema de pertenencia ASP.NET Core identidad, que almacena información de usuario en un almacén de datos configurado por el desarrollador. Normalmente, este almacén de datos será un almacén de EntityFramework, aunque se pueden usar almacenes personalizados o paquetes de terceros para almacenar información de identidad en Azure Storage, Azure Cosmos DB u otras ubicaciones.

En los escenarios de autenticación que usan un almacén de datos de usuario local y que conservan la información de identidad entre las solicitudes a través de cookies (como es habitual en las aplicaciones web MVC de ASP.NET), ASP.NET Core identidad es una solución adecuada. Sin embargo, las cookies no son siempre un medio natural de conservar y transmitir datos. Por ejemplo, una aplicación Web ASP.NET Core que exponga extremos RESTful a los que se tiene acceso desde una aplicación móvil normalmente tendrá que usar la autenticación de token de portador, ya que las cookies no se pueden usar en este escenario. Sin embargo, los tokens de portador pueden recuperarse e incluirse fácilmente en el encabezado de autorización de las solicitudes web realizadas desde la aplicación móvil.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emisión de tokens de portador con IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) es un marco de OpenID Connect y OAuth 2,0 de código abierto para ASP.net Core, que se puede usar para muchos escenarios de autenticación y autorización, como la emisión de tokens de seguridad para usuarios de identidad de ASP.net Core local.

> [!NOTE]
> OpenID Connect y OAuth 2,0 son muy similares y tienen responsabilidades diferentes.

OpenID Connect es una capa de autenticación sobre el protocolo OAuth 2,0. OAuth 2 es un protocolo que permite a las aplicaciones solicitar tokens de acceso de un servicio de token de seguridad y usarlos para comunicarse con las API. Esta delegación reduce la complejidad de las aplicaciones cliente y las API, ya que la autenticación y la autorización se pueden centralizar.

La combinación de OpenID Connect y OAuth 2,0 combina los dos aspectos fundamentales de la seguridad de la autenticación y el acceso a la API, y IdentityServer 4 es una implementación de estos protocolos.

En las aplicaciones que usan la comunicación directa de cliente a microservicio, como la aplicación de referencia eShopOnContainers, se puede usar un microservicio de autenticación dedicado que actúe como servicio de token de seguridad (STS) para autenticar a los usuarios, como se muestra en la figura. 9-1. Para obtener más información acerca de la comunicación directa de cliente a microservicio, consulte [comunicación entre cliente y microservicios](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticación mediante un microservicio de autenticación dedicado")

**Figura 9-1:** Autenticación mediante un microservicio de autenticación dedicado

La aplicación móvil eShopOnContainers se comunica con el microservicio de identidad, que usa IdentityServer 4 para realizar la autenticación y el control de acceso para las API. Por lo tanto, la aplicación móvil solicita tokens de IdentityServer, ya sea para autenticar a un usuario o para tener acceso a un recurso:

- La autenticación de usuarios con IdentityServer se logra mediante la aplicación móvil que solicita un token de *identidad* , que representa el resultado de un proceso de autenticación. Como mínimo, contiene un identificador para el usuario e información sobre cómo y cuándo se autenticó el usuario. También puede contener datos de identidad adicionales.
- El acceso a un recurso con IdentityServer se logra mediante la aplicación móvil que solicita un token de *acceso* , que permite el acceso a un recurso de la API. Los clientes solicitan tokens de acceso y los reenvían a la API. Los tokens de acceso contienen información sobre el cliente y el usuario (si existe). A continuación, las API usan esa información para autorizar el acceso a sus datos.

> [!NOTE]
> Un cliente debe estar registrado con IdentityServer para poder solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Agregar IdentityServer a una aplicación Web

Para que una aplicación Web de ASP.NET Core use IdentityServer 4, se debe agregar a la solución de Visual Studio de la aplicación Web. Para obtener más información, vea [información general](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) en la documentación de IdentityServer.

Una vez que IdentityServer se incluye en la solución de Visual Studio de la aplicación Web, debe agregarse a la canalización de procesamiento de solicitudes HTTP de la aplicación web para que pueda atender solicitudes a los puntos de conexión de OpenID Connect y OAuth 2,0. Esto se logra en el `Configure` método de la `Startup` clase de la aplicación Web, como se muestra en el ejemplo de código siguiente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

El orden es importante en la canalización de procesamiento de solicitudes HTTP de la aplicación Web. Por lo tanto, se debe agregar IdentityServer a la canalización antes del marco de interfaz de usuario que implementa la pantalla de inicio de sesión.

### <a name="configuring-identityserver"></a>Configuración de IdentityServer

IdentityServer se debe configurar en el `ConfigureServices` método en la `Startup` clase de la aplicación web llamando al `services.AddIdentityServer` método, como se muestra en el ejemplo de código siguiente de la aplicación de referencia eShopOnContainers:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Después de llamar `services.AddIdentityServer` al método, se llama a las API fluida adicionales para configurar lo siguiente:

- Credenciales usadas para firmar.
- Recursos de API e identidad a los que los usuarios pueden solicitar acceso.
- Los clientes que se conectarán a los tokens de solicitud.
- Identidad de ASP.NET Core.

> [!TIP]
> Cargue dinámicamente la configuración de IdentityServer 4. Las API de IdentityServer 4 permiten configurar IdentityServer a partir de una lista en memoria de objetos de configuración. En la aplicación de referencia eShopOnContainers, estas colecciones en memoria están codificadas de forma rígida en la aplicación. Sin embargo, en escenarios de producción se pueden cargar dinámicamente desde un archivo de configuración o desde una base de datos.

Para obtener información sobre la configuración de IdentityServer para usar ASP.NET Core identidad, consulte [uso de ASP.net Core Identity](https://identityserver4.readthedocs.io/en/latest/quickstarts/8_aspnet_identity.html) en la documentación de IdentityServer.

#### <a name="configuring-api-resources"></a>Configuración de recursos de API

Al configurar los recursos de la `AddInMemoryApiResources` API, el método `IEnumerable<ApiResource>` espera una colección. En el ejemplo de código siguiente `GetApis` se muestra el método que proporciona esta colección en la aplicación de referencia eShopOnContainers:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Este método especifica que IdentityServer debe proteger las API de pedidos y cestas. Por lo tanto, los tokens de acceso administrados de IdentityServer serán necesarios al realizar llamadas a estas API. Para obtener más información sobre `ApiResource` el tipo, consulte [recurso de API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) en la documentación de IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configuración de recursos de identidad

Al configurar recursos de identidad, `AddInMemoryIdentityResources` el método espera una `IEnumerable<IdentityResource>` colección. Los recursos de identidad son datos como el identificador de usuario, el nombre o la dirección de correo electrónico. Cada recurso de identidad tiene un nombre único y se le pueden asignar tipos de notificaciones arbitrarias, que luego se incluirán en el token de identidad del usuario. En el ejemplo de código siguiente `GetResources` se muestra el método que proporciona esta colección en la aplicación de referencia eShopOnContainers:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

La especificación de OpenID Connect especifica algunos [recursos de identidad estándar](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). El requisito mínimo es que se proporcione soporte para emitir un identificador único para los usuarios. Esto se logra exponiendo el recurso `IdentityResources.OpenId` de identidad.

> [!NOTE]
> La `IdentityResources` clase admite todos los ámbitos definidos en la especificación de OpenID Connect (OpenID, email, Profile, Phone y Address).

IdentityServer también admite la definición de recursos de identidad personalizados. Para obtener más información, vea [definir recursos de identidad personalizados](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) en la documentación de IdentityServer. Para obtener más información sobre `IdentityResource` el tipo, vea [recursos de identidad](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) en la documentación de IdentityServer 4.

#### <a name="configuring-clients"></a>Configuración de clientes

Los clientes son aplicaciones que pueden solicitar tokens de IdentityServer. Normalmente, se debe definir la configuración siguiente para cada cliente como mínimo:

- IDENTIFICADOR de cliente único.
- Interacciones permitidas con el servicio de token (conocido como tipo de concesión).
- La ubicación a la que se envían los tokens de acceso y de identidad (conocido como URI de redirección).
- Una lista de recursos a los que el cliente puede tener acceso (conocidos como ámbitos).

Al configurar clientes, el `AddInMemoryClients` método espera una `IEnumerable<Client>` colección. En el ejemplo de código siguiente se muestra la configuración de la aplicación móvil `GetClients` eShopOnContainers en el método que proporciona esta colección en la aplicación de referencia eShopOnContainers:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Esta configuración especifica datos para las siguientes propiedades:

- `ClientId`: IDENTIFICADOR único para el cliente.
- `ClientName`: El nombre para mostrar del cliente, que se usa para el registro y la pantalla de consentimiento.
- `AllowedGrantTypes`: Especifica cómo un cliente desea interactuar con IdentityServer. Para obtener más información, consulte [configuración del flujo de autenticación](#configuring_the_authentication_flow).
- `ClientSecrets`: Especifica las credenciales de secreto de cliente que se usan al solicitar tokens desde el punto de conexión del token.
- `RedirectUris`: Especifica los URI permitidos a los que se devuelven los tokens o códigos de autorización.
- `RequireConsent`: Especifica si se requiere una pantalla de consentimiento.
- `RequirePkce`: Especifica si los clientes que usan un código de autorización deben enviar una clave de prueba.
- `PostLogoutRedirectUris`: Especifica los URI permitidos a los que redirigirse después del cierre de sesión.
- `AllowedCorsOrigins`: Especifica el origen del cliente para que IdentityServer pueda permitir llamadas entre orígenes desde el origen.
- `AllowedScopes`: Especifica los recursos a los que el cliente tiene acceso. De forma predeterminada, un cliente no tiene acceso a ningún recurso.
- `AllowOfflineAccess`: Especifica si el cliente puede solicitar tokens de actualización.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configuración del flujo de autenticación

El flujo de autenticación entre un cliente y IdentityServer se puede configurar especificando los tipos de concesión en `Client.AllowedGrantTypes` la propiedad. Las especificaciones de OpenID Connect y OAuth 2,0 definen un número de flujos de autenticación, entre los que se incluyen:

- Explícito. Este flujo está optimizado para las aplicaciones basadas en explorador y debe usarse para la autenticación de usuario únicamente, o bien para las solicitudes de autenticación y de token de acceso. Todos los tokens se transmiten a través del explorador y, por tanto, no se permiten características avanzadas como los tokens de actualización.
- Código de autorización. Este flujo proporciona la capacidad de recuperar tokens en un canal posterior, en contraposición al canal frontal del explorador, a la vez que también admite la autenticación del cliente.
- Híbrido. Este flujo es una combinación de los tipos de concesión de código de autorización y implícitos. El token de identidad se transmite a través del canal del explorador y contiene la respuesta del protocolo firmada junto con otros artefactos, como el código de autorización. Después de la validación correcta de la respuesta, se debe usar el canal posterior para recuperar el acceso y el token de actualización.

> [!TIP]
> Use el flujo de autenticación híbrida. El flujo de autenticación híbrida reduce una serie de ataques que se aplican al canal del explorador y es el flujo recomendado para las aplicaciones nativas que desean recuperar tokens de acceso (y posiblemente tokens de actualización).

Para obtener más información sobre los flujos de autenticación, consulte [Grant Types](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) en la documentación de IdentityServer 4.

### <a name="performing-authentication"></a>Realización de la autenticación

Para que IdentityServer emita tokens en nombre de un usuario, el usuario debe iniciar sesión en IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario ni una base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, se utiliza ASP.NET Core identidad para este propósito.

La aplicación móvil eShopOnContainers se autentica con IdentityServer con el flujo de autenticación híbrida, que se muestra en la figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Información general de alto nivel del proceso de inicio de sesión")

**Figura 9-2:** Información general de alto nivel del proceso de inicio de sesión

Se realiza una solicitud de inicio de sesión `<base endpoint>:5105/connect/authorize`a. Después de la autenticación correcta, IdentityServer devuelve una respuesta de autenticación que contiene un código de autorización y un token de identidad. A continuación, el código de autorización `<base endpoint>:5105/connect/token`se envía a, que responde con los tokens de acceso, identidad y actualización.

La aplicación móvil eShopOnContainers cierra la sesión de IdentityServer mediante el envío de una `<base endpoint>:5105/connect/endsession`solicitud a, con parámetros adicionales. Después del cierre de sesión, IdentityServer responde enviando un URI de redireccionamiento de cierre de sesión a la aplicación móvil. En la figura 9-3 se ilustra este proceso.

![](authentication-and-authorization-images/sign-out.png "Información general de alto nivel del proceso de cierre de sesión")

**Figura 9-3:** Información general de alto nivel del proceso de cierre de sesión

En la aplicación móvil eShopOnContainers, la comunicación con IdentityServer se realiza mediante `IdentityService` la clase, que implementa la `IIdentityService` interfaz. Esta interfaz especifica que la clase de implementación debe `CreateAuthorizationRequest`proporcionar `CreateLogoutRequest`los métodos `GetTokenAsync` , y.

#### <a name="signing-in"></a>Iniciar sesión

Cuando el usuario pulsa el botón de **Inicio** de `LoginView`sesión en `SignInCommand` el, `LoginViewModel` se ejecuta en la clase, que a su vez `SignInAsync` ejecuta el método. El siguiente ejemplo de código muestra este método:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Este método invoca el `CreateAuthorizationRequest` método en la `IdentityService` clase, que se muestra en el ejemplo de código siguiente:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Este método crea el URI para el [extremo de autorización](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)de IdentityServer, con los parámetros necesarios. El punto de conexión de `/connect/authorize` autorización está en el puerto 5105 del punto de conexión base expuesto como una configuración de usuario. Para obtener más información sobre la configuración de usuario, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La superficie expuesta a ataques de la aplicación móvil eShopOnContainers se reduce implementando la clave de prueba para la extensión de intercambio de código (PKCE) en OAuth. PKCE protege el código de autorización de su uso si se intercepta. Esto lo consigue el cliente que genera un comprobador secreto, un hash de que se pasa en la solicitud de autorización y que se presenta sin hash al canjear el código de autorización. Para obtener más información sobre PKCE, consulte la [clave de prueba para el intercambio de código por parte de clientes públicos de OAuth](https://tools.ietf.org/html/rfc7636) en el sitio web del equipo de ingeniería de Internet.

El URI devuelto se almacena en `LoginUrl` la propiedad de `LoginViewModel` la clase. Cuando la `IsLogin` propiedad se `true`convierte [`WebView`](xref:Xamarin.Forms.WebView) en, `LoginView` se hace visible en el. Los `WebView` datos enlazan su [`Source`](xref:Xamarin.Forms.WebView.Source) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por tanto, realiza una solicitud de inicio de sesión a `LoginUrl` IdentityServer cuando la propiedad está establecida en el extremo de autorización de IdentityServer. Cuando IdentityServer recibe esta solicitud y el usuario no está autenticado, `WebView` se le redirigirá a la página de inicio de sesión configurada, que se muestra en la figura 9-4.

![](authentication-and-authorization-images/login.png "Página de inicio de sesión que muestra la vista Web")

**Figura 9-4:** Página de inicio de sesión que muestra la vista Web

Una vez completado el [`WebView`](xref:Xamarin.Forms.WebView) inicio de sesión, se redirigirá a un URI de devolución. Esta `WebView` navegación hará que se `NavigateAsync` ejecute el método `LoginViewModel` en la clase, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Este método analiza la respuesta de autenticación incluida en el URI devuelto y siempre que haya un código de autorización válido, realiza una solicitud al [extremo del token](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)de IdentityServer, pasando el código de autorización, el comprobador de secreto de PKCE, y otros parámetros necesarios. El punto de conexión del `/connect/token` token está en el puerto 5105 del punto de conexión base expuesto como una configuración de usuario. Para obtener más información sobre la configuración de usuario, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Valida los URI devueltos. Aunque la aplicación móvil eShopOnContainers no valida el URI de devolución, el procedimiento recomendado es validar que el URI de devolución hace referencia a una ubicación conocida para evitar ataques de redireccionamiento abierto.

Si el extremo del token recibe un código de autorización válido y un comprobador de secreto de PKCE, responde con un token de acceso, un token de identidad y un token de actualización. El token de acceso (que permite el acceso a los recursos de la API) y el token de identidad se almacenan después como configuración de la aplicación y se realiza la navegación por la página. Por lo tanto, el efecto general en la aplicación móvil eShopOnContainers es el siguiente: siempre que los usuarios puedan autenticarse correctamente con IdentityServer, se navegan a `MainView` la página, que [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) muestra la `CatalogView` como la pestaña seleccionada.

Para obtener información sobre la navegación de páginas, consulte [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información sobre [`WebView`](xref:Xamarin.Forms.WebView) cómo la navegación hace que se ejecute un método de modelo de vista, vea [invocación de navegación mediante comportamientos](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información sobre la configuración de la aplicación, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers también permite un inicio de sesión ficticio cuando la aplicación está configurada para usar servicios ficticios `SettingsView`en el. En este modo, la aplicación no se comunica con IdentityServer, en lugar de permitir que el usuario inicie sesión con las credenciales.

#### <a name="signing-out"></a>Cerrar sesión

Cuando el usuario pulsa el botón **Cerrar sesión** en el `ProfileView`, `LogoutCommand` se ejecuta en `ProfileViewModel` la clase, que a su vez ejecuta el `LogoutAsync` método. Este método realiza la navegación por la `LoginView` página a la página `LogoutParameter` , pasando una `true` instancia establecida en como un parámetro. Para obtener más información sobre cómo pasar parámetros durante la navegación por la página, vea [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Cuando se crea una vista y se navega a ella, `InitializeAsync` se ejecuta el método del modelo de vista asociado de la vista, que luego ejecuta `Logout` el método de `LoginViewModel` la clase, que se muestra en el ejemplo de código siguiente:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Este método invoca el `CreateLogoutRequest` método en la clase, pasando el token de identidad recuperado de la configuración de la `IdentityService` aplicación como un parámetro. Para obtener más información sobre la configuración de la aplicación, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). El siguiente ejemplo de código muestra la `CreateLogoutRequest` método:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Este método crea el URI para el extremo de la [sesión final](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)de IdentityServer, con los parámetros necesarios. El punto de conexión de finalización de la sesión se encuentra `/connect/endsession` en el puerto 5105 del punto de conexión base expuesto como una configuración de usuario. Para obtener más información sobre la configuración de usuario, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

El URI devuelto se almacena en `LoginUrl` la propiedad de `LoginViewModel` la clase. Mientras que `IsLogin` la propiedad `true`es, [`WebView`](xref:Xamarin.Forms.WebView) el de `LoginView` está visible. Los `WebView` datos enlazan su [`Source`](xref:Xamarin.Forms.WebView.Source) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por tanto, crea una solicitud de cierre de sesión para `LoginUrl` IdentityServer cuando la propiedad está establecida en el extremo de la sesión final de IdentityServer. Cuando IdentityServer recibe esta solicitud, siempre que el usuario haya iniciado sesión, se produce el cierre de sesión. Se realiza un seguimiento de la autenticación con una cookie administrada por el middleware de autenticación de cookies desde ASP.NET Core. Por lo tanto, al cerrar la sesión de IdentityServer se quita la cookie de autenticación y se envía un URI de redirección de cierre de sesión de nuevo al cliente.

En la aplicación móvil, [`WebView`](xref:Xamarin.Forms.WebView) se redirigirá al URI de redireccionamiento posterior al cierre de sesión. Esta `WebView` navegación hará que se `NavigateAsync` ejecute el método `LoginViewModel` en la clase, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Este método borra el token de identidad y el token de acceso de la configuración de la aplicación, `IsLogin` y establece `false`la propiedad en, [`WebView`](xref:Xamarin.Forms.WebView) lo que `LoginView` hace que el de la página sea invisible. Por último, `LoginUrl` la propiedad se establece en el URI del [punto de conexión de autorización](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)de IdentityServer, con los parámetros necesarios, como preparación para la próxima vez que el usuario inicie un inicio de sesión.

Para obtener información sobre la navegación de páginas, consulte [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información sobre [`WebView`](xref:Xamarin.Forms.WebView) cómo la navegación hace que se ejecute un método de modelo de vista, vea [invocación de navegación mediante comportamientos](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información sobre la configuración de la aplicación, consulte [Administración de configuración](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers también permite el cierre de sesión ficticio cuando la aplicación está configurada para usar servicios ficticios en SettingsView. En este modo, la aplicación no se comunica con IdentityServer y, en su lugar, borra cualquier token almacenado de la configuración de la aplicación.

<a name="authorization" />

## <a name="authorization"></a>Autorización

Después de la autenticación, ASP.NET Core API Web a menudo necesitan autorizar el acceso, lo que permite a un servicio poner API a disposición de algunos usuarios autenticados, pero no de todos.

Restringir el acceso a una ruta de MVC de ASP.NET Core se puede lograr aplicando un atributo Authorize a un controlador o una acción, lo que limita el acceso al controlador o la acción a los usuarios autenticados, como se muestra en el ejemplo de código siguiente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Si un usuario no autorizado intenta tener acceso a un controlador o una acción marcados con el `Authorize` atributo, el marco de MVC devuelve un código de estado http 401 (no autorizado).

> [!NOTE]
> Los parámetros se pueden especificar en `Authorize` el atributo para restringir una API a usuarios específicos. Para obtener más información, vea [autorización](/aspnet/core/security/authorization/introduction/).

IdentityServer se puede integrar en el flujo de trabajo de autorización para que los tokens de acceso proporcionen autorización de control. Este enfoque se muestra en la figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorización por token de acceso")

**Figura 9-5:** Autorización por token de acceso

La aplicación móvil eShopOnContainers se comunica con el microservicio de identidad y solicita un token de acceso como parte del proceso de autenticación. El token de acceso se reenvía a las API expuestas por los microservicios de pedidos y cestas como parte de las solicitudes de acceso. Los tokens de acceso contienen información sobre el cliente y el usuario. A continuación, las API usan esa información para autorizar el acceso a sus datos. Para obtener información sobre cómo configurar IdentityServer para proteger las API, consulte [configuración de recursos de API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configuración de IdentityServer para realizar la autorización

Para realizar la autorización con IdentityServer, su middleware de autorización debe agregarse a la canalización de solicitudes HTTP de la aplicación Web. El middleware se agrega en `ConfigureAuth` el método en la `Startup` clase de la aplicación Web, que se invoca desde el `Configure` método, y se muestra en el siguiente ejemplo de código de la aplicación de referencia eShopOnContainers:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Este método garantiza que solo se puede tener acceso a la API con un token de acceso válido. El middleware valida el token de entrada para asegurarse de que se envía desde un emisor de confianza y valida que el token es válido para su uso con la API que lo recibe. Por lo tanto, al ir al controlador de pedidos o cestas, se devolverá un código de Estado HTTP 401 (no autorizado), que indica que se requiere un token de acceso.

> [!NOTE]
> El middleware de autorización de IdentityServer debe agregarse a la canalización de solicitudes HTTP de la aplicación `app.UseMvc()` Web `app.UseMvcWithDefaultRoute()`antes de agregar MVC con o.

### <a name="making-access-requests-to-apis"></a>Realización de solicitudes de acceso a las API

Al realizar solicitudes a los microservicios de pedidos y cestas, el token de acceso, Obtenido de IdentityServer durante el proceso de autenticación, se debe incluir en la solicitud, como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

El token de acceso se almacena como una configuración de aplicación y se recupera del almacenamiento específico de la plataforma y se incluye en la `GetOrderAsync` llamada al método `OrderService` en la clase.

Del mismo modo, se debe incluir el token de acceso al enviar datos a una API protegida por IdentityServer, como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

El token de acceso se recupera del almacenamiento específico de la plataforma y se incluye en la `UpdateBasketAsync` llamada al método `BasketService` en la clase.

La `RequestProvider` clase, en la aplicación móvil eShopOnContainers, usa la `HttpClient` clase para hacer solicitudes a las API de RESTful expuestas por la aplicación de referencia eShopOnContainers. Al realizar solicitudes a las API de pedidos y cestas, que requieren autorización, se debe incluir un token de acceso válido con la solicitud. Esto se logra agregando el token de acceso a los encabezados de la `HttpClient` instancia, como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

La `DefaultRequestHeaders` propiedad de la `HttpClient` clase expone los encabezados que se envían con cada solicitud y el `Authorization` token de acceso se agrega al encabezado con el prefijo de la cadena `Bearer`. Cuando la solicitud se envía a una API de RESTful, se extrae `Authorization` el valor del encabezado y se valida para asegurarse de que se envía desde un emisor de confianza y se usa para determinar si el usuario tiene permiso para invocar la API que lo recibe.

Para obtener más información sobre cómo la aplicación móvil eShopOnContainers realiza solicitudes Web, consulte [acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumen

Hay muchos enfoques para integrar la autenticación y la autorización en una aplicación de Xamarin. Forms que se comunica con una aplicación web MVC de ASP.NET. La aplicación móvil eShopOnContainers realiza la autenticación y la autorización con un microservicio de identidad en contenedor que usa IdentityServer 4. IdentityServer es un marco de OpenID Connect y OAuth 2,0 de código abierto para ASP.NET Core que se integra con ASP.NET Core identidad para realizar la autenticación de token de portador.

La aplicación móvil solicita tokens de seguridad de IdentityServer, ya sea para autenticar a un usuario o para tener acceso a un recurso. Al acceder a un recurso, se debe incluir un token de acceso en la solicitud a las API que requieren autorización. El middleware de IdentityServer valida los tokens de acceso entrantes para asegurarse de que se envían desde un emisor de confianza y que son válidos para su uso con la API que los recibe.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
