---
title: Mostrar alertas en Xamarin. iOS
description: En este documento se describe cómo mostrar las alertas en Xamarin. iOS mediante las API de UIAlertController introducidas en iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4c47e12468d9107cbe03d4bc45ffa77e4ad918e9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652046"
---
# <a name="displaying-alerts-in-xamarinios"></a>Mostrar alertas en Xamarin. iOS

A partir de iOS 8, UIAlertController ha terminado de reemplazar UIActionSheet y UIAlertView, que ahora están en desuso.

A diferencia de las clases reemplazadas, que son subclases de UIView, UIAlertController es una subclase de UIViewController.

Se `UIAlertControllerStyle` usa para indicar el tipo de alerta que se va a mostrar. Estos tipos de alertas son:

- **UIAlertControllerStyleActionSheet**
    * Anterior a iOS 8 esto habría sido UIActionSheet
- **UIAlertControllerStyleAlert**
    * Anterior a iOS 8 esto se habría UIAlertView 

Existen tres pasos necesarios para crear un controlador de alertas:

- Cree y configure la alerta con un:
    * title
    * message
    * preferredStyle
    
- Opta Agregar un campo de texto
- Agregar las acciones necesarias
- Presentar el controlador de vista

La alerta más sencilla contiene un solo botón, como se muestra en esta captura de pantalla:

 ![Alerta con un botón](alerts-images/alert1.png)

El código para mostrar una alerta simple es el siguiente:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

Mostrar una alerta con varias opciones se realiza de forma similar, pero agrega dos acciones. Por ejemplo, en la siguiente captura de pantalla se muestra una alerta con dos botones:

 ![Alerta con dos botones](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Las alertas también pueden mostrar una hoja de acción, similar a la siguiente captura de pantalla:

 ![Alerta de hoja de acción](alerts-images/alert3.png)

Los botones se agregan a la alerta `AddAction` con el método:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controlador de alertas](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
