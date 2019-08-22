---
title: Marco de redes sociales en Xamarin. iOS
description: El marco de redes sociales proporciona una API unificada para interactuar con redes sociales, como Twitter y Facebook, así como SinaWeibo para los usuarios de China.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b95c68b43e4c7fda4d60f6976ab626968800d3dc
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889808"
---
# <a name="social-framework-in-xamarinios"></a>Marco de redes sociales en Xamarin. iOS

_El marco de redes sociales proporciona una API unificada para interactuar con redes sociales, como Twitter y Facebook, así como SinaWeibo para los usuarios de China._

El uso del marco de redes sociales permite a las aplicaciones interactuar con redes sociales desde una sola API sin tener que administrar la autenticación. Incluye un controlador de vista proporcionado por el sistema para la creación de publicaciones, así como una abstracción que permite el consumo de la API de cada red social a través de HTTP.

> [!IMPORTANT]
> Para que una API multiplataforma se conecte a varias redes sociales, consulte el componente [Xamarin. social](http://components.xamarin.com/view/xamarin.social/) en el almacén de componentes de Xamarin.

## <a name="connecting-to-twitter"></a>Conexión a Twitter

### <a name="twitter-account-settings"></a>Configuración de la cuenta de Twitter

Para conectarse a Twitter mediante el marco de redes sociales, es necesario configurar una cuenta en la configuración del dispositivo, tal como se muestra a continuación:

 [![](social-framework-images/twitter01.png "Configuración de la cuenta de Twitter")](social-framework-images/twitter01.png#lightbox)

Una vez que se ha escrito y comprobado una cuenta con Twitter, cualquier aplicación en el dispositivo que use las clases de marco de redes sociales para acceder a Twitter usará esta cuenta.

### <a name="sending-tweets"></a>Envío de tweets

El marco de redes sociales incluye un `SLComposeViewController` controlador denominado que presenta una vista proporcionada por el sistema para editar y enviar un tweet. En la captura de pantalla siguiente se muestra un ejemplo de esta vista:

 [![](social-framework-images/twitter02.png "En esta captura de pantalla se muestra un ejemplo de SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Para usar `SLComposeViewController` con Twitter, se debe crear una instancia del controlador `FromService` llamando al método con `SLServiceType.Twitter` como se muestra a continuación:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Una vez `SLComposeViewController` devuelta la instancia, se puede usar para presentar una interfaz de usuario para publicar en Twitter. Sin embargo, lo primero que hay que hacer es comprobar la disponibilidad de la red social, en este caso Twitter, llamando `IsAvailable`a:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController`nunca envía un tweet directamente sin interacción del usuario. Sin embargo, se puede inicializar con los métodos siguientes:

- `SetInitialText`: Agrega el texto inicial que se va a mostrar en el tweet. 
- `AddUrl`: Agrega una dirección URL al tweet.
- `AddImage`: Agrega una imagen al tweet.


Una vez inicializado, `PresentVIewController` al llamar a se muestra la `SLComposeViewController`vista creada por. El usuario puede editar y enviar el tweet opcionalmente, o cancelar su envío. En cualquier caso, el controlador debe descartarse en `CompletionHandler`, donde también se puede comprobar el resultado para ver si se ha enviado o cancelado el tweet, como se muestra a continuación:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Ejemplo de Tweet

En el código siguiente se muestra `SLComposeViewController` el uso de para presentar una vista usada para enviar un Tweet:

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Llamada a la API de Twitter

El marco de redes sociales también incluye compatibilidad para hacer solicitudes HTTP a redes sociales. Encapsula la solicitud en una `SLRequest` clase que se usa para dirigirse a la API de la red social concreta.

Por ejemplo, el código siguiente realiza una solicitud a Twitter para obtener la escala de tiempo pública (expandiendo en el código indicado anteriormente):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Echemos un vistazo a este código en detalle. En primer lugar, obtiene acceso al almacén de cuentas y obtiene el tipo de una cuenta de Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Después, pregunta al usuario si la aplicación puede tener acceso a su cuenta de Twitter y, si se concede el acceso, la cuenta se carga en la memoria y la interfaz de usuario se actualiza:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Cuando el usuario solicita los datos de la escala de tiempo (al pulsar un botón en la interfaz de usuario), la aplicación crea primero una solicitud para acceder a los datos desde Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

En este ejemplo se limitan los resultados devueltos a las últimas `?count=10` diez entradas mediante la inclusión de en la dirección URL. Por último, adjunta la solicitud a la cuenta de Twitter (que se cargó anteriormente) y realiza la llamada a Twitter para capturar los datos:

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Si los datos se cargaron correctamente, se mostrarán los datos JSON sin procesar (como se muestra en la salida del ejemplo siguiente):

[![](social-framework-images/twitter03.png "Un ejemplo de la presentación de datos JSON sin procesar")](social-framework-images/twitter03.png#lightbox)

En una aplicación real, los resultados de JSON se pueden analizar como normal y los resultados se presentan al usuario. Vea [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md) para obtener información sobre cómo analizar JSON.

## <a name="connecting-to-facebook"></a>Conexión a Facebook

### <a name="facebook-account-settings"></a>Configuración de la cuenta de Facebook

La conexión a Facebook con el marco de redes sociales es casi idéntica al proceso usado para Twitter mostrado anteriormente. Una cuenta de usuario de Facebook debe estar configurada en la configuración del dispositivo, tal como se muestra a continuación:

[![](social-framework-images/facebook01.png "Configuración de la cuenta de Facebook")](social-framework-images/facebook01.png#lightbox)

Una vez configurada, cualquier aplicación en el dispositivo que use el marco de redes sociales usará esta cuenta para conectarse a Facebook.

### <a name="posting-to-facebook"></a>Publicar en Facebook

Como el marco de redes sociales es una API unificada diseñada para tener acceso a varias redes sociales, el código sigue siendo casi idéntico, independientemente de la red social utilizada.

Por ejemplo, `SLComposeViewController` se puede utilizar exactamente como en el ejemplo de Twitter mostrado anteriormente, el único diferente es cambiar a la configuración y las opciones específicas de Facebook. Por ejemplo:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

Cuando se usa con Facebook, `SLComposeViewController` muestra una vista que es prácticamente idéntica al ejemplo de Twitter, que muestra **Facebook** como título en este caso:

[![](social-framework-images/facebook02.png "Presentación de SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Llamada a Graph API de Facebook

Al igual que en el ejemplo de Twitter, el `SLRequest` objeto de la plataforma social se puede usar con la API Graph de Facebook. Por ejemplo, el código siguiente devuelve información de la API Graph sobre la cuenta de Xamarin (mediante la expansión del código anterior):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

La única diferencia real entre este código y la versión de Twitter presentada anteriormente es el requisito de Facebook de obtener un identificador específico para desarrolladores o aplicaciones (que puede generar en el portal para desarrolladores de Facebook), que debe establecerse como una opción al realizar la solicitud:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Si no se establece esta opción (o se usa una clave no válida), se producirá un error o no se devolverá ningún dato.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo usar el marco de redes sociales para interactuar con Twitter y Facebook. Se mostró dónde configurar cuentas para cada red social en la configuración del dispositivo. También se describe cómo utilizar `SLComposeViewController` para presentar una vista unificada para la publicación en redes sociales. Además, examinó la clase `SLRequest` que se usa para llamar a la API de cada red social.


## <a name="related-links"></a>Vínculos relacionados

- [SocialFrameworkDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/socialframeworkdemo)
- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
