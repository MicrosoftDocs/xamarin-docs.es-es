---
title: Accesibilidad en macOS
description: En este documento se describe cómo trabajar con las características de accesibilidad de macOS en una aplicación de Xamarin. Mac. Describe la descripción de los elementos de la interfaz de usuario en guiones gráficos y código, controles personalizados y la accesibilidad de las pruebas.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 087dcdc7024026e6a3ed3a05baca3b2648053cc8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769946"
---
# <a name="accessibility-on-macos"></a>Accesibilidad en macOS

En esta página se describe cómo usar las API de accesibilidad de macOS para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Consulte las páginas [accesibilidad de Android](~/android/app-fundamentals/accessibility.md) y [accesibilidad de iOS](~/ios/app-fundamentals/accessibility.md) para otras API de la plataforma.

Para comprender cómo funcionan las API de accesibilidad en macOS (anteriormente denominado OS X), primero revise el [modelo de accesibilidad de os x](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Describir elementos de la interfaz de usuario

AppKit usa el `NSAccessibility` protocolo para exponer las API que ayudan a que la interfaz de usuario sea accesible. Esto incluye un comportamiento predeterminado que intenta establecer valores significativos para las propiedades de accesibilidad, como establecer el valor de `AccessibilityLabel`un botón. La etiqueta suele ser una palabra única o una frase corta que describe el control o la vista.

### <a name="storyboard-files"></a>Guiones gráficos

Xamarin. Mac usa el Interface Builder Xcode para editar archivos de guion gráfico.
La información de accesibilidad se puede editar en el **Inspector de identidad** cuando se selecciona un control en la superficie de diseño (como se muestra en la siguiente captura de pantalla):

[![Agregar accesibilidad en Interface Builder de Xcode](accessibility-images/xcode.png "Agregar accesibilidad en Interface Builder de Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Código

Xamarin. Mac no se expone actualmente como `AccessibilityLabel` Setter.  Agregue el siguiente método auxiliar para establecer la etiqueta de accesibilidad:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Este método se puede usar en el código tal y como se muestra:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

La `AccessibilityHelp` propiedad es para obtener una explicación de lo que hace el control o la vista, y solo se debe agregar cuando es posible que la etiqueta no proporcione información suficiente. El texto de ayuda debe mantenerse lo más corto posible, por ejemplo, "elimina el documento".

Algunos elementos de la interfaz de usuario no son relevantes para el acceso accesible (por ejemplo, una etiqueta junto a una entrada que tiene su propia etiqueta de accesibilidad y ayuda).
En estos casos, establezca `AccessibilityElement = false` para que los lectores de pantalla u otras herramientas de accesibilidad omitan estos controles o vistas.

Apple proporciona [directrices de accesibilidad](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) que explican las prácticas recomendadas para las etiquetas de accesibilidad y el texto de ayuda.

## <a name="custom-controls"></a>Controles personalizados

Consulte las directrices de Apple [para ver los controles personalizados accesibles](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) para más información sobre los pasos adicionales necesarios.

## <a name="testing-accessibility"></a>Probar la accesibilidad

macOS proporciona un **Inspector de accesibilidad** que ayuda a probar la funcionalidad de accesibilidad. El inspector se incluye con Xcode.

La primera vez que se inicia, el **Inspector de accesibilidad** necesitará permiso para controlar el equipo a través de la accesibilidad:

![Inspector de accesibilidad que solicita permiso para ejecutarse](accessibility-images/accessibility-inspector-1.png "Inspector de accesibilidad que solicita permiso para ejecutarse")

Desbloquee la pantalla de configuración (si es necesario, en la parte inferior izquierda) y marque el **Inspector de accesibilidad**:

![Pantalla de configuración para habilitar el inspector de accesibilidad](accessibility-images/accessibility-inspector-2.png "Pantalla de configuración para habilitar el inspector de accesibilidad")

Una vez habilitado, el inspector aparece como una ventana flotante que se puede mover alrededor de la pantalla. En la captura de pantalla siguiente se muestra el inspector que se ejecuta junto a una aplicación Mac de ejemplo. Cuando el cursor se mueve sobre la ventana, el Inspector muestra todas las propiedades accesibles de cada control:

[![Ejemplo de inspector de accesibilidad en ejecución](accessibility-images/accessibility-example.png "Ejemplo de inspector de accesibilidad en ejecución")](accessibility-images/accessibility-example-large.png#lightbox)

Para obtener más información, lea la [Guía de prueba de accesibilidad para OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad entre plataformas](~/cross-platform/app-fundamentals/accessibility.md)
- [Accesibilidad de Mac](https://www.apple.com/accessibility/mac/)
