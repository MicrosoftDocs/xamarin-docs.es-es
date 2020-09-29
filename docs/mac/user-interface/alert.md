---
title: Alertas en Xamarin. Mac
description: En este artículo se explica cómo trabajar con alertas en una aplicación de Xamarin. Mac. Describe cómo crear y mostrar las alertas del código de C# y responder a las interacciones del usuario.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 55c4d4e6154ef75f15b2e75edc020b7cb4c96e38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435309"
---
# <a name="alerts-in-xamarinmac"></a>Alertas en Xamarin. Mac

_En este artículo se explica cómo trabajar con alertas en una aplicación de Xamarin. Mac. Describe cómo crear y mostrar las alertas del código de C# y responder a las interacciones del usuario._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a las mismas alertas que un desarrollador que trabaja en *Objective-C* y *Xcode* . 

Una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (como un error) o como una advertencia (como preparar la eliminación de un archivo). Dado que una alerta es un diálogo, también requiere una respuesta de usuario antes de que se pueda cerrar.

[![Una alerta de ejemplo](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

En este artículo, trataremos los conceptos básicos sobre cómo trabajar con alertas en una aplicación de Xamarin. Mac. 

<a name="Introduction_to_Alerts"></a>

## <a name="introduction-to-alerts"></a>Introducción a las alertas

Una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (como un error) o como una advertencia (como preparar la eliminación de un archivo). Dado que las alertas interrumpen al usuario, ya que se deben descartar antes de que el usuario pueda continuar con su tarea, evite mostrar una alerta a menos que sea absolutamente necesario.

Apple sugiere las siguientes directrices:

- No use una alerta simplemente para proporcionar información a los usuarios.
- No mostrar una alerta para las acciones comunes que se puedan deshacer. Incluso si esta situación puede provocar la pérdida de datos.
- Si una situación es merecente de una alerta, evite usar cualquier otro elemento o método de la interfaz de usuario para mostrarla.
- El icono de precaución debe usarse con moderación.
- Describir la situación de alerta de forma clara y concisa en el mensaje de alerta.
- El nombre del botón predeterminado debe corresponder a la acción que se describe en el mensaje de alerta.

Para obtener más información, consulte la sección de [alertas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) de las directrices de la [interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) .

<a name="Anatomy_of_an_Alert"></a>

## <a name="anatomy-of-an-alert"></a>Anatomía de una alerta

Como se indicó anteriormente, se deben mostrar alertas al usuario de la aplicación cuando se produce un problema grave o como una advertencia de posible pérdida de datos (como cerrar un archivo no guardado). En Xamarin. Mac, se crea una alerta en el código de C#, por ejemplo:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Critical,
  InformativeText = "We need to save the document here...",
  MessageText = "Save Document",
};
alert.RunModal ();
```

El código anterior muestra una alerta con el icono de aplicaciones superpuesto en el icono de advertencia, un título, un mensaje de advertencia y un solo botón **Aceptar** :

[![Una alerta con un botón Aceptar](alert-images/alert01.png)](alert-images/alert01.png#lightbox)

Apple proporciona varias propiedades que se pueden usar para personalizar una alerta:

- **AlertStyle** define el tipo de una alerta como una de las siguientes:
  - **ADVERTENCIA** : se usa para advertir al usuario de un evento actual o inminente que no es crítico. Éste es el estilo predeterminado.
  - **Informativo** : se usa para advertir al usuario sobre un evento actual o inminente. Actualmente, no hay ninguna diferencia visible entre una **ADVERTENCIA** y una **información**
  - **Crítico** : se usa para advertir al usuario sobre las consecuencias graves de un evento inminente (como la eliminación de un archivo). Este tipo de alerta debe usarse con moderación.
- **MessageText** : este es el mensaje principal o el título de la alerta y debe definir rápidamente la situación al usuario.
- **InformativeText** : este es el cuerpo de la alerta en el que debe definir la situación claramente y presentar las opciones que sean factibles al usuario.
- **Icono** : permite mostrar un icono personalizado al usuario.
- **HelpAnchor**  &  **ShowsHelp** : permite asociar la alerta a la HelpBook de la aplicación y mostrar la ayuda de la alerta.
- **Botones** : de forma predeterminada, una alerta solo tiene el botón **Aceptar** , pero la colección **botones** permite agregar más opciones según sea necesario.
- **ShowsSuppressionButton** : Si `true` muestra una casilla que el usuario puede usar para suprimir la alerta de las repeticiones posteriores del evento que la desencadenó.
- **AccessoryView** : permite adjuntar otra subvista a la alerta para proporcionar información adicional, como agregar un **campo de texto** para la entrada de datos. Si establece un nuevo **AccessoryView** o modifica uno existente, debe llamar al `Layout()` método para ajustar el diseño visible de la alerta.

<a name="Displaying_an_Alert"></a>

## <a name="displaying-an-alert"></a>Mostrar una alerta

Hay dos maneras diferentes de mostrar una alerta: flotante o en una hoja. En el código siguiente se muestra una alerta como flotante libre:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.RunModal ();
```

Si se ejecuta este código, se muestra lo siguiente:

[![Una alerta simple](alert-images/alert02.png)](alert-images/alert02.png#lightbox)

En el código siguiente se muestra la misma alerta que una hoja:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Si se ejecuta este código, se mostrará lo siguiente:

[![Una alerta que se muestra como una hoja](alert-images/alert03.png)](alert-images/alert03.png#lightbox)

<a name="Working_with_Alert_Buttons"></a>

## <a name="working-with-alert-buttons"></a>Trabajar con botones de alerta

De forma predeterminada, una alerta muestra solo el botón **Aceptar** . Sin embargo, no se limita a eso, se pueden crear botones adicionales si se anexan a la colección de **botones** . En el código siguiente se crea una alerta de nivel flotante con un botón **Aceptar**, **Cancelar** y **maybe** :

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

El primer botón que se agregue será el _botón predeterminado_ que se activará si el usuario presiona la tecla entrar. El valor devuelto será un entero que represente el botón que el usuario presionó. En nuestro caso, se devolverán los valores siguientes:

- **Correcto** : 1000.
- **Cancele** -1001.
- **Quizás** -1002.

Si ejecutamos el código, se mostrará lo siguiente:

[![Una alerta con tres opciones de botón](alert-images/alert04.png)](alert-images/alert04.png#lightbox)

Este es el código de la misma alerta que una hoja:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}", result);
});
```

Si se ejecuta este código, se mostrará lo siguiente:

[![Una alerta de tres botones que se muestra como una hoja](alert-images/alert05.png)](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Nunca debe agregar más de tres botones a una alerta.

<a name="Showing_the_Suppress_Button"></a>

## <a name="showing-the-suppress-button"></a>Mostrar el botón suprimir

Si la propiedad de la alerta `ShowSuppressButton` es `true` , la alerta muestra una casilla que el usuario puede utilizar para suprimir la alerta para las repeticiones posteriores del evento que la desencadenó. En el código siguiente se muestra una alerta de libre flotante con un botón suprimir:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Si el valor de `alert.SuppressionButton.State` es `NSCellStateValue.On` , el usuario ha activado la casilla suprimir, de lo contrario, no.

Si el código se ejecuta, se mostrará lo siguiente:

[![Una alerta con un botón suprimir](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

Este es el código de la misma alerta que una hoja:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si se ejecuta este código, se mostrará lo siguiente:

[![Una alerta con un botón suprimir Mostrar como una hoja](alert-images/alert07.png)](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView"></a>

## <a name="adding-a-custom-subview"></a>Agregar una subvista personalizada

Las alertas tienen una `AccessoryView` propiedad que se puede usar para personalizar más la alerta y agregar elementos como un **campo de texto** para la entrada del usuario. En el código siguiente se crea una alerta de libre flotante con un campo de entrada de texto agregado:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Las líneas clave aquí son `var input = new NSTextField (new CGRect (0, 0, 300, 20));` , que crea un nuevo **campo de texto** al que se agregará la alerta. `alert.AccessoryView = input;` que adjunta el **campo de texto** a la alerta y la llamada al `Layout()` método, que es necesaria para cambiar el tamaño de la alerta de forma que quepa en la nueva subvista.

Si ejecutamos el código, se mostrará lo siguiente:

[![Si ejecutamos el código, se mostrará lo siguiente:](alert-images/alert08.png)](alert-images/alert08.png#lightbox)

Esta es la misma alerta que una hoja:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si ejecutamos este código, se mostrará lo siguiente:

[![Una alerta con una vista personalizada](alert-images/alert09.png)](alert-images/alert09.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con alertas en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de las alertas, cómo crear y personalizar alertas y cómo trabajar con alertas en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)