---
title: 'Xamarin.Essentials: Autenticador web'
description: En este documento se describe la clase WebAuthenticator de Xamarin.Essentials, que permite iniciar flujos de autenticación basados en explorador que permanecen atentos a una devolución de llamada a la aplicación.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3df31f500290189bb9ce36148729a7b1d22df3ae
ms.sourcegitcommit: 9bf375b43907384551188ec6f0ebd3290b3e9295
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2020
ms.locfileid: "92436959"
---
# <a name="no-locxamarinessentials-web-authenticator"></a>Xamarin.Essentials: Autenticador web

La clase **WebAuthenticator** permite iniciar flujos basados en explorador que escuchan una devolución de llamada a una dirección URL específica registrada en la aplicación.

## <a name="overview"></a>Información general

Muchas aplicaciones requieren la adición de la autenticación de usuario y esto suele significar permitir que los usuarios inicien sesión en sus cuentas de inicio de sesión existentes de Microsoft, Facebook, Google y ahora Apple.

La [biblioteca de autenticación de Microsoft (MSAL)](/azure/active-directory/develop/msal-overview) proporciona una excelente solución llave en mano para agregar la autenticación a la aplicación. Hay incluso compatibilidad con las aplicaciones de Xamarin en su paquete NuGet de cliente.

Si le interesa usar su propio servicio web para la autenticación, es posible usar **WebAuthenticator** para implementar la funcionalidad del lado cliente.

## <a name="why-use-a-server-back-end"></a>¿Por qué usar un back-end de servidor?

Muchos proveedores de autenticación han pasado a ofrecer solo flujos de autenticación explícitos o de dos segmentos para garantizar una mayor seguridad. Esto significa que necesitará un _"secreto de cliente"_ del proveedor para completar el flujo de autenticación. Lamentablemente, las aplicaciones móviles no son un lugar ideal para almacenar secretos y todo lo que se almacena en el código o los archivos binarios de una aplicación móvil o, en caso contrario, se considera que no son seguros a nivel general.

El procedimiento recomendado es usar un back-end web como una capa intermedia entre su aplicación móvil y el proveedor de autenticación.

> [!IMPORTANT]
> Se recomienda encarecidamente usar patrones y bibliotecas de autenticación anteriores destinados exclusivamente para móviles y que no usen un back-end web en el flujo de autenticación, debido a la falta de seguridad inherente para almacenar secretos de cliente.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **WebAuthenticator** , se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

Android requiere una configuración del filtro de intención para controlar el identificador URI de devolución de llamada. Esto se realiza con facilidad mediante la creación de subclases de la clase `WebAuthenticatorCallbackActivity`:

> [!NOTE]
> Considere la posibilidad de implementar [vínculos a aplicaciones Android](https://developer.android.com/training/app-links/) para controlar el identificador URI de devolución de llamada y asegurarse de que la aplicación es la única que se puede registrar para controlar el identificador URI de devolución de llamada.

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

También tendrá que volver a llamar a Essentials desde la invalidación `OnResume` en `MainActivity`:

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

En iOS, deberá agregar el patrón del identificador URI de devolución de llamada de la aplicación a Info.plist, como:

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLName</key>
        <string>xamarinessentials</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>xamarinessentials</string>
        </array>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
    </dict>
</array>
```

> [!NOTE]
> Considere la posibilidad de usar [vínculos a aplicaciones universales](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content) para registrar el identificador URI de devolución de llamada de la aplicación como un procedimiento recomendado.

También tendrá que invalidar los métodos `OpenUrl` y `ContinueUserActivity` de `AppDelegate` para llamar a Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}

public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    if (Xamarin.Essentials.Platform.ContinueUserActivity(application, userActivity, completionHandler))
        return true;
    return base.ContinueUserActivity(application, userActivity, completionHandler);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

En el caso de UWP, deberá declarar el identificador URI de devolución de llamada en el archivo `Package.appxmanifest`:

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>Uso de WebAuthenticator

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La API se compone principalmente de un único método `AuthenticateAsync` que adopta dos parámetros: la dirección URL que se debe usar para iniciar el flujo del explorador web y el identificador URI al que se espera que el flujo devuelva la llamada en última instancia y en la que la aplicación debe estar registrada para poder realizar el control.

El resultado es un elemento `WebAuthenticatorResult` que incluye todos los parámetros de consulta analizados desde el identificador URI de devolución de llamada:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

La API `WebAuthenticator` se encarga de iniciar la dirección URL en el explorador y esperar a que se reciba la devolución de llamada:

![Flujo de autenticación web típico](images/web-authenticator.png)

Si el usuario cancela el flujo en cualquier momento, se devuelve una excepción `TaskCanceledException`.

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

Las pestañas personalizadas se usan siempre que estén disponibles; de lo contrario, se inicia una intención para la dirección URL.

# <a name="ios"></a>[iOS](#tab/ios)

En iOS 12 o posterior, se usa `ASWebAuthenticationSession`.  En iOS 11, se usa `SFAuthenticationSession`.  En versiones anteriores de iOS, se usa `SFSafariViewController` si está disponible; de lo contrario, se utiliza Safari.

# <a name="uwp"></a>[UWP](#tab/uwp)

En UWP, se usa `WebAuthenticationBroker` si se admite; de lo contrario, se usa el explorador del sistema.

-----

## <a name="apple-sign-in"></a>Inicio de sesión de Apple

Según las [directrices de revisión de Apple](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple), si su aplicación usa cualquier servicio de inicio de sesión social para autenticarse, también debe ofrecer el inicio de sesión de Apple como una opción.

Para agregar el inicio de sesión de Apple a sus aplicaciones, primero necesitará [configurar la aplicación para que use el inicio de sesión de Apple](../ios/platform/ios13/sign-in.md).

Para iOS 13 y versiones posteriores, deberá llamar al método `AppleSignInAuthenticator.AuthenticateAsync()`. De este modo, se usará la API de inicio de sesión de Apple nativa de forma subyacente para que los usuarios obtengan la mejor experiencia posible en estos dispositivos. Puede escribir el código compartido para usar la API correcta en tiempo de ejecución de la siguiente manera:

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var accessToken = r?.AccessToken;
```

> [!TIP]
> En el caso de dispositivos que no son iOS 13, se iniciará el flujo de autenticación web, que también se puede usar para habilitar el inicio de sesión de Apple en los dispositivos Android y UWP.
> Puede iniciar sesión en su cuenta de iCloud en el simulador de iOS para probar el inicio de sesión de Apple.

-----

## <a name="aspnet-core-server-back-end"></a>Back-end de servidor de ASP.NET Core

Es posible usar la API `WebAuthenticator` con cualquier servicio back-end web.  Para usarla con una aplicación de ASP.NET Core, primero debe configurar la aplicación web con los pasos siguientes:

1. Configure los [proveedores de autenticación social externos](/aspnet/core/security/authentication/social/?tabs=visual-studio) que desee en una aplicación web de ASP.NET Core.
2. Establezca el esquema de autenticación predeterminado en `CookieAuthenticationDefaults.AuthenticationScheme` en la llamada `.AddAuthentication()`.
3. Use `.AddCookie()` en la llamada `.AddAuthentication()` de Startup.cs.
4. Todos los proveedores deben configurarse con `.SaveTokens = true;`.


```csharp
services.AddAuthentication(o =>
    {
        o.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddFacebook(fb =>
    {
        fb.AppId = Configuration["FacebookAppId"];
        fb.AppSecret = Configuration["FacebookAppSecret"];
        fb.SaveTokens = true;
    });
```

> [!TIP]
> Si desea incluir el inicio de sesión de Apple, puede usar el paquete NuGet `AspNet.Security.OAuth.Apple`.  Puede ver el [ejemplo de Startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) completo en el repositorio de GitHub de Essentials.

### <a name="add-a-custom-mobile-auth-controller"></a>Agregar un controlador de autenticación móvil personalizado

Con un flujo de autenticación móvil, suele ser conveniente iniciar el flujo directamente en un proveedor que el usuario ha elegido (por ejemplo, haciendo clic en un botón de "Microsoft" en la pantalla de inicio de sesión de la aplicación).  También es importante poder devolver información relevante a la aplicación en un identificador URI de devolución de llamada específico para finalizar el flujo de autenticación.

Para ello, use un controlador de API personalizado:

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

El propósito de este controlador es deducir el esquema (proveedor) que la aplicación está solicitando e iniciar el flujo de autenticación con el proveedor social. Cuando el proveedor devuelve la llamada al back-end web, el controlador analiza el resultado y redirige al identificador URI de devolución de llamada de la aplicación con parámetros.

En ocasiones, puede que desee devolver datos como el elemento `access_token` del proveedor a la aplicación, algo que se puede hacer con los parámetros de consulta del identificador URI de devolución de llamada. O bien, puede que desee crear su propia identidad en el servidor y pasar su propio token a la aplicación. En qué y hasta qué punto puede intervenir el usuario.

Consulte el [ejemplo del controlador completo](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) en el repositorio de Essentials.

> [!NOTE]
> En el ejemplo anterior, se muestra cómo devolver el token de acceso desde el proveedor de autenticación de terceros (es decir: OAuth). Para obtener un token que pueda usar para autorizar solicitudes web al propio back-end web, debe crear su propio token en la aplicación web y devolverlo en su lugar.  En [Información general sobre la autenticación de ASP.NET Core](/aspnet/core/security/authentication) se ofrece más información sobre los escenarios de autenticación avanzada en ASP.NET Core.

-----
## <a name="api"></a>API

- [Código fuente de WebAuthenticator](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [Documentación de la API de WebAuthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [Ejemplo de servidor de ASP.NET Core](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
