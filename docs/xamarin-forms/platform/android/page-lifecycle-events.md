---
title: Eventos del ciclo de vida de la página en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que deshabilita los eventos de la página que aparecen en la pausa y en la detención de la aplicación, respectivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76724ff17613fcebe35cb68518a1c932eee8aad7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128731"
---
# <a name="page-lifecycle-events-on-android"></a>Eventos del ciclo de vida de la página en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para deshabilitar los [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos de página y en la pausa y reanudación de la aplicación, respectivamente, para las aplicaciones que usan AppCompat. Además, incluye la posibilidad de controlar si se muestra el teclado en pantalla al reanudar, en caso de que se muestre en pausa, siempre que el modo de funcionamiento del teclado en pantalla esté establecido en [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

> [!NOTE]
> Tenga en cuenta que estos eventos están habilitados de forma predeterminada para conservar el comportamiento existente de las aplicaciones que se basan en los eventos. La deshabilitación de estos eventos hace que el ciclo de eventos AppCompat coincida con el ciclo de eventos anterior a la AppCompat.

Este específico de la plataforma se puede consumir en XAML estableciendo las [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) propiedades, y [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) asociadas a `boolean` los valores:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

El `Application.Current.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `Application.SendDisappearingEventOnPause` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendDisappearingEventOnPause ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres, se usa para habilitar o deshabilitar la activación del [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) evento de página, cuando la aplicación entra en el fondo. [ `Application.SendAppearingEventOnResume` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendAppearingEventOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) se usa para habilitar o deshabilitar la activación del [`Appearing`](xref:Xamarin.Forms.Page.Appearing) evento de página cuando la aplicación se reanuda desde el fondo. [ `Application.ShouldPreserveKeyboardOnResume` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. ShouldPreserveKeyboardOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) el método se usa para controlar si se muestra el teclado en pantalla al reanudar, en caso de que se muestre en pausa, siempre que el modo de funcionamiento del teclado en pantalla esté establecido en [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

El resultado es que los [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos de página y no se activarán en la pausa y la reanudación de la aplicación, respectivamente, y que si se mostró el teclado en pantalla cuando se pausó la aplicación, también se mostrará cuando se reanude la aplicación:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
