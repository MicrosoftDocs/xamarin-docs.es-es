---
title: AuthenticateUsers con un proveedor de identidades
description: Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 25a09e27fb25e477c5176af0ee4a75a836751ccf
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487638"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticación de usuarios con un proveedor de identidades

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin. auth es un SDK multiplataforma para autenticar a los usuarios y almacenar sus cuentas. Incluye autenticadores de OAuth que proporcionan compatibilidad para consumir proveedores de identidades, como Google, Microsoft, Facebook y Twitter. En este artículo se explica cómo usar Xamarin. auth para administrar el proceso de autenticación en una aplicación de Xamarin. Forms._

OAuth es un estándar abierto para la autenticación y permite un propietario del recurso notificar a un proveedor de recursos que se debe conceder permiso a un tercero para tener acceso a su información sin compartir la identidad de los propietarios de recursos. Un ejemplo de esto sería la habilitación de un usuario notificar a un proveedor de identidades (por ejemplo, Google, Microsoft, Facebook o Twitter) que se debe conceder permiso a una aplicación para acceder a sus datos, sin compartir la identidad del usuario. Normalmente se usa como un enfoque para los usuarios para iniciar sesión en sitios Web y aplicaciones mediante un proveedor de identidades, pero sin exponer su contraseña para el sitio Web o aplicación.

Una descripción general del flujo de autenticación al consumir un proveedor de identidades de OAuth es como sigue:

1. La aplicación navega en un explorador a una dirección URL de proveedor de identidad.
1. El proveedor de identidades controla la autenticación de usuario y devuelve un código de autorización a la aplicación.
1. La aplicación intercambia el código de autorización para un token de acceso del proveedor de identidades.
1. La aplicación utiliza el token de acceso para acceder a las API en el proveedor de identidades, como una API para solicitar datos de usuario básica.

La aplicación de ejemplo muestra cómo usar Xamarin.Auth para implementar un flujo de autenticación nativa con Google. Mientras se usa Google como proveedor de identidades en este tema, el enfoque es igualmente aplicable a otros proveedores de identidades. Para obtener más información sobre la autenticación mediante el punto de conexión de Google OAuth 2.0, consulte [utilizando OAuth2.0 para el acceso a Google APIs](https://developers.google.com/identity/protocols/OAuth2) en el sitio Web de Google.

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige conexiones seguras entre los recursos de Internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse al actualizar el archivo **Info.plist** de la aplicación. Para obtener más información, consulte [Seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Uso de Xamarin.Auth para autenticar a los usuarios

Xamarin.Auth admite dos enfoques para que las aplicaciones interactúan con el punto de conexión de autorización del proveedor de identidades:

1. Uso de una vista web incrustado. Aunque esto ha sido una práctica común, ya no se recomienda por las razones siguientes:

    - La aplicación que hospeda la vista web puede tener acceso a las credenciales del usuario autenticación completa, no solo la concesión de autorización de OAuth que fue diseñada para la aplicación. Esto infringe el principio de privilegio mínimo, ya que la aplicación tiene acceso a las credenciales más eficaces que requiere, aumentando potencialmente la superficie de ataque de la aplicación.
    - La aplicación host podría capturar los nombres de usuario y contraseñas, automáticamente enviar formularios y omitir el consentimiento del usuario y copie las cookies de sesión y usarlos para realizar acciones autenticadas como usuario.
    - Vistas web incrustadas no compartan el estado de autenticación con otras aplicaciones o el explorador web del dispositivo, solicitar al usuario que inicie sesión para cada solicitud de autorización que se considera una experiencia de usuario inferiores.
    - Algunos puntos de conexión de autorización tomar medidas para detectar y bloquear las solicitudes de autorización que procedan de vistas web.

1. Utilizando el explorador web del dispositivo, que es el enfoque recomendado. Utilizando el explorador del dispositivo para las solicitudes de OAuth mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión en el proveedor de identidad una vez por dispositivo, mejora las tasas de conversión de flujos de inicio de sesión y la autorización en la aplicación. El explorador del dispositivo también proporciona seguridad mejorada, como las aplicaciones pueden inspeccionar y modificar el contenido en una vista web, pero no el contenido que se muestra en el explorador. Este es el enfoque adoptado en este artículo y aplicación de ejemplo.

En el siguiente diagrama se muestra una descripción general de cómo la aplicación de ejemplo utiliza Xamarin.Auth para autenticar a los usuarios y recuperar sus datos básicos:

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

La aplicación realiza una solicitud de autenticación para usar Google el `OAuth2Authenticator` clase. Se devuelve una respuesta de autenticación, una vez que el usuario se ha autenticado correctamente con Google a través de su página de inicio de sesión, que incluye un token de acceso. A continuación, la aplicación realiza una solicitud a Google para datos de usuario básica, mediante el `OAuth2Request` (clase), con el token de acceso que se incluye en la solicitud.

### <a name="setup"></a>Instalación

Debe crearse un proyecto de consola de API de Google para integrar el inicio de sesión de Google con una aplicación de Xamarin.Forms. Esto se puede lograr de la siguiente manera:

1. Vaya a la [consola de API de Google](https://console.developers.google.com) sitio Web e inicie sesión con credenciales de cuenta de Google.
1. En la lista desplegable proyecto, seleccione un proyecto existente o crear uno nuevo.
1. En la barra lateral, en "Administrador de API", seleccione **credenciales**y, a continuación, seleccione la **pestaña pantalla de consentimiento de OAuth**. Elija una **dirección de correo electrónico**, especifique un **nombre de producto que se mostrará a los usuarios**y presione **Guardar**.
1. En el **credenciales** ficha, seleccione el **crear credenciales** desplegable lista y elija **Id. de cliente de OAuth**.
1. En **tipo de aplicación**, seleccione la plataforma que se ejecutará la aplicación móvil (**iOS** o **Android**).
1. Rellene los detalles necesarios y seleccione el **crear** botón.

> [!NOTE]
> Un identificador de cliente permite que una aplicación tener acceso a habilitado APIs Google y para las aplicaciones móviles es único para una sola plataforma. Por lo tanto, un **identificador de cliente OAuth** debe crearse para cada plataforma que va a usar inicio de sesión de Google.

Después de realizar estos pasos, Xamarin.Auth puede utilizarse para iniciar un flujo de autenticación de OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Crear y configurar un autenticador

De Xamarin.Auth `OAuth2Authenticator` clase es responsable de controlar el flujo de autenticación de OAuth. En el ejemplo de código siguiente se muestra la creación de instancias de la `OAuth2Authenticator` clase al realizar la autenticación mediante el explorador web del dispositivo:

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

La `OAuth2Authenticator` clase requiere un número de parámetros, que son los siguientes:

- **Id. de cliente** : Esto identifica al cliente que realiza la solicitud y se puede recuperar desde el proyecto en el [consola de API de Google](https://console.developers.google.com).
- **Secreto de cliente** : debe ser `null` o `string.Empty`.
- **Ámbito** : Esto identifica a la API de acceso que está solicitando la aplicación y el valor informa a la pantalla de consentimiento que se muestra al usuario. Para obtener más información sobre los ámbitos, consulte [solicitud de autorización de API](https://developers.google.com/+/web/api/rest/oauth) en el sitio Web de Google.
- **Autorizar a la dirección URL** : identifica la dirección URL donde se obtendrán el código de autorización de.
- **Dirección URL de redireccionamiento** : identifica la dirección URL donde se enviará la respuesta. El valor de este parámetro debe coincidir con uno de los valores que aparece en el **credenciales** pestaña para el proyecto en el [Google Developers Console](https://console.developers.google.com/).
- **Dirección AccessToken Url** : identifica la dirección URL usada para solicitar tokens de acceso una vez obtenido un código de autorización.
- **GetUserNameAsync Func** : opcional `Func` que se usará para recuperar el nombre de usuario de la cuenta de forma asincrónica después de que se ha autenticado correctamente.
- **Usar la interfaz de usuario nativa** : un `boolean` valor que indica si se debe usar el explorador web del dispositivo para realizar la solicitud de autenticación.

### <a name="setup-authentication-event-handlers"></a>Configurar controladores de eventos de autenticación

Antes de presentar la interfaz de usuario, un controlador de eventos para el `OAuth2Authenticator.Completed` evento debe estar registrado, como se muestra en el ejemplo de código siguiente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Este evento se desencadenará cuando el usuario se autentica correctamente o cancela el inicio de sesión.

Opcionalmente, un controlador de eventos para el `OAuth2Authenticator.Error` también se pueden registrar eventos.

### <a name="presenting-the-sign-in-user-interface"></a>Presentar la interfaz de usuario de inicio de sesión

La interfaz de usuario de inicio de sesión se puede presentar al usuario mediante el uso de un presentador de inicio de sesión de Xamarin.Auth, lo que debe inicializarse en cada proyecto de la plataforma. El ejemplo de código siguiente muestra cómo inicializar un presentador de inicio de sesión en el `AppDelegate` clase en el proyecto de iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

El ejemplo de código siguiente muestra cómo inicializar un presentador de inicio de sesión en el `MainActivity` clase en el proyecto de Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

El proyecto de biblioteca estándar. NET, a continuación, puede invocar el presentador de inicio de sesión como sigue:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Tenga en cuenta que el argumento para el `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` método es el `OAuth2Authenticator` instancia. Cuando el `Login` se invoca el método, la interfaz de usuario de inicio de sesión se presenta al usuario en una pestaña de explorador web del dispositivo, que se muestra en las capturas de pantalla siguiente:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Procesa la dirección URL de redireccionamiento

Una vez que el usuario complete el proceso de autenticación, el control volverá a la aplicación desde la pestaña del explorador Web. Esto se consigue registrando un esquema de dirección URL personalizado para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectando y administrando la dirección URL personalizada una vez enviada.

Al elegir un esquema de dirección URL personalizado para asociar a una aplicación, las aplicaciones deben usar un esquema basado en un nombre bajo su control. Esto puede lograrse mediante el nombre del identificador de lote en iOS y el nombre del paquete en Android y, a continuación, invirtiéndola para realizar el esquema de dirección URL. Sin embargo, algunos proveedores de identidades, como Google, asignan identificadores de cliente basados en nombres de dominio, que, a continuación, se puede deshacer y utilizados como esquema de dirección URL. Por ejemplo, si Google crea un identificador de cliente de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, el esquema de dirección URL será `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Tenga en cuenta que solo un único `/` puede aparecer después del componente de esquema. Por lo tanto, es un ejemplo completo de una dirección URL de redireccionamiento utilizando una combinación de dirección URL personalizada `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Cuando el explorador web recibe una respuesta del proveedor de identidad que contiene una combinación de dirección URL personalizada, intenta cargar la dirección URL, que se producirá un error. En su lugar, el esquema de dirección URL personalizado se notifica al sistema operativo al generar un evento. El sistema operativo, a continuación, se comprueba para esquemas registrados, y si encuentra uno, el sistema operativo se inicia la aplicación que registra el esquema y enviarlo en la dirección URL de redireccionamiento.

El mecanismo para registrar un esquema de dirección URL personalizado con el sistema operativo y el esquema de control es específico para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, se registra una combinación de dirección URL personalizada en **Info.plist**, tal y como se muestra en la captura de pantalla siguiente:

![](oauth-images/info-plist.png "URL Scheme Registration")

El **identificador** valor puede ser cualquier cosa y el **rol** valor debe establecerse en **Visor**. El **esquemas Url** valor, que comienza con `com.googleusercontent.apps`, puede obtenerse desde el identificador de cliente de iOS para el proyecto en [consola de API de Google](https://console.developers.google.com).

Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Dado que la dirección URL usa un esquema personalizado, da como resultado de iniciar la aplicación de iOS, pasando la dirección URL como un parámetro de inicio, donde se procesó la `OpenUrl` la invalidación de la aplicación `AppDelegate` (clase), que se muestra en el ejemplo de código siguiente:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

El `OpenUrl` método convierte la dirección URL recibida desde un `NSUrl` a .NET `Uri`, antes de procesar la dirección URL de redireccionamiento con el `OnPageLoading` método para un público `OAuth2Authenticator` objeto. Esto hace que Xamarin.Auth para cerrar la pestaña del explorador web y analizar los datos recibidos de OAuth.

#### <a name="android"></a>Android

En Android, se registra un esquema de dirección URL personalizado mediante la especificación de un [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) atributo el `Activity` que va a controlar el esquema. Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Como la dirección URL utiliza un esquema personalizado, da como resultado de iniciar la aplicación de Android, pasando la dirección URL como un parámetro de inicio, donde se procesó la `OnCreate` método de la `Activity` registrado para controlar el esquema de dirección URL personalizado. El ejemplo de código siguiente muestra la clase de la aplicación de ejemplo que controla el esquema de dirección URL personalizado:

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

El `DataSchemes` propiedad de la [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) debe establecerse en el identificador de cliente invertido que se obtiene del identificador de cliente Android para el proyecto en [consola de API de Google](https://console.developers.google.com).

El `OnCreate` método convierte la dirección URL recibida desde un `Android.Net.Url` a .NET `Uri`, antes de procesar la dirección URL de redireccionamiento con el `OnPageLoading` método para un público `OAuth2Authenticator` objeto. Esto hace que Xamarin.Auth cerrar la pestaña del explorador web y analizar los datos recibidos de OAuth.

> [!IMPORTANT]
> En Android, se utiliza Xamarin.Auth el `CustomTabs` API para comunicarse con el sistema operativo y el explorador web. Sin embargo, no se garantiza que un `CustomTabs` explorador compatible se instalará en el dispositivo del usuario.

### <a name="examining-the-oauth-response"></a>Examinar la respuesta de OAuth

Después de analizar los datos recibidos de OAuth, Xamarin.Auth, se producirá la `OAuth2Authenticator.Completed` eventos. En el controlador de eventos para este evento, el `AuthenticatorCompletedEventArgs.IsAuthenticated` propiedad puede usarse para identificar si la autenticación se realizó correctamente, tal como se muestra en el ejemplo de código siguiente:

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

Los datos recopilados de una autenticación correcta están disponibles en el `AuthenticatorCompletedEventArgs.Account` propiedad. Esto incluye un token de acceso, que se puede usar para firmar las solicitudes de datos a una API proporcionada por el proveedor de identidades.

### <a name="making-requests-for-data"></a>Realizar solicitudes de datos

Después de la aplicación obtiene un token de acceso, se utiliza para realizar una solicitud para el `https://www.googleapis.com/oauth2/v2/userinfo` API para solicitar datos de usuario básica del proveedor de identidades. Esta solicitud se realiza con de Xamarin.Auth `OAuth2Request` (clase), que representa una solicitud que se autentica utilizando una cuenta que se recuperan de un `OAuth2Authenticator` de instancia, tal como se muestra en el ejemplo de código siguiente:

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

Así como el método HTTP y la dirección URL de API, el `OAuth2Request` instancia especifica también una `Account` instancia que contiene el token de acceso que firma la solicitud a la dirección URL especificada por el `Constants.UserInfoUrl` propiedad. El proveedor de identidades, a continuación, devuelve los datos básicos de usuario como una respuesta JSON, incluidos el nombre de los usuarios y la dirección de correo electrónico, siempre que se reconoce como válido el token de acceso. La respuesta JSON, a continuación, se lee y deserializarse la `user` variable.

Para obtener más información, consulte [una llamada a una API de Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) en el portal de desarrolladores de Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Almacenar y recuperar información de cuenta en los dispositivos

Almacena de forma segura Xamarin.Auth `Account` objetos en una cuenta de almacén para que las aplicaciones no tienen siempre que vuelva a autenticar a los usuarios. El `AccountStore` clase es responsable de almacenar información de la cuenta y está respaldado por servicios de la cadena de claves en iOS y el `KeyStore` clase en Android.

> [!IMPORTANT]
> La clase `AccountStore` de Xamarin. auth ha quedado en desuso y en su lugar se debe usar la clase Xamarin. Essentials `SecureStorage`. Para obtener más información, consulte [Migrating from AccountStore to Xamarin. Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

El siguiente ejemplo de código muestra cómo un `Account` objeto se guarda de forma segura:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Guardado de las cuentas se identifican mediante una clave compuesta por la cuenta `Username` propiedad y un identificador de servicio, que es una cadena que se utiliza cuando capturando cuentas desde el almacén de cuentas. Si un `Account` guardado anteriormente, una llamada a la `Save` método nuevo sobrescribirá.

`Account` los objetos para un servicio se puede recuperar mediante una llamada a la `FindAccountsForService` método, como se muestra en el ejemplo de código siguiente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

El `FindAccountsForService` método devuelve un `IEnumerable` colección de `Account` objetos, con el primer elemento de la colección que se establece como la cuenta coincidente.

## <a name="troubleshooting"></a>Solucionar problemas

- En Android, si recibe una notificación del sistema cuando cierra el explorador después de la autenticación y desea detener la notificación del sistema, agregue el código siguiente al proyecto de Android después de inicializar Xamarin. auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- En Android, si el explorador no se cierra automáticamente, una solución temporal consiste en degradar el paquete Xamarin. auth a la versión 1.5.0.3. Después, agregue la [Crypto Crypto v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) al proyecto de Android.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms. Xamarin.Auth proporciona el `OAuth2Authenticator` y `OAuth2Request` clases que son usadas por las aplicaciones de Xamarin.Forms para consumir los proveedores de identidades como Microsoft, Google, Facebook y Twitter.

## <a name="related-links"></a>Vínculos relacionados

- [OAuthNativeFlow (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2.0 para aplicaciones nativas](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso de OAuth2.0 para tener acceso a las API de Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
