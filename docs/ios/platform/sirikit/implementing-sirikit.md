---
title: Implementación de SiriKit en Xamarin. iOS
description: En este documento se describen los pasos necesarios para implementar la compatibilidad con SiriKit en las aplicaciones de Xamarin. iOS. Se describen las extensiones de intents y las extensiones de la interfaz de usuario de intents.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: ed31e0262fecccf8974961d45d02388aeb8e85ea
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434425"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementación de SiriKit en Xamarin. iOS

_En este artículo se describen los pasos necesarios para implementar la compatibilidad con SiriKit en las aplicaciones de Xamarin. iOS._

Novedad de iOS 10, SiriKit permite que una aplicación de Xamarin. iOS proporcione servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS. En este artículo se describen los pasos necesarios para implementar la compatibilidad con SiriKit en las aplicaciones de Xamarin. iOS agregando las extensiones de intents necesarias, las extensiones de la interfaz de usuario y el vocabulario.

Siri funciona con el concepto de **dominios**, grupos de acciones conocidas para tareas relacionadas. Cada interacción que tiene la aplicación con Siri debe encontrarse en uno de sus dominios de servicio conocidos de la siguiente manera:

- Llamada de audio o vídeo.
- Reservar un viaje.
- Administrar entrenamientos.
- Correo.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implica a uno de los servicios de la extensión de la aplicación, SiriKit envía a la extensión un objeto de **intención** que describe la solicitud del usuario junto con los datos auxiliares. a continuación, la extensión de la aplicación genera el objeto de **respuesta** adecuado para el **objetivo**dado, lo que detalla cómo la extensión puede controlar la solicitud.

En esta guía se presentará un ejemplo rápido de cómo incluir la compatibilidad con SiriKit en una aplicación existente. En este ejemplo, vamos a usar la aplicación MonkeyChat falsa:

[![El icono de MonkeyChat](implementing-sirikit-images/monkeychat01.png)](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro de contactos de los amigos del usuario, cada uno asociado a un nombre de pantalla (por ejemplo, Bobo) y permite al usuario enviar chats de texto a cada amigo por su nombre de pantalla.

## <a name="extending-the-app-with-sirikit"></a>Extensión de la aplicación con SiriKit

Como se muestra en la guía Descripción de los [conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) , hay tres partes principales implicadas en la extensión de una aplicación con SiriKit:

[![Extender la aplicación con el diagrama de SiriKit](implementing-sirikit-images/elements01.png)](implementing-sirikit-images/elements01.png#lightbox)

Entre ellas se incluyen las siguientes:

1. **Extensión intents** : comprueba las respuestas de los usuarios, confirma que la aplicación puede controlar la solicitud y realiza realmente la tarea para completar la solicitud del usuario.
2. Extensión de la **interfaz de usuario de intents**  -  *Opcional*, proporciona una interfaz de usuario personalizada para las respuestas en el entorno de Siri y puede llevar la interfaz de usuario de las aplicaciones y la personalización de marca a Siri para enriquecer la experiencia del usuario.
3. **Aplicación** : proporciona a la aplicación vocabularios específicos del usuario para ayudar a Siri a trabajar con ella. 

Todos estos elementos y los pasos para incluirlos en la aplicación se tratarán en detalle en las secciones siguientes.

## <a name="preparing-the-app"></a>Preparar la aplicación

Sin embargo, SiriKit se basa en las extensiones, antes de agregar extensiones a la aplicación, hay algunas cosas que el desarrollador tiene que hacer para ayudar a la adopción de SiriKit.

### <a name="moving-common-shared-code"></a>Mover código compartido común 

En primer lugar, el desarrollador puede mover parte del código común que se compartirá entre la aplicación y las extensiones en _proyectos compartidos_, _bibliotecas de clases portables (PCL)_ o _bibliotecas nativas_.

Las extensiones deberán poder realizar todas las acciones que realiza la aplicación. En los términos de la aplicación de MonkeyChat de ejemplo, cosas como buscar contactos, agregar nuevos contactos, enviar mensajes y recuperar el historial de mensajes.

Al mover este código común a un proyecto compartido, PCL o biblioteca nativa, facilita el mantenimiento de ese código en un lugar común y garantiza que la extensión y la aplicación primaria proporcionan experiencias y funciones uniformes para el usuario.

En el caso del MonkeyChat de la aplicación de ejemplo, los modelos de datos y el código de procesamiento, como el acceso a la red y a la base de datos, se moverán a una biblioteca nativa.

Siga estos pasos:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac y abra la aplicación MonkeyChat.
2. Haga clic con el botón derecho en el nombre de la solución en el **Panel de solución** y seleccione **Agregar**  >  **nuevo proyecto...**: 

    [![Incorporación de proyecto nuevo](implementing-sirikit-images/prep01.png)](implementing-sirikit-images/prep01.png#lightbox)
3. Seleccione **iOS**  >  **Library**  >  **biblioteca de clases** de biblioteca de iOS y haga clic en el botón **siguiente** : 

    [![Seleccionar biblioteca de clases](implementing-sirikit-images/prep02.png)](implementing-sirikit-images/prep02.png#lightbox)
4. Escriba `MonkeyChatCommon` como **nombre** y haga clic en el botón **crear** : 

    [![Escriba MonkeyChatCommon para el nombre](implementing-sirikit-images/prep03.png)](implementing-sirikit-images/prep03.png#lightbox)
5. Haga clic con el botón derecho en la carpeta **referencias** de la aplicación principal en el **Explorador de soluciones** y seleccione **Editar referencias..**.. Compruebe el proyecto **MonkeyChatCommon** y haga clic en el botón **Aceptar** : 

    [![Comprobar el proyecto MonkeyChatCommon](implementing-sirikit-images/prep05.png)](implementing-sirikit-images/prep05.png#lightbox)
6. En el **Explorador de soluciones**, arrastre el código compartido común desde la aplicación principal a la biblioteca nativa.
7. En el caso de MonkeyChat, arrastre las carpetas **Models** y **processors** de la aplicación principal a la biblioteca nativa: 

    [![Las carpetas Models y processors en el Explorador de soluciones](implementing-sirikit-images/prep06.png)](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie Visual Studio y abra la aplicación MonkeyChat.
2. Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones** y seleccione **Agregar**  >  **nuevo proyecto..**..
3. Seleccione **Visual C#**  >  **proyecto compartido** de Visual C# y haga clic en el botón **siguiente** : 

    [![Seleccionar biblioteca de clases](implementing-sirikit-images/prep02.w157-sml.png)](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Escriba `MonkeyChatCommon` como **nombre** y haga clic en el botón **crear** .
5. Haga clic con el botón derecho en la carpeta **referencias** de la aplicación principal en el **Explorador de soluciones** y seleccione **Editar referencias..**.. Compruebe el proyecto **MonkeyChatCommon** y haga clic en el botón **Aceptar** : 

    [![Comprobar el proyecto MonkeyChatCommon](implementing-sirikit-images/prep05w.png)](implementing-sirikit-images/prep05w.png#lightbox)
6. En el **Explorador de soluciones**, arrastre el código compartido común desde la aplicación principal al proyecto compartido.
7. En el caso de MonkeyChat, arrastre las carpetas **Models** y **processors** de la aplicación principal a la biblioteca nativa.

-----

Edite los archivos que se movieron a la biblioteca nativa y cambie el espacio de nombres para que coincida con el de la biblioteca. Por ejemplo, si `MonkeyChat` se cambia a `MonkeyChatCommon` :

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

Después, vuelva a la aplicación principal y agregue una `using` instrucción para el espacio de nombres de la biblioteca nativa en cualquier lugar en el que la aplicación use una de las clases que se han desplazado:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Diseño de la aplicación para extensiones

Normalmente, una aplicación se suscribirá a varias intenciones y el desarrollador debe asegurarse de que la aplicación está diseñada para el número de extensiones de intención adecuado.

En la situación en la que una aplicación requiere más de un intento, el desarrollador tiene la opción de colocar todo el control de intención en una extensión de intención o crear una extensión de intención independiente para cada intento.

Si opta por crear una extensión de intención independiente para cada intento, el desarrollador podría acabar duplicando una gran cantidad de código reutilizable en cada extensión y crear una gran cantidad de sobrecarga de procesador y memoria.

Para ayudar a elegir entre las dos opciones, consulte si cualquiera de las intenciones pertenece de forma natural. Por ejemplo, una aplicación que ha realizado llamadas de audio y vídeo podría querer incluir ambos intentos en una única extensión de intención, ya que están administrando tareas similares y pueden proporcionar la mayor reutilización de código.

Para cualquier intención o grupo de intenciones que no quepan en un grupo existente, cree una nueva extensión de intención en la solución de la aplicación para contenerlos.

### <a name="setting-the-required-entitlements"></a>Establecer los derechos necesarios

Cualquier aplicación de Xamarin. iOS que incluya la integración con SiriKit debe tener establecidos los derechos correctos. Si el desarrollador no establece correctamente estos derechos necesarios, no podrá instalar o probar la aplicación en hardware iOS 10 (o superior) real, que también es un requisito, ya que el simulador de iOS 10 no es compatible con SiriKit.

Siga estos pasos:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Entitlements.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Cambie a la pestaña **Source** (Origen).
3. Agregue la `com.apple.developer.siri` **propiedad**, establezca el **tipo** en `Boolean` y el **valor** en `Yes` : 

    [![Agregue la propiedad com. Apple. Developer. Siri](implementing-sirikit-images/setup01.png)](implementing-sirikit-images/setup01.png#lightbox)
4. Guarde los cambios en el archivo.
5. Haga doble clic en el **archivo de proyecto** en el **Explorador de soluciones** para abrirlo para su edición.
6. Seleccione **firma de lote de iOS** y asegúrese de que el `Entitlements.plist` archivo está seleccionado en el campo **derechos personalizados** : 

    [![Seleccione el archivo contitles. plist en el campo derechos personalizados.](implementing-sirikit-images/setup02.png)](implementing-sirikit-images/setup02.png#lightbox)
7. Haga clic en el botón **Aceptar** para guardar los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Entitlements.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Agregue la `com.apple.developer.siri` **propiedad**, establezca el **tipo** en `Boolean` y el **valor** en `Yes` : 

    [![Agregue la propiedad com. Apple. Developer. Siri](implementing-sirikit-images/setup01w.png)](implementing-sirikit-images/setup01w.png#lightbox)
3. Guarde los cambios en el archivo.
4. Haga doble clic en el **archivo de proyecto** en el **Explorador de soluciones** para abrirlo para su edición.
5. Seleccione **firma de lote de iOS** y asegúrese de que el `Entitlements.plist` archivo está seleccionado en el campo **derechos personalizados** .

-----

Cuando termine, el archivo de la aplicación `Entitlements.plist` debería tener un aspecto similar al siguiente (en abierto en un editor externo):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>Aprovisionar correctamente la aplicación

Debido a la estricta seguridad que Apple ha colocado en torno al marco SiriKit, cualquier aplicación de Xamarin. iOS que implemente SiriKit _debe_ tener el identificador de aplicación y los derechos correctos (consulte la sección anterior) y debe estar firmado con un perfil de aprovisionamiento adecuado.

Haga lo siguiente en el equipo Mac:

1. En un explorador Web, vaya a [https://developer.apple.com](https://developer.apple.com) e inicie sesión en su cuenta.
2. Haga clic en **certificados**, **identificadores** y **perfiles**.
3. Seleccione **perfiles de aprovisionamiento** y, a continuación, seleccione ID. de **aplicación**y haga clic en el **+** botón.
4. Escriba un **nombre** para el nuevo perfil.
5. Escriba un **identificador de paquete** después de la recomendación de nomenclatura de Apple.
6. Desplácese hacia abajo hasta la sección **App Services** , seleccione **SiriKit** y haga clic en el botón **continuar** : 

    [![Seleccionar SiriKit](implementing-sirikit-images/setup03.png)](implementing-sirikit-images/setup03.png#lightbox)
7. Compruebe todas las opciones y, a continuación, **envíe** el identificador de la aplicación.
8. Seleccione el desarrollo de **perfiles de aprovisionamiento**  >  **Development**, haga clic en el **+** botón, seleccione el ID. de **Apple**y, a continuación, haga clic en **continuar**.
9. Haga clic en seleccionar **todo**y, a continuación, en **continuar**.
10. Vuelva a hacer clic en **seleccionar todo** y, a continuación, haga clic en **continuar**.
11. Escriba un **nombre de perfil** con las sugerencias de nomenclatura de Apple y, a continuación, haga clic en **continuar**.
12. Inicie Xcode.
13. En el menú de Xcode, seleccione **preferencias...**
14. Seleccione **cuentas**y, a continuación, haga clic en **Ver detalles..** . botón 

    [![Seleccionar cuentas](implementing-sirikit-images/setup04.png)](implementing-sirikit-images/setup04.png#lightbox)
15. Haga clic en el botón **descargar todos los perfiles** situado en la esquina inferior izquierda: 

    [![Descargar todos los perfiles](implementing-sirikit-images/setup05.png)](implementing-sirikit-images/setup05.png#lightbox)
16. Asegúrese de que el **Perfil de aprovisionamiento** creado anteriormente se ha instalado en Xcode.
17. Abra el proyecto para agregar compatibilidad con SiriKit a en Visual Studio para Mac.
18. Haga doble clic `Info.plist` en el archivo en el **Explorador de soluciones**.
19. Asegúrese de que el **identificador de paquete** coincide con el que se creó en el portal para desarrolladores de Apple anterior: 

    [![El identificador de la agrupación](implementing-sirikit-images/setup06.png)](implementing-sirikit-images/setup06.png#lightbox)
20. En el **Explorador de soluciones**, seleccione el **proyecto**.
21. Haga clic con el botón derecho en el proyecto y seleccione **Opciones**.
22. Seleccione **firma de lote de iOS**, seleccione la **identidad de firma** y el **Perfil de aprovisionamiento** que creó anteriormente: 

    [![Seleccionar la identidad de firma y el perfil de aprovisionamiento](implementing-sirikit-images/setup07.png)](implementing-sirikit-images/setup07.png#lightbox)
23. Haga clic en el botón **Aceptar** para guardar los cambios.

> [!IMPORTANT]
> La prueba de SiriKit solo funciona en un dispositivo de hardware de iOS 10 real y no en el simulador de iOS 10. Si tiene problemas para instalar una aplicación de Xamarin. iOS habilitada para SiriKit en hardware real, asegúrese de que los derechos necesarios, el identificador de la aplicación, el identificador de firma y el perfil de aprovisionamiento se han configurado correctamente en el portal para desarrolladores de Apple y Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicitud de autorización de Siri

Antes de que la aplicación agregue un vocabulario específico del usuario o las extensiones de intents se conecten a Siri, debe solicitar la autorización del usuario para acceder a Siri.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Edite el archivo de la aplicación `Info.plist` , cambie a la vista de **código fuente** y agregue la `NSSiriUsageDescription` clave con un valor de cadena que describa el modo en que la aplicación usará Siri y los tipos de datos que se enviarán. Por ejemplo, la aplicación MonkeyChat podría decir "MonkeyChat Contacts se enviarán a Siri":

[![NSSiriUsageDescription en el editor info. plist](implementing-sirikit-images/request01.png)](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Edite el archivo de la aplicación `Info.plist` y agregue la `NSSiriUsageDescription` clave con un valor de cadena que describa el modo en que la aplicación usará Siri y los tipos de datos que se enviarán. Por ejemplo, la aplicación MonkeyChat podría decir "MonkeyChat Contacts se enviarán a Siri":

[![NSSiriUsageDescription en el editor info. plist](implementing-sirikit-images/request01w.png)](implementing-sirikit-images/request01w.png#lightbox)

-----

Llame al `RequestSiriAuthorization` método de la `INPreferences` clase cuando se inicie la aplicación por primera vez. Edite la `AppDelegate.cs` clase y haga que el método tenga el `FinishedLaunching` aspecto siguiente:

```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });

    return true;
}
```

La primera vez que se llama a este método, se muestra una alerta que solicita al usuario que permita que la aplicación tenga acceso a Siri. En esta alerta se mostrará el mensaje que el desarrollador agregó a la `NSSiriUsageDescription` anterior. Si el usuario deniega inicialmente el acceso, puede usar la aplicación de **configuración** para conceder acceso a la aplicación.

En cualquier momento, la aplicación puede comprobar la capacidad de la aplicación para acceder a Siri llamando al `SiriAuthorizationStatus` método de la `INPreferences` clase.

### <a name="localization-and-siri"></a>Localización y Siri

En un dispositivo iOS, el usuario puede seleccionar un idioma para Siri que sea diferente a la predeterminada del sistema. Cuando se trabaja con datos localizados, la aplicación debe usar el `SiriLanguageCode` método de la `INPreferences` clase para obtener el código de idioma de Siri. Por ejemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Agregando vocabulario específico del usuario

El vocabulario específico del usuario va a proporcionar palabras o frases que son únicas para los usuarios individuales de la aplicación. Estos se proporcionarán en tiempo de ejecución desde la aplicación principal (no las extensiones de aplicación) como un conjunto ordenado de términos, ordenados en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

El vocabulario específico del usuario debe pertenecer a una de las siguientes categorías:

- Nombres de contacto (que no están administrados por el marco de contactos).
- Etiquetas de fotografía.
- Nombres de álbumes de fotografías.
- Nombres de entrenamiento.

Al seleccionar la terminología que se va a registrar como vocabulario personalizado, seleccione solo los términos que puede que alguien no esté familiarizado con la aplicación. No registre nunca términos comunes, como "mi entrenamiento" o "mi álbum". Por ejemplo, la aplicación MonkeyChat registrará los alias asociados con cada contacto en la libreta de direcciones del usuario.

La aplicación proporciona el vocabulario específico del usuario mediante una llamada al `SetVocabularyStrings` método de la `INVocabulary` clase y pasando una `NSOrderedSet` colección de la aplicación principal. La aplicación siempre debe llamar `RemoveAllVocabularyStrings` primero al método para quitar los términos existentes antes de agregar otros nuevos. Por ejemplo:

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Con este código en su lugar, se podría llamar de la siguiente manera:

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri trata el vocabulario personalizado como sugerencias y incorporará toda la terminología posible. Sin embargo, el espacio para el vocabulario personalizado está limitado, por lo que es importante registrar _solo_ la terminología que puede resultar confusa, lo que mantiene el número total de términos registrados en un valor mínimo.

Para obtener más información, consulte nuestra [referencia de vocabulario específico del usuario](~/ios/platform/sirikit/understanding-sirikit.md) y la referencia de un [vocabulario personalizado que especifica](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)Apple.

### <a name="adding-app-specific-vocabulary"></a>Agregando vocabulario específico de la aplicación

El vocabulario específico de la aplicación define las palabras y frases específicas que conocerán todos los usuarios de la aplicación, como los tipos de vehículos o los nombres de entrenamiento. Dado que forman parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte del grupo de aplicaciones principal. Además, se deben localizar estas palabras y frases.

Los términos de vocabulario específico de la aplicación deben pertenecer a una de las siguientes categorías:

- Opciones de conducción.
- Nombres de entrenamiento.

El archivo de vocabulario específico de la aplicación contiene dos claves de nivel raíz:

- `ParameterVocabularies`**Requerido** : define los términos y los parámetros de intención personalizados de la aplicación a los que se aplican.
- `IntentPhrases`**Opcional** : contiene frases de ejemplo mediante los términos personalizados definidos en `ParameterVocabularies` .

Cada entrada de `ParameterVocabularies` debe especificar una cadena de identificador, un término y la intención a la que se aplica el término. Además, un único término puede aplicarse a varias intenciones.

Para obtener una lista completa de valores aceptables y la estructura de archivos necesaria, consulte [referencia de formato de archivo de vocabulario de aplicación](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)de Apple.

Para agregar un `AppIntentVocabulary.plist` archivo al proyecto de la aplicación, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Agregar**  >  **nuevo archivo..**  >  . **iOS**:

    [![Agregar una lista de propiedades](implementing-sirikit-images/plist01.png)](implementing-sirikit-images/plist01.png#lightbox)
2. Haga doble clic en el `AppIntentVocabulary.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
3. Haga clic en **+** para agregar una clave, establezca el **nombre** en `ParameterVocabularies` y el **tipo** en `Array` :

    [![Establezca el nombre en ParameterVocabularies y el tipo en matriz.](implementing-sirikit-images/plist02.png)](implementing-sirikit-images/plist02.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el **+** botón y establezca el **tipo** en `Dictionary` :

    [![Establecer el tipo en Diccionario](implementing-sirikit-images/plist03.png)](implementing-sirikit-images/plist03.png#lightbox)
5. Haga clic en **+** para agregar una nueva clave, establezca el **nombre** en `ParameterNames` y el **tipo** en `Array` :

    [![Establezca el nombre en ParameterNames y el tipo en matriz.](implementing-sirikit-images/plist04.png)](implementing-sirikit-images/plist04.png#lightbox)
6. Haga clic en **+** para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetros disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![La clave INStartWorkoutIntent. workoutName](implementing-sirikit-images/plist05.png)](implementing-sirikit-images/plist05.png#lightbox)
7. Agregue la `ParameterVocabulary` clave a la `ParameterVocabularies` clave con el **tipo** de `Array` :

    [![Agregue la clave ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz.](implementing-sirikit-images/plist06.png)](implementing-sirikit-images/plist06.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist07.png)](implementing-sirikit-images/plist07.png#lightbox)
9. Agregue la `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único.](implementing-sirikit-images/plist08.png)](implementing-sirikit-images/plist08.png#lightbox)
10. Agregue la `VocabularyItemSynonyms` clave con el **tipo** de `Array` :

    [![Agregar la clave VocabularyItemSynonyms con el tipo de matriz](implementing-sirikit-images/plist09.png)](implementing-sirikit-images/plist09.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist10.png)](implementing-sirikit-images/plist10.png#lightbox)
12. Agregue la `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que está definiendo la aplicación:

    [![Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que está definiendo la aplicación](implementing-sirikit-images/plist11.png)](implementing-sirikit-images/plist11.png#lightbox)
13. Agregue la `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término.](implementing-sirikit-images/plist12.png)](implementing-sirikit-images/plist12.png#lightbox)
14. Agregue la `VocabularyItemExamples` clave con el **tipo** de `Array` :

    [![Agregar la clave VocabularyItemExamples con el tipo de matriz](implementing-sirikit-images/plist13.png)](implementing-sirikit-images/plist13.png#lightbox)
15. Agregue algunas `String` claves con los usos de ejemplo del término:

    [![Agregar algunas claves de cadena con usos de ejemplo del término](implementing-sirikit-images/plist14.png)](implementing-sirikit-images/plist14.png#lightbox)
16. Repita los pasos anteriores para cualquier otro término personalizado que la aplicación necesite definir.
17. Contrae la `ParameterVocabularies` clave.
18. Agregue la `IntentPhrases` clave con el **tipo** de `Array` :

    [![Agregar la clave IntentPhrases con el tipo de matriz](implementing-sirikit-images/plist15.png)](implementing-sirikit-images/plist15.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist16.png)](implementing-sirikit-images/plist16.png#lightbox)
20. Agregue la `IntentName` clave con el **tipo** `String` y la intención del ejemplo:

    [![Agregue la clave IntentName con el tipo de cadena y la intención del ejemplo.](implementing-sirikit-images/plist17.png)](implementing-sirikit-images/plist17.png#lightbox)
21. Agregue la `IntentExamples` clave con el **tipo** de `Array` :

    [![Agregar la clave IntentExamples con el tipo de matriz](implementing-sirikit-images/plist18.png)](implementing-sirikit-images/plist18.png#lightbox)
22. Agregue algunas `String` claves con los usos de ejemplo del término:

    [![Agregar algunas claves de cadena con usos de ejemplo del término](implementing-sirikit-images/plist19.png)](implementing-sirikit-images/plist19.png#lightbox)
23. Repita los pasos anteriores para los intentos que la aplicación necesita para proporcionar el uso de ejemplo de.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Agregar > nuevo elemento... > lista de propiedades de Apple > > info. plist**:

    [![Agregar un nuevo archivo info. plist](implementing-sirikit-images/plist01.w157-sml.png)](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Haga doble clic en el `AppIntentVocabulary.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
3. Haga clic en **+** para agregar una clave, establezca el **nombre** en `ParameterVocabularies` y el **tipo** en `Array` :

    [![Establezca el nombre en ParameterVocabularies y el tipo en matriz.](implementing-sirikit-images/plist02w.png)](implementing-sirikit-images/plist02w.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el **+** botón y establezca el **tipo** en `Dictionary` :

    [![Establecer el tipo en Diccionario](implementing-sirikit-images/plist03w.png)](implementing-sirikit-images/plist03w.png#lightbox)
5. Haga clic en **+** para agregar una nueva clave, establezca el **nombre** en `ParameterNames` y el **tipo** en `Array` :

    [![Establezca el nombre en ParameterNames y el tipo en matriz.](implementing-sirikit-images/plist04w.png)](implementing-sirikit-images/plist04w.png#lightbox)
6. Haga clic en **+** para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetros disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![La clave INStartWorkoutIntent. workoutName](implementing-sirikit-images/plist05w.png)](implementing-sirikit-images/plist05w.png#lightbox)
7. Agregue la `ParameterVocabulary` clave a la `ParameterVocabularies` clave con el **tipo** de `Array` :

    [![Agregue la clave ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz.](implementing-sirikit-images/plist06w.png)](implementing-sirikit-images/plist06w.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist07w.png)](implementing-sirikit-images/plist07w.png#lightbox)
9. Agregue la `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único para el término.](implementing-sirikit-images/plist08w.png)](implementing-sirikit-images/plist08w.png#lightbox)
10. Agregue la `VocabularyItemSynonyms` clave con el **tipo** de `Array` :

    [![Agregar la clave VocabularyItemSynonyms con el tipo de matriz](implementing-sirikit-images/plist09w.png)](implementing-sirikit-images/plist09w.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist10w.png)](implementing-sirikit-images/plist10w.png#lightbox)
12. Agregue la `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que está definiendo la aplicación:

    [![Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que está definiendo la aplicación](implementing-sirikit-images/plist11w.png)](implementing-sirikit-images/plist11w.png#lightbox)
13. Agregue la `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término.](implementing-sirikit-images/plist12w.png)](implementing-sirikit-images/plist12w.png#lightbox)
14. Agregue la `VocabularyItemExamples` clave con el **tipo** de `Array` :

    [![Agregar la clave VocabularyItemExamples con el tipo de matriz](implementing-sirikit-images/plist13w.png)](implementing-sirikit-images/plist13w.png#lightbox)
15. Agregue algunas `String` claves con los usos de ejemplo del término:

    [![Agregar algunas claves de cadena con usos de ejemplo del término](implementing-sirikit-images/plist14w.png)](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita los pasos anteriores para cualquier otro término personalizado que la aplicación necesite definir.
17. Contrae la `ParameterVocabularies` clave.
18. Agregue la `IntentPhrases` clave con el **tipo** de `Array` :

    [![Agregar la clave IntentPhrases con el tipo de matriz](implementing-sirikit-images/plist15w.png)](implementing-sirikit-images/plist15w.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary` :

    [![Agregar una nueva clave con el tipo de Diccionario](implementing-sirikit-images/plist16w.png)](implementing-sirikit-images/plist16w.png#lightbox)
20. Agregue la `IntentName` clave con el **tipo** `String` y la intención del ejemplo:

    [![Agregue la clave IntentName con el tipo de cadena y la intención del ejemplo.](implementing-sirikit-images/plist17w.png)](implementing-sirikit-images/plist17w.png#lightbox)
21. Agregue la `IntentExamples` clave con el **tipo** de `Array` :

    [![Agregar la clave IntentExamples con el tipo de matriz](implementing-sirikit-images/plist18w.png)](implementing-sirikit-images/plist18w.png#lightbox)
22. Agregue algunas `String` claves con los usos de ejemplo del término:

    [![Agregar algunas claves de cadena con usos de ejemplo del término](implementing-sirikit-images/plist19w.png)](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita los pasos anteriores para los intentos que la aplicación necesita para proporcionar el uso de ejemplo de.

-----

> [!IMPORTANT]
> El `AppIntentVocabulary.plist` se registrará con Siri en los dispositivos de prueba durante el desarrollo y puede tardar algún tiempo en que Siri incorpore el vocabulario personalizado. Como resultado, el evaluador deberá esperar varios minutos antes de intentar probar el vocabulario específico de la aplicación cuando se ha actualizado.

Para obtener más información, consulte nuestra [referencia de vocabulario específica](~/ios/platform/sirikit/understanding-sirikit.md) de la aplicación y la referencia de Apple [que especifica el vocabulario personalizado](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Agregar una extensión de intents

Ahora que la aplicación se ha preparado para adoptar SiriKit, el desarrollador tendrá que agregar una o más extensiones de intents a la solución para controlar los intentos necesarios para la integración de Siri.

Para cada extensión de intents necesaria, haga lo siguiente:

- Agregue un proyecto de extensión intents a la solución de aplicación de Xamarin. iOS.
- Configure el archivo de extensión intents `Info.plist` .
- Modifique la clase principal de la extensión intents.

Para obtener más información, consulte nuestra [referencia de la extensión intents](~/ios/platform/sirikit/understanding-sirikit.md) y la [creación de la referencia de la extensión intents](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)en Apple.

### <a name="creating-the-extension"></a>Crear la extensión

Para agregar una extensión de intents a la solución, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en el **nombre** de la solución en el **Panel de solución** y seleccione **Agregar**  >  **Agregar nuevo proyecto..**..
2. En el cuadro de diálogo, seleccione **iOS**  >  **extensions**is  >  **Extension** y haga clic en el botón **siguiente** : 

    [![Seleccionar extensión de intención](implementing-sirikit-images/intents05.png)](implementing-sirikit-images/intents05.png#lightbox)
3. A continuación, escriba un **nombre** para la extensión intención y haga clic en el botón **siguiente** : 

    [![Escriba un nombre para la extensión de intención](implementing-sirikit-images/intents06.png)](implementing-sirikit-images/intents06.png#lightbox)
4. Por último, haga clic en el botón **crear** para agregar la extensión intención a la solución aplicaciones: 

    [![Agregar la extensión de intención a la solución de aplicaciones](implementing-sirikit-images/intents07.png)](implementing-sirikit-images/intents07.png#lightbox)
5. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **referencias** de la extensión intención recién creada. Compruebe el nombre del proyecto común de biblioteca de código compartido (que creó la aplicación anteriormente) y haga clic en el botón **Aceptar** : 

    [![Seleccione el nombre del proyecto común de biblioteca de código compartido](implementing-sirikit-images/intents08.png)](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga clic con el botón derecho en el **nombre** de la solución en el **Explorador de soluciones** y seleccione **Agregar**  >  **Agregar nuevo proyecto..**..
2. En el cuadro de diálogo, seleccione **Visual C# > extensión de > intención de extensiones de iOS** y haga clic en el botón **siguiente** :

    [![Seleccionar extensión de intención](implementing-sirikit-images/intents05.w157-sml.png)](implementing-sirikit-images/intents05.w157.png#lightbox)
3. A continuación, escriba un **nombre** para la extensión intención y haga clic en el botón **Aceptar** .
4. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **referencias** de la extensión intents recién creada y elija **Agregar referencia de >**. Compruebe el nombre del proyecto común de biblioteca de código compartido (que creó la aplicación anteriormente) y haga clic en el botón **Aceptar** :

    [![Seleccione el nombre del proyecto común de biblioteca de código compartido](implementing-sirikit-images/intents08w.png)](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita estos pasos para el número de extensiones de intención (según la [arquitectura de la aplicación para la sección de extensiones](#architecting-the-app-for-extensions) anterior) que requerirá la aplicación.

### <a name="configuring-the-infoplist"></a>Configuración de info. plist

Para cada una de las extensiones de intents que se han agregado a la solución de la aplicación, debe configurarse en los `Info.plist` archivos para que funcionen con la aplicación.

Al igual que cualquier extensión de aplicación típica, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes` . En el caso de una extensión de intents, hay dos atributos nuevos que se deben configurar:

[![Los dos atributos nuevos que se deben configurar](implementing-sirikit-images/intents01.png)](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : es necesario y consta de una matriz de nombres de clase de intención que la aplicación quiere admitir desde la extensión de intención.
- **IntentsRestrictedWhileLocked** : es una clave opcional para que la aplicación especifique el comportamiento de la pantalla de bloqueo de la extensión. Consta de una matriz de nombres de clase de intención que la aplicación quiere requerir que el usuario inicie sesión para usarlo desde la extensión de intención.

Para configurar el archivo de la extensión de intención `Info.plist` , haga doble clic en el **Explorador de soluciones** para abrirlo para su edición. A continuación, cambie a la vista de **código fuente** y, a continuación, expanda las `NSExtension` `NSExtensionAttributes` claves y en el editor:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Las claves NSExtension y NSExtensionAttributes en el editor](implementing-sirikit-images/intents02.png)](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Las claves NSExtension y NSExtensionAttributes en el editor](implementing-sirikit-images/intents02w.png)](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda la `IntentsSupported` clave y agregue el nombre de cualquier clase de intención que admita esta extensión. Para la aplicación MonkeyChat de ejemplo, admite `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La clave INSendMessageIntent](implementing-sirikit-images/intents09.png)](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La clave INSendMessageIntent](implementing-sirikit-images/intents09w.png)](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si la aplicación requiere opcionalmente que el usuario inicie sesión en el dispositivo para usar un intento determinado, expanda la `IntentRestrictedWhileLocked` clave y agregue los nombres de clase de las intenciones que tengan acceso restringido. En el caso de la aplicación MonkeyChat de ejemplo, el usuario debe haber iniciado sesión para enviar un mensaje de chat, por lo que hemos agregado `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La clave INSendMessageIntent agregada](implementing-sirikit-images/intents10.png)](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La clave INSendMessageIntent agregada](implementing-sirikit-images/intents10w.png)](implementing-sirikit-images/intents10w.png#lightbox)

-----

Para obtener una lista completa de los dominios de intención disponibles, consulte la referencia de los [dominios de intención](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)de Apple.

### <a name="configuring-the-main-class"></a>Configurar la clase Main

A continuación, el desarrollador tendrá que configurar la clase principal que actúa como el punto de entrada principal para la extensión de intención en Siri. Debe ser una subclase de `INExtension` que se ajuste al `IINIntentHandler` delegado. Por ejemplo:

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

Hay un método solitarios que la aplicación debe implementar en la clase principal de la extensión de intención, el `GetHandler` método. A este método se le pasa una intención por parte de SiriKit y la aplicación debe devolver un **controlador de intención** que coincida con el tipo de la intención determinada.

Dado que la aplicación de ejemplo MonkeyChat solo controla un intento, se devuelve en el `GetHandler` método. Si la extensión controla más de un intento, el desarrollador agregaría una clase para cada tipo de intento y devolvería una instancia aquí basándose en la `Intent` pasada al método.

### <a name="handling-the-resolve-stage"></a>Controlar la fase de resolución

En la fase de resolución se aclara la extensión de intención y se validan los parámetros pasados desde Siri y se han establecido a través de la conversación del usuario.

Para cada parámetro que se envía desde Siri, hay un `Resolve` método. La aplicación tendrá que implementar este método para cada parámetro que la aplicación pueda necesitar para obtener la respuesta correcta del usuario.

En el caso de la aplicación MonkeyChat de ejemplo, la extensión intención requerirá uno o varios destinatarios a los que enviar el mensaje. Para cada destinatario de la lista, la extensión deberá realizar una búsqueda de contactos que pueda tener el siguiente resultado:

- Se encontró exactamente un contacto coincidente.
- Se encuentran dos o más contactos coincidentes.
- No se encuentran contactos coincidentes.

Además, MonkeyChat requiere contenido para el cuerpo del mensaje. Si el usuario no lo ha proporcionado, Siri debe pedir al usuario el contenido.

La extensión intención tendrá que controlar correctamente cada uno de estos casos.

```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Para obtener más información, consulte la referencia de [la fase de resolución y la](~/ios/platform/sirikit/understanding-sirikit.md) referencia de resolución de Apple [y control de problemas](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Controlar la fase de confirmación

La fase de confirmación es donde la extensión intención comprueba para ver que tiene toda la información para completar la solicitud del usuario. La aplicación desea enviar la confirmación y todos los detalles de la compatibilidad de lo que se va a pasar a Siri para que se pueda confirmar con el usuario que es la acción deseada.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Para obtener más información, consulte [la referencia de la fase CONFIRM](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Procesamiento de la intención

Este es el punto en el que la extensión de intención realiza realmente la tarea para completar la solicitud del usuario y devolver los resultados a Siri para que se pueda informar al usuario.

```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Para obtener más información, consulte nuestra [referencia de la fase de identificador](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Agregar una extensión de interfaz de usuario de intents

La extensión de interfaz de usuario de intents opcional presenta la oportunidad de llevar la interfaz de usuario y la personalización de marca de la aplicación a la experiencia de Siri y hacer que los usuarios se sientan conectados a la aplicación. Con esta extensión, la aplicación puede traer la marca y el visual y otra información en la transcripción.

[![Un ejemplo de salida de extensión de la interfaz de usuario de intents](implementing-sirikit-images/intentsui01.png)](implementing-sirikit-images/intentsui01.png#lightbox)

Al igual que la extensión intents, el desarrollador realizará el paso siguiente para la extensión de la interfaz de usuario de intents:

- Agregue un proyecto de extensión de interfaz de usuario de intents a la solución de aplicación de Xamarin. iOS.
- Configure el archivo de extensión de la interfaz de usuario de intents `Info.plist` .
- Modifique la clase principal de la extensión de la interfaz de usuario de intents.

Para obtener más información, consulte [la referencia de la extensión de la interfaz de usuario de intents](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [que proporciona una referencia de interfaz personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Crear la extensión

Para agregar una extensión de interfaz de usuario de intents a la solución, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en el **nombre** de la solución en el **Panel de solución** y seleccione **Agregar**  >  **Agregar nuevo proyecto..**..
2. En el cuadro de diálogo **iOS**  >  **Extensions**  >  , seleccione la**extensión de interfaz de usuario calidad** de extensiones de iOS y haga clic en el botón **siguiente** : 

    [![Seleccionar la extensión de IU de intención](implementing-sirikit-images/intents11.png)](implementing-sirikit-images/intents11.png#lightbox)
3. A continuación, escriba un **nombre** para la extensión intención y haga clic en el botón **siguiente** : 

    [![Escriba un nombre para la extensión de intención](implementing-sirikit-images/intents12.png)](implementing-sirikit-images/intents12.png#lightbox)
4. Por último, haga clic en el botón **crear** para agregar la extensión intención a la solución aplicaciones: 

    [![Agregar la extensión de intención a la solución de aplicaciones](implementing-sirikit-images/intents13.png)](implementing-sirikit-images/intents13.png#lightbox)
5. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **referencias** de la extensión intención recién creada. Compruebe el nombre del proyecto común de biblioteca de código compartido (que creó la aplicación anteriormente) y haga clic en el botón **Aceptar** : 

    [![Seleccione el nombre del proyecto común de biblioteca de código compartido](implementing-sirikit-images/intents14.png)](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Haga clic con el botón derecho en el nombre de la **solución** en el **Explorador de soluciones** y seleccione **Agregar**  >  **Agregar nuevo proyecto..** .
2. En el cuadro de diálogo, seleccione **iOS**  >  **extensions**  >  **interfaz UI Extension** y haga clic en el botón **siguiente** .
3. A continuación, escriba un **nombre** para la extensión intención y haga clic en el botón **Aceptar** .
4. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **referencias** de la extensión intención recién creada. Compruebe el nombre del proyecto común de biblioteca de código compartido (que creó la aplicación anteriormente) y haga clic en el botón **Aceptar** .
    
-----

### <a name="configuring-the-infoplist"></a>Configuración de info. plist

Configure el archivo de la extensión de la interfaz de usuario de intents `Info.plist` para que funcione con la aplicación.

Al igual que cualquier extensión de aplicación típica, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes` . Para una extensión de intents hay un nuevo atributo que debe configurarse:

[![Un nuevo atributo que se debe configurar](implementing-sirikit-images/intents03.png)](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** es necesario y consta de una matriz de nombres de clase de intención que la aplicación desea admitir de la extensión de intención.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para configurar el archivo de la extensión de la interfaz de usuario de intención `Info.plist` , haga doble clic en el **Explorador de soluciones** para abrirlo para su edición. A continuación, cambie a la vista de **código fuente** y, a continuación, expanda las `NSExtension` `NSExtensionAttributes` claves y en el editor:

[![Las claves NSExtension y NSExtensionAttributes en el editor](implementing-sirikit-images/intents04.png)](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para configurar el archivo de la extensión de la interfaz de usuario de intención `Info.plist` , haga doble clic en el **Explorador de soluciones** para abrirlo para su edición. Expanda `NSExtension` las `NSExtensionAttributes` claves y en el editor:

[![Las claves NSExtension y NSExtensionAttributes en el editor](implementing-sirikit-images/intents04w.png)](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda la `IntentsSupported` clave y agregue el nombre de cualquier clase de intención que admita esta extensión. Para la aplicación MonkeyChat de ejemplo, admite `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La clave INSendMessageIntent](implementing-sirikit-images/intents15.png)](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La clave INSendMessageIntent](implementing-sirikit-images/intents15w.png)](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obtener una lista completa de los dominios de intención disponibles, consulte la referencia de los [dominios de intención](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)de Apple.

### <a name="configuring-the-main-class"></a>Configurar la clase Main

Configure la clase principal que actúa como el punto de entrada principal para la extensión de interfaz de usuario de intención en Siri. Debe ser una subclase de `UIViewController` que se ajuste a la `IINUIHostedViewController` interfaz. Por ejemplo:

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri pasará una `INInteraction` instancia de clase al `Configure` método de la `UIViewController` instancia dentro de la extensión de interfaz de usuario de intención.

El `INInteraction` objeto proporciona tres elementos clave de información a la extensión:

1. Objeto de intención que se está procesando.
2. Objeto de respuesta de intención de `Confirm` los `Handle` métodos y de la extensión de intención.
3. Estado de interacción que define el estado de la interacción entre la aplicación y Siri.

La `UIViewController` instancia es la clase principal para la interacción con Siri y, como hereda de `UIViewController` , tiene acceso a todas las características de UIKit.

Cuando Siri llama al `Configure` método de `UIViewController` que pasa en un contexto de la vista que indica que el controlador de vista se hospedará en una tarjeta Siri Snippit o Maps.

Siri también pasará un controlador de finalización que la aplicación necesite para devolver el tamaño deseado de la vista una vez que la aplicación haya terminado de configurarla.

### <a name="design-the-ui-in-ios-designer"></a>Diseño de la interfaz de usuario en iOS Designer

Diseño de la interfaz de usuario de la extensión de IU de intents en el diseñador de iOS. Haga doble clic en el archivo de la extensión `MainInterface.storyboard` en el **Explorador de soluciones** para abrirlo para su edición. Arrastre todos los elementos de la interfaz de usuario necesarios para compilar la interfaz de usuario y guardar los cambios.

> [!IMPORTANT]
> Aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de la interfaz de usuario de intención `UIViewController` , están estrictamente prohibidos como la interfaz de usuario de intención en el modo no interactivo y el usuario no podrá interactuar con ellos.

### <a name="wire-up-the-user-interface"></a>Conectar la interfaz de usuario

Con la interfaz de usuario de la extensión de IU de intents creada en el diseñador de iOS, edite la `UIViewController` subclase e invalide el `Configure` método de la manera siguiente:

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>Invalidación de la interfaz de usuario predeterminada de Siri

La extensión de la interfaz de usuario de intents siempre se mostrará junto con otro contenido de Siri, como el icono y el nombre de la aplicación en la parte superior de la interfaz de usuario o, en función de la intención, los botones (como enviar o cancelar) se pueden mostrar en la parte inferior.

Hay algunos casos en los que la aplicación puede reemplazar la información que Siri muestra al usuario de forma predeterminada, como la mensajería o las asignaciones en las que la aplicación puede reemplazar la experiencia predeterminada por una adaptada a la aplicación.

Si la extensión de la interfaz de usuario de intents necesita reemplazar elementos de la interfaz de usuario de Siri predeterminada, la `UIViewController` subclase deberá implementar la `IINUIHostedViewSiriProviding` interfaz y participar en la visualización de un elemento de interfaz determinado.

Agregue el código siguiente a la `UIViewController` subclase para indicar a Siri que la extensión de la interfaz de usuario de intención ya muestra el contenido del mensaje:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Consideraciones

Apple sugiere que el desarrollador tenga en cuenta las siguientes consideraciones al diseñar e implementar las extensiones de la interfaz de usuario de intención:

- **Sea consciente del uso de memoria** : dado que las extensiones de la interfaz de usuario de intención son temporales y solo se muestran durante un breve período de tiempo, el sistema impone restricciones de memoria más estrictas que las que se usan con una aplicación completa.
- **Tenga en cuenta los tamaños de vista mínimo y máximo** : Asegúrese de que las extensiones de la interfaz de usuario de intención se ven bien en todos los tipos de dispositivos iOS, el tamaño y la orientación. Además, es posible que no se pueda conceder el tamaño deseado que devuelve la aplicación a Siri.
- **Use patrones de diseño flexibles y adaptables** de nuevo para asegurarse de que la interfaz de usuario tenga un aspecto excelente en cada dispositivo.

## <a name="summary"></a>Resumen

En este artículo se ha explicado SiriKit y se ha mostrado cómo puede agregarse a las aplicaciones de Xamarin. iOS para proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](/samples/xamarin/ios-samples/ios10-elizachat)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de trabajo](https://developer.apple.com/reference/intents)
- [Referencia del marco de interfaz de usuario de Intent](https://developer.apple.com/reference/intentsui)
- [Referencia de dominios de intención](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)