---
title: Modo de color heredado de VisualElement en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir los específicos de la plataforma Android que deshabilita el Xamarin.Forms modo de color heredado.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d4d270b2aeb1bd5e27f8a7a175e4707fd4a9b16
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368484"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Modo de color heredado de VisualElement en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algunas de las Xamarin.Forms vistas presentan un modo de color heredado. En este modo, cuando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de la vista está establecida en `false` , la vista invalidará los colores establecidos por el usuario con los colores nativos predeterminados para el Estado deshabilitado. Por compatibilidad con versiones anteriores, este modo de color heredado sigue siendo el comportamiento predeterminado para las vistas admitidas.

Este modo específico de la plataforma Android deshabilita este modo de color heredado, de modo que los colores establecidos en una vista por parte del usuario permanecen incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo la [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta en `false` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. VisualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . VisualElement}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si el modo de color heredado está deshabilitado. Además, [ `VisualElement.GetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. VisualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . VisualElement})) que se puede usar para devolver si el modo de color heredado está deshabilitado.

El resultado es que el modo de color heredado se puede deshabilitar, de modo que los colores establecidos en una vista por parte del usuario permanezcan incluso cuando la vista esté deshabilitada:

![Modo de color heredado deshabilitado](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> Al establecer [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) en una vista, el modo de color heredado se omite por completo. Para obtener más información sobre los Estados visuales, consulte [el Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)