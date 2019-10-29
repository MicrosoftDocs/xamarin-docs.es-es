---
title: Multitarea para iPad en Xamarin. iOS
description: iOS 9 admite dos aplicaciones que se ejecutan al mismo tiempo, usando la vista de diapositiva o de división. También admite la reproducción de vídeo de imagen.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: aeb3d01a3d0f7edbe92c9959073d859fc63486a6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031651"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitarea para iPad en Xamarin. iOS

_iOS 9 admite dos aplicaciones que se ejecutan al mismo tiempo, usando la vista de diapositiva o de división. También admite la reproducción de vídeo de imagen._

![](multitasking-images/about02-sml.png "Ejemplo de pantalla dividida") ![](multitasking-images/about03-sml.png "Ejemplo de imagen en imagen")

iOS 9 agrega compatibilidad con la multitarea para ejecutar dos aplicaciones al mismo tiempo en hardware de iPad específico. La multitarea se admite a través de las siguientes características:

- [**Deslizar sobre**](#Slide-Over) : permite al usuario ejecutar temporalmente una segunda aplicación iOS en un panel deslizante (en el lado derecho o izquierdo de la pantalla en función de la dirección del idioma) que cubre aproximadamente el 25% de la aplicación principal que se está ejecutando actualmente. La diapositiva solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.
- [**Vista en dos paneles**](#Split-View) : en el hardware de iPad compatible (solo iPad Air 2, iPad Mini 4 y iPad Pro), el usuario puede elegir una segunda aplicación y ejecutarla en paralelo con la aplicación que se está ejecutando en el modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación.
- [**Imagen**](#Picture-in-Picture) : para las aplicaciones que reproducen el contenido de vídeo, el vídeo ahora se puede reproducir en una ventana móvil y de tamaño variable que flota por las demás aplicaciones que se ejecutan actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y la posición de esta ventana. Imagen en imagen solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.

Hay varios aspectos que se deben tener en cuenta a [la hora de admitir la multitarea en la aplicación](#Supporting-Multitasking-in-your-App), entre las que se incluyen:

- [Tamaño y orientación de la pantalla](#Screen-Size-Considerations)
- [Métodos abreviados de teclado de hardware personalizado](#Custom-Hardware-Keyboard-Shortcuts)
- [Administración de recursos](#Resource-Management-Considerations)

Como desarrollador de aplicaciones, también puede [rechazar la multitarea](#Opting-Out-of-Multitasking), incluida la [deshabilitación de la reproducción de vídeo PIP](#Disabling-PIP-Video-Playback).

En este artículo se explican los pasos necesarios para asegurarse de que la aplicación de Xamarin. iOS se ejecuta correctamente en un entorno de multitarea o de cómo no participar en la multitarea si no es una buena opción para la aplicación.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Vídeo de multitarea para iPad**

<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Inicio rápido de multitarea

Para admitir la visualización de **diapositivas** o de **vista dividida** , la aplicación debe hacer lo siguiente:

- Crearse con iOS 9 (o superior).
- Use un guion gráfico para su pantalla de inicio (y no recursos de imagen).
- Use un guion gráfico con las clases de diseño automático y tamaño para su interfaz de usuario.
- Compatibilidad con las cuatro orientaciones de dispositivos iOS (vertical, vertical, vertical, horizontal a la izquierda & horizontal derecha).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Acerca de la multitarea para iPad

iOS 9 ofrece nuevas capacidades de multitarea en iPad con la introducción de _diapositivas_, _vista en dos paneles_ (solo iPad Air 2, iPad Mini 4 y iPad Pro) e _imagen en imagen_. Veremos más detenidamente estas características en las secciones siguientes.

<a name="Slide-Over" />

### <a name="slide-over"></a>Deslizar sobre

La característica de deslizamiento permite al usuario elegir una segunda aplicación y mostrarla en un pequeño panel deslizante para proporcionar una interacción rápida. El panel deslizante es temporal y se cerrará cuando el usuario vuelva a trabajar con la aplicación principal de nuevo.

[![](multitasking-images/about01.png "The Slide Over panel")](multitasking-images/about01.png#lightbox)

Lo principal que hay que recordar es que el usuario decide qué dos aplicaciones se ejecutarán en paralelo y que el desarrollador no tiene ningún control sobre este proceso. Como resultado, hay algunas cosas que debe hacer para asegurarse de que la aplicación de Xamarin. iOS se ejecuta correctamente en un panel deslizante:

- **Usar las clases de diseño automático y tamaño** : dado que la aplicación de Xamarin. iOS se puede ejecutar ahora en el panel lateral de deslizamiento, ya no puede confiar en el dispositivo, su tamaño de pantalla ni su orientación para diseñar la interfaz de usuario. Para asegurarse de que la aplicación escale correctamente la interfaz, tendrá que usar las clases de diseño automático y tamaño. Para obtener más información, consulte la documentación [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Usar recursos de manera eficaz** : dado que la aplicación ahora puede compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación use los recursos del sistema de forma eficaz. Cuando la memoria se vuelve dispersa, el sistema finalizará automáticamente la aplicación que consume más memoria. Para más información, consulte la [Guía de eficiencia energética](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) de Apple para aplicaciones iOS.

La diapositiva solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4. Para obtener más información sobre la preparación de la aplicación para la desactivación, consulte la documentación sobre la adopción de las [mejoras en la multitarea en iPad de](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) Apple.

<a name="Split-View" />

### <a name="split-view"></a>Vista dividida

En el hardware de iPad compatible (solo iPad Air 2, iPad Mini 4 y iPad Pro), el usuario puede elegir una segunda aplicación y ejecutarla en paralelo con la aplicación que se está ejecutando en el modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación arrastrando un divisor en pantalla.

[![](multitasking-images/about02.png "The Split View")](multitasking-images/about02.png#lightbox)

Al igual que la diapositiva, el usuario decide qué dos aplicaciones se van a ejecutar en paralelo y de nuevo, el desarrollador no tiene ningún control sobre este proceso. Como resultado, la vista en dos paneles coloca requisitos similares en una aplicación de Xamarin. iOS:

- **Usar las clases de diseño automático y tamaño** : dado que la aplicación de Xamarin. iOS se puede ejecutar ahora en modo de pantalla dividida con el tamaño especificado por el usuario, ya no se puede confiar en el dispositivo, su tamaño de pantalla ni su orientación para diseñar la interfaz de usuario. Para asegurarse de que la aplicación escale correctamente la interfaz, tendrá que usar las clases de diseño automático y tamaño. Para obtener más información, consulte la documentación [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
- **Usar recursos de manera eficaz** : dado que la aplicación ahora puede compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación use los recursos del sistema de forma eficaz. Cuando la memoria se vuelve dispersa, el sistema finalizará automáticamente la aplicación que consume más memoria. Para más información, consulte la [Guía de eficiencia energética](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) de Apple para aplicaciones iOS.

Para obtener más información sobre la preparación de la aplicación para la vista en dos paneles, consulte la documentación sobre la adopción de las [mejoras en la multitarea en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) de Apple.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Imagen en imagen

La nueva característica de imagen en imagen (también conocida como _PIP_) permite al usuario ver un vídeo en una ventana pequeña y flotante que el usuario puede colocar en cualquier parte de la pantalla sobre otras aplicaciones en ejecución.

[![](multitasking-images/about03.png "An example Picture in Picture floating window")](multitasking-images/about03.png#lightbox)

Al igual que con las vistas de diapositiva y de división, el usuario tiene control total sobre la visualización de un vídeo en el modo de imagen. Si la función principal de la aplicación es ver vídeo, necesitará alguna modificación para que se comporte correctamente en el modo PIP. De lo contrario, no es necesario realizar ningún cambio para admitir PIP.

Para que la aplicación muestre el vídeo de PIP en la solicitud del usuario, debe usar _AVKit_ o las _API de AV Foundation_. El marco de Media Player se ha depreciado en iOS 9 y no es compatible con PIP.

Imagen en imagen solo está disponible en un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4. Para obtener más información, consulte la [aplicación de ejemplo PictureInPicture](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) y la [imagen de Apple en Picture Inicio rápido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentación.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Compatibilidad con la multitarea en la aplicación

En el caso de las aplicaciones de Xamarin. iOS existentes, la compatibilidad con la multitarea es una tarea transparente siempre y cuando la aplicación ya siga las guías de diseño de Apple y los procedimientos recomendados para iOS 8. Esto significa que la aplicación debe usar guiones gráficos con clases de diseño automático y tamaño para sus diseños de interfaz de usuario (vea nuestra [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información).

Para estas aplicaciones, se requieren pocos cambios o ningún cambio para admitir la multitarea y para que se comporten correctamente dentro de ella. Si la interfaz de usuario de la aplicación se creó con otros métodos, como el posicionamiento directo y el C# ajuste de tamaño de los elementos de la interfaz de usuario en el código o si se basa en tamaños de pantalla o orientaciones de dispositivo específicos, se necesitará una modificación importante para admitir correctamente la multitarea de iOS 9.

Para admitir la multitarea de iOS 9 en cualquier nueva aplicación de Xamarin. iOS, use guiones gráficos con las clases de diseño automático y tamaño para todos los diseños de interfaz de usuario de la aplicación e implemente las instrucciones en las secciones siguientes.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Consideraciones sobre el tamaño y la orientación de la pantalla

Antes de iOS 9, podría diseñar la aplicación con tamaños y orientaciones de pantalla específicos del dispositivo. Dado que una aplicación ahora puede ejecutarse en un panel desplazable o en el modo de vista en dos paneles, puede encontrarse en ejecución en una clase de tamaño horizontal compacta o normal en iPad, independientemente de la orientación física o el tamaño de pantalla del dispositivo.

[![](multitasking-images/sizeclasses01.png "Screen Size and Orientation Considerations")](multitasking-images/sizeclasses01.png#lightbox)

En un iPad, una aplicación de pantalla completa tiene clases de tamaño horizontal y vertical normal. Todos los iPhone, pero iPhone 6 Plus y iPhone 6S Plus, tienen clases de tamaño compacto en ambas direcciones en cualquier orientación. Los dispositivos iPhone 6 Plus y iPhone 6S Plus en modo horizontal tienen una clase de tamaño horizontal normal y una clase de tamaño vertical compacto (muy similar a un iPad mini).

En iPad que admiten diapositivas y la vista en dos paneles, puede acabar con las siguientes combinaciones:

| **Orientación** | **Aplicación principal** | **Aplicación secundaria** |
|--- |--- |--- |
| **Vertical** |75% de la pantalla<br />Horizontal compacta<br />Vertical normal|25% de la pantalla<br />Horizontal compacta<br />Vertical normal|
| **Horiz** |75% de la pantalla<br />Horizontal normal<br />Vertical normal|25% de la pantalla<br />Horizontal compacta<br />Vertical normal|
| **Horiz** |50% de la pantalla<br />Horizontal compacta<br />Vertical normal|50% de la pantalla<br />Horizontal compacta<br />Vertical normal|

En la aplicación de [MuliTask](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) de ejemplo, si se ejecuta en pantalla completa en un iPad en el modo horizontal, presentará la lista y la vista de detalle al mismo tiempo:

[![](multitasking-images/sizeclasses03.png "The list and the detail view presented at the same time")](multitasking-images/sizeclasses03.png#lightbox)

Si se ejecuta la misma aplicación en una diapositiva sobre el panel, se diseña como una clase de tamaño horizontal compacta y muestra solo la lista:

[![](multitasking-images/sizeclasses04.png "Only the list presented when the device is horizontal")](multitasking-images/sizeclasses04.png#lightbox)

Para asegurarse de que la aplicación se comporta correctamente en estas situaciones, debe adoptar las colecciones de rasgos junto con las clases de tamaño y ajustarse a las interfaces `IUIContentContainer` y `IUITraitEnvironment`. Consulte la referencia de la [clase UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) de Apple y la guía de [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información.

Además, ya no puede confiar en los límites de pantalla de los dispositivos para definir el área visible de la aplicación; en su lugar, deberá usar los límites de la ventana de la aplicación. Dado que los límites de la ventana están totalmente bajo el control del usuario, no se pueden ajustar mediante programación ni evitar que el usuario cambie estos límites.

Por último, la aplicación debe usar un archivo de guion gráfico para presentar su pantalla de inicio en lugar de usar un conjunto de archivos de imagen **. png** y admitir las cuatro orientaciones de interfaz (vertical, vertical, vertical, horizontalmente a la izquierda y horizontal derecha) que se deben tener en cuenta para ejecutar en un panel deslizante o en el modo de vista en dos paneles.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Métodos abreviados de teclado de hardware personalizado

En iOS 9 que se ejecuta en un iPad, Apple ha ampliado la compatibilidad con los teclados de hardware. iPad siempre incluía compatibilidad básica con los teclados externos a través de Bluetooth y algunos fabricantes de teclado crearon teclados que incluían claves específicas de iOS cableadas.

Ahora, con iOS 9, las aplicaciones pueden crear sus propios métodos abreviados de teclado personalizados. Además, algunos métodos abreviados de teclado básicos están disponibles como **Command-C** (copiar), **Command-X** (CUT), **Command-V** (Paste) y **Command-Shift-H** (Home), sin que una aplicación que se escribe específicamente responda a ellos.

La **pestaña de comandos** abrirá un conmutador de aplicación que permite al usuario cambiar rápidamente entre las aplicaciones del teclado, de forma muy parecida a la Mac OS:

[![](multitasking-images/keyboard01.png "The app switcher")](multitasking-images/keyboard01.png#lightbox)

Si una aplicación de iOS 9 incluye métodos abreviados de teclado, el usuario puede mantener presionadas las teclas **comando**, **opción** o **control** para mostrarlas en un elemento emergente:

[![](multitasking-images/keyboard02.png "The keyboard shortcuts popup")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definición de métodos abreviados de teclado personalizados

Si agregamos el código siguiente a un controlador de vista o vista de nuestra aplicación, cuando esa vista o controlador esté visible, habrá disponible un método abreviado de teclado personalizado:

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

En primer lugar, reemplazamos la propiedad `CanBecomeFirstResponder` y devuelven `true` para que el controlador de vista o vista pueda recibir entradas del teclado. 

A continuación, se invalida la propiedad `KeyCommands` y se crea un nuevo `UIKeyCommand` para la pulsación de tecla **comando N** . Cuando se activa la pulsación de tecla, llamamos al método `NewEntry` (que exponemos a iOS 9 mediante el comando `Export`) para realizar la acción solicitada.

Si ejecutamos esta aplicación en un iPad con un teclado de hardware conectado y el comando Types **-N**de usuario, se agregará una nueva entrada a la lista. Si el usuario mantiene presionada la tecla de **comando** , se mostrará la lista de accesos directos:

[![](multitasking-images/keyboard03.png "The keyboard shortcuts popup")](multitasking-images/keyboard03.png#lightbox)

Vea la [aplicación](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask) de ejemplo para obtener una implementación de ejemplo.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Consideraciones sobre la administración de recursos

Incluso en el caso de las aplicaciones que ya usan las guías de diseño y los procedimientos recomendados de iOS 8, la administración eficaz de recursos puede seguir siendo un problema. En iOS 9, las aplicaciones ya no tienen uso exclusivo de memoria, CPU u otros recursos del sistema.

Como resultado, debe ajustar la aplicación de Xamarin. iOS para usar los recursos del sistema de forma eficaz o afrontar la terminación en situaciones de poca memoria. Esto es igualmente cierto en el caso de las aplicaciones que no pueden usar la multitarea, ya que es posible que una segunda aplicación se siga ejecutando en un panel deslizante o en una imagen en una ventana de imagen que requiera recursos adicionales o que la frecuencia de actualización quede por debajo de 60 fotogramas por segundo.

Tenga en cuenta las siguientes acciones del usuario y sus implicaciones:

- **Escribir texto en una diapositiva sobre el panel** : aunque la aplicación no tenga entrada de texto, el teclado del sistema ahora se puede mostrar sobre la interfaz de usuario. Como resultado, es posible que la aplicación tenga que responder a las notificaciones de pantalla del teclado (como mostrar y ocultar el teclado).
- **Ejecutar una segunda aplicación en una diapositiva en el panel** : la nueva aplicación ahora se está ejecutando en primer plano y compite con la aplicación existente para los recursos del sistema, como la memoria y los ciclos de la CPU.
- **Reproducir un vídeo en una ventana de PIP** : no solo es posible que esta ventana cubra parte de la interfaz de la aplicación, pero la aplicación que inició el vídeo sigue ejecutándose en segundo plano y consumiendo recursos de CPU y memoria.

Para asegurarse de que la aplicación usa los recursos de forma eficaz, debe hacer lo siguiente:

- **Generar perfiles de la aplicación con Instruments** : Compruebe si hay pérdidas de memoria, el uso de la CPU de manifiesto y las áreas en las que la aplicación podría estar bloqueando el subproceso principal.
- **Responda a los métodos de transiciones de estado** : en la invalidación del archivo **AppDelegate.CS** y la respuesta a métodos de cambio de estado, como la aplicación que entra o se devuelve desde el fondo. Libere los recursos no necesarios, como imágenes, datos o vistas, y el controlador de vista.
- **Pruebas en paralelo con aplicaciones con un uso intensivo de memoria** : ejecute la aplicación con las opciones de deslizamiento y la vista en dos paneles del hardware de iOS físico con una aplicación que consume mucha memoria, como Maps (en el modo de vista satélite), y compruebe que ambas aplicaciones siguen respondiendo y no se bloquean.

Para más información sobre la administración de recursos, consulte la [Guía de eficiencia energética](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) de Apple para aplicaciones iOS.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>No participar en la multitarea

Aunque Apple recomienda que todas las aplicaciones de iOS 9 admitan la multitarea, es posible que no se necesiten también motivos específicos para una aplicación, como juegos o aplicaciones de cámara que requieren que la pantalla completa funcione correctamente.

Para que la aplicación de Xamarin. iOS no se ejecute en un panel deslizante o en el modo de vista en dos paneles, edite el archivo **info. plist** del proyecto y active la casilla **requiere pantalla completa**:

[![](multitasking-images/fullscreen01.png "Opting Out of Multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Aunque no participar en la multitarea impide que la aplicación se ejecute en la vista de deslizamiento o en la vista en dos paneles, no impide que otra aplicación se ejecute en un gráfico deslizante o se muestre una imagen en el vídeo de la imagen junto con la aplicación.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Deshabilitación de la reproducción de vídeo PIP

En la mayoría de los casos, la aplicación debe permitir al usuario reproducir el contenido de vídeo que se muestra en la ventana flotante de imagen en una imagen. Sin embargo, puede haber situaciones en las que esto podría no ser deseable, como los vídeos de escenas de corte de juegos.

Para no participar en la reproducción de vídeo PIP, haga lo siguiente en la aplicación:

- Si usa un `AVPlayerViewController` para mostrar vídeo, establezca la propiedad `AllowsPictureInPicturePlayback` en `false`.
- Si usa el `AVPlayerLayer` para mostrar el vídeo, no cree instancias de un `AVPictureInPictureController`.
- Si usa un `WKWebView` para mostrar vídeo, establezca la propiedad `AllowsPictureInPictureMediaPlayback` en `false`.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han explicado los pasos necesarios para asegurarse de que una aplicación de Xamarin. iOS se ejecutará y se comportará correctamente en la nueva capacidad de multitarea de iOS 9 para iPad. Además, se ha incluido la aceptación de la multitarea para las aplicaciones en las que no es una buena opción.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Multitarea (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)
- [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adopción de mejoras de multitarea en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Entrada de blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
