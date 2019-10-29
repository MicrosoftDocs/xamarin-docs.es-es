---
title: Técnicas de interacción rápida para watchos 3 en Xamarin
description: En este artículo se describen las técnicas de interacción rápida que Apple ha agregado en watchos 3 y cómo implementarlas en Xamarin. iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 8b851721aa5b2b993ad64b89d90d02b5f2bd0ee3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028186"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Técnicas de interacción rápida para watchos 3 en Xamarin

_En este artículo se describen las técnicas de interacción rápida que Apple ha agregado en watchos 3 y cómo implementarlas en Xamarin. iOS para Apple Watch._

Proporcionar interacciones de usuario rápidas es esencial para crear atractivas aplicaciones de Apple Watch y complicaciones. Novedad de watchos 3, Apple ha agregado compatibilidad con los reconocedores de gestos, el acceso a la Digital Crown y nuevas técnicas de navegación y notificación de usuario. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permite al desarrollador crear fácilmente interfaces enriquecidas y de gran visión que son rápidas y con capacidad de respuesta.

## <a name="what-are-quick-interactions"></a>Qué son las interacciones rápidas

Para un desarrollador que se usa para crear aplicaciones para iOS o macOS (donde la cantidad de tiempo que un usuario invierte en interactuar con la aplicación se mide en minutos u horas), el diseño de una aplicación correcta para el Apple Watch puede ser un desafío y requiere un valor diferente. medida.

En watchos, el usuario normalmente desea levantar su muñeca, interactuar rápidamente con una aplicación (normalmente durante un breve par de segundos) y luego quitar su muñeca y continuar con todo lo que estaba haciendo.

A continuación se muestran algunos ejemplos de interacciones rápidas típicas en el Apple Watch:

- Iniciar un temporizador.
- Comprobando el tiempo.
- Marcar un elemento en una lista de tareas pendientes.

Para lograr estos objetivos, una aplicación en el Apple Watch debe ser:

- Con **un solo vistazo, lo** que significa que, con una vista rápida, el usuario debe poder obtener la información que necesitan. 
- **Accionable** : es decir, los usuarios deben poder tomar decisiones rápidas y bien fundamentadas.
- **Responde** , lo que significa que el usuario nunca debe esperar para recibir la información que necesitan o para lograr la acción que desean.

### <a name="quick-interactions-length"></a>Longitud de interacciones rápidas

Debido a la naturaleza de la vista de las aplicaciones Apple Watch, Apple sugiere que la longitud ideal de una interacción rápida debe ser de dos segundos o menos. Como resultado de este límite de dos segundos, el desarrollador tendrá que dedicar una cantidad considerable de tiempo diseñando e implementando una aplicación Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nuevas características y API de watchos 3

Apple ha agregado varias características nuevas y API a WatchKit para ayudar al desarrollador a agregar interacciones rápidas a sus aplicaciones Apple Watch:

- watchos 3 proporciona acceso a los nuevos tipos de datos proporcionados por el usuario, como:
  - Reconocedores de gestos
  - Rotación Digital Crown 
- watchos 3 proporciona nuevas formas de mostrar y actualizar información, como:
  - Navegación de tabla mejorada
  - Nueva compatibilidad con el marco de notificaciones de usuario
  - Integración de SpriteKit y SceneKit

Mediante la implementación de estas nuevas características, el desarrollador puede asegurarse de que la aplicación watchos 3 sea de vista rápida, accionable y con capacidad de respuesta.

### <a name="gesture-recognizer-support"></a>Compatibilidad del reconocedor de gestos

Si el desarrollador ha implementado reconocedores de gestos en iOS, deberían estar familiarizados con el funcionamiento de los reconocedores de gestos en watchos 3. Para actualizar, los reconocedores de gestos son objetos que analizan los eventos táctiles de bajo nivel en gestos reconocibles y predefinidos.

watchos 3 será compatible con los cuatro reconocedores de gesto siguientes:

- Tipos de gestos discretos:
  - El gesto de deslizar rápidamente (`WKSwipeGestureRecognizer`).
  - Gesto de punteo (`WKTapGestureRecognizer`).
- Tipos de gestos continuos:
  - El gesto de movimiento panorámico (`WKPanGestureRecognizer`).
  - El gesto de presionar largo (`WKLongPressGestureRecognizer`).

Para implementar uno de los reconocedores de gestos nuevos, basta con arrastrarlo a una superficie de diseño en el diseñador de iOS en Visual Studio para Mac y configurar sus propiedades.

En el código, responda a la acción del reconocedor para controlar el movimiento que desencadena el usuario. Una vez más, esto se hace de la misma manera que en iOS.

#### <a name="discrete-gesture-states"></a>Estados de gestos discretos

En el caso de gestos discretos, se llama a la acción cuando se reconoce el gesto y se asigna un estado (`WKGestureRecognizerState`) como:

[![](quick-interaction-techniques-images/quick01.png "Discrete Gesture States")](quick-interaction-techniques-images/quick01.png#lightbox)

Todos los gestos discretos empiezan en el estado `Possible` y pasan al estado `Failed` o `Recognized`. Cuando se usan gestos discretos, el desarrollador generalmente no trata directamente con el estado. En su lugar, se basan en la acción a la que se llama cuando solo se reconoce el gesto.

#### <a name="continuous-gesture-states"></a>Estados de gesto continuos

Los gestos continuos son ligeramente diferentes de los gestos discretos, donde la acción se llama varias veces a medida que se reconoce el gesto:

[![](quick-interaction-techniques-images/quick02.png "Continuous Gesture States")](quick-interaction-techniques-images/quick02.png#lightbox)

De nuevo, los gestos continuos se inician en el estado `Possible`, pero progresan en varias actualizaciones. Aquí el desarrollador deberá tener en cuenta el estado del reconocedor y actualizar la interfaz de usuario de la aplicación durante la fase `Changed` hasta que el gesto finalice `Recognized` o `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Sugerencias de uso del reconocedor de gestos

Apple sugiere lo siguiente al trabajar con reconocedores de gestos en watchos 3:

- Agregue los reconocedores de gestos a los elementos de grupo en lugar de a los controles individuales. Puesto que el Apple Watch tiene un tamaño de pantalla físico más pequeño, los elementos de grupo tienden a ser objetivos más grandes y más fáciles de alcanzar para el usuario. Además, los reconocedores de gestos pueden entrar en conflicto con los gestos integrados que ya están en los controles de interfaz de usuario nativa.
- Establezca relaciones de dependencia en el guión gráfico de la aplicación Watch.
- Algunos gestos tienen prioridad sobre otros tipos de gestos, como:
  - Desplazarse
  - Force Touch

### <a name="digital-crown-rotation"></a>Rotación Digital Crown

Al implementar la compatibilidad de Digital Crown en sus aplicaciones de watchos 3, un desarrollador puede proporcionar mayor velocidad de navegación y interacciones de precisión para sus usuarios.

Desde watchos 2, Apple Watch aplicación podría usar el `WKInterfacePicker` objeto para tener acceso al Digital Crown proporcionando una lista de `WKPickerItems` y un estilo de selector (secuencia de lista, apilada o imagen). watchos entonces permite al usuario usar el Digital Crown para seleccionar un elemento de la lista.

Cuando se usa un `WKInterfacePicker`, WatchKit controla la mayor parte del trabajo de la siguiente manera:

- Dibujar la lista y los elementos de la interfaz individual.
- Procesando los eventos de Digital Crown.
- Llamar a una acción cuando se selecciona un elemento.

Como novedad de watchos 3, el desarrollador tiene acceso directo a los eventos de rotación Digital Crown, lo que les permite crear sus propios elementos de interfaz de usuario que respondan a los valores de rotación.

Los siguientes elementos proporcionan acceso Digital Crown:

- `WKCrownSequencer`: proporciona acceso a los giros por segundo.
- `WKCrownDelegate`: proporciona acceso a los eventos Delta de rotación.

#### <a name="rotations-per-second"></a>Rotaciones por segundo

El acceso a los giros por segundo desde el Digital Crown resulta útil cuando se trabaja con animaciones basadas en la física. Para tener acceso a los giros por segundo, use la propiedad `CrownSequencer` del `WKInterfaceController` de la extensión inspección. Por ejemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Diferencias de giro

Use las diferencias de rotación de la Digital Crown para contar el número de giros. Utilice el método de invalidación `CrownDidRotate` del `WKCrownDelegate` para tener acceso a las diferencias de giro. Por ejemplo:

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

Aquí, la aplicación mantiene un acumulador (`AccumulatedRotations`) para determinar el número de giros. Un giro completo de la Digital Crown es igual a un delta acumulado de `1.0` y se `0.5`un giro medio.

Apple ha dejado al desarrollador para determinar cómo se corresponden los recuentos de rotación con la confidencialidad de los cambios en el elemento de la interfaz de usuario que se está actualizando.

El signo (`+/-`) del delta de rotación indica la dirección en la que el usuario está convirtiendo el Digital Crown:

[![](quick-interaction-techniques-images/quick03.png "The sign of the Rotational Delta indicates the direction that the user is turning the Digital Crown")](quick-interaction-techniques-images/quick03.png#lightbox)

Si el usuario se desplaza hacia arriba, WatchKit devolverá deltas positivos y, si se desplaza hacia abajo, se devolverán diferencias negativas, independientemente de la orientación en la que el usuario esté contratando el reloj.

#### <a name="digital-crown-focus"></a>Digital Crown foco

Al igual que cualquier otro elemento de la interfaz, el Digital Crown tiene el concepto de foco. Este enfoque puede desplazarse fuera del Digital Crown a otros elementos de la interfaz en función de cómo interactúe el usuario con el reloj. 

Por ejemplo, cualquiera de los controles siguientes podría robar el foco del Digital Crown:

- Selector
- Slider
- Controlador de desplazamiento

Depende del desarrollador determinar cuándo el elemento de interfaz personalizado debe ser el foco del Digital Crown. Apple sugiere el uso de los nuevos reconocedores de gestos para obtener el foco en el elemento de la interfaz de usuario personalizado.

### <a name="vertical-paging"></a>Paginación vertical

La manera estándar en que un usuario navega por una vista de tabla en una aplicación de watchos es desplazarse hasta el fragmento de datos deseado, puntear en una fila específica para mostrar la vista detallada, puntear en el botón atrás cuando termine de ver los detalles y repita el proceso para cualquier otra información que el y están interesados en dentro de la tabla:

[![](quick-interaction-techniques-images/quick04.png "Moving between a table and the Detail view")](quick-interaction-techniques-images/quick04.png#lightbox)

Como novedad de watchos 3, el desarrollador puede habilitar la paginación vertical en los controles de vista de tabla. Con esta característica habilitada, el usuario puede desplazarse para buscar una fila de la vista de tabla y puntear en la fila para ver sus detalles como antes. Sin embargo, ahora pueden deslizarse hacia arriba para seleccionar la fila siguiente de la tabla o hacia abajo para seleccionar la fila anterior (o usar el Digital Crown), sin tener que volver a la vista de tabla en primer lugar:

[![](quick-interaction-techniques-images/quick05.png "Moving between a table and the Detail view and swiping up and down to move between the other rows")](quick-interaction-techniques-images/quick05.png#lightbox)

Para habilitar este modo, abra el guión gráfico de la aplicación watchos en Xcode para editarlo, seleccione la vista de tabla y active la casilla **paginación de detalles vertical** :

[![](quick-interaction-techniques-images/quick06.png "Check the Vertical Detail Paging checkbox")](quick-interaction-techniques-images/quick06.png#lightbox)

Asegúrese de que la tabla usa objetos segue para mostrar la vista detallada y de guardar los cambios en el guión gráfico y volver a Visual Studio para Mac para sincronizar.

El desarrollador puede interaccionar mediante programación la paginación vertical con una fila concreta mediante el código siguiente en una vista de tabla:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Cuando se usa la paginación vertical, el desarrollador debe tener en cuenta que WatchKit controlará automáticamente la carga previa de los controladores y, como resultado, se puede llamar a algunos métodos del ciclo de vida del controlador antes de que la interfaz de usuario esté realmente visible.

### <a name="notification-enhancements"></a>Mejoras en las notificaciones

La notificación es la forma principal de interacción rápida que un usuario experimenta normalmente en watchos y que ha estado disponible desde el primer Apple Watch y watchos 1.

Una interacción rápida de notificación típica es la siguiente:

1. El usuario percibe la notificación hápticos cuando se recibe una nueva notificación.
2. Elevan su muñeca para ver la interfaz de apariencia breve de la notificación.
3. Si continúan manteniendo su muñeca, watchos pasará automáticamente a la interfaz de notificación de larga apariencia.

Hay varias maneras en las que un usuario puede responder a la notificación:

- En el caso de una notificación bien definida y presentada, el usuario no hará nada y simplemente descartará la notificación.
- También pueden pulsar la notificación para iniciar la aplicación watchos.
- En el caso de una notificación que admita acciones personalizadas, el usuario puede seleccionar una de las acciones personalizadas. Pueden ser:
  - **Acciones de primer plano** : inician la aplicación para realizar la acción.
  - **Acciones en segundo plano** : siempre se enrutaron al iPhone en watchos 2, pero se pueden enrutar al watchApp en watchos 3.

Novedades de watchos 3:

- Notificación use una API similar en todas las plataformas (iOS, watchos, tvOS y macOS).
- La notificación local se puede programar en el Apple Watch.
- La notificación en segundo plano se enrutará a la extensión de la aplicación si se programaron en el Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Programación y entrega de notificaciones

La notificación del iPhone del usuario se redirigirá a la Apple Watch cuando se produzca lo siguiente:

- La pantalla del iPhone está desactivada.
- El Apple Watch se está gastando y se ha desbloqueado.

En watchos 3, las notificaciones locales se pueden programar en el Apple Watch y solo se entregan en el reloj. Es el desarrollador quien programa la notificación de iPhone correspondiente si es necesaria para la aplicación.

Al incluir el mismo identificador de notificación en las versiones Apple Watch y iPhone de las notificaciones, se evita que se muestren notificaciones duplicadas en el reloj. La versión Apple Watch de la notificación tendrá prioridad sobre la versión de iPhone.

Como watchos 3 usa el mismo `UINotification` marco de API que iOS 10, consulte la documentación del [marco de notificaciones de usuario](~/ios/platform/user-notifications/index.md) de iOS 10 para obtener más detalles.

### <a name="using-spritekit-and-scenekit"></a>Usar SpriteKit y SceneKit

Como novedad de watchos 3, el desarrollador ahora puede usar los objetos SpritKit y SceneKit en el diseño de la interfaz de usuario de la aplicación para presentar gráficos 2D y 3D.

Se han agregado dos nuevas clases de interfaz para admitir esta característica:

- `WKInterfaceSKScene`: para trabajar con gráficos 2D de SpriteKit.
- `WKInterfaceSCNScene`: para trabajar con gráficos SceneKit 3D.

Para usar estos objetos, simplemente arrástrelos a la superficie de diseño dentro del guión gráfico de la aplicación de inspección en la Interface Builder de Xcode y use el **Inspector de atributos** para configurarlos.

Desde este punto, trabajar con SpriteKit o SceneKit Scenes funciona igual que en una aplicación de iOS. La aplicación de inspección presentará un `WKInterfaceSKScene` llamando a uno de los métodos de `Present`. En el caso de SceneKit, basta con establecer la propiedad `Scene` del objeto `WKInterfaceSCNScene`.

## <a name="actionable-complications"></a>Complicaciones procesables

En watchos 2, Apple presentó complicaciones para aplicaciones de terceros. En watchos 3, Apple ha ampliado las capacidades que un desarrollador puede incluir en una complicación WatchKit. 

Además, el mayor número de caras de inspección integradas ahora puede incluir complicaciones y las caras de inspección existentes que ya admiten complicaciones pueden incluir aún más complicaciones.

También nueva es la posibilidad de que un usuario se deslice rápidamente hacia la izquierda o hacia la derecha para realizar la transición a través de todos los rostros de la inspección que han instalado en su Apple Watch. Con la nueva galería de la aplicación complementaria de iPhone del Apple Watch, el usuario puede Agregar y personalizar nuevas caras de inspección y cualquiera de las complicaciones que pueden incluir.

Debido a estas nuevas características, Apple sugiere que todas las aplicaciones en Apple Watch deben incluir al menos una complicación y, por lo tanto, todas las aplicaciones de Apple Watch nativas ahora tienen complicaciones.

Las complicaciones proporcionan las siguientes características a una aplicación:

- Son muy eficaces, ya que siempre están presentes en la esfera del reloj.
- Watchos suele actualizar las complicaciones. Cualquier aplicación que incluya una complicación en la superficie de inspección mostrada actualmente del usuario se actualiza al menos dos veces por hora.
- Cualquier aplicación con una complicación en la superficie de inspección mostrada actualmente del usuario se mantiene en la memoria, lo que hace que la aplicación se inicie rápidamente y mejora la velocidad de las respuestas de la aplicación.
- Las complicaciones facilitan que el usuario inicie una funcionalidad específica en una aplicación de watchos.

## <a name="glanceable-notification"></a>Notificación con visión

Las notificaciones sobre Apple Watch proporcionan una manera excelente y personalizable de informar rápidamente al usuario de eventos o información nueva, como mensajes entrantes o la obtención de un objetivo en una aplicación de entrenamiento.

Mediante el uso de una notificación, se puede presentar rápidamente información valiosa al usuario. En muchas situaciones, una notificación bien diseñada puede eliminar la necesidad de que el usuario inicie realmente la aplicación.

Novedades de watchos 3, todas las notificaciones admiten ahora:

- SpriteKit
- SceneKit
- Vídeo en línea

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaz de usuario mejorada con SpriteKit y SceneKit

Normalmente, un desarrollador podría pensar en la interfaz de usuario del juego cuando se mencionan SpriteKit y SceneKit. Sin embargo, tanto SpriteKit como SceneKit pueden ser útiles para crear interfaces de usuario que no son de juegos, que incluyen diseños personalizados, contenido y animaciones que, de lo contrario, no son posibles en WatchKit solo.

Por ejemplo, una notificación de usuario de una aplicación de uso compartido de fotografías puede usar SpriteKit para proporcionar una experiencia de usuario enriquecida mediante la inclusión del usuario que publicó la imagen junto con una imagen real y otra información personalizada que enriquece la experiencia del usuario.

Además, tanto SpriteKit como SceneKit se pueden mezclar con elementos de interfaz de usuario de WatchKit estándar en el diseño de la interfaz de usuario de la aplicación.

## <a name="simple-navigation"></a>Navegación simple

watchos 3 presenta varias maneras de que un desarrollador puede simplificar la navegación dentro de sus aplicaciones de watchos, como la nueva [paginación vertical](#vertical-paging), la [compatibilidad con el reconocedor de gestos](#gesture-recognizer-support) y las características de [rotación de digital Crown](#digital-crown-rotation) presentadas anteriormente.

El Digital Crown es único en el Apple Watch y se puede usar de muchas formas diferentes para simplificar la navegación. Por ejemplo, una aplicación de temporizador puede usar el Digital Crown para desplazarse a través de las longitudes de temporizador disponibles.

Los gestos personalizados pueden presentar formas nuevas y únicas para que el usuario interactúe con una aplicación de inspección y también se puede usar para simplificar la navegación por la aplicación.

Apple sugiere buscar formas de combinar todas las nuevas características de interacción rápida agregadas en watchos 3 para presentar interfaces de aplicación de watchos enriquecidas, fáciles y rápidas.

## <a name="finishing-the-quick-interaction"></a>Finalización de la interacción rápida

Una experiencia de interacción rápida bien diseñada dará al usuario la confianza de quitar su muñeca (y desactivarla con la aplicación) cuando haya finalizado la interacción actual.

En este caso, se trata de un problema específico cuando la aplicación de inspección realiza cualquier tipo de conexión de red o comparte información con su aplicación de iPhone complementaria. A menudo, esto puede conducir a un indicador de espera mientras se realiza la transacción, lo que no es deseable durante una interacción rápida. Considere el ejemplo siguiente:

[![](quick-interaction-techniques-images/quick07.png "Diagram of the watch app doing a network connection and sharing information with its companion iPhone app")](quick-interaction-techniques-images/quick07.png#lightbox)

1. El usuario elige un elemento para comprar en el reloj.
2. Puntean en el botón comprar.
3. La aplicación inicia la transacción de red y muestra un indicador de carga.
4. En algún momento posterior, la transacción se completa y la aplicación muestra un acuerdo de compra.
5. El usuario deja su muñeca y se desconecta con la aplicación.

Desde el momento en que el usuario pulsa el botón comprar hasta que se completa la transacción, su muñeca se ve en un indicador de carga. Para solucionar esta situación, Apple sugiere que el desarrollador debe presentar comentarios instantáneos al usuario en lugar de mostrar un indicador de carga. 

Con el modelo sugerido de Apple, eche un vistazo a la misma interacción rápida de nuevo:

[![](quick-interaction-techniques-images/quick08.png "Apples suggested model diagram")](quick-interaction-techniques-images/quick08.png#lightbox)

1. El usuario elige un elemento para comprar en el reloj.
2. Puntean en el botón comprar.
3. La aplicación inicia la transacción de red y muestra un mensaje que indica que la compra se ha iniciado correctamente.
4. El usuario deja su muñeca y se desconecta con la aplicación.
5. Cuando la transacción se completa correctamente una vez más tarde, la aplicación muestra una notificación local para informar al usuario de una compra correcta.

Esta vez, tan pronto como el usuario pulsa el botón comprar, se muestra un mensaje que indica que la compra se ha iniciado, por lo que puede colocar su muñeca y finalizar la interacción rápida en este momento. Más adelante, se les informará de si la transacción se ha realizado correctamente o no en una notificación de usuario. De esta manera, el usuario solo está interactuando con la aplicación durante las fases "activas" del proceso.

En el caso de las aplicaciones que están realizando redes, pueden usar un `NSURLSession` en segundo plano para controlar la comunicación de red con una tarea de descarga. Esto permitirá que la aplicación se reactivarán en segundo plano para procesar la información descargada. En el caso de una aplicación que requiera procesamiento en segundo plano, use una aserción de tarea en segundo plano para controlar el procesamiento necesario.

## <a name="quick-interaction-design-tips"></a>Sugerencias de diseño de interacción rápida

Dado que la duración deseada de una interacción rápida es de dos segundos o menos, el desarrollador debe centrarse en el diseño de las interacciones de la aplicación desde el principio del proceso de diseño. Busque áreas en las que se puedan simplificar esas interacciones (mediante la técnica presentada anteriormente) y use las nuevas características de watchos 3 para que la aplicación sea rápida y receptiva.

Apple sugiere lo siguiente:

- Céntrese en las interacciones rápidas mediante la incorporación de las características más usadas de la aplicación.
- Use complicaciones y notificaciones de usuario para exponer características y funciones comunes.
- Cree una interfaz de usuario enriquecida y de gran visión con SceneKit y SpriteKit.
- Siempre que sea posible, simplifique la navegación dentro de la aplicación.
- No haga nunca que el usuario espere, le permita quitar su muñeca y desconectarse con la aplicación lo antes posible.

## <a name="summary"></a>Resumen

En este artículo se han tratado las técnicas de interacción rápida que Apple ha agregado en watchos 3 y cómo implementarlas en Xamarin. iOS para Apple Watch.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
