---
title: Trabajar con alertas de tvOS en Xamarin
description: En este documento se describe cómo trabajar con alertas de tvOS en Xamarin. Describe cómo mostrar una alerta, agregar campos de texto y una clase auxiliar.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: de7c8918ff500cb2353214fd84eaa4c97713493e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227267"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Trabajar con alertas de tvOS en Xamarin

_En este artículo se describe cómo trabajar con UIAlertController para mostrar un mensaje de alerta al usuario en Xamarin. tvOS._

Si necesita obtener la atención del usuario tvOS o solicitar permiso para realizar una acción destructiva (por ejemplo, eliminar un archivo), puede presentar un mensaje de alerta mediante `UIAlertViewController`:

[![](alerts-images/alert01.png "Un ejemplo de UIAlertViewController")](alerts-images/alert01.png#lightbox)

Si además de mostrar un mensaje, puede agregar botones y campos de texto a una alerta para que el usuario pueda responder a las acciones y proporcionar comentarios.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Acerca de las alertas

Como se indicó anteriormente, las alertas se usan para obtener la atención del usuario e informarles del estado de la aplicación o solicitar comentarios. Las alertas deben presentar un título; opcionalmente, pueden tener un mensaje y uno o varios botones o campos de texto.

[![](alerts-images/alert04.png "Una alerta de ejemplo")](alerts-images/alert04.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con alertas:

- **Usar alertas** con moderación: las alertas interrumpen el flujo del usuario con la aplicación e interrumpen la experiencia del usuario y, por lo tanto, solo se deben usar en situaciones importantes como notificaciones de error, compras desde la aplicación y acciones destructivas.
- **Proporciona opciones útiles** : Si la alerta presenta opciones al usuario, debe asegurarse de que cada opción ofrezca información crítica y proporcione acciones útiles para que el usuario las realice.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Títulos y mensajes de alerta

Apple tiene las siguientes sugerencias para presentar el título de una alerta y el mensaje opcional:

- **Usar títulos** de varias palabras: el título de una alerta debe obtener el punto de la situación en todo momento y, al mismo tiempo, seguir siendo sencillo. Un solo título de palabra suele proporcionar información suficiente.
- **Use títulos descriptivos que no requieran un mensaje** : siempre que sea posible, considere la posibilidad de hacer que el título de la alerta sea lo suficientemente descriptivo como para que el texto del mensaje opcional no sea necesario.
- **Haga que el mensaje sea una oración corta y completa** : Si el mensaje opcional es necesario para obtener el punto de la alerta, manténgalo lo más simple posible y conviértalo en una oración completa con las mayúsculas y minúsculas adecuadas.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Botones de alerta

Apple tiene la siguiente sugerencia para agregar botones a una alerta:

- **Limite dos botones** : siempre que sea posible, limite la alerta a un máximo de dos botones. Las alertas de un solo botón proporcionan información pero ninguna acción. Dos alertas de botón proporcionan una opción de acción sí/no simple.
- **Use títulos de botón lógicos y concisos** : sencillos de uno a dos títulos de botón de Word que describen claramente el funcionamiento óptimo de la acción del botón. Para obtener más información, consulte nuestra documentación sobre [Cómo trabajar con los botones](~/ios/tvos/user-interface/buttons.md) .
- **Marque claramente** los botones destructivos: los botones que realizan una acción destructiva (como eliminar un archivo) los marcan claramente con `UIAlertActionStyle.Destructive` el estilo.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Mostrar una alerta

Para mostrar una alerta, cree una instancia de `UIAlertViewController` y configúrela agregando acciones (botones) y seleccionando el estilo de la alerta. Por ejemplo, en el código siguiente se muestra una alerta de aceptar o cancelar:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...

var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Echemos un vistazo a este código en detalle. En primer lugar, se crea una nueva alerta con el título y el mensaje especificados:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Después, para cada botón que deseamos Mostrar en la alerta, se crea una acción que define el título del botón, su estilo y la acción que queremos realizar si se presiona el botón:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

La `UIAlertActionStyle` enumeración le permite establecer el estilo del botón como uno de los siguientes:

- **Predeterminado** : el botón es el botón predeterminado que se selecciona cuando se muestra la alerta.
- **Cancelar** : el botón es el botón Cancelar de la alerta.
- **Destructiva** : resalta el botón como una acción destructiva, como la eliminación de un archivo. Actualmente, tvOS representa el botón destructivo con un fondo rojo.

El `AddAction` método agrega la acción especificada `UIAlertViewController` a y, por último `PresentViewController (alertController, true, null)` , el método muestra al usuario la alerta especificada.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Agregar campos de texto

Además de agregar acciones (botones) a la alerta, puede agregar campos de texto a la alerta para permitir que el usuario rellene información como los identificadores de usuario y las contraseñas:

[![](alerts-images/alert02.png "Campo de texto en una alerta")](alerts-images/alert02.png#lightbox)

Si el usuario selecciona el campo de texto, se mostrará el teclado tvOS estándar, lo que le permitirá especificar un valor para el campo:

[![](alerts-images/alert03.png "Escribir texto")](alerts-images/alert03.png#lightbox)

En el código siguiente se muestra una alerta de aceptar/cancelar con un solo campo de texto para escribir un valor:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

El `AddTextField` método agrega un nuevo campo de texto a la alerta que se puede configurar estableciendo propiedades como el texto del marcador de posición (el texto que aparece cuando el campo está vacío), el valor de texto predeterminado y el tipo de teclado. Por ejemplo:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Para que podamos actuar en el valor del campo de texto más adelante, también estamos guardando una copia de con el código siguiente:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Una vez que el usuario ha escrito un valor en el campo de texto, se `field` puede usar la variable para obtener acceso a ese valor.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Clase auxiliar de controlador de vista de alertas

Dado que Mostrar tipos de alertas simples y comunes `UIAlertViewController` mediante puede producir bastante código duplicado, puede utilizar una clase auxiliar para reducir la cantidad de código repetitivo. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

Con esta clase, se pueden mostrar y responder a las alertas simples de la siguiente manera:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado `UIAlertController` el uso de para mostrar un mensaje de alerta al usuario en Xamarin. tvOS. En primer lugar, se mostró cómo mostrar una alerta simple y agregar botones. A continuación, se mostró cómo agregar campos de texto a una alerta. Por último, ha mostrado cómo usar una clase auxiliar para reducir la cantidad de código repetitivo necesario para mostrar una alerta.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
