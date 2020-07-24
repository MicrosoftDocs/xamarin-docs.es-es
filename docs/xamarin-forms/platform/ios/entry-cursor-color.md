---
title: Color del cursor de entrada en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de iOS que establece el color del cursor de una entrada.
ms.prod: xamarin
ms.assetid: 867D70BA-53F9-4434-8094-85D71DCECC2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 501210f669c7d62eddaf7ebe3b4ebf19614589a7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936349"
---
# <a name="entry-cursor-color-on-ios"></a>Color del cursor de entrada en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS establece el color del cursor de un [`Entry`](xref:Xamarin.Forms.Entry) en un color especificado. Se consume en XAML estableciendo la [`Entry.CursorColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) propiedad Bindable en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

El `Entry.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Entry.SetCursorColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}, Xamarin.Forms . Color)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, establece el color del cursor en un especificado [`Color`](xref:Xamarin.Forms.Color) . Además, [ `Entry.GetCursorColor` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. GetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})) que se puede usar para recuperar el color actual del cursor.

El resultado es que el color del cursor de un [`Entry`](xref:Xamarin.Forms.Entry) se puede establecer en un específico [`Color`](xref:Xamarin.Forms.Color) :

![Color del cursor de entrada](entry-cursor-color-images/entry-cursorcolor.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
