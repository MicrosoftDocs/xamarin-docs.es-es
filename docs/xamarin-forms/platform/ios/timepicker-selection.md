---
title: Selección de elementos de TimePicker en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla cuándo se produce la selección de elementos en un TimePicker.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 818f368da8ebb375fbacd97d3d48185ba60470d4
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646681"
---
# <a name="timepicker-item-selection-on-ios"></a>Selección de elementos de TimePicker en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Estos controles específicos de la plataforma iOS cuando se realiza la selección de elementos en un [`TimePicker`](xref:Xamarin.Forms.TimePicker), lo que permite al usuario especificar la selección de elementos que se produce al examinar elementos en el control o solo una vez que se presiona el botón **listo** . Se consume en XAML estableciendo el `TimePicker.UpdateMode` propiedad adjunta en un valor de la enumeración `UpdateMode`:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

El método `TimePicker.On<iOS>` especifica que este específico de la plataforma solo se ejecutará en iOS. El método `TimePicker.SetUpdateMode`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , se utiliza para controlar cuándo se produce la selección del elemento, con la enumeración `UpdateMode` que proporciona dos valores posibles:

- `Immediately`: la selección de elementos se produce cuando el usuario examina los elementos de la [`TimePicker`](xref:Xamarin.Forms.TimePicker). Este es el comportamiento predeterminado en Xamarin.Forms.
- `WhenFinished`: la selección de elementos solo se produce una vez que el usuario ha presionado el botón **Done** en el [`TimePicker`](xref:Xamarin.Forms.TimePicker).

Además, el método `SetUpdateMode` se puede utilizar para alternar los valores de enumeración llamando al método `UpdateMode`, que devuelve la `UpdateMode`actual:

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

El resultado es que un `UpdateMode` especificado se aplica a la [`TimePicker`](xref:Xamarin.Forms.TimePicker), que controla cuándo se produce la selección de elementos:

[![Captura de pantalla de los modos de actualización de TimePicker](timepicker-selection-images/timepicker-updatemode.png "Específico de la plataforma TimePicker UpdateMode")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "Específico de la plataforma TimePicker UpdateMode")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
