---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c65cac4c777150185524b291adc6e9d1e79958d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138559"
---
# <a name="datepicker-item-selection-on-ios"></a>Selección de elementos del DatePicker en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Estos controles específicos de la plataforma iOS cuando se selecciona el elemento en un objeto [`DatePicker`](xref:Xamarin.Forms.DatePicker) , lo que permite al usuario especificar la selección de elementos que se produce al examinar elementos del control o solo una vez que se presiona el botón **listo** . Se consume en XAML estableciendo la `DatePicker.UpdateMode` propiedad adjunta en un valor de la `UpdateMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

El `DatePicker.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `DatePicker.SetUpdateMode` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar cuándo se produce la selección del elemento, con la `UpdateMode` enumeración que proporciona dos valores posibles:

- `Immediately`: la selección de elementos se produce cuando el usuario examina los elementos de [`DatePicker`](xref:Xamarin.Forms.DatePicker) . Éste es el comportamiento predeterminado en Xamarin.Forms .
- `WhenFinished`: la selección de elementos solo se produce una vez que el usuario ha presionado el botón **Done** en el [`DatePicker`](xref:Xamarin.Forms.DatePicker) .

Además, el `SetUpdateMode` método se puede utilizar para alternar los valores de enumeración llamando al `UpdateMode` método, que devuelve la actual `UpdateMode` :

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

El resultado es que `UpdateMode` se aplica un especificado a [`DatePicker`](xref:Xamarin.Forms.DatePicker) , que controla cuándo se produce la selección de elementos:

[![Captura de pantalla de los modos de actualización de DatePicker](datepicker-selection-images/datepicker-updatemode.png "Específico de la plataforma DatePicker UpdateMode")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "Específico de la plataforma DatePicker UpdateMode")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
