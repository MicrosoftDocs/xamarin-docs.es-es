---
title: Mostrar elementos emergentes
description: 'Xamarin.Forms proporciona tres elementos de interfaz de usuario de tipo emergente: una alerta, una hoja de acción y un símbolo del sistema. En este artículo se muestra el uso de alertas, hojas de acción y API de mensajes para mostrar cuadros de diálogo que piden a los usuarios preguntas sencillas, guiar a los usuarios a través de tareas y mostrar mensajes.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09cffb4e5c7d8f6b78d5ab1de6ec9839c3969e87
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940125"
---
# <a name="display-pop-ups"></a>Mostrar elementos emergentes

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

Mostrar una alerta, pedir al usuario que elija una opción o mostrar un mensaje es una tarea común de la interfaz de usuario. Xamarin.Forms tiene tres métodos en la [`Page`](xref:Xamarin.Forms.Page) clase para interactuar con el usuario a través de un elemento emergente: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) , [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) y `DisplayPromptAsync` . Se representan con controles nativos adecuados en cada plataforma.

## <a name="display-an-alert"></a>Visualización de una alerta

Todas las Xamarin.Forms plataformas compatibles con son un elemento emergente modal para avisar al usuario o realizar preguntas sencillas. Para mostrar estas alertas en Xamarin.Forms , utilice el [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método en any [`Page`](xref:Xamarin.Forms.Page) . En la siguiente línea de código, se muestra un mensaje sencillo al usuario:

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![Cuadro de diálogo de alerta con un botón](pop-ups-images/alert.png)

En este ejemplo, no se recopila información del usuario. La alerta se muestra modalmente y, después de cerrarla, el usuario sigue interactuando con la aplicación.

El [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método también se puede utilizar para capturar la respuesta de un usuario presentando dos botones y devolviendo un `boolean` . Para obtener una respuesta de una alerta, agregue texto para los dos botones y use `await` para esperar el método. Cuando el usuario seleccione una de las opciones, se devolverá la respuesta al código. Observe las palabras clave `async` y `await` en el código de ejemplo siguiente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![Cuadro de diálogo de alerta con dos botones](pop-ups-images/alert2-sml.png)](pop-ups-images/alert2.png#lightbox)

El [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método también tiene sobrecargas que aceptan un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argumento que especifica la dirección en la que fluyen los elementos de la interfaz de usuario dentro de la alerta. Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

> [!WARNING]
> De forma predeterminada, en UWP, cuando se muestra una alerta, cualquier clave de acceso que se haya definido en la página detrás de la alerta todavía se puede activar. Para obtener más información, consulte [VisualElement Access Keys in Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="guide-users-through-tasks"></a>Guiar a los usuarios a través de tareas

El elemento [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) es un elemento de interfaz de usuario común en iOS. El Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) método permite incluir este control en aplicaciones multiplataforma y representar alternativas nativas en Android y UWP.

Para mostrar una hoja de acción, use `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) en cualquier elemento [`Page`](xref:Xamarin.Forms.Page) y pase el mensaje y las etiquetas de botón como cadenas. Este método devuelve la etiqueta de cadena del botón en el que hizo clic el usuario. A continuación, se muestra un ejemplo sencillo:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![Cuadro de diálogo de ActionSheet](pop-ups-images/action.png)

El botón `destroy` se representa de forma distinta y puede dejarse como `null`, o bien se puede especificar como el tercer parámetro de cadena. En el ejemplo siguiente, se usa el botón `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Cuadro de diálogo de hoja de acción con el botón destruir")](pop-ups-images/action2.png#lightbox "Cuadro de diálogo de hoja de acción con el botón destruir")

El [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) método también tiene una sobrecarga que acepta un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argumento que especifica la dirección en la que los elementos de la interfaz de usuario fluyen dentro de la hoja de acción. Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="display-a-prompt"></a>mostrar un mensaje

Para mostrar un símbolo del sistema, llame a `DisplayPromptAsync` en cualquier [`Page`](xref:Xamarin.Forms.Page) , pasando un título y un mensaje como `string` argumentos:

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

El mensaje se muestra de forma modal:

[![Captura de pantalla de un símbolo del sistema modal, en iOS y Android](pop-ups-images/simple-prompt.png "Símbolo del sistema modal")](pop-ups-images/simple-prompt-large.png#lightbox "Símbolo del sistema modal")

Si se puntea el botón Aceptar, la respuesta especificada se devuelve como un `string` . Si se puntea el botón Cancelar, `null` se devuelve.

La lista de argumentos completa para el `DisplayPromptAsync` método es:

- `title`, de tipo `string` , es el título que se va a mostrar en el símbolo del sistema.
- `message`, de tipo `string` , es el mensaje que se va a mostrar en el símbolo del sistema.
- `accept`, de tipo `string` , es el texto del botón Aceptar. Este argumento es opcional, cuyo valor predeterminado es OK.
- `cancel`, de tipo `string` , es el texto del botón Cancelar. Es un argumento opcional, cuyo valor predeterminado es cancelar.
- `placeholder`, de tipo `string` , es el texto del marcador de posición que se va a mostrar en el símbolo del sistema. Este argumento es opcional, cuyo valor predeterminado es `null` .
- `maxLength`, de tipo `int` , es la longitud máxima de la respuesta del usuario. Este argumento es opcional, cuyo valor predeterminado es-1.
- `keyboard`, de tipo `Keyboard` , es el tipo de teclado que se va a utilizar para la respuesta del usuario. Este argumento es opcional, cuyo valor predeterminado es `Keyboard.Default` .
- `initialValue`, de tipo `string` , es una respuesta predefinida que se mostrará y que se puede editar. Este argumento es opcional, cuyo valor predeterminado es un vacío `string` .

En el ejemplo siguiente se muestra cómo establecer algunos de los argumentos opcionales:

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Este código muestra una respuesta predefinida de 10, limita el número de caracteres que se pueden introducir en 2 y muestra el teclado numérico para la entrada del usuario:

[![Captura de pantalla de un mensaje modal opcional, en iOS y Android](pop-ups-images/keyboard-prompt.png "Símbolo del sistema modal")](pop-ups-images/keyboard-prompt-large.png#lightbox "Símbolo del sistema modal")

> [!WARNING]
> De forma predeterminada, en UWP, cuando se muestra un mensaje, las teclas de acceso que se definen en la página detrás del mensaje se pueden activar. Para obtener más información, consulte [VisualElement Access Keys in Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="related-links"></a>Vínculos relacionados

- [PopupsSample](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
