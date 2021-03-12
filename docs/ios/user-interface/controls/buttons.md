---
title: Botones de Xamarin. iOS
description: La clase botón se usa para representar distintos estilos de botón en pantallas de iOS. En esta guía se presentan las diferentes opciones para trabajar con botones en iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: 2de52400241d45046f58222231b8d865ecf6666d
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602806"
---
# <a name="buttons-in-xamarinios"></a>Botones de Xamarin. iOS

En iOS, la `UIButton` clase representa un control de botón.

Las propiedades de un botón se pueden modificar mediante programación o con la Interface Builder de Xcode.

## <a name="creating-a-button-programmatically"></a>Crear un botón mediante programación

`UIButton`Se puede crear un solo con unas pocas líneas de código.

- Cree una instancia de un botón y especifique su tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  El tipo del botón se especifica mediante una `UIButtonType` propiedad:

  - `UIButtonType.System` -Un botón de uso general
  - `UIButtonType.DetailDisclosure` : Indica la disponibilidad de información detallada, normalmente sobre un elemento específico de una tabla.
  - `UIButtonType.InfoDark` : Indica la disponibilidad de la información de configuración; color oscuro
  - `UIButtonType.InfoLight` : Indica la disponibilidad de la información de configuración; color claro
  - `UIButtonType..AddContact` : Indica que se puede Agregar un contacto
  - `UIButtonType.Custom` -Botón personalizable

  Para obtener más información sobre los distintos tipos de botón, eche un vistazo a:
  
  - La sección [tipos de botón personalizados](#custom-button-types) de este documento
  - La receta de [tipos de botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)
  - Directrices de la [interfaz humana de iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)de Apple.

- Defina el tamaño y la posición del botón:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Establezca el texto del botón. Use el `SetTitle` método, que requiere el texto y un `UIControlState` valor para el estado del botón:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```
  
  Los tipos de estado del botón se enumeran a continuación:
  
  - `UIControlState.Normal`
  - `UIControlState.Highlighted`
  - `UIControlState.Disabled`
  - `UIControlState.Selected`
  - `UIControlState.Focused`
  - `UIControlState.Application`
  - `UIControlState.Reserved`
  
  Para obtener más información acerca de cómo aplicar estilos a un botón y establecer su texto, consulte:

  - La sección [aplicar estilo a un botón](#styling-a-button) de este documento
  - La receta de [texto del botón establecer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) .

## <a name="handling-a-button-tap"></a>Controlar una derivación de botones

Para responder a una derivación de botones, proporcione un controlador para el `TouchUpInside` evento del botón:

```csharp
myButton.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` no es el único evento de botón disponible. `UIButton` es una clase secundaria de `UIControl` , que define [muchos eventos diferentes](xref:UIKit.UIControlEvent).

## <a name="styling-a-button"></a>Aplicar estilo a un botón

`UIButton` los controles pueden existir en varios Estados diferentes, cada uno de ellos especificado por un `UIControlState` valor: `Normal` , `Disabled` , `Focused` , `Highlighted` , etc. A cada Estado se le puede asignar un estilo único, especificado mediante programación o con el diseñador de iOS.

> [!NOTE]
> Para obtener una lista completa de todos `UIControlState` los valores, eche un vistazo al [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> en línea.

Por ejemplo, para establecer el color del título y el color de la sombra para `UIControlState.Normal` :

```csharp
myButton.SetTitleColor(UIColor.White, UIControlState.Normal);
myButton.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

El código siguiente establece el título del botón en una cadena con atributos (estilizada) para `UIControlState.Normal` y `UIControlState.Highlighted` :

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botón personalizados

Los botones con un `UIButtonType` de `Custom` no tienen estilos predeterminados. Sin embargo, es posible configurar la apariencia del botón estableciendo una imagen para sus diferentes Estados:

```csharp
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

En función de si el usuario toca el botón o no, se representará como una de las siguientes imágenes ( `UIControlState.Normal` , `UIControlState.Highlighted` y `UIControlState.Selected` Estados, respectivamente):

![UIControlState. normal](buttons-images/image22.png "UIControlState. normal") 
 ![UIControlState. resaltado](buttons-images/image23.png "UIControlState. resaltado") 
 ![UIControlState. seleccionado](buttons-images/image24.png "UIControlState. seleccionado")

Para obtener más información sobre cómo trabajar con botones personalizados, consulte la receta [uso de una imagen para un botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) .
