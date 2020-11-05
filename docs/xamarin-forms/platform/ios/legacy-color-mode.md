---
title: Modo de color heredado de VisualElement en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir los específicos de la plataforma iOS que deshabilita el Xamarin.Forms modo de color heredado.
ms.prod: xamarin
ms.assetid: 60FFBA67-6E06-439B-A5EB-8C808285E2CD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d10b48399fd0457bbf9cf63b4a57e17bce8f0f8f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372722"
---
# <a name="visualelement-legacy-color-mode-on-ios"></a>Modo de color heredado de VisualElement en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algunas de las Xamarin.Forms vistas presentan un modo de color heredado. En este modo, cuando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de la vista está establecida en `false` , la vista invalidará los colores establecidos por el usuario con los colores nativos predeterminados para el Estado deshabilitado. Por compatibilidad con versiones anteriores, este modo de color heredado sigue siendo el comportamiento predeterminado para las vistas admitidas.

Este modo específico de la plataforma iOS deshabilita este modo de color heredado en una [`VisualElement`](xref:Xamarin.Forms.VisualElement) , de modo que los colores establecidos en una vista por parte del usuario permanecen incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo la [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta en `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si el modo de color heredado está deshabilitado. Además, [ `VisualElement.GetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) que se puede usar para devolver si el modo de color heredado está deshabilitado.

El resultado es que el modo de color heredado se puede deshabilitar, de modo que los colores establecidos en una vista por parte del usuario permanezcan incluso cuando la vista esté deshabilitada:

![Modo de color heredado deshabilitado](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> Al establecer [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) en una vista, el modo de color heredado se omite por completo. Para obtener más información sobre los Estados visuales, consulte [el Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)