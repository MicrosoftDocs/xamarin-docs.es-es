---
title: Autenticación de usuarios con Azure Active Directory B2C
description: Azure Active Directory B2C proporciona administración de identidades en la nube para aplicaciones web y móviles orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para integrar la administración de identidades en una aplicación móvil con la biblioteca de autenticación de Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 946cf65f7d83722fd388bed555b9d3f35c487708
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489822"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticación de usuarios con Azure Active Directory B2C

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C proporciona administración de identidades en la nube para aplicaciones web y móviles orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para integrar la administración de identidades en una aplicación móvil con la biblioteca de autenticación de Microsoft._

## <a name="overview"></a>Información general del

Azure Active Directory B2C (ADB2C) es un servicio de administración de identidades para aplicaciones orientadas al consumidor. Permite a los usuarios iniciar sesión en su aplicación mediante sus cuentas de redes sociales existentes o credenciales personalizadas, como el correo electrónico o el nombre de usuario, y la contraseña. Las cuentas de credenciales personalizadas se conocen como cuentas _locales_ .

El proceso para integrar el servicio de administración de identidades de Azure Active Directory B2C en una aplicación móvil es como sigue:

1. Crear a un inquilino de Azure Active Directory B2C.
1. Registrar la aplicación móvil con el inquilino de Azure Active Directory B2C.
1. Cree directivas para el registro y el inicio de sesión y olvidó los flujos de usuario de contraseñas.
1. Use la biblioteca de autenticación de Microsoft (MSAL) para iniciar un flujo de trabajo de autenticación con su inquilino de Azure Active Directory B2C.

> [!NOTE]
> Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de empezar.

Azure Active Directory B2C admite varios proveedores de identidades, como Microsoft, GitHub, Facebook, Twitter, etc. Para obtener más información sobre las funcionalidades de Azure Active Directory B2C, consulte la [documentación de Azure Active Directory B2C](/azure/active-directory-b2c/).

La biblioteca de autenticación de Microsoft admite varias arquitecturas de aplicaciones y plataformas. Para obtener información sobre las funcionalidades de MSAL, consulte [biblioteca de autenticación de Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) en github.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configuración de un inquilino de Azure Active Directory B2C

Para ejecutar el proyecto de ejemplo, debe crear un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [crear un inquilino de Azure Active Directory B2C en Azure portal](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Una vez que cree un inquilino, necesitará el **nombre de inquilino** y el **identificador de inquilino** para configurar la aplicación móvil. El identificador de inquilino y el nombre se definen mediante el dominio que se genera cuando se crea la dirección URL del inquilino. Si la dirección URL de inquilino generada es `https://contoso20190410tenant.onmicrosoft.com/` se `contoso20190410tenant.onmicrosoft.com` el **identificador de inquilino** y el **nombre del inquilino** es `contoso20190410tenant`. Busque el dominio del inquilino en el Azure Portal; para ello, haga clic en el **filtro directorio y suscripción** en el menú superior. En la captura de pantalla siguiente se muestra el botón de filtro de suscripción y el directorio de Azure y el dominio del inquilino:

[![nombre de inquilino en la vista de filtro de suscripciones y directorios de Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

En el proyecto de ejemplo, edite el archivo **constants.CS** para establecer los campos `tenantName` y `tenantId`. En el código siguiente se muestra cómo se deben establecer estos valores si el dominio del inquilino es `https://contoso20190410tenant.onmicrosoft.com/`, reemplace estos valores con los valores de su portal:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registre su aplicación móvil con Azure Active Directory B2C

Una aplicación móvil debe estar registrada con el inquilino para poder conectarse y autenticar a los usuarios. El proceso de registro asigna un identificador de **aplicación** único a la aplicación y una **dirección URL de redireccionamiento** que dirige las respuestas de vuelta a la aplicación después de la autenticación. Para obtener más información, consulte [Azure Active Directory B2C: registro de la aplicación](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Deberá conocer el ID. de **aplicación** asignado a la aplicación, que aparece después del nombre de la aplicación en la vista Propiedades. En la captura de pantalla siguiente se muestra dónde encontrar el identificador de la aplicación:

[![ID. de aplicación en la vista de propiedades de la aplicación de Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

La biblioteca de autenticación de Microsoft espera que la **dirección URL de redireccionamiento** de la aplicación sea el identificador de la **aplicación** con el prefijo "msal" de texto y seguido por un punto de conexión denominado "auth". Si el identificador de la aplicación es "1234abcd", la dirección URL completa debe estar `msal1234abcd://auth`. Asegúrese de que la aplicación ha habilitado la configuración de **Native Client** y cree un **URI de redireccionamiento personalizado** mediante el identificador de la aplicación, tal como se muestra en la siguiente captura de pantalla:

![URI de redireccionamiento personalizado en la vista de propiedades de la aplicación de Azure](azure-ad-b2c-images/azure-redirect-uri.png)

La dirección URL se usará más adelante en Android **ApplicationManifest. XML** y en iOS **info. plist**.

En el proyecto de ejemplo, edite el archivo **constants.CS** para establecer el campo `clientId` en el identificador de la **aplicación**. En el código siguiente se muestra cómo se debe establecer este valor si el identificador de la aplicación es `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Crear directivas de registro e inicio de sesión y olvidar las directivas de contraseñas

Una directiva es una experiencia que recorren los usuarios para completar una tarea, como crear una cuenta o restablecer una contraseña. Una directiva también especifica el contenido de los tokens que recibe la aplicación cuando el usuario vuelve de la experiencia. Debe configurar las directivas para el registro y el inicio de sesión de la cuenta y para restablecer la contraseña. Azure tiene directivas integradas que simplifican la creación de directivas comunes. Para obtener más información, consulte [Azure Active Directory B2C: directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Cuando haya completado la configuración de la Directiva, debe tener dos directivas en la vista **flujos de usuario (directivas)** en el Azure portal. En la captura de pantalla siguiente se muestran dos directivas configuradas en el Azure Portal:

![Dos directivas configuradas en la vista flujos de usuario (directivas) de Azure](azure-ad-b2c-images/azure-application-policies.png)

En el proyecto de ejemplo, edite el archivo **constants.CS** para establecer los campos `policySignin` y `policyPassword` para que reflejen los nombres elegidos durante la configuración de la Directiva:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Usar la biblioteca de autenticación de Microsoft (MSAL) para la autenticación

El paquete NuGet de la biblioteca de autenticación de Microsoft (MSAL) debe agregarse al proyecto compartido, .NET Standard y los proyectos de plataforma en una solución de Xamarin. Forms. MSAL incluye una clase `PublicClientApplicationBuilder` que crea un objeto que se adhiere a la interfaz `IPublicClientApplication`. MSAL emplea cláusulas `With` para proporcionar parámetros adicionales al constructor y a los métodos de autenticación.

En el proyecto de ejemplo, el código subyacente de **app. Xaml** define las propiedades estáticas denominadas `AuthenticationClient` y `UIParent`, y crea una instancia del objeto `AuthenticationClient` en el constructor. La cláusula `WithIosKeychainSecurityGroup` proporciona un nombre de grupo de seguridad para las aplicaciones de iOS. La cláusula `WithB2CAuthority` proporciona la **autoridad**o directiva predeterminada que se utilizará para autenticar a los usuarios. La cláusula `WithRedirectUri` indica a la instancia de Notification Hubs de Azure el URI de redireccionamiento que se usará si se especifican varios URI. En el ejemplo siguiente se muestra cómo crear una instancia del `PublicClientApplication`:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .WithRedirectUri($"msal{Constants.ClientId}://auth")
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

> [!NOTE]
> Si la instancia de Azure Notification Hubs solo tiene definido un URI de redirección, la instancia de `AuthenticationClient` puede funcionar sin especificar el URI de redirección con la cláusula `WithRedirectUri`. Sin embargo, siempre debe especificar este valor en caso de que la configuración de Azure se expanda para admitir otros clientes o métodos de autenticación.

El controlador de eventos `OnAppearing` en el código **LoginPage.Xaml.CS** subyacente llama `AcquireTokenSilentAsync` para actualizar el token de autenticación para los usuarios que iniciaron sesión antes. El proceso de autenticación redirige al `LogoutPage` si es correcto y no realiza ninguna acción en caso de error. En el ejemplo siguiente se muestra el proceso de reautenticación silenciosa en `OnAppearing`:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

El controlador de eventos `OnLoginButtonClicked` (que se desencadena cuando se hace clic en el botón de inicio de sesión) llama a `AcquireTokenAsync`. La biblioteca MSAL abre automáticamente el explorador de dispositivos móviles y navega a la página de inicio de sesión. La dirección URL de inicio de sesión, denominada **entidad de certificación**, es una combinación del nombre de inquilino y las directivas definidas en el archivo **constants.CS** . Si el usuario elige la opción Olvidó la contraseña, se devuelve a la aplicación con una excepción, lo que inicia la experiencia de olvido de contraseña. En el ejemplo siguiente se muestra el proceso de autenticación:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

El método `OnForgotPassword` es similar al proceso de inicio de sesión, pero implementa una directiva personalizada. `OnForgotPassword` usa una sobrecarga diferente de `AcquireTokenAsync`, lo que le permite proporcionar una **autoridad**específica. En el ejemplo siguiente se muestra cómo proporcionar una **entidad de certificación** personalizada al adquirir un token:

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

La parte final de la autenticación es el proceso de cierre de sesión. Se llama al método `OnLogoutButtonClicked` cuando el usuario presiona el botón Cerrar sesión. Recorre en bucle todas las cuentas y garantiza que sus tokens se han invalidado. En el ejemplo siguiente se muestra la implementación de cierre de sesión:

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

En iOS, el esquema de la dirección URL personalizada que se registró con Azure Active Directory B2C debe estar registrado en **info. plist**. MSAL espera que el esquema de la dirección URL se adhiere a un patrón específico, descrito anteriormente en [registrar la aplicación móvil con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). En la captura de pantalla siguiente se muestra el esquema de la dirección URL personalizada en **info. plist**.

!["Registrar un esquema de dirección URL personalizado en iOS"](azure-ad-b2c-images/customurl-ios.png)

MSAL también requiere derechos de cadena de claves en iOS, registrados en **Entitilements. plist**, tal como se muestra en la siguiente captura de pantalla:

!["Configuración de los derechos de aplicación en iOS"](azure-ad-b2c-images/entitlements-ios.png)

Cuando Azure Active Directory B2C se completa la solicitud de autorización, redirige a la URL de redireccionamiento registrados. El esquema de la dirección URL personalizada hace que iOS inicie la aplicación móvil y pase la dirección URL como un parámetro de inicio, donde se procesa mediante el `OpenUrl` invalidación de la clase `AppDelegate` de la aplicación y devuelve el control de la experiencia a MSAL. La implementación de `OpenUrl` se muestra en el ejemplo de código siguiente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

En Android, el esquema de la dirección URL personalizada que se registró con Azure Active Directory B2C debe estar registrado en **archivo AndroidManifest. XML**. MSAL espera que el esquema de la dirección URL se adhiere a un patrón específico, descrito anteriormente en [registrar la aplicación móvil con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). En el ejemplo siguiente se muestra el esquema de la dirección URL personalizada en **archivo AndroidManifest. XML**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

La clase `MainActivity` debe modificarse para proporcionar el objeto de `UIParent` a la aplicación durante la llamada a `OnCreate`. Cuando Azure Active Directory B2C completa la solicitud de autorización, redirige al esquema de URL registrado desde **archivo AndroidManifest. XML**. El esquema de URI registrado hace que Android llame al método `OnActivityResult` con la dirección URL como parámetro de inicio, donde lo procesa el método `SetAuthenticationContinuationEventArgs`.

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

No es necesario realizar ninguna configuración adicional para usar MSAL en el Plataforma universal de Windows

## <a name="run-the-project"></a>Ejecución del proyecto

Ejecute la aplicación en un dispositivo físico o virtual. Al pulsar el botón **Inicio de sesión** , debe abrir el explorador y navegar a una página donde puede iniciar sesión o crear una cuenta. Después de completar el proceso de inicio de sesión, debe volver a la página de cierre de sesión de la aplicación. En la captura de pantalla siguiente se muestra la pantalla de inicio de sesión de usuario que se ejecuta en Android e iOS:

!["Pantalla de inicio de sesión de Azure ADB2C en Android e iOS"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Vínculos relacionados

- [AzureADB2CAuth (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentación de la biblioteca de autenticación de Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
