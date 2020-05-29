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
ms.openlocfilehash: 6c73f46d2845be7bb54e24cd02ec22f3c2cd386d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137257"
---
# <a name="listview-selectionmode-on-windows"></a>ListView (SelectionMode) en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

En el Plataforma universal de Windows, de forma predeterminada, Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) usa el `ItemClick` evento nativo para responder a la interacción, en lugar del `Tapped` evento nativo. Esto proporciona funcionalidad de accesibilidad para que el narrador de Windows y el teclado puedan interactuar con `ListView` . Sin embargo, también representa todos los gestos de TAP dentro de `ListView` inoperativo.

Esta Plataforma universal de Windows controles específicos de la plataforma si los elementos de un [`ListView`](xref:Xamarin.Forms.ListView) pueden responder a los gestos de TAP y, por tanto, si el nativo `ListView` desencadena el `ItemClick` `Tapped` evento o. Se consume en XAML estableciendo la [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propiedad adjunta en un valor de la [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

El `ListView.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. [ `ListView.SetSelectionMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. SetSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView}, Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListViewSelectionMode)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si los elementos de un [`ListView`](xref:Xamarin.Forms.ListView) pueden responder a los gestos de TAP, con la [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración que proporciona dos valores posibles:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible): indica que `ListView` desencadenará el evento nativo `ItemClick` para controlar la interacción y, por tanto, proporcionará la funcionalidad de accesibilidad. Por lo tanto, el narrador de Windows y el teclado pueden interactuar con `ListView` . Sin embargo, los elementos del `ListView` no pueden responder a los gestos de TAP. Éste es el comportamiento predeterminado de las `ListView` instancias en el plataforma universal de Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible): indica que `ListView` desencadenará el evento nativo `Tapped` para controlar la interacción. Por lo tanto, los elementos de `ListView` pueden responder a los gestos de TAP. Sin embargo, no hay ninguna funcionalidad de accesibilidad y, por lo tanto, el narrador de Windows y el teclado no pueden interactuar con `ListView` .

> [!NOTE]
> Los `Accessible` `Inaccessible` modos de selección y son mutuamente excluyentes y tendrá que elegir entre un accesible [`ListView`](xref:Xamarin.Forms.ListView) o un `ListView` que pueda responder a los gestos de TAP.

Además, [ `GetSelectionMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. GetSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView})) que se puede usar para devolver el objeto actual [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) .

El resultado es que [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) se aplica un especificado a [`ListView`](xref:Xamarin.Forms.ListView) , que controla si los elementos de `ListView` pueden responder a los gestos de punteo y, por tanto, si el nativo `ListView` activa el `ItemClick` `Tapped` evento o.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
