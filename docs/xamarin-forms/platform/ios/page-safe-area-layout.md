---
title: Guía de diseño de área segura en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma de iOS específica que garantiza que el contenido de la página se coloca en un área de la pantalla que es segura para todos los dispositivos que usan iOS 11 y versiones posteriores.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5907e5493ff43c6a69dc4a8a8e5d2b4fc78210e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374880"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guía de diseño de área segura en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para garantizar que el contenido de la página se coloca en un área de la pantalla que es segura para todos los dispositivos que usan iOS 11 y versiones posteriores. En concreto, le ayudará a asegurarse de que el contenido no se recorta con las esquinas redondeadas del dispositivo, el indicador de inicio o la carcasa del sensor en un iPhone X. Se consume en XAML estableciendo la `Page.UseSafeArea` propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Page.SetUseSafeArea` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla si está habilitada la guía de diseño de área segura.

El resultado es que el contenido de la página puede colocarse en un área de la pantalla que sea segura para todos los iPhone:

[![Guía de diseño de área segura](page-safe-area-images/safe-area-layout.png)](page-safe-area-images/safe-area-layout-large.png#lightbox "Guía de diseño de área segura")

> [!NOTE]
> El área segura definida por Apple se usa en Xamarin.Forms para establecer la [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) propiedad y reemplazará los valores anteriores de esta propiedad que se hayan establecido.

El área segura se puede personalizar recuperando su [`Thickness`](xref:Xamarin.Forms.Thickness) valor con el `Page.SafeAreaInsets` método del espacio de [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) nombres. Después, se puede modificar según sea necesario y volver a asignarse a la `Padding` propiedad en la [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) invalidación:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)