---
title: Etiquetas en Xamarin. iOS
description: En este documento se explica cómo usar las etiquetas en Xamarin. iOS. Describe cómo crear etiquetas mediante programación y con el diseñador de iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 41cd0eb93cee216311ea42f7ca027a1556b322e6
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227074"
---
# <a name="labels-in-xamarinios"></a>Etiquetas en Xamarin. iOS

El `UILabel` control se usa para mostrar texto de solo lectura y de una sola línea.

## <a name="implementing-a-label"></a>Implementar una etiqueta

Se crea una nueva etiqueta creando una instancia de [`UILabel`](xref:UIKit.UILabel):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Etiquetas y guiones gráficos

También puede Agregar una etiqueta a la interfaz de usuario al usar el diseñador de iOS. Busque **etiqueta** en el **cuadro de herramientas** y arrástrela a la vista:

![Etiqueta en el cuadro de herramientas](labels-images/image3.png)

Las siguientes propiedades se pueden ajustar en el panel de propiedades:

![Panel de propiedades etiqueta](labels-images/image2.png)

- **Contexto de texto** : sin formato o con atributos. El texto sin formato le permite establecer los [atributos de formato](#Formatting_Text_and_Label) en toda la cadena. Los textos con atributos permiten establecer el formato en caracteres o palabras diferentes en la cadena.
- **Colores, fuentes, alineación** : atributos de formato que se pueden aplicar a la etiqueta.
- **Líneas** : establece el número de líneas que la etiqueta puede abarcar. Establézcalo en 0 para permitir que la etiqueta use tantas líneas como sea necesario.
- **Comportamiento** : se puede establecer en habilitado o resaltado. Habilitado está establecido de forma predeterminada, el texto deshabilitado se mostrará en color gris más claro. Resaltado está deshabilitado de forma predeterminada y permite volver a dibujar la etiqueta con un estado resaltado cuando lo selecciona un usuario.
- **Baselane y salto de línea** :
  - Base determina cómo se colocará el texto si el tamaño de fuente es diferente del especificado.
  - Los saltos de línea determinan el modo en que una cadena se ajustará o se truncará si es mayor que una sola línea.
- **AutoShrink** : determina cómo se minimizará el tamaño de fuente dentro de una etiqueta, si es necesario.
- **Resaltado, sombra, desplazamiento** : permite establecer el color de la y de sombra y el desplazamiento de la sombra.

## <a name="truncating-and-wrapping"></a>Truncamiento y ajuste

Para obtener información sobre el uso de los saltos de línea en iOS, consulte la receta [truncar y ajustar texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) .

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Aplicar formato a texto y etiqueta

Para dar formato a la cadena que se usa en una etiqueta, puede establecer los atributos de formato en toda la cadena o puede usar cadenas con atributos. En los siguientes ejemplos se muestra cómo implementarlos:

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Para obtener más información sobre el estilo `NSAttributedString` de texto, consulte la receta de [texto de estilo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) .

De forma predeterminada, las `Enabled` etiquetas tienen el valor true, pero es posible establecerlo en deshabilitado para dar al usuario una sugerencia de que un control determinado está deshabilitado:

```csharp
label.Enabled = false;
```

Esto establece la etiqueta en un color gris claro, tal como se muestra en la siguiente imagen de ejemplo de la pantalla restricciones de iOS:

![Botón deshabilitado en iOS](labels-images/image1.png)

También puede establecer los colores del texto resaltado y sombra en el texto de la etiqueta para obtener efectos adicionales:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que muestra el texto de la siguiente manera:

![Resaltar y establecer sombra en el texto](labels-images/image4.png)

Para obtener más información sobre cómo cambiar la fuente de un UILabel, consulte la receta de [cambio de fuente](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) .





