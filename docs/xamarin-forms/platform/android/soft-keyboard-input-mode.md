---
title: Modo de entrada de teclado de software en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que establece el modo de funcionamiento de un área de entrada de teclado en pantalla.
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c62d09c7d7848d9f62c018caa1698bb53a2a39a8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128720"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Modo de entrada de teclado de software en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para establecer el modo de funcionamiento de un área de entrada de teclado en pantalla y se utiliza en XAML estableciendo la [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) propiedad adjunta en un valor de la [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeración:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

El `Application.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `Application.UseWindowSoftInputModeAdjust` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Application. UseWindowSoftInputModeAdjust ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Aplicación}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer el modo de funcionamiento del área de entrada de teclado en pantalla, con la [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeración que proporciona dos valores: [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) y [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) . El `Pan` valor usa la [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) opción de ajuste, que no cambia el tamaño de la ventana cuando un control de entrada tiene el foco. En su lugar, el contenido de la ventana se panorámica para que el foco actual no se oculte por el teclado en pantalla. El `Resize` valor usa la [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) opción de ajuste, que cambia el tamaño de la ventana cuando un control de entrada tiene el foco, para dejar espacio para el teclado en pantalla.

El resultado es que el modo de funcionamiento del área de entrada de teclado en pantalla se puede establecer cuando un control de entrada tiene el foco:

[![](soft-keyboard-input-mode-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
