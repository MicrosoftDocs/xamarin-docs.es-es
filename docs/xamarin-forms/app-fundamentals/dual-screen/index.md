---
title: Doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo crear aplicaciones de Xamarin.Forms para dispositivos de doble pantalla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a414127c01d26db6bf7b462d6fc5a7f9ae44dddc
ms.sourcegitcommit: 69bd0fdc698c9b0c0d73217776d7084f32ae88ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832285"
---
# <a name="no-locxamarinforms-dual-screen"></a>Doble pantalla de Xamarin.Forms

Los dispositivos de doble pantalla como Microsoft Surface Duo ofrecen nuevas posibilidades de experiencia del usuario para las aplicaciones. Xamarin.Forms incluye las clases `TwoPaneView` y `DualScreenInfo` para que pueda desarrollar aplicaciones para dispositivos de doble pantalla.

## <a name="get-started"></a>Primeros pasos

Siga estos pasos para agregar funcionalidades de doble pantalla a una aplicación Xamarin.Forms:

1. Abra el cuadro de diálogo **Administrador de paquetes de NuGet** para su solución.
2. En la pestaña **Examinar**, busque `Xamarin.Forms.DualScreen`.
3. Instale el paquete de `Xamarin.Forms.DualScreen` en la solución.
4. Agregue la siguiente llamada al método de inicialización a la clase `MainActivity` del proyecto de Android, en el evento `OnCreate`:

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    Este método es necesario para que la aplicación pueda detectar cambios en el estado de la aplicación, como el hecho de abarcar un espacio de dos pantallas.

5. Actualice el atributo `Activity` en la clase `MainActivity` del proyecto de Android, de modo que incluya _todas_ estas opciones de `ConfigurationChanges`:

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    Estos valores son necesarios para que los cambios de configuración y el espacio abarcado se puedan notificar de forma más confiable. De forma predeterminada, solo dos se agregan a los proyectos de Xamarin.Forms, por lo que no olvide agregar el resto para ofrecer compatibilidad de doble pantalla confiable.

## <a name="troubleshooting"></a>Solución de problemas

Si la clase `DualScreenInfo` o el diseño `TwoPaneView` no funcionan del modo esperado, revise de nuevo las instrucciones de configuración de esta página. La omisión o la incorrecta configuración del método `Init` o de los valores de atributo de `ConfigurationChanges` son causas comunes de error.

Revise los [ejemplos de doble pantalla de Xamarin.Forms](https://docs.microsoft.com/dual-screen/xamarin/samples) para obtener orientación adicional y una implementación de referencia.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya agregado NuGet, agregue características de doble pantalla a la aplicación con las siguientes instrucciones:

- [Modelos de diseño de doble pantalla](design-patterns.md): al considerar el uso óptimo de varias pantallas en un dispositivo de doble pantalla, consulte estas instrucciones de patrones para encontrar la mejor opción para la interfaz de su aplicación.
- [Diseño TwoPaneView](twopaneview.md): la clase `TwoPaneView` de Xamarin.Forms, inspirada en el control UWP del mismo nombre, es un diseño multiplataforma optimizado para los dispositivos de pantalla doble.
- [Clase auxiliar DualScreenInfo](dual-screen-info.md): la clase `DualScreenInfo` permite determinar en qué panel se encuentra la vista, qué tamaño tiene, en qué posición está el dispositivo, el ángulo de la bisagra y mucho más.
- [Desencadenadores de pantalla doble](triggers.md): el espacio de nombres de [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) incluye dos desencadenadores de estado que desencadenan un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando varía el modo de visualización del diseño adjunto o de la ventana.

Para obtener más información, consulte la [documentación sobre la doble pantalla para desarrolladores](https://docs.microsoft.com/dual-screen/).
