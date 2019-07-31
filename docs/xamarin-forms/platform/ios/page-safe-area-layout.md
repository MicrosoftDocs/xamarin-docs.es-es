---
title: Guía de diseño de área segura en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma de iOS específica que garantiza que el contenido de la página se coloca en un área de la pantalla que es segura para todos los dispositivos que usan iOS 11 y versiones posteriores.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c6a2ec5a4d1466b7118e6cc7b03cc5518b27e2fb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644541"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guía de diseño de área segura en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para garantizar que el contenido de la página se coloca en un área de la pantalla que es segura para todos los dispositivos que usan iOS 11 y versiones posteriores. En concreto, le ayudará a asegurarse de que el contenido no recortado por dispositivo esquinas redondeadas, el indicador principal o el alojamiento del sensor en un iPhone X. Se consume en XAML estableciendo el `Page.UseSafeArea` propiedad adjunta un `boolean` valor:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetUseSafeArea` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) controla el espacio de nombres, si está habilitada la Guía de diseño de área segura.

El resultado es que el contenido de la página puede colocarse en un área de la pantalla que es seguro para todos los iPhone:

[![](page-safe-area-images/safe-area-layout.png "Guía de diseño de área segura")](page-safe-area-images/safe-area-layout-large.png#lightbox "Guía de diseño de área segura")

> [!NOTE]
> El área segura definido por Apple que se usa en Xamarin.Forms para establecer el [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propiedad y reemplazará los valores anteriores de esta propiedad que se han establecido.

El área segura se puede personalizar mediante la recuperación de su [ `Thickness` ](xref:Xamarin.Forms.Thickness) valor con el `Page.SafeAreaInsets` método desde el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres. A continuación, se puede modificar como necesarios y volver a asignar a la `Padding` propiedad en el constructor de la página o [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidar:

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

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
