---
title: Propiedades de automatización
description: Este artículo explica cómo usar la clase AutomationProperties en una aplicación de Xamarin.Forms, para que un lector de pantalla puede leer los elementos en la página.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 12c6229c1922f0bd4a4d25ca796bcb46141a326c
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131140"
---
# <a name="automation-properties-in-xamarinforms"></a>Propiedades de automatización en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms permite que los valores de accesibilidad se establezcan en elementos de la interfaz de usuario mediante el uso de las propiedades adjuntas de la clase AutomationProperties, que a su vez establece valores de accesibilidad nativa. Este artículo explica cómo usar la clase AutomationProperties, para que un lector de pantalla pueda leer los elementos en la página._

Xamarin.Forms permite que las propiedades de automatización se establezcan en elementos de la interfaz de usuario a través de las propiedades adjuntas siguientes:

- `AutomationProperties.IsInAccessibleTree`: indica si el elemento está disponible para una aplicación accesible. Para obtener más información, consulte [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name`: una breve descripción del elemento que actúa como un identificador con capacidad de lectura para el elemento. Para obtener más información, consulte [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText`: una descripción más larga del elemento, que puede considerarse como el texto de información sobre herramientas asociado al elemento. Para obtener más información, consulte [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy`: permite que otro elemento defina la información de accesibilidad para el elemento actual. Para obtener más información, consulte [AutomationProperties.LabeledBy](#labeledby).

Estas propiedades adjuntas establecen los valores de accesibilidad nativa para que un lector de pantalla pueda leer el elemento. Para más información sobre las propiedades adjuntas, consulte [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> El uso de las propiedades adjuntas `AutomationProperties` puede afectar a la ejecución de prueba de IU en Android. Las propiedades [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId), `AutomationProperties.Name` y `AutomationProperties.HelpText`, establecen la propiedad `ContentDescription` nativa, con los valores de propiedad `AutomationProperties.Name` y `AutomationProperties.HelpText` con prioridad sobre el valor `AutomationId` (si tanto `AutomationProperties.Name` como `AutomationProperties.HelpText` están establecidos, los valores se concatenarán). Esto significa que cualquier prueba que busque `AutomationId` fallará si `AutomationProperties.Name` o `AutomationProperties.HelpText` también están establecidos en el elemento. En este escenario, las pruebas de IU deben modificarse para buscar el valor de `AutomationProperties.Name` o `AutomationProperties.HelpText`, o una concatenación de ambos.

Cada plataforma tiene un lector de pantalla diferente para leer los valores de accesibilidad:

- iOS tiene VoiceOver. Para obtener más información, consulte [Test Accessibility on Your Device with VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) (Probar la accesibilidad en un dispositivo con VoiceOver), en developer.apple.com.
- Android tiene TalkBack. Para obtener más información, consulte [Testing Your App's Accessibility](https://developer.android.com/training/accessibility/testing.html#talkback) (Prueba de la accesibilidad de una aplicación), en developer.android.com.
- Windows tiene el Narrador. Para obtener más información, consulte [Verify main app scenarios by using Narrator](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator) (Comprobar escenarios de aplicaciones principales mediante el uso del Narrador).

Sin embargo, el comportamiento exacto de un lector de pantalla depende del software y de la configuración del usuario en él. Por ejemplo, la mayoría de los lectores de pantalla leen el texto asociado con un control cuando queda focalizado, permitiendo a los usuarios orientarse a medida que se mueven entre los controles en la página. Algunos lectores de pantalla leen también la interfaz de usuario de la aplicación completa cuando aparece una página, lo cual permite que el usuario reciba todo de contenido informativo disponible de la página antes de intentar navegar por ella.

Los lectores de pantalla también leen valores de accesibilidad distintos. En la aplicación de ejemplo:

- VoiceOver leerá el valor `Placeholder` de `Entry`, seguido de las instrucciones para usar el control.
- TalkBack leerá el valor `Placeholder` de `Entry`, seguido del valor `AutomationProperties.HelpText`, seguido de las instrucciones para usar el control.
- El Narrador leerá el valor `AutomationProperties.LabeledBy` de `Entry`, seguido de las instrucciones para usar el control.

Además, el Narrador dará prioridad a `AutomationProperties.Name`, `AutomationProperties.LabeledBy` y, a continuación, `AutomationProperties.HelpText`. En Android, TalkBack puede combinar los valores `AutomationProperties.Name` y `AutomationProperties.HelpText`. Por lo tanto, se recomienda llevar a cabo pruebas de accesibilidad exhaustivas en cada plataforma para garantizar una experiencia óptima.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

La propiedad adjunta `AutomationProperties.IsInAccessibleTree` es un valor `boolean` que determina si el elemento es accesible, y por lo tanto visible, para los lectores de pantalla. Debe establecerse en `true` para usar las otras propiedades adjuntas de accesibilidad. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Como alternativa, puede establecerse en C# de la siguiente manera:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Tenga en cuenta que el método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) también se puede utilizar para establecer la propiedad adjunta de `AutomationProperties.IsInAccessibleTree`, `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

El valor de la propiedad adjunta `AutomationProperties.Name` debe ser una cadena de texto corta y descriptiva que usa un lector de pantalla anunciar un elemento. Esta propiedad debe establecerse para los elementos que tengan un significado que sea importante para comprender el contenido o interactuar con la interfaz de usuario. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Como alternativa, puede establecerse en C# de la siguiente manera:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Tenga en cuenta que el método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) también se puede utilizar para establecer la propiedad adjunta de `AutomationProperties.Name`, `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

La propiedad adjunta de `AutomationProperties.HelpText` debe establecerse en el texto que describe el elemento de interfaz de usuario y puede ser considerarse el texto de información sobre herramientas asociado al elemento. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Como alternativa, puede establecerse en C# de la siguiente manera:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Tenga en cuenta que el método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) también se puede utilizar para establecer la propiedad adjunta de `AutomationProperties.HelpText`, `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

En algunas plataformas, para editar controles, como un elemento [`Entry`](xref:Xamarin.Forms.Entry), la propiedad `HelpText` a veces puede omitirse y reemplazarse por el texto de marcador de posición. Por ejemplo, "Escriba aquí su nombre" es un buen candidato para la propiedad [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) que coloca el texto en el control antes de la entrada real del usuario.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

La propiedad adjunta `AutomationProperties.LabeledBy` permite que otro elemento defina la información de accesibilidad para el elemento actual. Por ejemplo, un elemento [`Label`](xref:Xamarin.Forms.Label) junto a un elemento [`Entry`](xref:Xamarin.Forms.Entry) puede utilizarse para describir lo que representa `Entry`. Esto se puede lograr en XAML de la siguiente manera:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Como alternativa, puede establecerse en C# de la siguiente manera:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Tenga en cuenta que el método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) también se puede utilizar para establecer la propiedad adjunta de `AutomationProperties.IsInAccessibleTree`, `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>Pormenores relativos a la accesibilidad

En las secciones siguientes se describen los pormenores que supone el hecho de establecer valores de accesibilidad en ciertos controles.

### <a name="navigationpage"></a>NavigationPage

En Android, para establecer el texto que el lector de pantalla leerá para la flecha Atrás de la barra de acciones [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), defina las propiedades `AutomationProperties.Name` y `AutomationProperties.HelpText` en [`Page`](xref:Xamarin.Forms.Page). Sin embargo, tenga en cuenta que esto no se aplicará a los botones Atrás del sistema operativo.

### <a name="masterdetailpage"></a>MasterDetailPage

En iOS y la Plataforma Universal de Windows (UWP), para establecer el texto que el lector de pantalla leerá para el botón de alternancia en [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), defina las propiedades `AutomationProperties.Name` y `AutomationProperties.HelpText` en `MasterDetailPage`, o bien en la propiedad `IconImageSource` de la página `Master`.

En Android, para establecer el texto que el lector de pantalla leerá para el botón de alternancia en [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), agregue los recursos de cadena al proyecto de Android:

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

A continuación, defina la `AutomationId` propiedad de la propiedad `IconImageSource` de la página `Master` en la cadena que corresponda:

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

En iOS, Android y UWP, los lectores de pantalla leerán el valor de propiedad `Text` de las instancias de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), siempre que no haya definido los valores `AutomationProperties.Name` y `AutomationProperties.HelpText`.

En iOS y UWP, el valor de propiedad `AutomationProperties.Name` reemplazará el valor de propiedad `Text` que el lector de pantalla lee.

En Android, los valores de propiedad `AutomationProperties.Name` o `AutomationProperties.HelpText` reemplazarán por completo el valor de propiedad `Text`, que es visible y el lector de pantalla leerá. Tenga en cuenta que se trata de una limitación de API inferior a 26.

## <a name="related-links"></a>Vínculos relacionados

- [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md)
- [Accesibilidad (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
