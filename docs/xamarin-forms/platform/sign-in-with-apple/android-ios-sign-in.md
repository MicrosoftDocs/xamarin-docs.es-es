---
title: Uso del inicio de sesión con Apple para Xamarin. Forms
description: Aprenda a implementar el inicio de sesión con Apple en sus aplicaciones móviles de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: df011a6eb72b6eb30af0a197d4be48b0f2494384
ms.sourcegitcommit: fc689c1a6b641c124378dedc1bd157d96fc759a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319521"
---
# <a name="use-sign-in-with-apple-in-xamarinforms"></a>Uso del inicio de sesión con Apple en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/signinwithapple/)

Iniciar sesión con Apple es para todas las aplicaciones nuevas de iOS 13 que usan servicios de autenticación de terceros. Los detalles de implementación entre iOS y Android son bastante diferentes. En esta guía se explica cómo puede hacerlo hoy mismo en Xamarin. Forms.

En esta guía y muestra, se usan servicios de plataforma específicos para controlar el inicio de sesión con Apple:

- Android con un servicio Web genérico que se comunica con Azure Functions con OpenID/OpenAuth
- iOS usa la API nativa para la autenticación en iOS 13 y recurre a un servicio Web genérico para iOS 12 y versiones anteriores

## <a name="a-sample-apple-sign-in-flow"></a>Un flujo de inicio de sesión de Apple de ejemplo

Este ejemplo ofrece una implementación de bien fundamentadas para que Apple Sign in funcione en su aplicación de Xamarin. Forms.

Usamos dos Azure Functions para ayudar con el flujo de autenticación:

1. `applesignin_auth`: Genera la dirección URL de autorización de inicio de sesión de Apple y la redirige a ella.  Haremos esto en el lado del servidor, en lugar de en la aplicación móvil, para que podamos `state` almacenar en caché el y validarlo cuando los servidores de Apple envíen una devolución de llamada.
2. `applesignin_callback`: Controla la devolución de llamada POST de Apple y intercambia de forma segura el código de autorización para un token de acceso y un token de identificador.  Por último, redirige de nuevo al esquema de URI de la aplicación, devolviendo los tokens en un fragmento de dirección URL.

La aplicación móvil se registra para controlar el esquema de URI personalizado que hemos seleccionado (en este caso `xamarinformsapplesignin://`), por `applesignin_callback` lo que la función puede retransmitir los tokens de vuelta a él.

Cuando el usuario inicia la autenticación, se producen los pasos siguientes:

1. La aplicación móvil genera un `nonce` valor `state` y y los pasa a la `applesignin_auth` función de Azure.
2. La `applesignin_auth` función de Azure genera una dirección URL de autorización de inicio de sesión `state` de `nonce`Apple (con el proporcionado y el) y redirige al explorador de aplicaciones móviles.
3. El usuario escribe sus credenciales de forma segura en la página de autorización de inicio de sesión de Apple hospedada en los servidores de Apple.
4. Una vez que el flujo de inicio de sesión de Apple finaliza en los servidores de Apple, `redirect_uri` Apple redirige a `applesignin_callback` la que será la función de Azure.
5. La solicitud de Apple enviada a la `applesignin_callback` función se valida para asegurarse de que se `state` devuelve el correcto y que las notificaciones del token de identificador son válidas.
6. La `applesignin_callback` función de Azure intercambia `code` el publicado por Apple para un token de _acceso_, un _token de actualización_y un _token de identificador_ (que contiene notificaciones sobre el ID. de usuario, el nombre y el correo electrónico).
7. Finalmente `applesignin_callback` , la función de Azure redirige de nuevo al esquema de URI de`xamarinformsapplesignin://`la aplicación () anexando un fragmento de URI con los `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...`tokens (por ejemplo,).
8. La aplicación móvil analiza el fragmento de URI en `AppleAccount` y valida que la `nonce` demanda recibida coincida con la `nonce` generada al inicio del flujo.
9. La aplicación móvil ya está autenticada.

## <a name="azure-functions"></a>Comprobación de

Este ejemplo utiliza Azure Functions. Como alternativa, un controlador de ASP.NET Core o una solución de servidor Web similar podría ofrecer la misma funcionalidad.

### <a name="configuration"></a>Configuración

Deben configurarse varias opciones de configuración de la aplicación al usar Azure Functions:

- `APPLE_SIGNIN_KEY_ID`: Este es el `KeyId` anterior.
- `APPLE_SIGNIN_TEAM_ID`: Suele ser el identificador de equipo que se encuentra en el [Perfil de pertenencia](https://developer.apple.com/account/#/membership) _._
- `APPLE_SIGNIN_SERVER_ID`: Este es el `ServerId` anterior.  *No* es el identificador de la _agrupación_de aplicaciones, sino el *identificador* del *identificador de servicios* que creó.
- `APPLE_SIGNIN_APP_CALLBACK_URI`: Este es el esquema de URI personalizado que quiere redirigir de nuevo a la aplicación con.  En este ejemplo `xamarinformsapplesignin://` se utiliza.
- `APPLE_SIGNIN_REDIRECT_URI`-La *dirección URL de redireccionamiento* que se configura al crear el *identificador de servicios* en la sección de configuración *de inicio de sesión de Apple* .  Para probarlo, podría ser similar a lo siguiente:`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`: El contenido de texto del `.p8` archivo, con todas las `\n` nuevas líneas quitadas, por lo que es una cadena larga.

### <a name="security-considerations"></a>Consideraciones de seguridad

**Nunca** almacene la clave de P8 dentro del código de la aplicación. El código de aplicación es fácil de descargar y desensamblar. 

También se considera una práctica inadecuada usar un `WebView` para hospedar el flujo de autenticación y para interceptar eventos de navegación por la dirección URL para obtener el código de autorización. En este momento, actualmente no hay ninguna manera totalmente segura de controlar el inicio de sesión con Apple en dispositivos que no son iOS13 y sin hospedar código en un servidor para controlar el intercambio de tokens. Se recomienda hospedar el código de generación de la dirección URL de autorización en un servidor para que pueda almacenar en caché el estado y validarlo cuando Apple emita una devolución de llamada POST al servidor.

## <a name="a-cross-platform-sign-in-service"></a>Un servicio de inicio de sesión multiplataforma

Con Xamarin. Forms DependencyService, puede crear servicios de autenticación independientes que usan los servicios de la plataforma en iOS y un servicio Web genérico para Android y otras plataformas que no son iOS basados en una interfaz compartida.

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

En iOS, se usan las API nativas:

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

La marca `__IOS__13` de compilación se usa para proporcionar compatibilidad con iOS 13 así como versiones heredadas que se reservan al servicio Web genérico.

En Android, se usa el servicio Web genérico con Azure Functions:

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>Resumen

En este artículo se describen los pasos necesarios para configurar el inicio de sesión con Apple para su uso en las aplicaciones de Xamarin. Forms.

## <a name="related-links"></a>Vínculos relacionados

- [XamarinFormsAppleSignIn (ejemplo)](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [Inicio de sesión con las directrices de Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
