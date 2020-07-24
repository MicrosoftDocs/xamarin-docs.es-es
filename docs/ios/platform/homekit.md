---
title: HomeKit en Xamarin. iOS
description: HomeKit es el marco de trabajo de Apple para controlar los dispositivos de automatización de inicio. En este artículo se presenta HomeKit y se describe la configuración de los accesorios de prueba en el simulador de accesorios de HomeKit y la escritura de una aplicación sencilla de Xamarin. iOS para interactuar con estos accesorios.
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7319cc50a147da93018bdd1c2036d70cf01e9b80
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936687"
---
# <a name="homekit-in-xamarinios"></a>HomeKit en Xamarin. iOS

_HomeKit es el marco de trabajo de Apple para controlar los dispositivos de automatización de inicio. En este artículo se presenta HomeKit y se describe la configuración de los accesorios de prueba en el simulador de accesorios de HomeKit y la escritura de una aplicación sencilla de Xamarin. iOS para interactuar con estos accesorios._

[![Una aplicación habilitada para HomeKit de ejemplo](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

Apple presentó HomeKit en iOS 8 como una manera de integrar sin problemas varios dispositivos de automatización de inicio de una variedad de proveedores en una única unidad coherente. Al promover un protocolo común para detectar, configurar y controlar los dispositivos de automatización principal, HomeKit permite que los dispositivos de proveedores no relacionados trabajen juntos, todo ello sin que los proveedores individuales tengan que coordinar los esfuerzos.

Con HomeKit, puede crear una aplicación de Xamarin. iOS que controle cualquier dispositivo habilitado para HomeKit sin usar las API o aplicaciones proporcionadas por el proveedor. Con HomeKit, puede hacer lo siguiente:

- Descubra los nuevos dispositivos de automatización de inicio habilitados para HomeKit y agréguelos a una base de datos que se conservará en todos los dispositivos iOS del usuario.
- Configurar, configurar, mostrar y controlar cualquier dispositivo en la _base de datos de configuración de inicio_de HomeKit.
- Comuníquese con cualquier dispositivo HomeKit preconfigurado y pídale que realice acciones individuales o trabaje en concierto, como la activación de todas las luces de la cocina.

Además de servir dispositivos en la base de datos de configuración de inicio para aplicaciones habilitadas para HomeKit, HomeKit proporciona acceso a los comandos de Siri Voice. Dada una configuración de HomeKit configurada correctamente, el usuario puede emitir comandos de voz, como "Siri", activar las luces en el salón ".

<a name="Home-Configuration-Database"></a>

## <a name="the-home-configuration-database"></a>La base de datos de configuración principal

HomeKit organiza todos los dispositivos de automatización en una ubicación determinada en una colección de inicio. Esta colección proporciona una manera para que el usuario agrupe sus dispositivos de automatización doméstica en unidades organizadas lógicamente con etiquetas descriptivas y legibles.

La colección Home se almacena en una base de datos de configuración principal de la que se realizará automáticamente una copia de seguridad y se sincronizará en todos los dispositivos iOS del usuario. HomeKit proporciona las siguientes clases para trabajar con la base de datos de configuración de Inicio:

- `HMHome`: Este es el contenedor de nivel superior que contiene toda la información y las configuraciones de todos los dispositivos de automatización doméstica en una única ubicación física (por ejemplo, residencia de una sola familia). El usuario puede tener más de una residencia, como su hogar principal y una casa de vacaciones. O bien, pueden tener diferentes "casas" en la misma propiedad, como la casa principal y una casa de invitados en el garaje. En cualquier caso, se `HMHome` _debe_ configurar al menos un objeto antes de que se pueda escribir cualquier otra información de HomeKit.
- `HMRoom`-Aunque es opcional, `HMRoom` permite al usuario definir salones específicos dentro de un hogar ( `HMHome` ) como cocina, baño, garaje o salón. El usuario puede agrupar todos los dispositivos de automatización doméstica en una ubicación específica de su casa en un `HMRoom` y actuar sobre ellos como una unidad. Por ejemplo, pedir a Siri que desactive las luces del garaje.
- `HMAccessory`: Representa un dispositivo de automatización habilitado para HomeKit físico que se ha instalado en la residencia del usuario (por ejemplo, un termostato inteligente). Cada `HMAccessory` se asigna a un `HMRoom` . Si el usuario no ha configurado ningún salón, HomeKit asigna accesorios a una sala especial predeterminada.
- `HMService`: Representa un servicio proporcionado por un determinado `HMAccessory` , como el estado ON/OFF de una luz o su color (si se admite el cambio de color). Cada `HMAccessory` una de ellas puede tener más de un servicio, como un abierto de puerta de garaje que también incluye una luz. Además, un determinado `HMAccessory` puede tener servicios, como la actualización de firmware, que están fuera del control de usuario.
- `HMZone`: Permite al usuario agrupar una colección de `HMRoom` objetos en zonas lógicas, como planta, downstairs o sótano. Aunque es opcional, esto permite interacciones como preguntar a Siri que convierta toda la luz downstairs desactivada.

<a name="Provisioning-a-HomeKit-App"></a>

## <a name="provisioning-a-homekit-app"></a>Aprovisionamiento de una aplicación de HomeKit

Debido a los requisitos de seguridad impuestos por HomeKit, una aplicación de Xamarin. iOS que usa el marco de HomeKit debe estar configurada correctamente en el portal para desarrolladores de Apple y en el archivo de proyecto de Xamarin. iOS.

Siga estos pasos:

1. Inicie sesión en el [portal para desarrolladores de Apple](https://developer.apple.com).
2. Haga clic en **certificados, identificadores & perfiles**.
3. Si todavía no lo ha hecho, haga clic en **identificadores** y cree un identificador para la aplicación (por ejemplo `com.company.appname` ,). en caso contrario, edite el identificador existente.
4. Asegúrese de que se ha comprobado el servicio **HomeKit** para el identificador dado: 

    [![Habilitar el servicio HomeKit para el identificador especificado](homekit-images/provision01.png)](homekit-images/provision01.png#lightbox)
5. Guarde los cambios.
6. Haga clic en **aprovisionar perfiles**  >  **desarrollo** y cree un nuevo perfil de aprovisionamiento de desarrollo para la aplicación: 

    [![Crear un nuevo perfil de aprovisionamiento de desarrollo para la aplicación](homekit-images/provision02.png)](homekit-images/provision02.png#lightbox)
7. Descargue e instale el nuevo perfil de aprovisionamiento o use Xcode para descargar e instalar el perfil.
8. Edite las opciones del proyecto de Xamarin. iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear: 

    [![Seleccione el perfil de aprovisionamiento que acaba de crear.](homekit-images/provision03.png)](homekit-images/provision03.png#lightbox)
9. Después, edite el archivo **info. plist** y asegúrese de que está usando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento: 

    [![Establecimiento del identificador de la aplicación](homekit-images/provision04.png)](homekit-images/provision04.png#lightbox)
10. Por último, edite el archivo **contitles. plist** y asegúrese de que se ha seleccionado el derecho **HomeKit** : 

    [![Habilitación del derecho HomeKit](homekit-images/provision05.png)](homekit-images/provision05.png#lightbox)
11. Guarde los cambios en todos los archivos.

Con esta configuración en contexto, la aplicación ya está lista para acceder a las API de HomeKit Framework. Para obtener información detallada sobre el aprovisionamiento, consulte las guías [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) y aprovisionamiento [de aplicaciones](~/ios/get-started/installation/device-provisioning/index.md) .

> [!IMPORTANT]
> La prueba de una aplicación habilitada para HomeKit requiere un dispositivo iOS real que se ha aprovisionado correctamente para el desarrollo. HomeKit no se puede probar desde el simulador de iOS.

## <a name="the-homekit-accessory-simulator"></a>El simulador accesorio HomeKit

Para proporcionar una manera de probar todos los servicios y dispositivos de automatización de inicio posibles, sin tener que tener un dispositivo físico, Apple creó el _simulador accesorio HomeKit_. Con este simulador, puede instalar y configurar dispositivos HomeKit virtuales.

### <a name="installing-the-simulator"></a>Instalación del simulador

Apple proporciona el simulador accesorio HomeKit como una descarga independiente de Xcode, por lo que tendrá que instalarlo antes de continuar.

Siga estos pasos:

1. En un explorador Web, visite [descargas para desarrolladores de Apple](https://developer.apple.com/download/more/?name=for%20Xcode) .
2. Descargue las **herramientas adicionales para Xcode XXX** (donde xxx es la versión de Xcode que ha instalado): 

    [![Descarga de las herramientas adicionales para Xcode](homekit-images/simulator01.png)](homekit-images/simulator01.png#lightbox)
3. Abra la imagen de disco e instale las herramientas en el directorio de **aplicaciones** .

Con el simulador de accesorio de HomeKit instalado, se pueden crear accesorios virtuales para realizar pruebas.

### <a name="creating-virtual-accessories"></a>Creación de accesorios virtuales

Para iniciar el simulador de accesorios de HomeKit y crear algunos accesorios virtuales, haga lo siguiente:

1. En la carpeta Aplicaciones, inicie el simulador accesorio HomeKit: 

    [![El simulador accesorio HomeKit](homekit-images/simulator02.png)](homekit-images/simulator02.png#lightbox)
2. Haga clic en el **+** botón y seleccione **nuevo accesorio...**: 

    [![Agregar un nuevo accesorio](homekit-images/simulator03.png)](homekit-images/simulator03.png#lightbox)
3. Rellene la información sobre el nuevo accesorio y haga clic en el botón **Finalizar** : 

    [![Rellene la información sobre el nuevo accesorio.](homekit-images/simulator04.png)](homekit-images/simulator04.png#lightbox)
4. Haga clic en **Agregar servicio.** y seleccione un tipo de servicio en la lista desplegable: 

    [![Seleccionar un tipo de servicio en la lista desplegable](homekit-images/simulator05.png)](homekit-images/simulator05.png#lightbox)
5. Proporcione un **nombre** para el servicio y haga clic en el botón **Finalizar** : 

    [![Escriba un nombre para el servicio](homekit-images/simulator06.png)](homekit-images/simulator06.png#lightbox)
6. Puede proporcionar características opcionales para un servicio haciendo clic en el botón **Agregar característica** y configurando las opciones necesarias: 

    [![Configuración de los valores obligatorios](homekit-images/simulator07.png)](homekit-images/simulator07.png#lightbox)
7. Repita los pasos anteriores para crear uno de cada tipo de dispositivo de automatización de inicio virtual que admite HomeKit.

Con algunos accesorios de HomeKit virtuales de ejemplo creados y configurados, ahora puede consumir y controlar estos dispositivos desde la aplicación de Xamarin. iOS.

## <a name="configuring-the-infoplist-file"></a>Configuración del archivo info. plist

Novedad de iOS 10 (y versiones posteriores), el desarrollador tendrá que agregar la `NSHomeKitUsageDescription` clave al archivo de la aplicación `Info.plist` y proporcionar una cadena que declare por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario. Esta cadena se presentará al usuario la primera vez que ejecute la aplicación:

[![Cuadro de diálogo de permiso HomeKit](homekit-images/info01.png)](homekit-images/info01.png#lightbox)

Para establecer esta clave, haga lo siguiente:

1. Haga doble clic en el `Info.plist` archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. En la parte inferior de la pantalla, cambie a la vista **código fuente** .
3. Agregar una nueva **entrada** a la lista.
4. En la lista desplegable, seleccione **privacidad-HomeKit uso Descripción**: 

    [![Seleccionar privacidad: Descripción de uso de HomeKit](homekit-images/info02.png)](homekit-images/info02.png#lightbox)
5. Escriba una descripción de por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario: 

    [![Escriba una descripción](homekit-images/info03.png)](homekit-images/info03.png#lightbox)
6. Guarde los cambios en el archivo.

> [!IMPORTANT]
> Si no se establece la `NSHomeKitUsageDescription` clave en el `Info.plist` archivo se producirá un error en la aplicación en _modo silencioso_ (cerrado por el sistema en tiempo de ejecución) sin errores cuando se ejecute en iOS 10 (o superior).

## <a name="connecting-to-homekit"></a>Conexión a HomeKit

Para comunicarse con HomeKit, la aplicación de Xamarin. iOS debe crear primero una instancia de la `HMHomeManager` clase. Home Manager es el punto de entrada central en HomeKit y es responsable de proporcionar una lista de los hogares disponibles, la actualización y el mantenimiento de esa lista y la devolución del _Inicio principal_del usuario.

El `HMHome` objeto contiene toda la información acerca de un proporcionar a la casa, incluidos los salones, grupos o zonas que puede contener, junto con los accesorios de automatización de inicio que se hayan instalado. Antes de que se puedan realizar operaciones en HomeKit, `HMHome` se debe crear al menos uno y asignarlo como principal principal.

La aplicación es responsable de comprobar si existe una principal principal y de crear y asignar una si no es así.

### <a name="adding-a-home-manager"></a>Agregar un administrador de inicio

Para agregar el reconocimiento de HomeKit a una aplicación de Xamarin. iOS, edite el archivo **AppDelegate.CS** para editarlo y haga que tenga un aspecto similar al siguiente:

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

Cuando se ejecute la aplicación por primera vez, se le preguntará al usuario si desea permitirle acceder a su información de HomeKit:

[![Se le preguntará al usuario si desea permitirle acceder a su información de HomeKit](homekit-images/home01.png)](homekit-images/home01.png#lightbox)

Si el usuario responde **correctamente**, la aplicación podrá trabajar con sus accesorios de HomeKit; de lo contrario, no se realizará ninguna llamada a HomeKit y se producirá un error.

Con el administrador principal en su lugar, la aplicación deberá ver si se ha configurado una página principal principal y, si no es así, proporcionar una manera para que el usuario cree y asigne una.

### <a name="accessing-the-primary-home"></a>Obtener acceso a la Página principal principal

Como se indicó anteriormente, debe crearse y configurarse una página principal principal antes de que HomeKit esté disponible y es responsabilidad de la aplicación proporcionar una manera para que el usuario cree y asigne una página principal principal si aún no existe una.

Cuando la aplicación se inicia por primera vez o vuelve desde el fondo, debe supervisar el `DidUpdateHomes` evento de la `HMHomeManager` clase para comprobar la existencia de una página principal principal. Si no existe ninguna, debe proporcionar una interfaz para que el usuario cree una.

Se puede Agregar el código siguiente a un controlador de vista para comprobar la Página principal principal:

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

Cuando el administrador de inicios realiza una conexión a HomeKit, se `DidUpdateHomes` desencadena el evento, se cargarán todos los hogares existentes en la colección de hogares del administrador y se cargará la Página principal principal, si está disponible.

### <a name="adding-a-primary-home"></a>Agregar una página principal principal

Si la `PrimaryHome` propiedad de `HMHomeManager` se encuentra `null` después de un `DidUpdateHomes` evento, debe proporcionar una manera para que el usuario cree y asigne una página principal principal antes de continuar.

Normalmente, la aplicación presentará un formulario para que el usuario asigne un nombre a una nueva página principal que, a continuación, se pasa al administrador principal para configurar como principal. En el caso de la aplicación de ejemplo **HomeKitIntro** , se creó una vista modal en el diseñador de iOS y la llamó `AddHomeSegue` segue desde la interfaz principal de la aplicación.

Proporciona un campo de texto para que el usuario escriba un nombre para la nueva página principal y un botón para agregar la Página principal. Cuando el usuario pulsa el botón **Agregar Inicio** , el código siguiente llama al administrador principal para agregar el inicio:

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

El `AddHome` método intentará crear un nuevo inicio y devolverlo a la rutina de devolución de llamada especificada. Si la `error` propiedad no es `null` , se ha producido un error y se debe presentar al usuario. Los errores más comunes se deben a que el nombre de casa no es único o que el administrador de inicio no puede comunicarse con HomeKit.

Si la Página principal se creó correctamente, debe llamar al `UpdatePrimaryHome` método para establecer el nuevo inicio como principal. De nuevo, si la `error` propiedad no es `null` , se ha producido un error y se debe presentar al usuario.

También debe supervisar los eventos y del administrador `DidAddHome` principal `DidRemoveHome` y actualizar la interfaz de usuario de la aplicación según sea necesario.

> [!IMPORTANT]
> El `AlertView.PresentOKAlert` método que se usa en el código de ejemplo anterior es una clase auxiliar en la aplicación HomeKitIntro que facilita el trabajo con las alertas de iOS.

## <a name="finding-new-accessories"></a>Buscar nuevos accesorios

Una vez que se ha definido o cargado una página principal principal desde el administrador de inicio, la aplicación de Xamarin. iOS puede llamar `HMAccessoryBrowser` a para buscar cualquier nuevo accesorio de automatización de inicio y agregarlo a un hogar.

Llame al `StartSearchingForNewAccessories` método para empezar a buscar nuevos accesorios y el `StopSearchingForNewAccessories` método cuando termine.

> [!IMPORTANT]
> `StartSearchingForNewAccessories`no debe dejarse en ejecución durante largos períodos de tiempo, ya que afectará negativamente a la duración de la batería y al rendimiento del dispositivo iOS. Apple sugiere llamar `StopSearchingForNewAccessories` después de un minuto o buscar solo cuando se presenta al usuario la interfaz de usuario del accesorio de búsqueda.

Se `DidFindNewAccessory` llamará al evento cuando se detecten nuevos accesorios y se agregarán a la `DiscoveredAccessories` lista en el explorador de accesorios.

La `DiscoveredAccessories` lista contendrá una colección de `HMAccessory` objetos que definen un dispositivo de automatización de inicio con HomeKit habilitado y sus servicios disponibles, como luces o control de puerta de garaje.

Una vez que se ha encontrado el accesorio nuevo, debe presentarse al usuario y, por tanto, puede seleccionarlo y agregarlo a un hogar. Ejemplo:

[![Búsqueda de un nuevo accesorio](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

Llame al `AddAccessory` método para agregar el accesorio seleccionado a la colección de la Página principal. Por ejemplo:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

Si la `err` propiedad no es `null` , se ha producido un error y se debe presentar al usuario. De lo contrario, se le pedirá al usuario que escriba el código de instalación del dispositivo que se va a agregar:

[![Escriba el código de configuración para el dispositivo que se va a agregar](homekit-images/accessory02.png)](homekit-images/accessory02.png#lightbox)

En el simulador accesorio HomeKit, este número se puede encontrar en el campo Código de la **instalación** :

[![El campo de código de instalación del simulador accesorio HomeKit](homekit-images/accessory03.png)](homekit-images/accessory03.png#lightbox)

En el caso de los accesorios HomeKit reales, el código de instalación se imprimirá en una etiqueta en el propio dispositivo, en el cuadro Product o en el manual del usuario del accesorio.

Debe supervisar el evento del explorador de accesorios `DidRemoveNewAccessory` y actualizar la interfaz de usuario para quitar un accesorio de la lista disponible una vez que el usuario lo haya agregado a su colección principal.

## <a name="working-with-accessories"></a>Trabajar con accesorios

Una vez que se ha establecido un inicio principal y se han agregado accesorios, puede presentar una lista de accesorios (y, opcionalmente, salones) para que el usuario pueda trabajar con ellos.

El `HMRoom` objeto contiene toda la información sobre un salón determinado y cualquier accesorio que pertenezca a él. Los salones se pueden organizar opcionalmente en una o varias zonas. `HMZone`Contiene toda la información sobre una zona determinada y todas las salas que pertenecen a ella.

En este ejemplo, haremos todo lo posible y trabajamos directamente con los accesorios de la casa, en lugar de organizarlos en salas o zonas.

El `HMHome` objeto contiene una lista de accesorios asignados que se pueden presentar al usuario en su `Accessories` propiedad. Por ejemplo:

[![Un accesorio de ejemplo](homekit-images/accessory04.png)](homekit-images/accessory04.png#lightbox)

Aquí, el usuario puede seleccionar un accesorio determinado y trabajar con los servicios que proporciona.

## <a name="working-with-services"></a>Trabajar con servicios

Cuando el usuario interactúa con un determinado dispositivo de automatización de inicio habilitado para HomeKit, suele ser a través de los servicios que proporciona. La `Services` propiedad de la `HMAccessory` clase contiene una colección de `HMService` objetos que definen los servicios que ofrece un dispositivo.

Los servicios son aspectos como luces, termostatos, aperturas de puertas de garaje, interruptores o bloqueos. Algunos dispositivos (por ejemplo, un abridor de puerta de garaje) proporcionarán más de un servicio, como una luz y la capacidad de abrir o cerrar una puerta.

Además de los servicios específicos que proporciona un accesorio determinado, cada accesorio contiene un `Information Service` que define propiedades como el nombre, el fabricante, el modelo y el número de serie.

### <a name="accessory-service-types"></a>Tipos de servicio de accesorios

Los siguientes tipos de servicio están disponibles a través de la `HMServiceType` enumeración:

- **AccessoryInformation** : proporciona información sobre el dispositivo de automatización de inicio (accesorio) especificado.
- **AirQualitySensor** : define un sensor de calidad de aire.
- **Batería** : define el estado de la batería de un accesorio.
- **CarbonDioxideSensor** : define un sensor de dióxido de carbono.
- **CarbonMonoxideSensor** : define un sensor de monóxido de carbono.
- **ContactSensor** : define un sensor de contacto (por ejemplo, una ventana que se abre o se cierra).
- **Puerta** : define el sensor del estado de una puerta (por ejemplo, abierto o cerrado).
- **Fan** : define un ventilador controlado remoto.
- **GarageDoorOpener** : define un abridor de puerta de garaje.
- **HumiditySensor** : define un sensor de humedad.
- **LeakSensor** : define un sensor de fuga (por ejemplo, para un calefactor de agua caliente o una lavadora).
- **Bombilla** : define una luz independiente o una luz que forma parte de otro accesorio (por ejemplo, un abridor de la puerta del garaje).
- **LightSensor** : define un sensor ligero.
- **LockManagement** : define un servicio que administra un bloqueo de puerta automatizado.
- **LockMechanism** : define un bloqueo controlado remoto (como un bloqueo de puerta).
- **MotionSensor** : define un sensor de movimiento.
- **OccupancySensor** : define un sensor de ocupación.
- **Salida** : define un tomacorriente de pared controlado remoto.
- **SecuritySystem** : define un sistema de seguridad doméstica.
- **StatefulProgrammableSwitch** : define un modificador programable que permanece en un estado de dar una vez que se desencadena (como un modificador Flip).
- **StatelessProgrammableSwitch** : define un modificador programable que vuelve a su estado inicial después de desencadenarse (como un botón de opción).
- **SmokeSensor** : define un detector de humo.
- **Conmutador** : define un conmutador de activado/desactivado como un conmutador de pared estándar.
- **TemperatureSensor** : define un sensor de temperatura.
- **Termostato** : define un termostato inteligente que se usa para controlar un sistema HVAC.
- **Window** : define una ventana automatizada que se abre o se cierra de forma remota.
- **WindowCovering** : define una ventana controlada de forma remota, como persianas que se pueden abrir o cerrar.

### <a name="displaying-service-information"></a>Mostrar información de servicio

Después de cargar `HMAccessory` , puede consultar los objetos individuales `HNService` que proporciona y mostrar esa información al usuario:

[![Mostrar información de servicio](homekit-images/accessory05.png)](homekit-images/accessory05.png#lightbox)

Siempre debe comprobar la `Reachable` propiedad de un `HMAccessory` antes de intentar trabajar con él. No se puede tener acceso a un accesorio si el usuario no está dentro del alcance del dispositivo o si se ha desconectado.

Una vez que se ha seleccionado un servicio, el usuario puede ver o modificar una o más características de ese servicio para supervisar o controlar un determinado dispositivo de automatización de inicio.

<a name="Working-with-Characteristics"></a>

## <a name="working-with-characteristics"></a>Trabajar con características

Cada `HMService` objeto puede contener una colección de `HMCharacteristic` objetos que pueden proporcionar información sobre el estado del servicio (por ejemplo, una puerta abierta o cerrada) o permitir que el usuario ajuste un estado (por ejemplo, establecer el color de una luz).

`HMCharacteristic`no solo proporciona información sobre una característica y su estado, sino que también proporciona métodos para trabajar con el estado a través de _metadatos característicos_ ( `HMCharacteristisMetadata` ). Estos metadatos pueden proporcionar propiedades (como los intervalos de valores mínimo y máximo) que son útiles cuando se muestra información al usuario o se les permite modificar los Estados.

La `HMCharacteristicType` enumeración proporciona un conjunto de valores de metadatos característicos que se pueden definir o modificar de la siguiente manera:

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- Calidad de la compra
- AudioFeedback
- BatteryLevel
- Brillo
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- ChargingState
- ContactState
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- Matiz
- Identificar
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- Registros
- Fabricante
- Modelo
- MotionDetected
- Nombre
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- Saturación
- SerialNumber
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- Versión

### <a name="working-with-a-characteristics-value"></a>Trabajar con el valor de una característica

Para asegurarse de que la aplicación tiene el estado más reciente de una característica determinada, llame al `ReadValue` método de la `HMCharacteristic` clase. Si la `err` propiedad no es `null` , se ha producido un error y puede o no presentarse al usuario.

La propiedad de la característica `Value` contiene el estado actual de la característica determinada como y `NSObject` , como tal, no puede trabajar directamente en C#.

Para leer el valor, se ha agregado la siguiente clase auxiliar a la aplicación de ejemplo **HomeKitIntro** :

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter`Se utiliza cuando la aplicación necesita leer el estado actual de una característica. Por ejemplo:

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

La línea anterior convierte el valor en un `float` que se puede usar en el código de Xamarin C#.

Para modificar un `HMCharacteristic` , llame a su `WriteValue` método y ajuste el nuevo valor en una `NSObject.FromObject` llamada. Por ejemplo:

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

Si la `err` propiedad no es `null` , se ha producido un error y se debe presentar al usuario.

### <a name="testing-characteristic-value-changes"></a>Probar cambios de valores característicos

Al trabajar con los `HMCharacteristics` accesorios de y simulados, las modificaciones en la `Value` propiedad se pueden supervisar dentro del simulador de accesorios de HomeKit.

Con la aplicación **HomeKitIntro** que se ejecuta en un hardware de dispositivo iOS real, los cambios en el valor de una característica deben aparecer de forma casi instantánea en el simulador de accesorios de HomeKit. Por ejemplo, al cambiar el estado de una luz en la aplicación iOS:

[![Cambiar el estado de una luz en una aplicación iOS](homekit-images/test01.png)](homekit-images/test01.png#lightbox)

Debe cambiar el estado de la luz en el simulador accesorio HomeKit. Si el valor no cambia, compruebe el estado del mensaje de error al escribir nuevos valores característicos y asegúrese de que el accesorio todavía está accesible.

## <a name="advanced-homekit-features"></a>Características avanzadas de HomeKit

En este artículo se han tratado las características básicas necesarias para trabajar con accesorios de HomeKit en una aplicación de Xamarin. iOS. Sin embargo, hay varias características avanzadas de HomeKit que no se describen en esta introducción:

- **Salones** : los accesorios habilitados para HomeKit pueden organizarse opcionalmente en salones por parte del usuario final. Esto permite a HomeKit presentar los accesorios de una manera que sea fácil de entender y trabajar con. Para obtener más información sobre la creación y el mantenimiento de salones, consulte la documentación de [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) de Apple.
- **Zonas** : los salones pueden organizarse opcionalmente en zonas por el usuario final. Una zona hace referencia a una colección de salones que el usuario podría tratar como una sola unidad. Por ejemplo: planta arriba, downstairs o sótano. De nuevo, esto permite que HomeKit presente y trabaje con los accesorios de una forma que tenga sentido para el usuario final. Para obtener más información sobre la creación y el mantenimiento de zonas, consulte la documentación de [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) de Apple.
- **Acciones y conjuntos** de acciones: las acciones modifican las características del servicio de accesorios y se pueden agrupar en conjuntos. Los conjuntos de acciones actúan como scripts para controlar un grupo de accesorios y coordinar sus acciones. Por ejemplo, un script "Watch TV" podría cerrar las persianas, atenuar las luces y encender el televisor y su sistema de sonido. Para obtener más información sobre la creación y el mantenimiento de acciones y conjuntos de acciones, consulte la documentación de [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) y [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) de Apple.
- **Desencadenadores** : un desencadenador puede activar uno o más conjuntos de acciones cuando se cumple un conjunto determinado de condiciones. Por ejemplo, active la luz portch y bloquee todas las puertas externas cuando se ilumina fuera. Para obtener más información sobre la creación y el mantenimiento de los desencadenadores, vea la documentación de [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) de Apple.

Dado que estas características usan las mismas técnicas descritas anteriormente, deben ser fáciles de implementar mediante la [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)de Apple, las directrices de la interfaz de [usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/) y la referencia de [marco de HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref).

## <a name="homekit-app-review-guidelines"></a>Instrucciones de revisión de la aplicación HomeKit

Antes de enviar una aplicación de Xamarin. iOS con HomeKit habilitada a iTunes Connect para su lanzamiento en iTunes App Store, asegúrese de seguir las directrices de Apple para las aplicaciones habilitadas para HomeKit:

- El propósito principal de la aplicación _debe_ ser la automatización doméstica si se usa el marco HomeKit.
- El texto de marketing de la aplicación debe notificar a los usuarios que se está usando HomeKit y deben proporcionar una directiva de privacidad.
- La recopilación de información de usuario o el uso de HomeKit para publicidad está estrictamente prohibida.

Para ver las instrucciones de revisión completa, consulte las [directrices de revisión de App Store](https://developer.apple.com/app-store/review/guidelines/)de Apple.

## <a name="whats-new-in-ios-9"></a>Novedades de iOS 9

Apple ha realizado los siguientes cambios y adiciones a HomeKit para iOS 9:

- **Mantenimiento de los objetos existentes** : si se modifica un accesorio existente, el administrador `HMHomeManager` de inicio () le informará del elemento específico que se ha modificado.
- **Identificadores persistentes** : todas las clases de HomeKit pertinentes incluyen ahora una `UniqueIdentifier` propiedad para identificar de forma única un elemento determinado en las aplicaciones habilitadas para HomeKit (o las instancias de la misma aplicación).
- **Administración de usuarios** : se ha agregado un controlador de vista integrado para proporcionar administración de usuarios a los usuarios que tienen acceso a los dispositivos HomeKit en el hogar del usuario principal.
- **Funcionalidades de usuario** : ahora, los usuarios de HomeKit tienen un conjunto de privilegios que controlan las funciones que pueden usar en los accesorios habilitados para HomeKit y HomeKit. La aplicación solo debe mostrar las funciones relevantes para el usuario actual. Por ejemplo, solo los administradores deben ser capaces de mantener a otros usuarios.
- Las escenas predefinidas de **escenas** predefinidas se han creado para cuatro eventos comunes que se producen para el usuario de HomeKit medio: Get up, Leave, Return, Go to lecho. Estas escenas predefinidas no se pueden eliminar de un hogar.
- **Scenes y Siri** -Siri tiene una compatibilidad más profunda para escenas en iOS 9 y puede reconocer el nombre de cualquier escena definida en HomeKit. Un usuario puede ejecutar una escena simplemente hablando su nombre en Siri.
- **Categorías de accesorios** : se ha agregado un conjunto de categorías predefinidas a todos los accesorios y ayuda a identificar el tipo de accesorio que se va a agregar a un hogar o a trabajar desde dentro de la aplicación. Estas nuevas categorías están disponibles durante la instalación del accesorio.
- **Apple Watch support** -HomeKit está ahora disponible para watchos y el Apple Watch podrá controlar los dispositivos habilitados para HomeKit sin que un iPhone esté cerca de la inspección. HomeKit para watchos admite las siguientes funcionalidades: ver casas, controlar accesorios y ejecutar escenas.
- **Nuevo tipo de desencadenador de evento** : además de los desencadenadores de tipo de temporizador admitidos en iOS 8, iOS 9 admite ahora desencadenadores de eventos basados en el estado del accesorio (como los datos del sensor) o la ubicación geográfica. Los desencadenadores de eventos usan `NSPredicates` para establecer las condiciones para su ejecución.
- **Acceso remoto** : con acceso remoto, el usuario ahora puede controlar sus accesorios de automatización doméstica habilitados para HomeKit cuando están lejos de la casa en una ubicación remota. En iOS 8 esto solo se admitía si el usuario tuviera una tercera generación de TV de Apple en casa. En iOS 9, esta limitación se eleva y el acceso remoto se admite a través de iCloud y el protocolo de accesorio de HomeKit (HAP).
- **Nuevas capacidades de Bluetooth de baja energía (ble)** : HomeKit admite ahora más tipos de accesorio que pueden comunicarse a través del protocolo Bluetooth de baja energía (ble). Mediante el uso de la tunelización segura de HAP, un accesorio de HomeKit puede exponer otro accesorio Bluetooth a través de Wi-Fi (si está fuera del intervalo de Bluetooth). En iOS 9, BLE los accesorios tienen compatibilidad total con las notificaciones y los metadatos.
- **Nuevas categorías de accesorio** : Apple ha agregado las siguientes categorías de accesorio nuevas en iOS 9: cubiertas de ventanas, puertas motorizadas y ventanas, sistemas de alarmas, sensores y conmutadores programables.

Para obtener más información sobre las nuevas características de HomeKit en iOS 9, consulte el [Índice de HomeKit](https://developer.apple.com/homekit/) de Apple y las novedades [de HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210) video.

## <a name="summary"></a>Resumen

En este artículo se ha introducido HomeKit Home Automation Framework de Apple. Se ha mostrado cómo configurar y configurar dispositivos de prueba mediante el simulador de accesorios de HomeKit y cómo crear una aplicación sencilla de Xamarin. iOS para detectar, comunicarse y controlar dispositivos de automatización de la casa con HomeKit.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de la interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
