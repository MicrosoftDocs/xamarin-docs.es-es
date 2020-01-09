---
title: Touch ID y facial ID con Xamarin. iOS
description: En este documento se proporciona una descripción de alto nivel de la autenticación biométrica en iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: 744a07343b9da87f196c0664f57b7d950844ab04
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490431"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>Uso de Touch ID y el identificador de caras con Xamarin. iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS admite dos sistemas de autenticación biométricos:

1. **Touch ID** usa un sensor de huellas digitales en el botón Inicio.
1. El ID. de **rostro** usa sensores de cámara frontal para autenticar a los usuarios con un examen facial.

Touch ID se presentó en iOS 7 y en el ID. de caras en iOS 11.

Estos sistemas de autenticación dependen de un procesador de seguridad basado en hardware denominado _enclave seguro_. El enclave seguro es responsable de cifrar las representaciones matemáticas de los datos de caras y huellas digitales y de autenticar a los usuarios con esta información. Según Apple, los datos de las huellas digitales y las caras no salen del dispositivo y no se realizan copias de seguridad en iCloud. Las aplicaciones interactúan con el enclave seguro a través de la API de _autenticación local_ y no pueden recuperar datos de huellas o huellas digitales ni acceder directamente a la enclave segura.

Las aplicaciones pueden usar el Touch ID y el identificador de la persona para autenticar a un usuario antes de proporcionar acceso a contenido protegido.

## <a name="local-authentication-context"></a>Contexto de autenticación local

La autenticación biométrica en iOS se basa en un objeto de _contexto de autenticación local_ , que es una instancia de la clase `LAContext`. La clase `LAContext` permite:

- Compruebe la disponibilidad del hardware biométrico.
- Evaluar las directivas de autenticación.
- Evaluar los controles de acceso.
- Personalizar y mostrar los mensajes de autenticación.
- Reutilizar o invalidar un estado de autenticación.
- Administrar credenciales.

## <a name="detect-available-authentication-methods"></a>Detectar métodos de autenticación disponibles

El proyecto de ejemplo incluye una `AuthenticationView` respaldada por un `AuthenticationViewController`. Esta clase invalida el método `ViewWillAppear` para detectar métodos de autenticación disponibles:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

Se llama al método `ViewWillAppear` cuando la interfaz de usuario está a punto de mostrarse al usuario. Este método define una nueva instancia de `LAContext` y usa el método `CanEvaluatePolicy` para determinar si está habilitada la autenticación biométrica. Si es así, comprueba la versión del sistema y `BiometryType` enumeración para determinar qué opciones biométricas están disponibles.

Si la autenticación biométrica no está habilitada, la aplicación intenta revertir a la autenticación de PIN. Si no hay ninguna autenticación biométrica o de PIN disponible, el propietario del dispositivo no ha habilitado las características de seguridad y el contenido no se puede proteger a través de la autenticación local.

## <a name="authenticate-a-user"></a>Autenticar a un usuario

En el `AuthenticationViewController` del proyecto de ejemplo se incluye un método `AuthenticateMe`, que es responsable de autenticar al usuario:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

Se llama al método `AuthenticateMe` como respuesta al usuario que pulsa un botón de **Inicio de sesión** . Se crea una instancia de un nuevo objeto `LAContext` y se comprueba la versión del dispositivo para determinar qué propiedades se establecen en el contexto de autenticación local.

Se llama al método `CanEvaluatePolicy` para comprobar si la autenticación biométrica está habilitada, revertir a la autenticación de PIN, si es posible, y, por último, ofrecer un modo no seguro si no hay ninguna autenticación disponible. Si hay un método de autenticación disponible, se usa el método `EvaluatePolicy` para mostrar la interfaz de usuario y completar el proceso de autenticación.

El proyecto de ejemplo contiene datos ficticios y una vista para mostrar los datos si la autenticación se realiza correctamente.

## <a name="related-links"></a>Vínculos relacionados

- [Autenticación local mediante el ejemplo Touch ID o facial ID](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)
- [Acerca de Touch ID](https://support.apple.com/en-us/HT204587) en support.Apple.com
- [Acerca del ID. de caras](https://support.apple.com/en-us/HT208108) en support.Apple.com
