---
title: Modo de color heredado de VisualElement en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que deshabilita el modo de color heredado de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: B8759309-07C7-4DCA-A18A-C1A198A7951B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 7319b0886476ea502b7b9c450416cb4fe69e01fa
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656910"
---
# <a name="visualelement-legacy-color-mode-on-windows"></a>Modo de color heredado de VisualElement en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algunas de las vistas de Xamarin.Forms cuentan con un modo de color heredado. En este modo, cuando el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) se establece la propiedad de la vista en `false`, la vista invalida los colores establecidos por el usuario con los colores nativo predeterminado para el estado deshabilitado. Para hacia atrás compatibilidad, este modo heredado de color permanece el comportamiento predeterminado para las vistas admitidas.

Esta Plataforma universal de Windows específica de la plataforma deshabilita este modo de color heredado, de modo que los colores establecidos en una vista por parte del usuario permanecen incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Windows>` método especifica que solo se ejecutarán este específicos de la plataforma en Windows. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) método puede utilizarse para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando se deshabilita la vista:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Modo heredado color deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite completamente el modo de color heredado. Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
