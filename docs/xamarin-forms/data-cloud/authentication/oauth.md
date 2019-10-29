---
title: AuthenticateUsers con un proveedor de identidades
description: En este artículo se explica cómo usar Xamarin. auth para administrar el proceso de autenticación en una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 3c167b025b41bd4fc7c8c93bdb2f825c9daa540c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032848"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticación de usuarios con un proveedor de identidades

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin. auth es un SDK multiplataforma para autenticar a los usuarios y almacenar sus cuentas. Incluye autenticadores de OAuth que proporcionan compatibilidad para consumir proveedores de identidades, como Google, Microsoft, Facebook y Twitter. En este artículo se explica cómo usar Xamarin. auth para administrar el proceso de autenticación en una aplicación de Xamarin. Forms._

OAuth es un estándar abierto para la autenticación y permite que el propietario de un recurso notifique a un proveedor de recursos que se debe conceder el permiso a un tercero para tener acceso a su información sin compartir la identidad de los propietarios de recursos. Un ejemplo de esto sería permitir que un usuario notifique a un proveedor de identidades (como Google, Microsoft, Facebook o Twitter) que ese permiso se debe conceder a una aplicación para tener acceso a sus datos, sin compartir la identidad del usuario. Normalmente, se usa como método para que los usuarios inicien sesión en sitios web y aplicaciones mediante un proveedor de identidades, pero sin exponer su contraseña en el sitio web o la aplicación.

A continuación se muestra una introducción de alto nivel del flujo de autenticación cuando se utiliza un proveedor de identidades de OAuth:

1. La aplicación navega por un explorador hasta una dirección URL del proveedor de identidades.
1. El proveedor de identidades controla la autenticación del usuario y devuelve un código de autorización a la aplicación.
1. La aplicación intercambia el código de autorización para un token de acceso del proveedor de identidades.
1. La aplicación usa el token de acceso para acceder a las API del proveedor de identidades, como una API para solicitar datos de usuario básicos.

En la aplicación de ejemplo se muestra cómo usar Xamarin. auth para implementar un flujo de autenticación nativo en Google. Aunque Google se usa como proveedor de identidades en este tema, el enfoque es igualmente aplicable a otros proveedores de identidades. Para obtener más información sobre la autenticación con el punto de conexión OAuth 2,0 de Google, consulte [uso de OAuth 2.0 para acceder a las API de Google](https://developers.google.com/identity/protocols/OAuth2) en el sitio web de Google.

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> ATS puede no participar en si no es posible usar el protocolo de `HTTPS` y proteger la comunicación para los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Uso de Xamarin. auth para autenticar a los usuarios

Xamarin. auth admite dos enfoques para que las aplicaciones interactúen con el punto de conexión de autorización de un proveedor de identidad:

1. Usar una vista Web incrustada. Aunque se trata de una práctica común, ya no se recomienda por las razones siguientes:

    - La aplicación que hospeda la vista Web puede tener acceso a la credencial de autenticación completa del usuario, no solo a la concesión de autorización de OAuth que se diseñó para la aplicación. Esto infringe el principio de privilegios mínimos, ya que la aplicación tiene acceso a credenciales más eficaces de las que necesita, lo que puede aumentar la superficie de ataque de la aplicación.
    - La aplicación host podría capturar nombres de usuario y contraseñas, enviar formularios automáticamente y omitir el consentimiento del usuario, y copiar cookies de la sesión y usarlas para realizar acciones autenticadas como usuario.
    - Las vistas web incrustadas no comparten el estado de autenticación con otras aplicaciones o el explorador Web del dispositivo, que requiere que el usuario inicie sesión para cada solicitud de autorización, lo que se considera una experiencia de usuario inferior.
    - Algunos puntos de conexión de autorización realizan pasos para detectar y bloquear solicitudes de autorización que proceden de vistas Web.

1. Usar el explorador Web del dispositivo, que es el método recomendado. El uso del explorador de dispositivos para solicitudes de OAuth mejora la facilidad de uso de una aplicación, ya que los usuarios solo necesitan iniciar sesión en el proveedor de identidades una vez por dispositivo, mejorando las tasas de conversión de los flujos de inicio de sesión y autorización en la aplicación. El explorador de dispositivos también proporciona seguridad mejorada a medida que las aplicaciones pueden inspeccionar y modificar el contenido de una vista Web, pero no el contenido que se muestra en el explorador. Este es el enfoque que se lleva a cabo en este artículo y en la aplicación de ejemplo.

En el diagrama siguiente se muestra información general de alto nivel sobre cómo la aplicación de ejemplo usa Xamarin. auth para autenticar a los usuarios y recuperar sus datos básicos:

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

La aplicación realiza una solicitud de autenticación a Google mediante la clase `OAuth2Authenticator`. Se devuelve una respuesta de autenticación, una vez que el usuario se ha autenticado correctamente con Google a través de su página de inicio de sesión, que incluye un token de acceso. A continuación, la aplicación realiza una solicitud a Google para los datos de usuario básicos mediante la clase `OAuth2Request`, con el token de acceso que se incluye en la solicitud.

### <a name="setup"></a>Programa de instalación

Se debe crear un proyecto de consola de API de Google para integrar el inicio de sesión de Google con una aplicación de Xamarin. Forms. Esto se puede lograr de la siguiente manera:

1. Vaya al sitio web de la [consola de API de Google](https://console.developers.google.com) e inicie sesión con las credenciales de la cuenta de Google.
1. En el menú desplegable proyecto, seleccione un proyecto existente o cree uno nuevo.
1. En la barra lateral, en "Administrador de API", seleccione **credenciales**y, a continuación, seleccione la **pestaña pantalla de consentimiento de OAuth**. Elija una **dirección de correo electrónico**, especifique un **nombre de producto que se mostrará a los usuarios**y presione **Guardar**.
1. En la pestaña **credenciales** , seleccione la lista desplegable **crear credenciales** y elija **ID**. de cliente de OAuth.
1. En **tipo de aplicación**, seleccione la plataforma en la que se ejecutará la aplicación móvil (**iOS** o **Android**).
1. Rellene los detalles necesarios y seleccione el botón **crear** .

> [!NOTE]
> Un identificador de cliente permite a una aplicación tener acceso a las API de Google habilitadas, y para las aplicaciones móviles es único para una única plataforma. Por lo tanto, se debe crear un **identificador de cliente de OAuth** para cada plataforma que utilizará el inicio de sesión de Google.

Después de realizar estos pasos, Xamarin. auth se puede usar para iniciar un flujo de autenticación de OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Creación y configuración de un autenticador

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
- **Ámbito** : identifica el acceso a la API que solicita la aplicación y el valor informa a la pantalla de consentimiento que se muestra al usuario. Para más información sobre los ámbitos, consulte [autorización](https://developers.google.com/+/web/api/rest/oauth) de la solicitud de API en el sitio web de Google.
- **Autorizar dirección URL** : identifica la dirección URL desde la que se obtendrá el código de autorización.
- **URL de redireccionamiento** : identifica la dirección URL a la que se enviará la respuesta. El valor de este parámetro debe coincidir con uno de los valores que aparece en la pestaña **credenciales** del proyecto en la [consola de desarrolladores de Google](https://console.developers.google.com/).
- **Dirección URL de AccessToken** : identifica la dirección URL que se usa para solicitar tokens de acceso después de obtener un código de autorización.
- **GetUserNameAsync FUNC** : un `Func` opcional que se usará para recuperar de forma asincrónica el nombre de usuario de la cuenta después de que se haya autenticado correctamente.
- **Usar la interfaz de usuario nativa** : un valor `boolean` que indica si se va a usar el explorador Web del dispositivo para realizar la solicitud de autenticación.

### <a name="setup-authentication-event-handlers"></a>Configuración de controladores de eventos de autenticación

Antes de presentar la interfaz de usuario, se debe registrar un controlador de eventos para el evento `OAuth2Authenticator.Completed`, como se muestra en el ejemplo de código siguiente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Este evento se desencadenará cuando el usuario se autentique o cancele correctamente el inicio de sesión.

Opcionalmente, también se puede registrar un controlador de eventos para el evento `OAuth2Authenticator.Error`.

### <a name="presenting-the-sign-in-user-interface"></a>Presentación de la interfaz de usuario de inicio de sesión

La interfaz de usuario de inicio de sesión se puede presentar al usuario mediante un presentador de inicio de sesión de Xamarin. auth, que debe inicializarse en cada proyecto de plataforma. En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la clase `AppDelegate` en el proyecto de iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la clase `MainActivity` en el proyecto de Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

A continuación, el proyecto de biblioteca de .NET Standard puede invocar el presentador de inicio de sesión como se indica a continuación:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Tenga en cuenta que el argumento para el método `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` es la instancia de `OAuth2Authenticator`. Cuando se invoca el método de `Login`, la interfaz de usuario de inicio de sesión se presenta al usuario en una pestaña del explorador Web del dispositivo, que se muestra en las siguientes capturas de pantallas:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Procesar la dirección URL de redireccionamiento

Una vez que el usuario complete el proceso de autenticación, el control volverá a la aplicación desde la pestaña del explorador Web. Esto se consigue registrando un esquema de dirección URL personalizado para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectando y administrando la dirección URL personalizada una vez enviada.

Al elegir un esquema de dirección URL personalizado para asociarlo a una aplicación, las aplicaciones deben usar un esquema basado en un nombre bajo su control. Esto puede lograrse mediante el nombre del identificador de paquete en iOS y el nombre del paquete en Android y, a continuación, revertirlos para crear el esquema de la dirección URL. Sin embargo, algunos proveedores de identidades, como Google, asignan identificadores de cliente basados en nombres de dominio, que luego se invierten y se usan como esquema de dirección URL. Por ejemplo, si Google crea un identificador de cliente de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, se `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`el esquema de la dirección URL. Tenga en cuenta que solo puede aparecer una sola `/` después del componente de esquema. Por lo tanto, se `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`un ejemplo completo de una dirección URL de redireccionamiento que utiliza un esquema de dirección URL personalizado.

Cuando el explorador web recibe una respuesta del proveedor de identidades que contiene un esquema de dirección URL personalizado, intenta cargar la dirección URL, lo que producirá un error. En su lugar, el esquema de la dirección URL personalizada se envía al sistema operativo mediante la generación de un evento. A continuación, el sistema operativo comprueba los esquemas registrados y, si se encuentra alguno, el sistema operativo iniciará la aplicación que registró el esquema y lo enviará a la dirección URL de redireccionamiento.

El mecanismo para registrar un esquema de dirección URL personalizado con el sistema operativo y controlar el esquema es específico de cada plataforma.

#### <a name="ios"></a>iOS

En iOS, se registra un esquema de dirección URL personalizado en **info. plist**, tal como se muestra en la siguiente captura de pantalla:

![](oauth-images/info-plist.png "URL Scheme Registration")

El valor del **identificador** puede ser cualquier cosa y el valor del **rol** debe establecerse en **visor**. El valor de **esquemas de dirección URL** , que comienza con `com.googleusercontent.apps`, puede obtenerse a partir del identificador de cliente de iOS para el proyecto en la [consola de API de Google](https://console.developers.google.com).

Cuando el proveedor de identidad completa la solicitud de autorización, se redirige a la dirección URL de redireccionamiento de la aplicación. Dado que la dirección URL usa un esquema personalizado, da como resultado que iOS inicie la aplicación, pasando la dirección URL como un parámetro de inicio, donde se procesa mediante el `OpenUrl` invalidación de la clase `AppDelegate` de la aplicación, que se muestra en el ejemplo de código siguiente. :

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

El método `OpenUrl` convierte la dirección URL recibida de una `NSUrl` a una `Uri`.NET, antes de procesar la dirección URL de redireccionamiento con el método `OnPageLoading` de un objeto `OAuth2Authenticator` público. Esto hace que Xamarin. auth cierre la pestaña del explorador Web y analice los datos recibidos de OAuth.

#### <a name="android"></a>Android

En Android, se registra un esquema de dirección URL personalizado especificando un [`IntentFilter`](xref:Android.App.IntentFilterAttribute) atributo en el `Activity` que controlará el esquema. Cuando el proveedor de identidad completa la solicitud de autorización, se redirige a la dirección URL de redireccionamiento de la aplicación. Como la dirección URL usa un esquema personalizado, hace que Android inicie la aplicación, pasando la dirección URL como un parámetro de inicio, donde se procesa mediante el método `OnCreate` de la `Activity` registrada para controlar el esquema de la dirección URL personalizada. En el ejemplo de código siguiente se muestra la clase de la aplicación de ejemplo que controla el esquema de la dirección URL personalizada:

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

El método `OnCreate` convierte la dirección URL recibida de una `Android.Net.Url` a una `Uri`.NET, antes de procesar la dirección URL de redireccionamiento con el método `OnPageLoading` de un objeto `OAuth2Authenticator` público. Esto hace que Xamarin. auth cierre la pestaña del explorador Web y analice los datos de OAuth recibidos.

> [!IMPORTANT]
> En Android, Xamarin. auth usa la API de `CustomTabs` para comunicarse con el explorador Web y el sistema operativo. Sin embargo, no se garantiza que se instale un explorador compatible con `CustomTabs` en el dispositivo del usuario.

### <a name="examining-the-oauth-response"></a>Examen de la respuesta de OAuth

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

### <a name="making-requests-for-data"></a>Realización de solicitudes de datos

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

Así como el método HTTP y la dirección URL de la API, la instancia de `OAuth2Request` también especifica una instancia de `Account` que contiene el token de acceso que firma la solicitud en la dirección URL especificada por la propiedad `Constants.UserInfoUrl`. A continuación, el proveedor de identidades devuelve datos de usuario básicos como respuesta JSON, incluidos el nombre y la dirección de correo electrónico de los usuarios, siempre que reconozca el token de acceso como válido. La respuesta JSON se lee y se deserializa en la variable `user`.

Para obtener más información, consulte [llamar a una API de Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) en el portal para desarrolladores de Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Almacenar y recuperar información de la cuenta en los dispositivos

Xamarin. auth almacena de forma segura `Account` objetos en un almacén de cuentas para que las aplicaciones no siempre tengan que volver a autenticar a los usuarios. La clase `AccountStore` es responsable de almacenar la información de la cuenta y está respaldada por los servicios de cadena de claves en iOS y la clase `KeyStore` en Android.

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

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Xamarin. auth para administrar el proceso de autenticación en una aplicación de Xamarin. Forms. Xamarin. auth proporciona las clases `OAuth2Authenticator` y `OAuth2Request` que usan las aplicaciones de Xamarin. Forms para consumir proveedores de identidades, como Google, Microsoft, Facebook y Twitter.

## <a name="related-links"></a>Vínculos relacionados

- [OAuthNativeFlow (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 para aplicaciones nativas](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso de OAuth 2.0 para acceder a las API de Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
