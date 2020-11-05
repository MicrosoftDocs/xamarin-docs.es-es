---
title: Selección de elementos de TimePicker en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla cuándo se produce la selección de elementos en un TimePicker.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a8c8a378775a9e52731e4b2df37b983fc15c1867
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375322"
---
# <a name="timepicker-item-selection-on-ios"></a>Selección de elementos de TimePicker en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Estos controles específicos de la plataforma iOS cuando se selecciona el elemento en un objeto [`TimePicker`](xref:Xamarin.Forms.TimePicker) , lo que permite al usuario especificar la selección de elementos que se produce al examinar elementos del control o solo una vez que se presiona el botón **listo** . Se consume en XAML estableciendo la `TimePicker.UpdateMode` propiedad adjunta en un valor de la `UpdateMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

El `TimePicker.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `TimePicker.SetUpdateMode` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar cuándo se produce la selección del elemento, con la `UpdateMode` enumeración que proporciona dos valores posibles:

- `Immediately` : la selección de elementos se produce cuando el usuario examina los elementos de [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Éste es el comportamiento predeterminado en Xamarin.Forms .
- `WhenFinished` : la selección de elementos solo se produce una vez que el usuario ha presionado el botón **Done** en el [`TimePicker`](xref:Xamarin.Forms.TimePicker) .

Además, el `SetUpdateMode` método se puede utilizar para alternar los valores de enumeración llamando al `UpdateMode` método, que devuelve la actual `UpdateMode` :

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

El resultado es que `UpdateMode` se aplica un especificado a [`TimePicker`](xref:Xamarin.Forms.TimePicker) , que controla cuándo se produce la selección de elementos:

[![Captura de pantalla de los modos de actualización de TimePicker](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode Platform-Specific")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)