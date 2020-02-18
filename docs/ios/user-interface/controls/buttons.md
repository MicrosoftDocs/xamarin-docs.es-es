---
title: Botones de Xamarin. iOS
description: La clase botón se usa para representar distintos estilos de botón en pantallas de iOS. En esta guía se presentan las diferentes opciones para trabajar con botones en iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: 0619488199c202e1877e4cfa60d622ef247e2b3f
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2020
ms.locfileid: "77374124"
---
# <a name="buttons-in-xamarinios"></a>Botones de Xamarin. iOS

En iOS, la clase `UIButton` representa un control de botón.

Las propiedades de un botón se pueden modificar mediante programación o con el **Panel de propiedades** del diseñador de iOS:

![Panel de propiedades del diseñador de iOS](buttons-images/properties.png "Panel de propiedades del diseñador de iOS")

## <a name="creating-a-button-programmatically"></a>Crear un botón mediante programación

Un `UIButton` se puede crear con solo unas pocas líneas de código.

- Cree una instancia de un botón y especifique su tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  El tipo del botón se especifica mediante un `UIButtonType`:

  - `UIButtonType.System`: botón de uso general
  - `UIButtonType.DetailDisclosure`: indica la disponibilidad de información detallada, normalmente sobre un elemento específico de una tabla
  - `UIButtonType.InfoDark`: indica la disponibilidad de la información de configuración; color oscuro
  - `UIButtonType.InfoLight`: indica la disponibilidad de la información de configuración; color claro
  - `UIButtonType..AddContact`: indica que se puede Agregar un contacto
  - `UIButtonType.Custom`: botón personalizable

  Para obtener más información sobre los distintos tipos de botón, eche un vistazo a:
  
  - La sección [tipos de botón personalizados](#custom-button-types) de este documento
  - La receta de [tipos de botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)
  - Directrices de la [interfaz humana de iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)de Apple.

- Defina el tamaño y la posición del botón:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Establezca el texto del botón. Use el método `SetTitle`, que requiere el texto y un valor `UIControlState`:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Para obtener más información acerca de cómo aplicar estilos a un botón y establecer su texto, consulte:

  - La sección [aplicar estilo a un botón](#styling-a-button) de este documento
  - La receta de [texto del botón establecer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) .

## <a name="handling-a-button-tap"></a>Controlar una derivación de botones

Para responder a una derivación de botones, proporcione un controlador para el evento de `TouchUpInside` del botón:

```csharp
myButton.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` no es el único evento de botón disponible. `UIButton` es una clase secundaria de `UIControl`, que define [muchos eventos diferentes](xref:UIKit.UIControlEvent).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Usar el diseñador de iOS para especificar controladores de eventos de botón

Use la pestaña **eventos** del **Panel de propiedades** para especificar controladores de eventos para los distintos eventos de un botón.

Para el evento adecuado, escriba el nombre de un nuevo controlador de eventos o seleccione uno de la lista. Al hacerlo, se creará un controlador de eventos en el código para el controlador de vistas del botón.

![Pestaña eventos del Panel de propiedades](buttons-images/image1.png "Pestaña eventos del Panel de propiedades")

## <a name="styling-a-button"></a>Aplicar estilo a un botón

`UIButton` controles pueden existir en varios Estados distintos, cada uno de ellos especificado por un valor de `UIControlState`: `Normal`, `Disabled`, `Focused`, `Highlighted`, etc. A cada Estado se le puede asignar un estilo único, especificado mediante programación o con el diseñador de iOS.

> [!NOTE]
> Para obtener una lista completa de todos los valores de `UIControlState`, eche un vistazo al [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> en línea.

Por ejemplo, para establecer el color del título y el color de la sombra de `UIControlState.Normal`:

```csharp
myButton.SetTitleColor(UIColor.White, UIControlState.Normal);
myButton.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

El código siguiente establece el título del botón en una cadena con atributos (estilizada) para `UIControlState.Normal` y `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botón personalizados

Los botones con una `UIButtonType` de `Custom` no tienen estilos predeterminados. Sin embargo, es posible configurar la apariencia del botón estableciendo una imagen para sus diferentes Estados:

```csharp
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

En función de si el usuario toca el botón o no, se representará como una de las siguientes imágenes (`UIControlState.Normal`, `UIControlState.Highlighted` y los Estados de `UIControlState.Selected`, respectivamente):

![UIControlState. Normal](buttons-images/image22.png "UIControlState. normal")
![UIControlState. resaltado](buttons-images/image23.png "UIControlState. resaltado")
![UIControlState. seleccionado](buttons-images/image24.png "UIControlState. seleccionado")

Para obtener más información sobre cómo trabajar con botones personalizados, consulte la receta [uso de una imagen para un botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) .
