---
title: AuthenticateUsers con un proveedor de identidades
description: Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 0fa433de7fd1acb6fb27741f1615a644315f373f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725564"
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

La aplicación de ejemplo muestra cómo usar Xamarin.Auth para implementar un flujo de autenticación nativa con Google. Mientras se usa Google como proveedor de identidades en este tema, el enfoque es igualmente aplicable a otros proveedores de identidades. Para obtener más información sobre la autenticación con el punto de conexión OAuth 2,0 de Google, consulte [uso de OAuth 2.0 para acceder a las API de Google](https://developers.google.com/identity/protocols/OAuth2) en el sitio web de Google.

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> ATS puede no participar en si no es posible usar el protocolo de `HTTPS` y proteger la comunicación para los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

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

La aplicación realiza una solicitud de autenticación a Google mediante la clase `OAuth2Authenticator`. Se devuelve una respuesta de autenticación, una vez que el usuario se ha autenticado correctamente con Google a través de su página de inicio de sesión, que incluye un token de acceso. A continuación, la aplicación realiza una solicitud a Google para los datos de usuario básicos mediante la clase `OAuth2Request`, con el token de acceso que se incluye en la solicitud.

### <a name="setup"></a>Configurar

Debe crearse un proyecto de consola de API de Google para integrar el inicio de sesión de Google con una aplicación de Xamarin.Forms. Esto se puede lograr de la siguiente manera:

1. Vaya al sitio web de la [consola de API de Google](https://console.developers.google.com) e inicie sesión con las credenciales de la cuenta de Google.
1. En la lista desplegable proyecto, seleccione un proyecto existente o crear uno nuevo.
1. En la barra lateral, en "Administrador de API", seleccione **credenciales**y, a continuación, seleccione la **pestaña pantalla de consentimiento de OAuth**. Elija una **dirección de correo electrónico**, especifique un **nombre de producto que se mostrará a los usuarios**y presione **Guardar**.
1. En la pestaña **credenciales** , seleccione la lista desplegable **crear credenciales** y elija **ID**. de cliente de OAuth.
1. En **tipo de aplicación**, seleccione la plataforma en la que se ejecutará la aplicación móvil (**iOS** o **Android**).
1. Rellene los detalles necesarios y seleccione el botón **crear** .

> [!NOTE]
> Un identificador de cliente permite que una aplicación tener acceso a habilitado APIs Google y para las aplicaciones móviles es único para una sola plataforma. Por lo tanto, se debe crear un **identificador de cliente de OAuth** para cada plataforma que utilizará el inicio de sesión de Google.

Después de realizar estos pasos, Xamarin.Auth puede utilizarse para iniciar un flujo de autenticación de OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Crear y configurar un autenticador

La clase de `OAuth2Authenticator` de Xamarin. auth es responsable de controlar el flujo de autenticación de OAuth. En el ejemplo de código siguiente se muestra la creación de instancias de la clase `OAuth2Authenticator` al realizar la autenticación mediante el explorador Web del dispositivo:

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

La clase `OAuth2Authenticator` requiere una serie de parámetros, que son los siguientes:

- **Identificador de cliente** : identifica el cliente que realiza la solicitud y se puede recuperar del proyecto en la consola de la [API de Google](https://console.developers.google.com).
- **Secreto de cliente** : debe ser `null` o `string.Empty`.
- **Ámbito** : identifica el acceso a la API que solicita la aplicación y el valor informa a la pantalla de consentimiento que se muestra al usuario. Para obtener más información sobre los ámbitos, consulte [autorización de solicitudes](https://developers.google.com/docs/api/how-tos/authorizing) en el sitio web de Google.
- **Autorizar dirección URL** : identifica la dirección URL desde la que se obtendrá el código de autorización.
- **URL de redireccionamiento** : identifica la dirección URL a la que se enviará la respuesta. El valor de este parámetro debe coincidir con uno de los valores que aparece en la pestaña **credenciales** del proyecto en la [consola de desarrolladores de Google](https://console.developers.google.com/).
- **Dirección URL de AccessToken** : identifica la dirección URL que se usa para solicitar tokens de acceso después de obtener un código de autorización.
- **GetUserNameAsync FUNC** : un `Func` opcional que se usará para recuperar de forma asincrónica el nombre de usuario de la cuenta después de que se haya autenticado correctamente.
- **Usar la interfaz de usuario nativa** : un valor `boolean` que indica si se va a usar el explorador Web del dispositivo para realizar la solicitud de autenticación.

### <a name="setup-authentication-event-handlers"></a>Configurar controladores de eventos de autenticación

Antes de presentar la interfaz de usuario, se debe registrar un controlador de eventos para el evento `OAuth2Authenticator.Completed`, como se muestra en el ejemplo de código siguiente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Este evento se desencadenará cuando el usuario se autentica correctamente o cancela el inicio de sesión.

Opcionalmente, también se puede registrar un controlador de eventos para el evento `OAuth2Authenticator.Error`.

### <a name="presenting-the-sign-in-user-interface"></a>Presentar la interfaz de usuario de inicio de sesión

La interfaz de usuario de inicio de sesión se puede presentar al usuario mediante el uso de un presentador de inicio de sesión de Xamarin.Auth, lo que debe inicializarse en cada proyecto de la plataforma. En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la clase `AppDelegate` en el proyecto de iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la clase `MainActivity` en el proyecto de Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

El proyecto de biblioteca estándar. NET, a continuación, puede invocar el presentador de inicio de sesión como sigue:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Tenga en cuenta que el argumento para el método `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` es la instancia de `OAuth2Authenticator`. Cuando se invoca el método de `Login`, la interfaz de usuario de inicio de sesión se presenta al usuario en una pestaña del explorador Web del dispositivo, que se muestra en las siguientes capturas de pantallas:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Procesa la dirección URL de redireccionamiento

Una vez que el usuario complete el proceso de autenticación, el control volverá a la aplicación desde la pestaña del explorador Web. Esto se consigue registrando un esquema de dirección URL personalizado para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectando y administrando la dirección URL personalizada una vez enviada.

Al elegir un esquema de dirección URL personalizado para asociar a una aplicación, las aplicaciones deben usar un esquema basado en un nombre bajo su control. Esto puede lograrse mediante el nombre del identificador de lote en iOS y el nombre del paquete en Android y, a continuación, invirtiéndola para realizar el esquema de dirección URL. Sin embargo, algunos proveedores de identidades, como Google, asignan identificadores de cliente basados en nombres de dominio, que, a continuación, se puede deshacer y utilizados como esquema de dirección URL. Por ejemplo, si Google crea un identificador de cliente de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, se `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`el esquema de la dirección URL. Tenga en cuenta que solo puede aparecer una sola `/` después del componente de esquema. Por lo tanto, se `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`un ejemplo completo de una dirección URL de redireccionamiento que utiliza un esquema de dirección URL personalizado.

Cuando el explorador web recibe una respuesta del proveedor de identidad que contiene una combinación de dirección URL personalizada, intenta cargar la dirección URL, que se producirá un error. En su lugar, el esquema de dirección URL personalizado se notifica al sistema operativo al generar un evento. El sistema operativo, a continuación, se comprueba para esquemas registrados, y si encuentra uno, el sistema operativo se inicia la aplicación que registra el esquema y enviarlo en la dirección URL de redireccionamiento.

El mecanismo para registrar un esquema de dirección URL personalizado con el sistema operativo y el esquema de control es específico para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, se registra un esquema de dirección URL personalizado en **info. plist**, tal como se muestra en la siguiente captura de pantalla:

![](oauth-images/info-plist.png "URL Scheme Registration")

El valor del **identificador** puede ser cualquier cosa y el valor del **rol** debe establecerse en **visor**. El valor de **esquemas de dirección URL** , que comienza con `com.googleusercontent.apps`, puede obtenerse a partir del identificador de cliente de iOS para el proyecto en la [consola de API de Google](https://console.developers.google.com).

Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Dado que la dirección URL usa un esquema personalizado, da como resultado que iOS inicie la aplicación, pasando la dirección URL como un parámetro de inicio, donde se procesa mediante el `OpenUrl` invalidación de la clase `AppDelegate` de la aplicación, que se muestra en el ejemplo de código siguiente:

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

El método `OpenUrl` convierte la dirección URL recibida de una `NSUrl` a una `Uri`.NET, antes de procesar la dirección URL de redireccionamiento con el método `OnPageLoading` de un objeto `OAuth2Authenticator` público. Esto hace que Xamarin.Auth para cerrar la pestaña del explorador web y analizar los datos recibidos de OAuth.

#### <a name="android"></a>Android

En Android, se registra un esquema de dirección URL personalizado especificando un [`IntentFilter`](xref:Android.App.IntentFilterAttribute) atributo en el `Activity` que controlará el esquema. Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Como la dirección URL usa un esquema personalizado, hace que Android inicie la aplicación, pasando la dirección URL como un parámetro de inicio, donde se procesa mediante el método `OnCreate` de la `Activity` registrada para controlar el esquema de la dirección URL personalizada. El ejemplo de código siguiente muestra la clase de la aplicación de ejemplo que controla el esquema de dirección URL personalizado:

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

La propiedad `DataSchemes` de la [`IntentFilter`](xref:Android.App.IntentFilterAttribute) debe establecerse en el identificador de cliente inverso que se obtiene del identificador de cliente de Android para el proyecto en la [consola de API de Google](https://console.developers.google.com).

El método `OnCreate` convierte la dirección URL recibida de una `Android.Net.Url` a una `Uri`.NET, antes de procesar la dirección URL de redireccionamiento con el método `OnPageLoading` de un objeto `OAuth2Authenticator` público. Esto hace que Xamarin.Auth cerrar la pestaña del explorador web y analizar los datos recibidos de OAuth.

> [!IMPORTANT]
> En Android, Xamarin. auth usa la API de `CustomTabs` para comunicarse con el explorador Web y el sistema operativo. Sin embargo, no se garantiza que se instale un explorador compatible con `CustomTabs` en el dispositivo del usuario.

### <a name="examining-the-oauth-response"></a>Examinar la respuesta de OAuth

Después de analizar los datos de OAuth recibidos, Xamarin. auth generará el evento `OAuth2Authenticator.Completed`. En el controlador de eventos para este evento, se puede usar la propiedad `AuthenticatorCompletedEventArgs.IsAuthenticated` para identificar si la autenticación se realizó correctamente, como se muestra en el ejemplo de código siguiente:

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

Los datos recopilados de una autenticación correcta están disponibles en la propiedad `AuthenticatorCompletedEventArgs.Account`. Esto incluye un token de acceso, que se puede usar para firmar las solicitudes de datos a una API proporcionada por el proveedor de identidades.

### <a name="making-requests-for-data"></a>Realizar solicitudes de datos

Una vez que la aplicación obtiene un token de acceso, se usa para hacer una solicitud a la API de `https://www.googleapis.com/oauth2/v2/userinfo`, para solicitar datos de usuario básicos del proveedor de identidades. Esta solicitud se realiza con la clase `OAuth2Request` de Xamarin. auth, que representa una solicitud que se autentica mediante una cuenta recuperada de una instancia de `OAuth2Authenticator`, como se muestra en el ejemplo de código siguiente:

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

Así como el método HTTP y la dirección URL de la API, la instancia de `OAuth2Request` también especifica una instancia de `Account` que contiene el token de acceso que firma la solicitud en la dirección URL especificada por la propiedad `Constants.UserInfoUrl`. El proveedor de identidades, a continuación, devuelve los datos básicos de usuario como una respuesta JSON, incluidos el nombre de los usuarios y la dirección de correo electrónico, siempre que se reconoce como válido el token de acceso. La respuesta JSON se lee y se deserializa en la variable `user`.

Para obtener más información, consulte [llamar a una API de Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) en el portal para desarrolladores de Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Almacenar y recuperar información de cuenta en los dispositivos

Xamarin. auth almacena de forma segura `Account` objetos en un almacén de cuentas para que las aplicaciones no siempre tengan que volver a autenticar a los usuarios. La clase `AccountStore` es responsable de almacenar la información de la cuenta y está respaldada por los servicios de cadena de claves en iOS y la clase `KeyStore` en Android.

> [!IMPORTANT]
> La clase `AccountStore` de Xamarin. auth ha quedado en desuso y en su lugar se debe usar la clase Xamarin. Essentials `SecureStorage`. Para obtener más información, consulte [Migrating from AccountStore to Xamarin. Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

En el ejemplo de código siguiente se muestra cómo se guarda de forma segura un objeto `Account`:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Las cuentas guardadas se identifican de forma única mediante una clave compuesta por la propiedad `Username` de la cuenta y un identificador de servicio, que es una cadena que se usa al capturar cuentas del almacén de cuentas. Si previamente se ha guardado un `Account`, al llamar al método de `Save` de nuevo se sobrescribirá.

`Account` objetos para un servicio se pueden recuperar llamando al método `FindAccountsForService`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

El método `FindAccountsForService` devuelve una colección `IEnumerable` de objetos `Account`, con el primer elemento de la colección que se establece como la cuenta coincidente.

## <a name="troubleshooting"></a>Solución de problemas

- En Android, si recibe una notificación del sistema cuando cierra el explorador después de la autenticación y desea detener la notificación del sistema, agregue el código siguiente al proyecto de Android después de inicializar Xamarin. auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- En Android, si el explorador no se cierra automáticamente, una solución temporal consiste en degradar el paquete Xamarin. auth a la versión 1.5.0.3. Después, agregue la [Crypto Crypto v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) al proyecto de Android.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms. Xamarin. auth proporciona las clases `OAuth2Authenticator` y `OAuth2Request` que usan las aplicaciones de Xamarin. Forms para consumir proveedores de identidades, como Google, Microsoft, Facebook y Twitter.

## <a name="related-links"></a>Vínculos relacionados

- [OAuthNativeFlow (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 para aplicaciones nativas](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso de OAuth 2.0 para acceder a las API de Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
