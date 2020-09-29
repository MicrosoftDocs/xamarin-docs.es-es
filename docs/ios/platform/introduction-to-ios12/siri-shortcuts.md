---
title: Métodos abreviados de Siri en Xamarin. iOS
description: En este documento se describe cómo usar los métodos abreviados de Siri en iOS 12. Describe NSUserActivities, los intentos personalizados, la asignación de accesos directos de voz, los métodos abreviados relevantes y mucho más.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/08/2018
ms.openlocfilehash: f2a350cb227e55188830161818779e4155c53bcc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434180"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Métodos abreviados de Siri en Xamarin. iOS

En [iOS 10](~/ios/platform/sirikit/index.md), Apple presentó SiriKit, lo que permite compilar aplicaciones de mensajería, llamadas VoIP, pagos, entrenamientos, reservas de la conducción y búsqueda fotográfica que interactúan con Siri.

En [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit obtuvo compatibilidad con más tipos de aplicaciones y mayor flexibilidad para la personalización de la interfaz de usuario.

iOS 12 agrega accesos directos a Siri, lo que permite que todos los tipos de aplicaciones expongan su funcionalidad a Siri. Siri aprende cuando algunas tareas basadas en aplicaciones son más relevantes para el usuario y usa este conocimiento para sugerir posibles acciones a través de _accesos directos_. Al pulsar un método abreviado o invocarlo con un comando de voz, se abre una aplicación o se ejecuta una tarea en segundo plano.

Los métodos abreviados se deben usar para acelerar la capacidad de un usuario para llevar a cabo una tarea común, en muchos casos sin necesidad de abrir la aplicación en cuestión.

## <a name="sample-app-soup-chef"></a>Aplicación de ejemplo: un chef de sopa

Para comprender mejor los métodos abreviados de Siri, eche un vistazo a la aplicación de ejemplo de [chef de sopa](/samples/xamarin/ios-samples/ios12-soupchef) . La chef de sopa permite a los usuarios realizar pedidos desde un restaurante de sopa de sopa, ver el historial de pedidos y definir frases que se usarán al ordenar la sopa interactuando con Siri.

> [!TIP]
> Antes de probar el chef de sopa en un dispositivo o simulador de iOS 12, habilite las dos configuraciones siguientes, que resultan útiles al depurar accesos directos:
>
> - En la aplicación **configuración** , habilite **desarrollador > Mostrar accesos directos recientes**.
> - En la aplicación **configuración** , habilite **desarrollador > Mostrar donaciones en la pantalla de bloqueo**.
>
> Esta configuración de depuración facilita la búsqueda de accesos directos creados recientemente (en lugar de previstos) en la pantalla de bloqueo y la pantalla de búsqueda de iOS.

Para usar la aplicación de ejemplo:

- Instale y ejecute la aplicación de ejemplo de chef de sopa en un [dispositivo](#testing-on-device)o simulador de iOS 12.
- Haga clic **+** en el botón de la esquina superior derecha para crear un nuevo pedido.
- Seleccione un tipo de sopa, especifique una cantidad y opciones y pulse en **realizar pedido**.
- En la pantalla **historial de pedidos** , puntee en el orden recién creado para ver sus detalles.
- En la parte inferior de la pantalla de detalles del pedido, puntee en **Agregar a Siri**.
- Grabe una frase de voz para asociarla con el pedido y pulse **listo**.
- Minimice la chef de sopa, invoque Siri y vuelva a colocar el pedido mediante la frase de voz que acaba de grabar.
- Una vez que Siri complete el pedido, vuelva a abrir el chef de sopa y observe que el nuevo pedido aparece en la pantalla del **historial de pedidos** .

En la aplicación de ejemplo se muestra cómo:

- [Use un acceso directo de NSUserActivity para abrir una aplicación](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Use un método abreviado de intención personalizado para realizar una tarea](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Proporcione una interfaz de usuario para un intento personalizado](#providing-a-user-interface-for-a-custom-intent).
- [Cree un acceso directo de voz](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info. plist y contitles. plist

Antes de profundizar más en el código de chef de sopa, eche un vistazo a los archivos **info. plist** y **aptitles. plist** .

### <a name="infoplist"></a>Info.plist

El archivo **info. plist** del proyecto **SoupChef** define el identificador de la **agrupación** como `com.xamarin.SoupChef` . Este identificador de paquete se usará como prefijo para los identificadores de agrupación de las extensiones de la interfaz de usuario de intents y intents que se describen más adelante en este documento.

El archivo **info. plist** también contiene lo siguiente:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Este `NSUserActivityTypes` par clave-valor indica que el chef de sopa sabe cómo controlar un `OrderSoupIntent` y un con [`NSUserActivity`](xref:Foundation.NSUserActivity) [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) "com. Xamarin. SoupChef. viewMenu".

Las actividades y los intentos personalizados que se pasan a la propia aplicación, en contraposición a sus extensiones, se controlan en `AppDelegate` (a [`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) mediante el [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) método.

### <a name="entitlementsplist"></a>Entitlements.plist

El archivo **contitles. plist** del proyecto **SoupChef** contiene lo siguiente:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Esta configuración indica que la aplicación usa el grupo de aplicaciones "grupo. com. Xamarin. SoupChef". La extensión de la aplicación **SoupChefIntents** usa este mismo grupo de aplicaciones, que permite compartir los dos proyectos [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
del modelo.

La `com.apple.developer.siri` clave indica que la aplicación interactúa con Siri.

> [!NOTE]
> La configuración de compilación del proyecto **SoupChef** establece los **derechos personalizados** en **contitles. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Uso de un acceso directo de NSUserActivity para abrir una aplicación

Para crear un acceso directo que abra una aplicación para mostrar contenido específico, cree un `NSUserActivity` y asócielo al controlador de vistas de la pantalla en la que desea que se abra el acceso directo.

### <a name="setting-up-an-nsuseractivity"></a>Configuración de un NSUserActivity

En la pantalla de menú, `SoupMenuViewController` crea `NSUserActivity` y lo asigna a la propiedad del controlador de vista [`UserActivity`](xref:UIKit.UIResponder.UserActivity) :

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

El establecimiento de la `UserActivity` propiedad _dona_ la actividad a Siri. A partir de esta donación, Siri obtiene información sobre cuándo y dónde es relevante esta actividad para el usuario y aprende a sugerirlo mejor en el futuro.

`NSUserActivityHelper` es una clase de utilidad incluida en la solución **SoupChef** , en la biblioteca de clases **SoupKit** . Crea `NSUserActivity` y establece diversas propiedades relacionadas con Siri y Search:

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Tenga en cuenta lo siguiente en concreto:

- Si se establece `EligibleForPrediction` en, `true` indica que Siri puede predecir esta actividad y mostrarla como un acceso directo.
- La [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet) matriz es un estándar [`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) que se usa para incluir `NSUserActivity` en los resultados de la búsqueda de iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) es una frase que Siri le sugerirá al usuario como una opción potencial al asignar una frase a un acceso directo.

### <a name="handling-an-nsuseractivity-shortcut"></a>Controlar un acceso directo de NSUserActivity

Para controlar un `NSUserActivity` acceso directo invocado por un usuario, una aplicación de iOS debe reemplazar el `ContinueUserActivity` método de la `AppDelegate` clase, respondiendo según el `ActivityType` campo del objeto pasado `NSUserActivity` :

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Este método llama `HandleUserActivity` a, que busca segue en la pantalla de menú y lo invoca:

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Asignación de una frase a un NSUserActivity

Para asignar una frase a un `NSUserActivity` , abra la aplicación de **configuración** de iOS y elija **Siri & búsqueda > mis accesos directos**. A continuación, seleccione el acceso directo (en este caso, "solicitar almuerzo") y grabe una frase.

La invocación de Siri y el uso de esta frase abrirá el chef de sopa en la pantalla de menús.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Usar un método abreviado de intención personalizado para realizar una tarea

### <a name="defining-a-custom-intent"></a>Definición de un intento personalizado

Para proporcionar un acceso directo que permita a un usuario completar rápidamente una tarea específica relacionada con la aplicación, cree un intento personalizado. Un intento personalizado representa una tarea que un usuario puede querer completar, parámetros relevantes para esa tarea y respuestas potenciales resultantes de la ejecución de la tarea. En función de cómo se defina una intención personalizada, al invocarla puede abrir la aplicación o ejecutar una tarea en segundo plano.

Use Xcode 10 para crear intentos personalizados. En el [repositorio de SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), el intento personalizado se define en **OrderSoupIntentCodeGen**, un proyecto de Objective-C. Abra este proyecto y seleccione el archivo **intents. intentdefinition** en el **navegador del proyecto** para ver el intento de **OrderSoup** .

Tenga en cuenta lo siguiente:

- La intención tiene una **categoría** de **orden**. Hay varias categorías predefinidas que se pueden utilizar para los intentos personalizados; Seleccione el que más se aproxime a la tarea que va a habilitar su intención personalizada. Dado que se trata de una aplicación de orden de sopa, **OrderSoupIntent** usa el **orden**.
- La casilla **confirmación** indica si Siri debe solicitar confirmación antes de ejecutar la tarea. Para el **orden** de la intención de sopa en el chef de sopa, esta opción está habilitada porque el usuario realiza una compra.
- La sección de **parámetros** del archivo. intentdefinition define los parámetros relevantes para un acceso directo. Para colocar un orden de sopa, el chef de sopa debe conocer el tipo de sopa, su cantidad y las opciones asociadas.
Cada parámetro tiene un tipo; un parámetro que no se puede representar mediante un tipo predefinido se establece como **personalizado**.
- La interfaz de **tipos de acceso directo** describe las diversas combinaciones de parámetros que Siri puede usar al sugerir el acceso directo. Las secciones **título** y **subtítulo** asociadas permiten definir los mensajes que Siri usará al presentar un acceso directo sugerido al usuario.
- La casilla **compatible con la ejecución en segundo plano** debe estar seleccionada para cualquier acceso directo que se pueda ejecutar sin abrir la aplicación para la interacción con el usuario.

### <a name="defining-custom-intent-responses"></a>Definir respuestas de intención personalizadas

El elemento de **respuesta** anidado debajo de la intención **OrderSoup** representa las posibles respuestas que se producen a partir de un orden de sopa.

En la definición de respuesta de la intención de **OrderSoup** , tenga en cuenta lo siguiente:

- **Las propiedades** de una respuesta se pueden usar para personalizar el mensaje que se comunica de vuelta al usuario. La respuesta de intención **OrderSoup** tiene propiedades **sopa** y **waitTime** .
- Las **plantillas de respuesta** especifican los distintos mensajes de error y correctos que se pueden usar para indicar el estado una vez completada la tarea de la intención.
- Se debe seleccionar la casilla **correcto** para las respuestas que indican que la operación se ha realizado correctamente.
- La respuesta **OrderSoupIntent** Success usa las propiedades **sopa** y **waitTime** para proporcionar un mensaje descriptivo y útil que describe cuándo estará listo el orden de la sopa.

### <a name="generating-code-for-the-custom-intent"></a>Generar código para la intención personalizada

Al compilar el proyecto de Xcode que contiene esta definición de intención personalizada, Xcode genera código que se puede usar para interactuar mediante programación con la intención personalizada y sus respuestas.

Para ver este código generado:

- Abra **AppDelegate. m**.
- Agregue una importación al archivo de encabezado de la intención personalizada: `#import "OrderSoupIntent.h"`
- En cualquier método de la clase, agregue una referencia a `OrderSoupIntent` .
- Haga clic con el botón derecho en `OrderSoupIntent` y elija **saltar a definición**.
- Haga clic con el botón derecho en el archivo recién abierto, **OrderSoupIntent. h**y seleccione **Mostrar en el buscador**.
- Se abrirá una ventana de **Finder** que contiene un archivo. h y. m que contiene el código generado.

Este código generado incluye:

- `OrderSoupIntent` : Una clase que representa la intención personalizada.
- `OrderSoupIntentHandling` : Un protocolo que define los métodos que se usarán para confirmar que se debe ejecutar la intención y el método que realmente lo ejecuta.
- `OrderSoupIntentResponseCode` : Una enumeración que define varios Estados de respuesta.
- `OrderSoupIntentResponse` : una clase que representa la respuesta a la ejecución de un intento.

### <a name="creating-a-binding-to-the-custom-intent"></a>Crear un enlace al intento personalizado

Para usar el código generado por Xcode en una aplicación de Xamarin. iOS, cree un enlace de C# para él.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Crear una biblioteca estática y definiciones de enlace de C#

En el [repositorio de SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), eche un vistazo a la carpeta **OrderSoupIntentStaticLib** y abra el proyecto de Xcode **OrderSoupIntentStaticLib. Xcodeproj** .

Este proyecto de **biblioteca estática de chocolate Touch** contiene los archivos **OrderSoupIntent. h** y **OrderSoupIntent. m** generados por Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configurar los valores de compilación del proyecto de biblioteca estática

En el **Explorador de proyectos**de Xcode, seleccione el proyecto de nivel superior, **OrderSoupIntentStaticLib**, y vaya a compilar **fases > orígenes de compilación**.
Observe que **OrderSoupIntent. m** (que importa **OrderSoupIntent. h**) aparece aquí. En **Link Binary with Libraries (vincular binario con bibliotecas**), tenga en cuenta que se incluyen **intents. Framework** y **Foundation. Framework** .
Con estos valores en su lugar, el marco de trabajo se compilará correctamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Compilar la biblioteca estática y generar definiciones de enlaces de C#

Para compilar la biblioteca estática y generar definiciones de enlaces de C# para ella, siga estos pasos:

- [Instale Objective Sharpie](../../../cross-platform/macios/binding/objective-sharpie/get-started.md?context=xamarin%252fmac#installing-objective-sharpie), la herramienta que se usa para generar definiciones de enlaces a partir de los archivos. h y. m creados por Xcode.

- Configure el sistema para usar las herramientas de línea de comandos de Xcode 10:

  > [!WARNING]
  > La actualización de las herramientas de línea de comandos seleccionadas afecta a todas las versiones instaladas de Xcode en el sistema. Cuando haya terminado de usar la aplicación de ejemplo de chef de sopa, asegúrese de revertir esta configuración a su configuración original.

  - En Xcode, elija **xcode > preferencias > ubicaciones** y establezca **herramientas de línea de comandos** en la instalación más reciente de Xcode 10 disponible en el sistema.

- En el terminal, en `cd` el directorio **OrderSoupIntentStaticLib**

- Tipo `make` , que compila:

  - La biblioteca estática, **libOrderSoupIntentStaticLib. a**
  - En el directorio de salida **Bo** , las definiciones de los enlaces de C#:
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

El proyecto **OrderSoupIntentBindings** , que se basa en esta biblioteca estática y sus definiciones de enlaces asociadas, genera estos elementos automáticamente.
Sin embargo, la ejecución manual mediante el proceso anterior garantizará que se compila según lo esperado.

Para obtener más información sobre la creación de una biblioteca estática y el uso de Objective Sharpie para crear definiciones de enlaces de C#, eche un vistazo al tema sobre el [enlace de una biblioteca de Objective-C de iOS](../binding-objective-c/walkthrough.md?tabs=vsmac#creating-a-static-library) .

#### <a name="creating-a-bindings-library"></a>Creación de una biblioteca de enlaces

Con la biblioteca estática y las definiciones de enlaces de C# creadas, la parte restante necesaria para consumir el código relacionado con la intención generado por Xcode en un proyecto de Xamarin. iOS es una biblioteca de enlaces.

En el [repositorio de chef de sopa](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), abra el archivo **SoupChef. sln** . Entre otras cosas, esta solución contiene **OrderSoupIntentBinding**, una biblioteca de enlaces para la biblioteca estática generada anteriormente.

Observe en particular que este proyecto incluye:

- **ApiDefinitions.CS** : un archivo generado anteriormente por Objective Sharpie y agregado a este proyecto. La acción de **compilación** de este archivo se establece en **ObjcBindingApiDefinition**.
- **StructsAndEnums.CS** : otro archivo generado anteriormente por Objective Sharpie y agregado a este proyecto. La acción de **compilación** de este archivo se establece en **ObjcBindingCoreSource**.
- Una **referencia nativa** a **libOrderSoupIntentStaticLib. a**, la biblioteca estática compilada anteriormente.

> [!NOTE]
> Tanto **ApiDefinitions.CS** como **StructsAndEnums.CS** contienen atributos como `[Watch (5,0), iOS (12,0)]` . Estos atributos, generados por Objective Sharpie, se han comentado, ya que no son necesarios para este proyecto.

Para obtener más información sobre cómo crear una biblioteca de enlaces de C#, eche un vistazo al tutorial sobre el [enlace de una biblioteca de Objective-C de iOS](../binding-objective-c/walkthrough.md?tabs=vsmac#create-a-xamarinios-binding-project) .

Observe que el proyecto **SoupChef** contiene una referencia a **OrderSoupIntentBinding**, lo que significa que ahora puede acceder a, en C#, a las clases, interfaces y enumeraciones que contiene:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Agregar el archivo. intentdefinition a la solución

En la solución de C# **SoupChef** , el proyecto **SoupKit** contiene código compartido entre la aplicación y sus extensiones. El archivo **intents. intentdefinition** se ha colocado en el **directorio base. lproj** de **SoupKit**y tiene una acción de **compilación** de **contenido**. El proceso de compilación copia este archivo en el grupo de aplicaciones de la aplicación de chef de sopa, donde es necesario para que la aplicación funcione correctamente.

### <a name="donating-an-intent"></a>Donar un intento

Para que Siri sugiera un acceso directo, primero debe comprender cuándo es relevante el acceso directo.

Para proporcionar a Siri esta información, el chef de sopa _dona_ una intención a Siri cada vez que el usuario coloca un orden de sopa. En función de esta donación, cuando se donó, donde se donó, los parámetros que contiene – Siri aprenden Cuándo sugerir el acceso directo en el futuro.

**SoupChef** usa la `SoupOrderDataManager` clase para colocar donaciones.
Cuando se llama para colocar un orden de sopa para un usuario, el `PlaceOrder` método a su vez llama a [`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*) :

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Después de capturar un intento, se encapsula en un [`INInteraction`](xref:Intents.INInteraction) .
`INInteraction`Se le proporciona un[`Identifier`](xref:Intents.INInteraction.Identifier*)
que coincide con el identificador único del pedido (esto resultará útil más adelante cuando se eliminen las donaciones de intención que ya no son válidas). A continuación, la interacción se dona a Siri.

La llamada al `order.Intent` captador captura un `OrderSoupIntent` objeto que representa el orden estableciendo su `Quantity` imagen, `Soup` , `Options` y, y una frase de invocación que se va a usar como una sugerencia cuando el usuario registra una frase para que Siri se asocie a la intención:

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Quitar donaciones no válidas

Es importante quitar las donaciones que ya no son válidas para que Siri no realice sugerencias de acceso directo inservibles o confusas.

En el chef de sopa, la pantalla del **menú configurar** se puede usar para marcar un elemento de menú como no disponible. Siri ya no debe sugerir un acceso directo para pedir un elemento de menú no disponible, por lo que el `RemoveDonation` método de `SoupMenuManager` elimina las donaciones para los elementos de menú que ya no están disponibles. Lo hace de la siguiente manera:

- Buscar pedidos asociados con el elemento de menú ahora no disponible.
- Capturar sus identificadores.
- Eliminar interacciones que tienen el mismo identificador.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Creación de una extensión de intents

El código que se ejecuta cuando Siri invoca un intento se coloca en una extensión intents, que se puede agregar como un nuevo proyecto a la misma solución que una aplicación de Xamarin. iOS existente, como la de sopa chef. En la solución **SoupChef** , la extensión se denomina **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents: info. plist y contitles. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents: info. plist

**Info. plist** en el proyecto **SoupChefIntents** define el identificador de la **agrupación** como `com.xamarin.SoupChef.SoupChefIntents` .

El archivo **info. plist** también contiene lo siguiente:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

En el archivo **info. plist**anterior:

- `IntentsRestrictedWhileLocked` enumera las intenciones que solo deben administrarse cuando el dispositivo está desbloqueado.
- `IntentsSupported` enumera las intenciones controladas por esta extensión.
- `NSExtensionPointIdentifier` especifica el tipo de extensión de aplicación (consulte la [documentación de Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obtener más información).
- `NSExtensionPrincipalClass` Especifica la clase que se debe usar para controlar los intentos admitidos por esta extensión.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents: contitles. plist

El archivo **contitles. plist** del proyecto **SoupChefIntents** tiene la funcionalidad de **grupos de aplicaciones** . Esta capacidad está configurada para usar el mismo grupo de aplicaciones que el proyecto **SoupChef** :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

El chef de sopa conserva los datos con `NSUserDefaults` . Para compartir datos entre la aplicación y la extensión de la aplicación, hacen referencia al mismo grupo de aplicaciones en los archivos de **derechos. plist** .

> [!NOTE]
> La configuración de compilación del proyecto **SoupChefIntents** establece los **derechos personalizados** en **contitles. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Controlar una tarea en segundo plano de OrderSoupIntent

Una extensión intents ejecuta las tareas en segundo plano necesarias para un acceso directo basado en un intento personalizado.

Siri llama al [`GetHandler`](xref:Intents.INExtension.GetHandler*) método de la `IntentHandler` clase (definido en **info. plist** como `NSExtensionPrincipalClass` ) para obtener una instancia de una clase que extiende `OrderSoupIntentHandling` , que se puede usar para controlar un `OrderSoupIntent` :

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, que se define en el proyecto **SoupKit** de código compartido, implementa dos métodos importantes:

- `ConfirmOrderSoup` : Confirma si la tarea asociada a la intención debe ejecutarse realmente.
- `HandleOrderSoup` : Coloca el orden de sopa y responde al usuario mediante una llamada al controlador de finalización que se ha pasado.

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Control de un OrderSoupIntent que abre la aplicación

Una aplicación debe administrar correctamente las intenciones que no se ejecutan en segundo plano.
Se administran de la misma manera que los `NSUserActivity` métodos abreviados, en el `ContinueUserActivity` método de `AppDelegate` :

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Proporcionar una interfaz de usuario para un intento personalizado

Una extensión de interfaz de usuario de intents proporciona una interfaz de usuario personalizada para una extensión de intents. En la solución **SoupChef** , **SoupChefIntentsUI** es una extensión de interfaz de usuario de intents que proporciona una interfaz para **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI: info. plist y contitles. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI: info. plist

**Info. plist** en el proyecto **SoupChefIntentsUI** define el identificador de la **agrupación** como `com.xamarin.SoupChef.SoupChefIntentsui` .

El archivo **info. plist** también contiene lo siguiente:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

En el archivo **info. plist**anterior:

- `IntentsSupported` indica que la `OrderSoupIntent` extensión de la interfaz de usuario de intents controla el control.
- `NSExtensionPointIdentifier` especifica el tipo de extensión de aplicación (consulte la [documentación de Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obtener más información).
- `NSExtensionMainStoryboard` especifica el guión gráfico que define la interfaz principal de esta extensión.

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI: contitles. plist

El proyecto **SoupChefIntentsUI** no necesita un archivo **contitles. plist** .

### <a name="creating-the-user-interface"></a>Creación de la interfaz de usuario

Dado que **info. plist** para **SoupChefIntentsUI** establece la `NSExtensionMainStoryboard` clave en `MainInterface` , el archivo **MainInterace. Storyboard** define la interfaz para la extensión de la interfaz de usuario de intents.

En este guión gráfico, hay un único controlador de vista, de tipo **IntentViewController**. Hace referencia a dos vistas:

- **invoiceView**, de tipo `InvoiceView`
- **confirmationView**, de tipo `ConfirmOrderView`

> [!NOTE]
> Las interfaces de **invoiceView** y **confirmationView** se definen en **Main. Storyboard** como vistas secundarias. IOS Designer en Visual Studio para Mac y Visual Studio 2017 no proporcionan compatibilidad para ver o editar vistas secundarias. para ello, Abra **Main. Storyboard** en la Interface Builder de Xcode.

`IntentViewController` implementa el [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interfaz, que se usa para proporcionar una interfaz personalizada cuando se trabaja con Siri intents. [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
se llama al método para personalizar la interfaz, que muestra la confirmación o la factura, dependiendo de si se confirma la interacción ( [`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus) ) o si se ha ejecutado correctamente [`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus) ():

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Para obtener más información sobre el `ConfigureView` método, vea la presentación de WWDC 2017 de Apple, [what's New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Crear un acceso directo de voz

La chef de sopa proporciona una interfaz para asignar un acceso directo de voz a cada pedido, lo que permite ordenar sopa con Siri. De hecho, iOS proporciona la interfaz usada para registrar y asignar accesos directos de voz y requiere poco código personalizado.

En `OrderDetailViewController` , cuando un usuario pulsa la fila **Agregar a Siri** de la tabla, el [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*) método presenta una pantalla para agregar o editar un acceso directo de voz:

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

En función de si existe o no un acceso directo de voz existente para el orden que se muestra actualmente, `RowSelected` presenta un controlador de vista de tipo [`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController) o [`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController) .
En cada caso, `OrderDetailViewController` se establece como el del controlador de vista `Delegate` , por lo que también implementa [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
y [`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Pruebas en el dispositivo

Para ejecutar el chef de sopa en un dispositivo, siga las instrucciones que se indican a continuación. Lea también la [Nota sobre el aprovisionamiento automático](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Grupo de aplicaciones, ID. de aplicación, perfiles de aprovisionamiento

En la sección **certificados, identificadores & perfiles** del [portal para desarrolladores de Apple](https://developer.apple.com/), haga lo siguiente:

- Cree un grupo de aplicaciones para compartir datos entre la aplicación de chef de sopa y sus extensiones. Por ejemplo: **Group. com. yourcompanyname. SoupChef**

- Cree tres ID. de aplicación: uno para la propia aplicación, uno para la extensión intents y otro para la extensión de la interfaz de usuario de intents. Por ejemplo:

  - Aplicación: **com. yourcompanyname. SoupChef**
    - Para este identificador de aplicación, asigne las funcionalidades SiriKit y **grupos de aplicaciones** .

  - Extensión intents: **com. yourcompanyname. SoupChef. intents**
    - Para este identificador de aplicación, asigne la funcionalidad de **grupos de aplicaciones** .

  - Extensión de la interfaz de usuario de intents: **com. yourcompanyname. SoupChef. Intentsui**
    - Este identificador de aplicación no necesita ninguna funcionalidad especial.

- Después de crear los identificadores de aplicación anteriores, edite la funcionalidad de **grupos de aplicaciones** asignada a la aplicación y la extensión intents, especificando el grupo de aplicaciones específico que se creó anteriormente.

- Cree tres nuevos perfiles de aprovisionamiento de desarrollo, uno para cada uno de los nuevos identificadores de aplicación.

- Descargue estos perfiles de aprovisionamiento y haga doble clic en cada uno de ellos para instalarlo. Si Visual Studio para Mac o Visual Studio 2017 ya se está ejecutando, reinícielo para asegurarse de que registra los nuevos perfiles de aprovisionamiento.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Edición de info. plist, contitles. plist y código fuente

En Visual Studio para Mac o Visual Studio 2017, haga lo siguiente:

- Actualice los distintos archivos **info. plist** de la solución. Establezca la aplicación, la extensión intents y el **identificador de paquete** de la extensión de la interfaz de usuario de intents en los identificadores de aplicación definidos anteriormente:

  - Aplicación: **com. yourcompanyname. SoupChef**
  - Extensión intents: **com. yourcompanyname. SoupChef. intents**
  - Extensión de la interfaz de usuario de intents: **com. yourcompanyname. SoupChef. Intentsui**

- Actualice el archivo **contitles. plist** para el proyecto **SoupChef** :
  - Para la funcionalidad de **grupos de aplicaciones** , establezca el grupo en el nuevo grupo de aplicaciones creado anteriormente (en el ejemplo anterior, era **grupo. com. yourcompanyname. SoupChef**).
  - Asegúrese de que **SiriKit** está habilitado.

- Actualice el archivo **contitles. plist** para el proyecto **SoupChefIntents** :
  - Para la funcionalidad de **grupos de aplicaciones** , establezca el grupo en el nuevo grupo de aplicaciones creado anteriormente (en el ejemplo anterior, era **grupo. com. yourcompanyname. SoupChef**).

- Por último, Abra **NSUserDefaultsHelper.CS**. Establezca la `AppGroup` variable en el valor de su nuevo grupo de aplicaciones (por ejemplo, establézcalo en `group.com.yourcompanyname.SoupChef` ).

### <a name="configuring-the-build-settings"></a>Configurar las opciones de compilación

En Visual Studio para Mac o Visual Studio 2017:

- Abra las opciones/propiedades del proyecto **SoupChef** . En la pestaña **firma de lote de iOS** , establezca la identidad de **firma** en automático y el perfil de **aprovisionamiento** en el nuevo perfil de aprovisionamiento específico de la aplicación que creó anteriormente.

- Abra las opciones/propiedades del proyecto **SoupChefIntents** . En la pestaña **firma de lote de iOS** , establezca la identidad de **firma** en automático y el perfil de **aprovisionamiento** en el nuevo perfil de aprovisionamiento específico de la extensión intents que creó anteriormente.

- Abra las opciones/propiedades del proyecto **SoupChefIntentsUI** . En la pestaña **firma de lote de iOS** , establezca la identidad de **firma** en automático y el perfil de **aprovisionamiento** en el nuevo perfil de aprovisionamiento específico de la extensión de la interfaz de usuario de Intent que creó anteriormente.

Una vez realizados estos cambios, la aplicación se ejecutará en un dispositivo iOS.

### <a name="automatic-provisioning"></a>Aprovisionamiento automático

Tenga en cuenta que puede usar el [aprovisionamiento automático](../../get-started/installation/device-provisioning/automatic-provisioning.md) para realizar muchas de estas tareas de aprovisionamiento directamente en el IDE.
Sin embargo, el aprovisionamiento automático no configura grupos de aplicaciones. Tendrá que configurar manualmente los **derechos. archivos plist** con el nombre del grupo de aplicaciones que desea usar, visite el portal para desarrolladores de Apple para crear el grupo de aplicaciones, asigne ese grupo de aplicaciones a cada identificador de aplicación creado por el aprovisionamiento automático, vuelva a generar los perfiles de aprovisionamiento (aplicación, extensión intents, extensión de la interfaz de usuario de intents) para incluir el grupo de aplicaciones recién creado y descargarlos

## <a name="related-links"></a>Vínculos relacionados

- [Chef de sopa (Xamarin)](/samples/xamarin/ios-samples/ios12-soupchef)
- [Chef de sopa (SWIFT)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introducción a los métodos abreviados de Siri: WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Compilación para voz con accesos directos de Siri: WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Métodos abreviados de Siri en la Siri de inspección: WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Novedades de SiriKit: WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Creación de una extensión de aplicación intents (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)