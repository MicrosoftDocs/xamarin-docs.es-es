---
title: Inicio de sesión con Apple en Xamarin. iOS
description: Iniciar sesión con Apple proporciona protección de identidad cuando se usan servicios de autenticación de terceros.
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: 5cbe3f36d1aeb12be671b14a4f76c79764e814e6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375010"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>Inicio de sesión con Apple en Xamarin. iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/ios-samples/ios13-addingthesigninwithappleflowtoyourapp/)

Iniciar sesión con Apple es un nuevo servicio que proporciona protección de identidad para los usuarios de servicios de autenticación de terceros. A partir de iOS 13, Apple requiere que cualquier aplicación nueva que use servicios de autenticación de terceros también debe proporcionar el inicio de sesión con Apple. Las aplicaciones existentes que se están actualizando no necesitan agregar el inicio de sesión con Apple hasta el 2020 de abril.

En este documento se explica cómo puede Agregar inicio de sesión con Apple a aplicaciones de iOS 13.

## <a name="apple-developer-setup"></a>Configuración de Apple Developer

Antes de compilar y ejecutar una aplicación con el inicio de sesión con Apple, debe completar estos pasos. En [certificados de desarrollador de Apple, identificadores & portal de perfiles][5] :

1. Cree un nuevo identificador de ID. de **aplicación** .
2. Establezca una descripción en el campo **Descripción** .
3. Elija un identificador de agrupación **explícito** y establezca `com.xamarin.AddingTheSignInWithAppleFlowToYourApp` en el campo.
4. Habilite el **Inicio de sesión con** la funcionalidad de Apple y registre la nueva identidad.
5. Cree un nuevo perfil de aprovisionamiento con la nueva identidad.
6. Descárguelo e instálelo en el dispositivo.
7. En Visual Studio, habilite el archivo de **Inicio de sesión con** la funcionalidad de Apple en **derechos. plist** .

## <a name="check-sign-in-status"></a>Comprobar el estado de inicio de sesión

Cuando se inicie la aplicación, o cuando necesite comprobar por primera vez el estado de autenticación de un usuario, cree una instancia de `ASAuthorizationAppleIdProvider` y compruebe el estado actual:

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

En este código, al que se llama durante `FinishedLaunching` en `AppDelegate.cs` , la aplicación se encargará de que el estado sea `NotFound` y presente al `LoginViewController` usuario. Si el estado ha devuelto `Authorized` o `Revoked` , se puede presentar una acción diferente al usuario.

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>Un LoginViewController para iniciar sesión con Apple

`UIViewController`Que implementa la lógica de inicio de sesión y proporciona inicio de sesión con Apple debe implementar `IASAuthorizationControllerDelegate` y `IASAuthorizationControllerPresentationContextProviding` como en el `LoginViewController` ejemplo siguiente.

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![Animación de una aplicación de ejemplo con el inicio de sesión con Apple](sign-in-images/sign-in-flow.png)

En este código de ejemplo se comprueba el estado de inicio de sesión actual en `PerformExistingAccountSetupFlows` y se conecta a la vista actual como delegado. Si se encuentra una credencial existente de la cadena de claves de iCloud o una credencial de identificador de Apple, se le pedirá al usuario que la use.

Apple proporciona `ASAuthorizationAppleIdButton` un botón específicamente para este propósito. Cuando se toca, el botón desencadenará el flujo de trabajo controlado en el método `HandleAuthorizationAppleIDButtonPress` .

## <a name="handling-authorization"></a>Controlar la autorización

En, `IASAuthorizationController` implemente cualquier lógica personalizada para almacenar la cuenta de usuario. En el ejemplo siguiente se almacena la cuenta del usuario en la cadena de claves, el propio servicio de almacenamiento de Apple.

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>Controlador de autorización

La parte final de esta implementación es la `ASAuthorizationController` que administra las solicitudes de autorización para el proveedor.

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="related-links"></a>Vínculos relacionados

* [Inicio de sesión con las directrices de Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [Inicie sesión con el derecho de Apple.][2]
* [Sesión de WWDC 2019 706: Introducción al inicio de sesión con Apple.][3]
* [Configuración iniciar sesión con Apple para Xamarin. Forms][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list