---
title: Modo de entrada de teclado de software en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo utilizar el específico de la plataforma Android que establece el modo de funcionamiento de un área de entrada de teclado en pantalla.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 835f75b473fe966b5e92e7cb599f7675a7a459dd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655671"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Modo de entrada de teclado de software en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para establecer el modo de funcionamiento de un área de entrada de teclado en pantalla y se utiliza en XAML estableciendo [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) la propiedad adjunta en un valor de [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) la enumeración:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

El `Application.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer el modo de funcionamiento del área de entrada de teclado en pantalla, con el [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeración que proporciona dos valores: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) y [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). El `Pan` valor usa la [ `AdjustPan` ](xref:Android.Views.SoftInput.AdjustPan) la opción de ajuste, que no cambiar el tamaño de la ventana cuando un control de entrada tiene el foco. En su lugar, el contenido de la ventana distribuido para que el foco actual no está oculto por el teclado en pantalla. El `Resize` valor usa la [ `AdjustResize` ](xref:Android.Views.SoftInput.AdjustResize) la opción de ajuste, que cambia el tamaño de la ventana cuando un control de entrada tiene el foco, para dejar espacio para el teclado en pantalla.

El resultado es que el teclado en pantalla se puede establecer el modo de funcionamiento cuando un control de entrada tiene el foco del área de entrada:

[![](soft-keyboard-input-mode-images/pan-resize.png "Funcionamiento modo específico de la plataforma de teclado temporal")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "teclado en pantalla funciona en modo específico de la plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
