---
title: Entrada de texto en Xamarin. iOS
description: En este documento se describe la entrada de texto en una aplicación Xamarin. iOS. Describe el uso de campo y UITextVIew mediante programación y en el diseñador de iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e8f8c2196edd5450ca2949d59c2fc06ff62274b7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435378"
---
# <a name="text-input-in-xamarinios"></a>Entrada de texto en Xamarin. iOS

La aceptación de la entrada de texto del usuario se logra con `UITextField` para las entradas de una sola línea y UITextView para el texto editable de varias líneas. Puede arrastrar cualquiera de estos controles a una pantalla y hacer doble clic para establecer el texto inicial.

Las capturas de pantallas siguientes muestran los iconos de estos controles, que se encuentran en el panel del cuadro de herramientas en Visual Studio para Mac:

 [![UITextField](text-input-images/image11a.png)](text-input-images/image11a.png#lightbox)

 [![UITextView](text-input-images/image13a.png)](text-input-images/image13a.png#lightbox)

Una vez que haya llamado a la toma y guardado el archivo de guion gráfico, Visual Studio para Mac actualizará la `.designer.cs` clase parcial y podrá agregar código C# que haga referencia al control en el archivo de clase. Cada control tiene sus propias propiedades y eventos únicos a los que se puede tener acceso en el código de C#.

 <a name="UITextField"></a>

## <a name="uitextfield"></a>UITextField

El `UITextField` control se usa con más frecuencia para aceptar una sola línea de entrada de texto, como un nombre de usuario o una contraseña. A continuación se muestran algunas de las opciones disponibles para personalizar el control:

 [![Propiedades de campo](text-input-images/image15a.png)](text-input-images/image15a.png#lightbox)

Estos controles se explican a continuación:

- **Marcador de posición** : es opcional. Si se establece, se muestra cuando el campo de texto está vacío, normalmente para explicar al usuario qué entrada se espera.
- **Botón Borrar** : controla cuándo aparece el botón Borrar estándar (el círculo gris con (X)) en el campo de texto, de modo que el usuario pueda borrar texto rápidamente. Se puede ocultar, ver o mostrar permanentemente de forma permanente, en función de si el campo se está editando o no.
- **Tamaño de fuente mínimo** y  **ajustar a ajuste** : permite ajustar automáticamente el tamaño de fuente para ajustarse al texto más largo y evitar el truncamiento, pero se limita a un tamaño inferior al especificado.
- **Capitalización** : si se van a poner en mayúsculas automáticamente palabras, oraciones o cualquier entrada.
- **Corrección** : indica si la revisión ortográfica y las sugerencias están habilitadas.
- **Teclado** : controla el estilo de teclado que se muestra para la entrada y, por tanto, las teclas que están disponibles en el teclado. Esto incluye el panel numérico, el panel de teléfono, el correo electrónico y la dirección URL junto con otras opciones.
- **Apariencia** : controla el estilo de apariencia del teclado y será oscuro o claro.
- **Tecla Retorno** : cambie la etiqueta de la tecla retorno para reflejar mejor la acción que se llevará a cabo. Los valores admitidos son Go, join, Next, Route, done y Search.
- **Secure** : identifica si la entrada está enmascarada (por ejemplo, para una entrada de contraseña).

Si se ha agregado una campo llamada `textfield1` a una pantalla con el diseñador, puede establecer o cambiar sus propiedades en C# de la manera siguiente:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin. iOS proporciona enumeraciones cuando es adecuado para facilitar la selección de la configuración deseada, como `UIKeyboardType` y `UIReturnKeyType` en el fragmento de código anterior.

### <a name="display-text-programmatically"></a>Mostrar texto mediante programación

Si no desea diseñar la pantalla con el diseñador o si desea agregar dinámicamente texto en tiempo de ejecución, puede crear y mostrar un campo mediante programación en el `ViewDidLoad` método de un controlador de vista como este:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView"></a>

## <a name="uitextview"></a>UITextView

El `UITextView` control se puede usar para mostrar texto de solo lectura o para aceptar la entrada de texto de varias líneas. Tiene muchas de las mismas opciones que el `UITextField` (por ejemplo, capitalización, corrección, etc.).

 [![Propiedades de UITextView](text-input-images/image16a.png)](text-input-images/image16a.png#lightbox)

Las propiedades específicas incluyen:

- **Comportamiento** : Si el texto es editable o de solo lectura.
- **Detección** : detecta y convierte los datos introducidos en elementos en los que se puede hacer clic, como números de teléfono que pueden desencadenar una llamada, direcciones que se convierten en vínculos a asignaciones, direcciones URL que se abren en Safari o fechas y horas que se convierten en eventos en el calendario.

Si se ha agregado un UITextView a una pantalla con el diseñador, puede establecer o cambiar sus propiedades de la siguiente manera:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](/samples/xamarin/ios-samples/controls)