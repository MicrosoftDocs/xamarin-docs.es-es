---
title: HealthKit en Xamarin. iOS
description: En este documento se describe HealthKit, un marco de trabajo incluido en iOS 8 que proporciona un almacén de datos centralizado, coordinado y seguro para información relacionada con el estado. En él se explica cómo aprovisionar una aplicación de HealthKit y cómo escribir código que usa el marco de HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: eb944b062f75ceec8ca8dbe22cde64b0fdd15625
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752954"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit en Xamarin. iOS

El kit de mantenimiento proporciona un almacén de datos seguro para la información relacionada con el estado del usuario. Las aplicaciones del kit de mantenimiento pueden, con el permiso explícito del usuario, leer y escribir en este almacén de datos y recibir notificaciones cuando se agregan los datos pertinentes. Las aplicaciones pueden presentar los datos o el usuario puede usar la aplicación de mantenimiento proporcionada por Apple para ver un panel de todos sus datos.

Dado que los datos relacionados con el estado son tan confidenciales y fundamentales, el kit de mantenimiento está fuertemente tipado, con unidades de medida y una asociación explícita con el tipo de información que se registra (por ejemplo, el nivel de glucosa de la sangre o la tarifa cardíaca). Además, las aplicaciones del kit de mantenimiento deben usar derechos explícitos, deben solicitar acceso a los tipos concretos de información y el usuario debe conceder explícitamente a la aplicación acceso a esos tipos de datos.

En este artículo se presenta:

- Requisitos de seguridad del kit de mantenimiento, incluido el aprovisionamiento de aplicaciones y la solicitud de permiso de usuario para acceder a la base de datos del kit de mantenimiento;
- Sistema de tipos del kit de mantenimiento, lo que minimiza la posibilidad de que se apliquen o malinterpreten los datos.
- Escribir en el almacén de almacenes de Estados de todo el sistema compartido.

En este artículo no se abordarán temas más avanzados, como la consulta de la base de datos, la conversión entre unidades de medida o la recepción de notificaciones de datos nuevos.

En este artículo, se creará una aplicación de ejemplo para registrar la tarifa cardíaca del usuario:

[![](healthkit-images/image01.png "Una aplicación de ejemplo para registrar la tasa de corazón de los usuarios")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Requisitos

Para completar los pasos que se describen en este artículo, es necesario lo siguiente:

- **Xcode 7 e iOS 8 (o superior)** : las API de Xcode y iOS más recientes de Apple deben instalarse y configurarse en el equipo del desarrollador.
- **Visual Studio para Mac o Visual Studio** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el equipo del desarrollador.
- **dispositivo iOS 8 (o superior)** : dispositivo iOS que ejecuta la versión más reciente de iOS 8 o superior para realizar pruebas.

> [!IMPORTANT]
> El kit de mantenimiento se presentó en iOS 8. Actualmente, el kit de mantenimiento no está disponible en el simulador de iOS y la depuración requiere conexión a un dispositivo iOS físico.

## <a name="creating-and-provisioning-a-health-kit-app"></a>Crear y aprovisionar una aplicación del kit de mantenimiento
Antes de que una aplicación de Xamarin iOS 8 pueda usar la API de HealthKit, debe estar correctamente configurada y aprovisionada. En esta sección se explican los pasos necesarios para configurar correctamente la aplicación de Xamarin.

Las aplicaciones del kit de mantenimiento requieren:

- Un **identificador de aplicación**explícito.
- Un **Perfil de aprovisionamiento** asociado a ese ID. de **aplicación** explícito y con permisos del **Kit de mantenimiento** .
- Con una `com.apple.developer.healthkit` propiedad de tipo `Boolean` establecida en `Yes`. `Entitlements.plist`
- Cuya clave contiene una entrada con el `String` valor `healthkit`. `Info.plist` `UIRequiredDeviceCapabilities`
- `NSHealthShareUsageDescription` `String` `NSHealthUpdateUsageDescription` `String` También debe tener las entradas adecuadas de la explicación de la privacidad: una explicación de la clave si la aplicación va a escribir datos y una explicación de la clave si la aplicación va a leer el kit de mantenimiento. `Info.plist` Data.

Para obtener más información sobre el aprovisionamiento de una aplicación de iOS, en el artículo [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) de la serie de **Introducción** de Xamarin se describe la relación entre los certificados de desarrollador, los ID. de aplicación, los perfiles de aprovisionamiento y los derechos de la aplicación.

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>IDENTIFICADOR de aplicación explícito y Perfil de aprovisionamiento

La creación de un **identificador de aplicación** explícito y un **Perfil de aprovisionamiento** adecuado se realiza en el centro de desarrollo de [iOS](https://developer.apple.com/devcenter/ios/index.action)de Apple. 

Los **identificadores de aplicación** actuales aparecen en la sección [certificados, identificadores & perfiles](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) del centro de desarrollo. A menudo, esta lista muestra los valores de `*`identificador de, que indican que el**nombre** del **identificador** - de aplicación se puede usar con cualquier número de sufijos. Estos *identificadores de aplicación comodín* no se pueden usar con el kit de mantenimiento.

Para crear un **identificador de aplicación**explícito, haga **+** clic en el botón situado en la parte superior derecha para pasar a la página **registrar el ID. de aplicación de iOS** :

[![](healthkit-images/image02.png "Registro de una aplicación en el portal para desarrolladores de Apple")](healthkit-images/image02.png#lightbox)

Como se muestra en la imagen anterior, después de crear una descripción de la aplicación, use la sección de ID. de **aplicación explícito** para crear un identificador para la aplicación. En la sección **App Services** , active el **Kit de estado** en la sección **habilitar servicios** .

Cuando haya terminado, haga clic en el botón **Continue (continuar** ) para registrar el identificador de la **aplicación** en su cuenta. Se le devolverá a la página **certificados, identificadores y perfiles** . Haga clic en **perfiles de aprovisionamiento** para pasar a la lista de perfiles de aprovisionamiento actuales y haga clic en el **+** botón situado en la esquina superior derecha para pasar a la página **Agregar Perfil de aprovisionamiento de iOS** . Seleccione la opción **desarrollo de aplicaciones de iOS** y haga clic en **continuar** para acceder a la página **seleccionar ID** . de aplicación. Aquí, seleccione el **identificador de aplicación** explícito que especificó anteriormente:

[![](healthkit-images/image03.png "Seleccionar el identificador de aplicación explícito")](healthkit-images/image03.png#lightbox)

Haga clic en **continuar** y trabaje a través de las pantallas restantes, donde se especificarán los **certificados de desarrollador**, los **dispositivos**y un **nombre** para este **Perfil de aprovisionamiento**:

[![](healthkit-images/image04.png "Generación del perfil de aprovisionamiento")](healthkit-images/image04.png#lightbox)

Haga clic en **generar** y espere a que se cree el perfil. Descargue el archivo y haga doble clic en él para instalarlo en Xcode. Puede confirmar su instalación en **Xcode > preferencias > cuentas > ver detalles..** . Debería ver el perfil de aprovisionamiento recién instalado y debe tener el icono del kit de mantenimiento y cualquier otro servicio especial en su fila **derechos** :

[![](healthkit-images/image05.png "Visualización del perfil en Xcode")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Asociar el identificador de aplicación y el perfil de aprovisionamiento a la aplicación de Xamarin. iOS

Una vez que haya creado e instalado un **Perfil de aprovisionamiento** adecuado, tal y como se describe, normalmente sería el momento de crear una solución en Visual Studio para Mac o Visual Studio. El acceso al kit de mantenimiento está disponible C# para F# cualquier iOS o proyecto.

En lugar de recorrer el proceso de creación de un proyecto de Xamarin iOS 8, abra la aplicación de ejemplo adjunta a este artículo (que incluye un guión gráfico y un código precompilados). Para asociar la aplicación de ejemplo con el **Perfil de aprovisionamiento**habilitado del kit de mantenimiento, en el **Panel de solución**, haga clic con el botón derecho en el proyecto y abra el cuadro de diálogo **Opciones** . Cambie al panel de la **aplicación iOS** y escriba el **identificador de aplicación** explícito que creó anteriormente como **identificador de lote**de la aplicación:

[![](healthkit-images/image06.png "Escriba el identificador de aplicación explícito")](healthkit-images/image06.png#lightbox)

Ahora, cambie al panel de **firma de lote de iOS** . El **Perfil de aprovisionamiento**instalado recientemente, con su asociación al **identificador de aplicación**explícito, ahora estará disponible como el perfil de **aprovisionamiento**:

[![](healthkit-images/image07.png "Seleccionar el perfil de aprovisionamiento")](healthkit-images/image07.png#lightbox)

Si el **Perfil de aprovisionamiento** no está disponible, compruebe el identificador de la **agrupación** en el panel de la **aplicación iOS** en comparación con el especificado en el **centro de desarrollo de iOS** y que el perfil de **aprovisionamiento** está instalado (**Xcode > Preferencias > cuentas > ver detalles...** ).

Cuando esté seleccionado el perfil de **aprovisionamiento** habilitado para el kit de mantenimiento, haga clic en **Aceptar** para cerrar el cuadro de diálogo Opciones del proyecto.

### <a name="entitlementsplist-and-infoplist-values"></a>Los valores de contitles. plist y info. plist

La aplicación de ejemplo incluye `Entitlements.plist` un archivo (que es necesario para las aplicaciones habilitadas para el kit de mantenimiento) y no se incluye en todas las plantillas de proyecto. Si el proyecto no incluye derechos, haga clic con el botón derecho en el proyecto, seleccione **archivo > nuevo archivo... > iOS > contitles. plist** para agregar uno manualmente.

En última instancia, `Entitlements.plist` debe tener el siguiente par de clave y valor:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

De forma similar `Info.plist` , para la aplicación debe tener un valor `healthkit` de asociado a `UIRequiredDeviceCapabilities` la clave:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

La aplicación de ejemplo `Entitlements.plist` que se proporciona con este artículo incluye una preconfigurada que incluye todas las claves necesarias.

<a name="programming" />

## <a name="programming-health-kit"></a>Kit de mantenimiento de programación

El almacén de información del kit de mantenimiento es un almacén de información privado específico del usuario que se comparte entre las aplicaciones. Dado que la información de estado es tan confidencial, el usuario debe seguir los pasos positivos para permitir el acceso a los datos. Este acceso puede ser parcial (escritura, pero no lectura, acceso a algunos tipos de datos, pero no a otros) y se puede revocar en cualquier momento. Las aplicaciones del kit de mantenimiento deben escribirse de forma defensiva, sabiendo que muchos usuarios tendrán dudas sobre el almacenamiento de la información relacionada con el estado.

Los datos del kit de mantenimiento se limitan a los tipos especificados de Apple. Estos tipos están estrictamente definidos: algunos, como el tipo de sangre, se limitan a los valores concretos de una enumeración proporcionada por Apple, mientras que otros combinan una magnitud con una unidad de medida (como gramos, calorías y litros). Incluso los datos que comparten una unidad de medida compatible se distinguen `HKObjectType`por su; por ejemplo, el sistema de tipos detectará un intento erróneo `HKQuantityTypeIdentifier.NumberOfTimesFallen` de almacenar un valor en un `HKQuantityTypeIdentifier.FlightsClimbed` campo que espera un aunque `HKUnit.Count` use el unidad de medida.

Los tipos pueda almacenar en el almacén de almacenamiento de los kits de mantenimiento son todas `HKObjectType`las subclases de. `HKCharacteristicType`los objetos almacenan sexo biológico, tipo de sangre y fecha de nacimiento. Sin embargo, es `HKSampleType` más común que los objetos, que representan los datos muestreados en un momento específico o durante un período de tiempo. 

[![](healthkit-images/image08.png "Gráfico de objetos de HKSampleType")](healthkit-images/image08.png#lightbox)

`HKSampleType`es abstracto y tiene cuatro subclases concretas. Actualmente solo hay un tipo de `HKCategoryType` datos, que es análisis de suspensión. La mayor parte de los datos del kit de mantenimiento son `HKQuantityType` del tipo y almacenan `HKQuantitySample` sus datos en objetos, que se crean con el modelo de diseño de fábrica conocido:

[![](healthkit-images/image09.png "La mayor parte de los datos del kit de mantenimiento son del tipo HKQuantityType y almacenan sus datos en objetos HKQuantitySample")](healthkit-images/image09.png#lightbox)

`HKQuantityType`los tipos van `HKQuantityTypeIdentifier.ActiveEnergyBurned` desde `HKQuantityTypeIdentifier.StepCount`a. 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>Solicitar permiso al usuario

Los usuarios finales deben realizar pasos positivos para permitir que una aplicación lea o escriba datos del kit de mantenimiento. Esto se hace a través de la aplicación de mantenimiento que viene preinstalada en dispositivos iOS 8. La primera vez que se ejecuta una aplicación del kit de mantenimiento, se presenta al usuario un cuadro de diálogo de **acceso de mantenimiento** controlado por el sistema:

[![](healthkit-images/image10.png "Se presenta al usuario un cuadro de diálogo de acceso de mantenimiento controlado por el sistema")](healthkit-images/image10.png#lightbox)

Más adelante, el usuario puede cambiar los permisos mediante el cuadro de diálogo **orígenes** de la aplicación Health:

[![](healthkit-images/image11.png "El usuario puede cambiar los permisos mediante el cuadro de diálogo orígenes de aplicaciones de mantenimiento")](healthkit-images/image11.png#lightbox)

Dado que la información de estado es extremadamente sensible, los desarrolladores de aplicaciones deben escribir sus programas de forma defensiva, con la expectativa de que los permisos se rechacen y cambien mientras se ejecuta la aplicación. La expresión más común es solicitar permisos en el `UIApplicationDelegate.OnActivated` método y, a continuación, modificar la interfaz de usuario según corresponda.

### <a name="permissions-walkthrough"></a>Tutorial de permisos

En el proyecto aprovisionado del kit de mantenimiento, abra el `AppDelegate.cs` archivo. Observe la instrucción usando `HealthKit`; en la parte superior del archivo.

El siguiente código se relaciona con los permisos del kit de mantenimiento:

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

Todo el código de estos métodos puede realizarse en línea en `OnActivated`, pero la aplicación de ejemplo usa métodos independientes para que su intención sea más clara: `ValidateAuthorization()` tiene los pasos necesarios para solicitar acceso a los tipos específicos que se escriben (y leen, si la aplicación desea) y `ReactToHealthCarePermissions()` es una devolución de llamada que se activa después de que el usuario haya interactuado con el cuadro de diálogo de permisos en Health. app.

El trabajo de `ValidateAuthorization()` es para compilar el `HKObjectTypes` conjunto de que la aplicación escribirá y solicitará autorización para actualizar los datos. En la aplicación de ejemplo, `HKObjectType` es para la clave `KHQuantityTypeIdentifierKey.HeartRate`. Este tipo se agrega al conjunto `typesToWrite`, mientras que el conjunto `typesToRead` se deja vacío. Estos conjuntos, y una referencia a la `ReactToHealthCarePermissions()` devolución de llamada, se `HKHealthStore.RequestAuthorizationToShare()`pasan a.

Se `ReactToHealthCarePermissions()` llamará a la devolución de llamada después de que el usuario haya interactuado con el cuadro de diálogo de permisos `bool` y se le pasen dos fragmentos de información: un valor que `true` será si el usuario ha interactuado con el cuadro de diálogo permisos y un `NSError`que, si no es null, indica algún tipo de error asociado a la presentación del cuadro de diálogo de permisos.

> [!IMPORTANT]
> Para estar claro sobre los argumentos de esta función: los parámetros _Success_ y _error_ no indican si el usuario ha concedido permiso para obtener acceso a los datos del kit de mantenimiento. Solo indican que el usuario tiene la oportunidad de permitir el acceso a los datos.

Para confirmar si la aplicación tiene acceso a los datos, `HKHealthStore.GetAuthorizationStatus()` se usa, `HKQuantityTypeIdentifierKey.HeartRate`pasando. En función del estado devuelto, la aplicación habilita o deshabilita la capacidad de escribir datos. No hay ninguna experiencia de usuario estándar para tratar con una denegación de acceso y hay muchas opciones posibles. En la aplicación de ejemplo, el estado se establece en `HeartRateModel` un objeto singleton que, a su vez, genera eventos relevantes.

## <a name="model-view-and-controller"></a>Modelo, vista y controlador

Para revisar el `HeartRateModel` objeto singleton, abra el `HeartRateModel.cs` archivo:

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

La primera sección es código reutilizable para crear eventos y controladores genéricos. La parte inicial de la `HeartRateModel` clase también es reutilizable para crear un objeto singleton seguro para subprocesos.

A continuación `HeartRateModel` , expone 3 eventos: 

- `EnabledChanged`: Indica que se ha habilitado o deshabilitado el almacenamiento de velocidad cardíaca (tenga en cuenta que el almacenamiento está deshabilitado inicialmente). 
- `ErrorMessageChanged`-En esta aplicación de ejemplo, tenemos un modelo de control de errores muy simple: una cadena con el último error. 
- `HeartRateStored`: Se genera cuando se almacena una tarifa de corazón en la base de datos del kit de mantenimiento.

Tenga en cuenta que cada vez que se activan estos eventos, `NSObject.InvokeOnMainThread()`se realiza a través de, lo que permite a los suscriptores actualizar la interfaz de usuario. Como alternativa, los eventos podrían estar documentados como generados en subprocesos en segundo plano y la responsabilidad de garantizar la compatibilidad podría dejarse en sus controladores. Las consideraciones sobre los subprocesos son importantes en las aplicaciones del kit de mantenimiento porque muchas de las funciones, como la solicitud de permiso, son asincrónicas y ejecutan sus devoluciones de llamada en subprocesos no principales.

El código específico del kit de `HeartRateModel` mantenimiento en está en las `HeartRateInBeatsPerMinute()` dos `StoreHeartRate()`funciones y. 

`HeartRateInBeatsPerMinute()`convierte su argumento en un kit `HKQuantity`de mantenimiento fuertemente tipado. El tipo de la cantidad es el especificado por el `HKQuantityTypeIdentifierKey.HeartRate` y las unidades de la cantidad se `HKUnit.Count` dividen `HKUnit.Minute` entre (es decir, la unidad es *pulsaciones por minuto*). 

La `StoreHeartRate()` función toma un `HKQuantity` (en la aplicación de ejemplo, uno creado `HeartRateInBeatsPerMinute()` por). Para validar sus datos, usa el `HKQuantity.IsCompatible()` método, que devuelve `true` si las unidades del objeto se pueden convertir en las unidades del argumento. Si la cantidad se creó con `HeartRateInBeatsPerMinute()` , se devolverá `true`obviamente, pero también devolverá `true` si la cantidad se creó como, por ejemplo, las *pulsaciones por hora*. Normalmente, `HKQuantity.IsCompatible()` se puede usar para validar la masa, la distancia y la energía que el usuario o un dispositivo pueden escribir o mostrarse en un sistema de medida (como unidades de datos imperial), pero que podrían estar almacenados en otro sistema (por ejemplo, unidades métricas). 

Una vez que se ha validado la compatibilidad de la cantidad `HKQuantitySample.FromType()` , el Factory Method se usa para crear un `heartRateSample` objeto fuertemente tipado. `HKSample`los objetos tienen una fecha de inicio y de finalización; en el caso de las lecturas instantáneas, estos valores deben ser los mismos que en el ejemplo. El ejemplo tampoco establece ningún dato de valor clave en su `HKMetadata` argumento, pero puede usar código como el código siguiente para especificar la ubicación del sensor:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

`heartRateSample` Una vez creado, el código crea una nueva conexión a la base de datos con el bloque using. Dentro de ese bloque, `HKHealthStore.SaveObject()` el método intenta realizar la escritura asincrónica en la base de datos. La llamada resultante a la expresión lambda desencadena eventos relevantes, ya sea `HeartRateStored` o `ErrorMessageChanged`.

Ahora que el modelo se ha programado, es el momento de ver cómo el controlador refleja el estado del modelo. Abra el `HKWorkViewController.cs` archivo. El constructor simplemente conecta el `HeartRateModel` singleton con los métodos de control de eventos (de nuevo, esto podría realizarse en línea con las expresiones lambda, pero los métodos independientes hacen que la intención sea un poco más obvia):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Estos son los controladores relevantes:

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

Obviamente, en una aplicación con un solo controlador, sería posible evitar la creación de un objeto de modelo independiente y el uso de eventos para el flujo de control, pero el uso de objetos de modelo es más adecuado para las aplicaciones reales.

## <a name="running-the-sample-app"></a>Ejecutar la aplicación de ejemplo

El simulador de iOS no es compatible con el kit de mantenimiento. La depuración debe realizarse en un dispositivo físico que ejecute iOS 8.

Conecte un dispositivo de desarrollo de iOS 8 aprovisionado correctamente al sistema. Selecciónelo como destino de implementación en Visual Studio para Mac y, en el menú, elija **ejecutar > depurar**.

> [!IMPORTANT]
> Los errores relacionados con el aprovisionamiento se verán en este momento. Para solucionar errores, revise la sección creación y aprovisionamiento de una aplicación del kit de mantenimiento anterior. Los componentes son: 
>
> - **centro de desarrollo de iOS** : ID. de aplicación explícito & el kit de mantenimiento habilitado. 
> - **Opciones de proyecto** : identificador de lote (identificador de aplicación explícito) & Perfil de aprovisionamiento.
> - **Código fuente** -derechos. plist & info. plist

Suponiendo que las aprovisionaciones se han establecido correctamente, se iniciará la aplicación. Cuando alcance su método `OnActivated` , solicitará la autorización del kit de mantenimiento. La primera vez que lo encuentre el sistema operativo, se mostrará al usuario el siguiente cuadro de diálogo:

[![](healthkit-images/image12.png "Se presentará al usuario este cuadro de diálogo")](healthkit-images/image12.png#lightbox)

Habilite la aplicación para actualizar los datos de la tarifa de corazón y la aplicación volverá a aparecer. La `ReactToHealthCarePermissions` devolución de llamada se activará de forma asincrónica. Esto hará que la `HeartRateModel’s` `Enabled` propiedad cambie, lo que generará el `EnabledChanged` evento, lo que hará que `HKPermissionsViewController.OnEnabledChanged()` se ejecute el controlador de eventos, lo `StoreData` que habilita el botón. En el diagrama siguiente se muestra la secuencia:

[![](healthkit-images/image13.png "Este diagrama muestra la secuencia de eventos")](healthkit-images/image13.png#lightbox)

Presione el botón **grabar** . Esto hará que se `StoreData_TouchUpInside()` ejecute el controlador, que intentará analizar el valor `heartRate` del `HKQuantity` campo de texto, convertir en a a través de la función `HeartRateModel.HeartRateInBeatsPerMinute()` descrita anteriormente y pasar esa cantidad `HeartRateModel.StoreHeartRate()`a. Como se explicó anteriormente, esto intentará almacenar los datos y producirá un `HeartRateStored` evento o. `ErrorMessageChanged`

Haga doble clic en el botón **Inicio** del dispositivo y abra aplicación de mantenimiento. Haga clic en la pestaña **orígenes** para ver la aplicación de ejemplo. Elíjalo y no permitir permiso para actualizar los datos de la frecuencia cardíaca. Haga doble clic en el botón **Inicio** y vuelva a cambiar a la aplicación. Una vez más, se llamará, pero esta vez, dado que se deniega el acceso, se deshabilitará el botón StoreData (tenga en cuenta que esto se produce de forma asincrónica y que el cambio en la interfaz de usuario puede ser visible para el usuario final). `ReactToHealthCarePermissions()`

## <a name="advanced-topics"></a>Temas avanzados

Leer datos de la base de datos del kit de mantenimiento es muy similar a escribir datos: uno especifica los tipos de datos a los que está intentando acceder, solicita autorización y, si se concede esa autorización, los datos están disponibles, con conversión automática a unidades compatibles de Medi.

Hay una serie de funciones de consulta más sofisticadas que permiten consultas basadas en predicado y consultas que realizan actualizaciones cuando se actualizan los datos pertinentes. 

Los desarrolladores de aplicaciones del kit de mantenimiento deben revisar la sección del kit de estado de las [directrices de revisión](https://developer.apple.com/app-store/review/guidelines/#healthkit)de la aplicación de Apple.

Una vez que se entienden los modelos de seguridad y tipo del sistema, el almacenamiento y la lectura de los datos en la base de datos del kit de mantenimiento Compartido son bastante sencillos. Muchas de las funciones del kit de mantenimiento funcionan de forma asincrónica y los desarrolladores de aplicaciones deben escribir sus programas adecuadamente.

En el momento de redactar este artículo, no hay ningún equivalente al kit de mantenimiento en Android o Windows Phone.

## <a name="summary"></a>Resumen

En este artículo hemos visto cómo Health kit permite a las aplicaciones almacenar, recuperar y compartir información relacionada con el estado, a la vez que proporciona una aplicación de estado estándar que permite al usuario tener acceso a los datos y controlarlos. 

También hemos visto cómo la privacidad, la seguridad y la integridad de los datos están invalidando los problemas relacionados con la información relacionada con el estado y las aplicaciones que usan el kit de mantenimiento deben tratar el aumento de la complejidad en los aspectos de la administración de aplicaciones (aprovisionamiento), codificación (tipo de kit de mantenimiento). sistema) y experiencia del usuario (control de usuario de permisos a través de cuadros de diálogo del sistema y de la aplicación de estado). 

Por último, hemos echado un vistazo a una implementación sencilla del kit de mantenimiento que usa la aplicación de ejemplo incluida que escribe los datos de latido en el almacén del kit de mantenimiento y tiene un diseño compatible con el asincrónico.

## <a name="related-links"></a>Vínculos relacionados

- [HKWork (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
