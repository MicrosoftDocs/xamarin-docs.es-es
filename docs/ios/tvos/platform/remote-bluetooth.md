---
title: Siri controladores de Bluetooth y remotos para tvOS en Xamarin
description: En este artículo se describe cómo trabajar con los dispositivos de juego Siri remoto y Bluetooth en las aplicaciones de tvOS escritas con Xamarin.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 71a74d8f9046cd978a40b03da1921cd1fac9405f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769180"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Siri controladores de Bluetooth y remotos para tvOS en Xamarin

Los usuarios de la aplicación de Xamarin. tvOS no interactuarán con su interfaz directamente como con iOS, donde tocan imágenes en la pantalla del dispositivo, pero de forma indirecta desde el salón mediante el uso [remoto de Siri](#The-Siri-Remote).

Si la aplicación es un juego, opcionalmente puede compilar en soporte técnico para terceros, creados para [dispositivos de juegos Bluetooth](#Bluetooth-Game-Controllers) (accesorios MFI) de iOS también en la aplicación.

[![](remote-bluetooth-images/intro01.png "Dispositivo remoto y de juego Bluetooth")](remote-bluetooth-images/intro01.png#lightbox)

En este artículo se describen los gestos remotos de [Siri](#The-Siri-Remote), [Touch Surface](#Touch-Surface-Gestures) y [Siri](#Siri-Remote-Buttons) y se muestra cómo trabajar con ellos a través de [gestos y guiones gráficos](#Gestures-and-Storyboards), [gestos y código](#Gestures-and-Code) y [control de eventos de bajo nivel](#Low-Level-Event-Handling). Por último, se describe el [trabajo con dispositivos de juego](#Working-with-Game-Controllers) en una aplicación Xamarin. tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>El Siri remoto

La principal forma en que los usuarios interactuarán con Apple TV y la aplicación de Xamarin. tvOS es a través del siri remoto incluido. Apple diseñó el control remoto para salvar la distancia entre el usuario que está sentado en el sofá y la interfaz de usuario de Apple TV que se muestra en la habitación de la pantalla de TV.

Su desafío como desarrollador de aplicaciones de tvOS es la creación de una interfaz de usuario rápida, fácil de usar y visualmente atractiva que aprovecha la superficie táctil de Siri remota, el acelerómetro, giroscopio y los botones.

[![](remote-bluetooth-images/remote01.png "El Siri remoto")](remote-bluetooth-images/remote01.png#lightbox)

El control remoto Siri tiene las siguientes características y usos previstos en la aplicación tvOS:

|Característica|Uso general de la aplicación|Uso de la aplicación de juego|
|---|---|---|
|**Superficie táctil**<br />Deslice el dedo hasta navegar, presione para seleccionar y mantener los menús contextuales.|**Pulse/Deslice el dedo**<br />Navegación de la interfaz de usuario entre elementos que tienen el foco.<br /><br />**Hizo**<br />Activa el elemento seleccionado (enfocado).|**Pulse/Deslice el dedo**<br />Depende del diseño del juego y se puede usar como un panel de la cruceta punteando en los bordes.<br /><br />**Hizo**<br />Realice la función de botón principal.|
|**Menu**<br />Presione para volver a la pantalla o el menú anterior.|Vuelve a la pantalla anterior y sale de la pantalla de inicio de Apple TV desde la pantalla principal de la aplicación.|Pausar y reanudar el juego, vuelve a la pantalla anterior y sale de la pantalla principal de Apple TV desde la pantalla principal de la aplicación.|
|**Siri/buscar**<br />En países con Siri, mantenga presionado el control de voz, en todos los demás países, para mostrar la pantalla de búsqueda.|N/D|N/D|
|**Reproducir/pausar**<br />Reproducir y pausar medios o proporciona una función secundaria en las aplicaciones.|Inicia la reproducción multimedia y pausa/reanuda la reproducción.|Realiza la función de botón secundario u omite el vídeo de introducción (si existe).|
|**Página principal**<br />Presione para volver a la pantalla principal, haga doble clic para mostrar las aplicaciones en ejecución y mantenga presionado el dispositivo de suspensión.|N/D|N/D|
|**Volumen**<br />Controla el volumen de equipos de audio y vídeo conectados.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Gestos de la superficie táctil

La superficie táctil de Siri remota es capaz de detectar una variedad de gestos de un solo dedo al que se puede responder en la aplicación Xamarin. tvOS:

|Deslizar rápidamente|Haga clic|Pulsar|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Mueve la selección (foco) entre los elementos de la interfaz de usuario en la pantalla (hacia arriba, hacia abajo, a la derecha). El deslizamiento rápido se puede usar para desplazarse a través de listas grandes de contenido con rapidez mediante la inercia.|Activa el elemento seleccionado (enfocado) o actúa como el botón principal de un juego. Al hacer clic y mantener, se pueden activar los menús contextuales o las funciones secundarias.|Pulsar ligeramente la superficie táctil en los bordes actúa como botones de dirección en un panel D, moviendo el foco hacia arriba, hacia abajo, a la izquierda o a la derecha, en función de la unidad punteada. Dependiendo de la aplicación, se puede usar para mostrar los controles ocultos.|

Apple proporciona las siguientes sugerencias para trabajar con gestos de la superficie táctil:

- **Diferenciar entre los clics y pulsaciones** : hacer clic en es una acción intencionada del usuario y es adecuada para la selección, la activación y el botón principal de un juego. Pulsar es más sutil y debe usarse con moderación porque el usuario a menudo mantiene el Siri remoto en su mano y puede activar accidentalmente un evento TAP fácilmente.
- **No volver a definir los gestos estándar** : el usuario tiene una expectativa de que determinados gestos realicen acciones específicas; no se debe volver a definir el significado o la función de estos gestos en la aplicación. La única excepción es una aplicación de juego durante el juego activo.
- **Definir nuevos gestos con moderación** , el usuario tiene una expectativa de que determinados gestos realicen acciones específicas. Debe evitar definir gestos personalizados para realizar acciones estándar. Y de nuevo, los juegos son la excepción más usual en la que los gestos personalizados pueden agregar diversión y una reproducción envolvente al juego.
- En **caso de que sea necesario, responder a los grifos** de la cruceta: al pulsar ligeramente en los bordes de las esquinas de la superficie táctil, se reaccionará como un panel de la cruceta en un dispositivo de juego moviendo el foco o la dirección arriba, abajo, izquierda o derecha. Si procede, debe responder a estos gestos en su aplicación o juego.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri botones remotos

Además de los gestos en la superficie táctil, la aplicación puede responder al usuario haciendo clic en la superficie táctil o presionando el botón reproducir/pausar. Si tiene acceso al remoto de Siri mediante el marco del controlador de juegos, también puede detectar el botón de menú que se presiona.

Además, las pulsaciones de botones de menú se pueden detectar mediante un reconocedor de gestos con elementos estándar `UIKit` . Si intercepta el botón de menú que se está presionando, será responsable de cerrar la vista y el controlador de vista actuales y volver a la anterior.

> [!IMPORTANT]
> **Siempre** debe asignar una función al botón reproducir/pausar en el control remoto. Tener un botón no funcional puede hacer que la aplicación parezca interrumpida para el usuario final. Si no tiene una función válida para este botón, asigne la misma función que el botón principal (haga clic en la superficie táctil).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Gestos y guiones gráficos

La forma más fácil de trabajar con el remoto Siri en la aplicación de Xamarin. tvOS es agregar reconocedores de gestos a las vistas en el diseñador de la interfaz.

Para agregar un reconocedor de gestos, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo y ábralo para editar el diseñador de la interfaz.
2. Arrastre un **reconocedor de gestos de punteo** de la **biblioteca** y colóquelo en la vista:

    [![](remote-bluetooth-images/storyboard01.png "Reconocedor de gestos de TAP")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Active **la casilla de** verificación en la sección **botón** del **Inspector de atributos**:

    [![](remote-bluetooth-images/storyboard02.png "Activar selección")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Seleccione** significa que el gesto responderá al usuario haciendo clic en la **superficie táctil** en el Siri remoto. También tiene la opción de responder a los botones **menú**, **reproducir/pausar**, **arriba**, **abajo**, **izquierda** y **derecha** .
5. A continuación, conecte una **acción** del **reconocedor de gestos** de TAP `TouchSurfaceClicked`y llámela:

    [![](remote-bluetooth-images/storyboard03.png "Una acción del reconocedor de gestos de TAP")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac.

Edite el archivo del controlador `FirstViewController.cs`de vista (ejemplo) y agregue el código siguiente para controlar el movimiento que se desencadena:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md). Específicamente, la [creación de la interfaz de usuario](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) y [la escritura del código con secciones de salidas y acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) .

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Gestos y código

Opcionalmente, puede crear gestos directamente en C# el código y agregarlos a las vistas en la interfaz de usuario. Por ejemplo, para agregar una serie de reconocedores de gestos de deslizamiento, edite el controlador de vista y agregue el código siguiente:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>Control de eventos de bajo nivel

Si va a crear un tipo personalizado basado en `UIKit` en la aplicación Xamarin. tvOS (por ejemplo `UIView`,), también tiene la posibilidad de proporcionar un control de bajo nivel de la pulsación de botones a través `UIPress` de eventos.

Un `UIPress` evento es tvOS qué es un `UITouch` evento para iOS, excepto `UIPress` que devuelve información acerca de las pulsaciones de botones en el Siri remoto o en otros dispositivos Bluetooth conectados (como un dispositivo de juego). `UIPress`los eventos describen el botón que se está presionando y su estado (iniciado, cancelado, cambiado o finalizado).

En el caso de los botones analógicos de dispositivos `UIPress` como dispositivos de juegos Bluetooth, también devuelve la cantidad de fuerza que se aplica al botón. La `Type` propiedad`UIPress` del evento define qué botón físico cambió de estado, mientras que el resto de las propiedades describen el cambio que se ha producido.

En el código siguiente se muestra un ejemplo de control de `UIPress` eventos de bajo `UIView`nivel para un:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

Al igual `UITouch` que con los eventos, si necesita implementar cualquiera de `UIPress` las invalidaciones de eventos, debe implementar los cuatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Dispositivos de juegos Bluetooth

Además de los Siri de elementos remotos estándar que se distribuyen con Apple TV, un tercero realizado para dispositivos de juego Bluetooth de iOS (accesorios MFI) se pueden emparejar con Apple TV y usarse para controlar la aplicación Xamarin. tvOS.

[![](remote-bluetooth-images/game01.png "Dispositivos de juegos Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Los dispositivos de juego se pueden usar para mejorar el juego y proporcionar una sensación de inmersión en un juego. También se pueden usar para controlar la interfaz de Apple TV estándar, por lo que el uso no tiene que cambiar entre el dispositivo remoto y el controlador.

> [!IMPORTANT]
> Los dispositivos de juegos Bluetooth son una compra opcional que los usuarios finales pueden realizar, la aplicación no puede obligar al usuario a comprar uno. Si la aplicación es compatible con los dispositivos de juego, también debe admitir el Siri remoto para que todos los usuarios de Apple TV puedan usar el juego.

Un dispositivo de juego tiene las siguientes características y usos previstos en la aplicación tvOS:

|Característica|Uso general de la aplicación|Uso de la aplicación de juego|
|---|---|---|
|**PAD D**|Navega por los elementos de la interfaz de usuario (cambia el foco).|Depende del juego.|
|**A**|Activa el elemento seleccionado (enfocado).|Realiza la función de botón principal y confirma las acciones del cuadro de diálogo.|
|**B**|Vuelve a la pantalla anterior o sale de la pantalla principal si se encuentra en la pantalla principal de la aplicación.|Realiza la función de botón secundario o vuelve a la pantalla anterior.|
|**X**|Inicia la reproducción de medios o pausa/reanuda la reproducción.|Depende del juego.|
|**Y**|N/D|Depende del juego.|
|**Menu**|Vuelve a la pantalla anterior o sale de la pantalla principal si se encuentra en la pantalla principal de la aplicación.|Pausar o reanudar el juego, vuelve a la pantalla anterior o sale de la pantalla principal si se encuentra en la pantalla principal de la aplicación.|
|**Botón de hombro izquierdo**|Navega a la izquierda.|Depende del juego.|
|**Desencadenador izquierdo**|Navega a la izquierda.|Depende del juego.|
|**Botón derecho de hombro**|Navega hacia la derecha.|Depende del juego.|
|**Desencadenador derecho**|Navega a la derecha|Depende del juego.|
|**Stick analógico izquierdo**|Navega por los elementos de la interfaz de usuario (cambia el foco).|Depende del juego.|
|**Stick analógico derecho**|N/D|Depende del juego.|

Apple proporciona las siguientes sugerencias para trabajar con dispositivos de juego:

- **Confirmar las conexiones del dispositivo de juego** : el usuario final puede iniciar y detener la aplicación tvOS en cualquier momento. Siempre debe comprobar la presencia de un dispositivo de juego en los tiempos de inicio o de activación de la aplicación y tomar medidas según sea necesario.
- **Asegúrese de que la aplicación funciona en dispositivos de Siri remotos y de juegos** : no es necesario que los usuarios cambien entre el Siri remoto y un dispositivo de juego para usar la aplicación. Pruebe la aplicación a menudo con ambos tipos de controladores para asegurarse de que todo sea fácil de navegar y funcione según lo previsto.
- **Proporcionar una manera** de presionar la tecla de retroceso el botón de menú siempre debe volver a la pantalla anterior. Si el usuario está en la pantalla principal de la aplicación, el botón de menú debería devolverlos a la pantalla principal de Apple TV. Durante el juego, el botón de menú debería mostrar una alerta que proporcione al usuario la capacidad de pausar o reanudar el juego o volver al menú principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabajar con dispositivos de juego

Como se indicó anteriormente, además del siri remoto estándar que se distribuye con Apple TV, el usuario puede adjuntar opcionalmente un tercero, realizado para dispositivos de juego Bluetooth (accesorios MFI) de iOS y usarlo para controlar la aplicación Xamarin. tvOS.

Si la aplicación requiere una entrada de controlador de bajo nivel, puede usar el [marco de trabajo del controlador de juegos](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) de Apple, que tiene las siguientes modificaciones para tvOS:

- Se ha agregado el perfil micro`GCMicroGamepad`Game Controller () para tener como destino el Siri remoto.
- La nueva `GCEventViewController` clase se puede usar para enrutar eventos de controlador de juego a través de la aplicación. Vea la sección [determinar la entrada del controlador de juego](#determining-game-controller-input) a continuación para obtener más detalles.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de compatibilidad del controlador de juego

Apple tiene varios requisitos específicos que deben cumplirse si la aplicación de Xamarin. tvOS es compatible con los controladores de juegos:

- **Debe admitir el Siri remoto** : debe admitir siempre el Siri remoto. Tu juego no puede requerir que se pueda reproducir un controlador de juegos de terceros.
- **Debe admitir el diseño de control extendido** : todos los controladores de juegos de tvOS son controladores extendidos que no son de Formfitting.
- Los **juegos deben ser reproducibles con controladores independientes** : Si la aplicación es compatible con un controlador de juego extendido, debe reproducirse únicamente con ese dispositivo de juego.
- **Debe admitir el botón reproducir/pausar** : durante el juego, si el usuario presiona el botón reproducir/pausar, debería mostrar una alerta que le permita pausar o reanudar el juego o volver al menú principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitar la compatibilidad con el controlador de juego

Para habilitar la compatibilidad con los controladores de juegos en la aplicación Xamarin. tvOS, `Info.plist` haga doble clic en el archivo en el **Explorador de soluciones** para abrirlo para su edición:

[![](remote-bluetooth-images/game02.png "Editor de info. plist")](remote-bluetooth-images/game02.png#lightbox)

En la sección **controlador de juego** , active la casilla **Habilitar dispositivos**de juego y, a continuación, compruebe todos los tipos de dispositivos de juego que admitirá la aplicación.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Uso de Siri remoto como dispositivo de juego

El Siri remoto que se incluye con Apple TV puede usarse como un dispositivo de juego limitado. Al igual que otros dispositivos de juego, se muestra en el marco del controlador `GCController` del juego como un objeto `GCMotion` y admite `GCMicroGamepad` los perfiles y.

El control remoto Siri tiene las siguientes características cuando se usa como dispositivo de juego:

- La superficie táctil se puede usar como un panel de D que proporciona datos de entrada analógicos.
- El control remoto se puede usar en una orientación vertical u horizontal, y la aplicación decide si el objeto de perfil debe voltear los datos de entrada automáticamente.
- Hacer clic en la superficie táctil actúa como presionar el botón **a** en un dispositivo de juego.
- El botón reproducir/pausa actúa como botón **X** en un dispositivo de juego.
- El botón de menú debería mostrar una alerta que le permita pausar o reanudar el juego o volver al menú principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinar la entrada del controlador de juego

A diferencia de iOS, donde los eventos del controlador de juego se pueden recibir en paralelo con eventos táctiles, tvOS procesa todos los eventos de `UIKit` bajo nivel para proporcionar eventos de alto nivel. Como resultado, si necesita tener acceso a los eventos del controlador de juegos de bajo nivel, deberá desactivar `UIKit`el comportamiento predeterminado de.

En tvOS, si desea procesar la entrada del controlador de juego directamente, debe usar una `GCEventViewController` (o una subclase) para mostrar la interfaz de usuario del juego. Siempre que `GCEventViewController` un sea el *primer respondedor*, la entrada del controlador de juego se capturará y entregará a la aplicación a través del marco del controlador de juegos.

Puede usar la `UserInteractionEnabled` propiedad de la `GCEventViewController` clase para alternar el modo en que se procesan y controlan los eventos.

Para obtener información acerca de la implementación de la compatibilidad con los dispositivos de juego, consulte la sección [trabajar con controladores de juegos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) de Apple en la [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) y [Guía de programación del controlador de juegos](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el nuevo Siri remoto que se incluye con Apple TV, gestos táctiles y botones remotos de Siri. A continuación, se ha tratado el trabajo con gestos y guiones gráficos, gestos y eventos de bajo nivel. Por último, si se describe cómo trabajar con dispositivos de juego.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
