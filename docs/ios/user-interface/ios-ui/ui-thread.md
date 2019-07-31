---
title: Trabajar con el subproceso de interfaz de usuario en Xamarin. iOS
description: En este documento se describe cómo trabajar con el subproceso de interfaz de usuario en Xamarin. iOS. Describe la ejecución de subprocesos de interfaz de usuario, proporciona un ejemplo de subproceso en segundo plano y examina Async/Await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6f9f11a84f9a57d699a219958883afae33824e95
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655285"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Trabajar con el subproceso de interfaz de usuario en Xamarin. iOS

Las interfaces de usuario de la aplicación siempre son de un solo subproceso, incluso en dispositivos de varios subprocesos, solo hay una representación de la pantalla y cualquier cambio en lo que se muestra debe coordinarse a través de un solo punto de acceso. Esto evita que varios subprocesos intenten actualizar el mismo píxel al mismo tiempo (por ejemplo).

El código solo debe realizar cambios en los controles de interfaz de usuario del subproceso principal (o UI). Es posible que las actualizaciones de la interfaz de usuario que se producen en un subproceso diferente (como una devolución de llamada o un subproceso en segundo plano) no se representen en la pantalla, o incluso podrían provocar un bloqueo.

## <a name="ui-thread-execution"></a>Ejecución del subproceso de interfaz de usuario

Al crear controles en una vista, o controlar un evento iniciado por el usuario como una función táctil, el código ya se está ejecutando en el contexto del subproceso de la interfaz de usuario.

Si el código se está ejecutando en un subproceso en segundo plano, en una tarea o en una devolución de llamada, es probable que no se esté ejecutando en el subproceso de la interfaz de usuario principal. En este caso, debe ajustar el código en una llamada a `InvokeOnMainThread` o `BeginInvokeOnMainThread` de la siguiente manera:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

El `InvokeOnMainThread` método se define en `NSObject` , por lo que se puede llamar desde los métodos definidos en cualquier objeto UIKit (por ejemplo, un controlador de vista o vista).

Durante la depuración de aplicaciones de Xamarin. iOS, se producirá un error si el código intenta tener acceso a un control de interfaz de usuario desde el subproceso equivocado. Esto le ayuda a realizar un seguimiento y corregir estos problemas con el método InvokeOnMainThread. Esto solo ocurre durante la depuración y no produce un error en las compilaciones de versión. El mensaje de error aparecerá de la siguiente manera:

 ![](ui-thread-images/image10.png "Ejecución del subproceso de interfaz de usuario")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Ejemplo de subproceso en segundo plano

Este es un ejemplo que intenta tener acceso a un control de la interfaz `UILabel`de usuario (a) desde un subproceso en segundo plano mediante un subproceso simple:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Ese código producirá durante `UIKitThreadAccessException` la depuración. Para corregir el problema (y asegurarse de que solo se tiene acceso al control de la interfaz de usuario desde el subproceso principal de la interfaz de usuario), `InvokeOnMainThread` ajuste cualquier código que haga referencia a los controles de interfaz de usuario dentro de una expresión similar a la siguiente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

No necesitará usar esto para el resto de los ejemplos de este documento, pero es un concepto importante que debe recordar cuando la aplicación realiza solicitudes de red, usa el centro de notificaciones u otros métodos que requieren un controlador de finalización que se ejecutará en otro. conversaciones.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Ejemplo de Async/Await

El uso de C# las 5 palabras clave `InvokeOnMainThread` Async/Await no es necesario porque cuando una tarea en espera finaliza el método continúa en el subproceso que realiza la llamada.

Este código de ejemplo (que espera en una llamada al método Delay, exclusivamente para fines de demostración) muestra un método asincrónico al que se llama en el subproceso de la interfaz de usuario (es un controlador TouchUpInside). Dado que se llama al método contenedor en el subproceso de la interfaz de usuario, se puede `UILabel` llamar a las `UIAlertView` operaciones de interfaz de usuario como establecer el texto en o mostrar una después de que las operaciones asincrónicas se hayan completado en subprocesos en segundo plano.

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Si se llama a un método asincrónico desde un subproceso en segundo plano (no el subproceso principal de la interfaz de usuario), `InvokeOnMainThread` seguirá siendo necesario.


## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Subprocesamiento](~/ios/app-fundamentals/threading.md)
