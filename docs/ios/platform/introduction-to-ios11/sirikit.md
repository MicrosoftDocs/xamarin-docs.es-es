---
title: SiriKit actualizaciones en iOS 11
description: En este documento se describe cómo trabajar con SiriKit en iOS 11. En concreto, examina cómo trabajar con tareas y notas y cómo proporcionar nombres alternativos para una aplicación.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/07/2017
ms.openlocfilehash: 7bc102069d673b9459c863282b0423952c8fa59d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437325"
---
# <a name="sirikit-updates-in-ios-11"></a>SiriKit actualizaciones en iOS 11

SiriKit se presentó en iOS 10, con varios dominios de servicio (incluidos los entrenamientos, la reserva de cambios y la realización de llamadas). Consulte la [sección SiriKit](~/ios/platform/sirikit/index.md) para conocer los conceptos de SiriKit y cómo implementar SiriKit en la aplicación.

![Demostración de la lista de tareas de Siri](sirikit-images/sirikit.png)

SiriKit en iOS 11 agrega estos dominios de intención nuevos y actualizados:

- [**Listas y notas**](#listsnotes) : nuevo Proporciona una API para que las aplicaciones procesen tareas y notas.
- **Códigos visuales** : nuevo Siri puede mostrar códigos QR para compartir información de contacto o participar en transacciones de pago.
- **Pagos** : se han agregado intentos de búsqueda y transferencia para las interacciones de pago.
- Cancelar la **reserva** : se han agregado intentos de cancelación y de comentarios.

Entre las otras características nuevas se incluyen estas:

- [**Nombres de aplicación alternativos**](#alternativenames) : proporciona alias que ayudan a los clientes a indicar a Siri que dirija su aplicación mediante la oferta de nombres alternativos.
- **Inicio de los entrenamientos** : proporciona la capacidad de iniciar un entrenamiento en segundo plano.

A continuación se explican algunas de estas características. Para obtener más información sobre los demás, consulte la [documentación de SiriKit de Apple](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes"></a>

## <a name="lists-and-notes"></a>Listas y notas

El nuevo dominio de listas y notas proporciona una API para que las aplicaciones procesen tareas y notas a través de solicitudes de Siri Voice.

**Tareas**

- Tener un título y un estado de finalización.
- Opcionalmente, puede incluir una fecha límite y una ubicación.

**Notas**

- Tienen un título y un campo de contenido.

Las tareas y las notas se pueden organizar en grupos. En el resto de esta sección se describe cómo implementar este nuevo dominio con SiriKit, mediante el [ejemplo de TasksNotes SiriKit](/samples/xamarin/ios-samples/ios11-sirikitsample).

### <a name="how-to-process-a-sirikit-request"></a>Cómo procesar una solicitud SiriKit

Procese una solicitud SiriKit siguiendo estos pasos:

1. **Resolver** : valide los parámetros y solicite más información del usuario (si es necesario).
2. **CONFIRM** : validación final y comprobación de que se puede procesar la solicitud.
3. **Identificador** : realizar la operación (actualizar datos o realizar operaciones de red).

Los dos primeros pasos son opcionales (aunque se recomienda) y se requiere el paso final.
Hay instrucciones más detalladas en la [sección SiriKit](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Métodos Resolve y CONFIRM

Estos métodos opcionales permiten que el código Realice la validación, seleccione valores predeterminados o solicite información adicional al usuario.

Por ejemplo, para la `IINCreateTaskListIntent` interfaz, el método necesario es `HandleCreateTaskList` . Hay cuatro métodos opcionales que proporcionan más control sobre la interacción de Siri:

- `ResolveTitle` : Valida el título, establece un título predeterminado (si es necesario) o indica que los datos no son necesarios.
- `ResolveTaskTitles` : Valida la lista de tareas que habla el usuario.
- `ResolveGroupName` : Valida el nombre del grupo, elige un grupo predeterminado o indica que los datos no son necesarios.
- `ConfirmCreateTaskList` : Valida que el código puede realizar la operación solicitada, pero no la realiza (solo los `Handle*` métodos deben modificar los datos).

### <a name="handle-the-intent"></a>Control de la intención

Hay seis intenciones en el dominio listas y notas, tres para tareas y tres para notas.
Los métodos que debe implementar para controlar estas intenciones son:

- Para tareas:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Para las notas:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Cada método tiene un tipo de intento específico que se le pasa, que contiene toda la información que Siri ha analizado de la solicitud del usuario (y posiblemente se ha actualizado en los `Resolve*` `Confirm*` métodos y).
La aplicación debe analizar los datos proporcionados y, a continuación, realizar algunas acciones para almacenar o procesar los datos de otro modo, y devolver el resultado que Siri habla y muestra al usuario.

### <a name="response-codes"></a>Códigos de respuesta

Los métodos obligatorios `Handle*` y opcionales `Confirm*` indican un código de respuesta estableciendo un valor en el objeto que pasan a su controlador de finalización. Las respuestas proceden de la `INCreateTaskListIntentResponseCode` enumeración:

- `Ready` : Devuelve durante la fase de confirmación (es decir, desde un `Confirm*` método, pero no desde un `Handle*` método).
- `InProgress` : Se usa para tareas de ejecución prolongada (como una operación de red/servidor).
- `Success` : Responde con los detalles de la operación correcta (solo desde un `Handle*` método).
- `Failure` : Significa que se ha producido un error y que la operación no se ha podido completar.
- `RequiringAppLaunch` : No se puede procesar por el intento, pero la operación es posible en la aplicación.
- `Unspecified` – No usar: el mensaje de error se mostrará al usuario.

Obtenga más información sobre estos métodos y respuestas en la [documentación de notas y listas de SiriKit](https://developer.apple.com/documentation/sirikit/lists_and_notes)de Apple.

### <a name="implementing-lists-and-notes"></a>Implementar listas y notas

El [ejemplo de TasksNotes SiriKit](/samples/xamarin/ios-samples/ios11-sirikitsample) se creó con los pasos siguientes para agregar compatibilidad con SiriKit a una aplicación de iOS en blanco.

En primer lugar, para agregar compatibilidad con SiriKit, siga estos pasos para la aplicación iOS:

1. Tick **SiriKit** en el archivo **contitles. plist**.
2. Agregue la clave **privacy-Siri Usage Description** a **info. plist**, junto con un mensaje para los clientes.
3. Llame al `INPreferences.RequestSiriAuthorization` método en la aplicación para solicitar al usuario que permita las interacciones de Siri.
4. Agregue SiriKit al identificador de la aplicación en el portal para desarrolladores y vuelva a crear los perfiles de aprovisionamiento para incluir el nuevo derecho.

Después, agregue un nuevo proyecto de extensión a la aplicación para controlar las solicitudes de Siri:

1. Haga clic con el botón derecho en la solución y elija **agregar > agregar nuevo proyecto..**..
2. Elija la **extensión de > de iOS > plantilla de extensión de intents** .
3. Se agregarán dos proyectos nuevos: Intent y IntentUI. La personalización de la interfaz de usuario es opcional, por lo que el ejemplo solo incluye código en el proyecto de **intención** .

El proyecto de extensión es donde se procesarán todas las solicitudes de SiriKit. Como una extensión independiente, no tiene ninguna manera de comunicarse de forma automática con la aplicación principal: esto se resuelve normalmente mediante la implementación de almacenamiento de archivos compartidos con grupos de aplicaciones.

#### <a name="configure-the-intenthandler"></a>Configuración de IntentHandler

La `IntentHandler` clase es el punto de entrada de las solicitudes de Siri: cada intento se pasa al `GetHandler` método, que devuelve un objeto que puede controlar la solicitud.

El código siguiente muestra una implementación simple:

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

La clase debe heredar de `INExtension` y, dado que el ejemplo va a controlar las representaciones de las listas y las notas, también implementa `IINNotebookDomainHandling` .

> [!NOTE]
>
> - Existe una Convención en .NET para que las interfaces vayan precedidas de mayúsculas `I` , que Xamarin se adhiere a los protocolos de enlace del SDK de iOS.
> - Xamarin también conserva los nombres de tipo de iOS y Apple usa los dos primeros caracteres de los nombres de tipo para reflejar el marco al que pertenece un tipo.
> - En el `Intents` marco de trabajo, los tipos tienen el prefijo `IN*` (por ejemplo, `INExtension`) pero _no_ son interfaces.
> - También sigue los protocolos (que se convierten en interfaces en C#) y terminan con dos `I` , como `IINAddTasksIntentHandling` .

#### <a name="handling-intents"></a>Control de la calidad

Cada intento (agregar tarea, establecer atributo de tarea, etc.) se implementa en un único método similar al que se muestra a continuación. El método debe realizar tres funciones principales:

1. **Procesar la intención** : los datos analizados por Siri están disponibles en un `intent` objeto específico del tipo de intento. Es posible que la aplicación haya validado los datos mediante `Resolve*` métodos opcionales.
2. **Validar y actualizar el almacén de datos** : guarda los datos en el sistema de archivos (con grupos de aplicaciones para que la aplicación de iOS principal también pueda acceder a ellos) o a través de una solicitud de red.
3. **Proporcionar respuesta** : Use el `completion` controlador para devolver una respuesta a Siri para que se pueda leer o mostrar al usuario:

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

Observe que `null` se pasa como el segundo parámetro a la respuesta: este es el parámetro de la actividad del usuario y, cuando no se proporciona, se usará un valor predeterminado.
Puede establecer un tipo de actividad personalizado siempre que la aplicación iOS lo admita a través de la `NSUserActivityTypes` clave en **info. plist**. Después, puede controlar este caso cuando se abre la aplicación y realizar operaciones específicas (como abrir en un controlador de vista pertinente y cargar los datos de la operación Siri).

En el ejemplo también se hardcodes el `Success` resultado, pero en escenarios reales se debe agregar el informe de errores adecuado.

### <a name="test-phrases"></a>Frases de prueba

Las siguientes frases de prueba deben funcionar en la aplicación de ejemplo:

- "Cree una lista de comestibles con manzanas, plátanos y peras en TasksNotes"
- "Agregar tarea WWDC en TasksNotes"
- "Agregar WWDC de tareas a la lista de entrenamiento en TasksNotes"
- "Marcar asista WWDC como completo en TasksNotes"
- "En TasksNotes, Recordármelo en comprar un iPhone cuando obtengo Inicio"
- "Marque comprar iPhone como completado en TasksNotes"
- "Recordármelo en casa a las 8 a.m. en TasksNotes"

![Crear un ejemplo de lista nuevo](sirikit-images/createtasklist-sml.png) ![Ejemplo de establecimiento de tarea como completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> El simulador de iOS 11 admite pruebas con Siri (a diferencia de las versiones anteriores).
>
> Si va a realizar pruebas en dispositivos reales, no olvide configurar el identificador de la aplicación y los perfiles de aprovisionamiento para la compatibilidad con SiriKit.

<a name="alternativenames"></a>

## <a name="alternative-names"></a>Nombres alternativos

Esta nueva característica de iOS 11 significa que puede configurar nombres alternativos para la aplicación para ayudar a los usuarios a desencadenarla correctamente con Siri. Agregue las siguientes claves al archivo **info. plist** del proyecto de aplicación de iOS:

![Info. plist que muestra claves y valores de nombre de aplicación alternativos](sirikit-images/alternative-names.png)

Con los nombres de aplicación alternativos establecidos, las siguientes frases también funcionarán para la aplicación de ejemplo (que se denomina realmente **TasksNotes**):

- "Cree una lista de comestibles con manzanas, plátanos y peras en _MonkeyNotes_"
- "Agregar tarea WWDC en _MonkeyTodo_"

## <a name="troubleshooting"></a>Solución de problemas

Algunos errores que pueden surgir al ejecutar el ejemplo o agregar SiriKit a sus propias aplicaciones:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Se produjo una excepción de Objective-C.  Name: NSInternalInconsistencyException Reason: uso de la clase <inferences: 0x60400082ff00> de una aplicación requiere el derecho com. Apple. Developer. Siri. ¿Ha habilitado la funcionalidad Siri en el proyecto de Xcode?_

- SiriKit se marca en los **derechos. plist**.
- **Contitles. plist** se configura en las **opciones del proyecto > compilar > la firma del lote de iOS**.

  [![Opciones de proyecto que muestran los derechos correctamente establecidos](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (para la implementación de dispositivos) El ID. de aplicación tiene SiriKit habilitado y Perfil de aprovisionamiento descargado.

## <a name="related-links"></a>Vínculos relacionados

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Ejemplo de TasksNotes SiriKit](/samples/xamarin/ios-samples/ios11-sirikitsample)
- [Novedades de SiriKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/214/)