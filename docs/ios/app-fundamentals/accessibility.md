---
title: Accesibilidad en iOS
description: En este documento se describe la accesibilidad en iOS y se tratan diversas propiedades y características que se pueden usar para que la aplicación pueda utilizarla tanto como sea posible.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/18/2016
ms.openlocfilehash: 2259566fc6342a40a8c0a94bacd1c146b6509d52
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574163"
---
# <a name="accessibility-on-ios"></a>Accesibilidad en iOS

En esta página se describe cómo usar las API de accesibilidad de iOS para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Consulte las páginas accesibilidad de [Android](~/android/app-fundamentals/accessibility.md) y [OS X accesibilidad](~/mac/app-fundamentals/accessibility.md) para otras API de la plataforma.

## <a name="describing-ui-elements"></a>Describir elementos de la interfaz de usuario

iOS proporciona las `AccessibilityLabel` `AccessibilityHint` propiedades y para que los desarrolladores agreguen texto descriptivo que el lector de pantalla VoiceOver puede usar para hacer que los controles sean más accesibles. Los controles también se pueden etiquetar con uno o más rasgos que proporcionan contexto adicional en los modos accesibles.

Es posible que no sea necesario tener acceso a algunos controles (por ejemplo, una etiqueta en una entrada de texto o una imagen meramente decorativa): `IsAccessibilityElement` se proporciona para deshabilitar la accesibilidad en esos casos.

**Diseñador de interfaz de usuario**

El **Panel de propiedades** contiene una sección de accesibilidad que permite editar esta configuración cuando se selecciona un control en el diseñador de la interfaz de usuario de iOS:

![](accessibility-images/ios-designer-sml.png "Accessibility Settings")

**C#**

Estas propiedades también se pueden establecer directamente en el código:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>¿Qué es AccessibilityIdentifier?

`AccessibilityIdentifier`Se usa para establecer una clave única que se puede usar para hacer referencia a los elementos de la interfaz de usuario a través de la API de UIAutomation.

El valor de `AccessibilityIdentifier` nunca se habla ni se muestra al usuario.

<a name="postnotification"></a>

## <a name="postnotification"></a>Postnotification

El `UIAccessibility.PostNotification` método permite que se generen eventos al usuario fuera de la interacción directa (por ejemplo, cuando interactúan con un control específico).

### <a name="announcement"></a>Anuncio

Se puede enviar un anuncio desde el código para informar al usuario de que algún Estado ha cambiado (por ejemplo, una operación en segundo plano se ha completado). Esto puede ir acompañado de una indicación visual en la interfaz de usuario:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

El `LayoutChanged` anuncio se usa cuando el diseño de pantalla:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>Accesibilidad y localización

Las propiedades de accesibilidad como la etiqueta y la sugerencia se pueden localizar como otro texto en la interfaz de usuario.

**Archivo mainstoryboard. Strings**

Si la interfaz de usuario se coloca en un guion gráfico, puede proporcionar traducciones para las propiedades de accesibilidad de la misma manera que otras propiedades. En el ejemplo siguiente, un `UITextField` tiene un **identificador de localización** de `Pqa-aa-ury` y dos propiedades de accesibilidad que se establecen en Español:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Este archivo se colocará en el directorio **es. lproj** para el contenido en español.

**Localizable. Strings**

Como alternativa, las traducciones se pueden agregar al archivo **. Strings localizable** en el directorio de contenido localizado (por ejemplo, **es. lproj** para español):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Estas traducciones se pueden usar en C# a través del `LocalizedString` método:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Consulte la [Guía de localización de iOS](~/ios/app-fundamentals/localization/index.md) para obtener más detalles sobre cómo localizar contenido.

<a name="testing"></a>

## <a name="testing-accessibility"></a>Probar la accesibilidad

VoiceOver está habilitado en la aplicación de **configuración** navegando a **General > accesibilidad > VoiceOver**:

![](accessibility-images/settings-sml.png "Setting the speaking rate")

La pantalla **accesibilidad** también proporciona opciones de zoom, tamaño del texto, color & contraste, configuración de voz y otras opciones de configuración.

Siga estas [instrucciones de VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) para probar la accesibilidad en dispositivos iOS.

## <a name="simulator-testing"></a>Pruebas del simulador

Al realizar pruebas en el simulador, el **Inspector de accesibilidad** está disponible para ayudar a comprobar que las propiedades de accesibilidad y los eventos están configurados correctamente. Active el inspector en la aplicación de **configuración** . para ello, vaya a **General > accesibilidad > inspector de accesibilidad**:

![](accessibility-images/settings-inspector-sml.png "Enable Accessibility Inspector")

Una vez habilitada, la ventana del inspector mantiene el mouse sobre la pantalla de iOS en todo momento.
A continuación se muestra un ejemplo de la salida cuando se selecciona una fila de la vista de tabla: Observe que la **etiqueta** contiene una frase que proporciona el contenido de la fila y también que está "listo" (es decir, la marca es visible):

![](accessibility-images/tableview-a11y-sml.png "Using Accessibility Inspector")

Mientras el inspector está visible, use el icono "X" en la parte superior izquierda para mostrar y ocultar temporalmente la superposición y habilitar o deshabilitar la configuración de accesibilidad.

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Accesibilidad de iOS (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [VoiceOver de iOS](https://www.apple.com/accessibility/ios/voiceover/)
