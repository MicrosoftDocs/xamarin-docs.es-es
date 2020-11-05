---
title: ListView (SelectionMode) en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que controla si los elementos de un control ListView pueden responder a los gestos de TAP.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9588feeaae1391bcd2ac7fd05e7340a3a40f6236
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374802"
---
# <a name="listview-selectionmode-on-windows"></a>ListView (SelectionMode) en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica que `ListView` desencadenará el evento nativo `ItemClick` para controlar la interacción y, por tanto, proporcionará la funcionalidad de accesibilidad. Por lo tanto, el narrador de Windows y el teclado pueden interactuar con `ListView` . Sin embargo, los elementos del `ListView` no pueden responder a los gestos de TAP. Éste es el comportamiento predeterminado de las `ListView` instancias en el plataforma universal de Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica que `ListView` desencadenará el evento nativo `Tapped` para controlar la interacción. Por lo tanto, los elementos de `ListView` pueden responder a los gestos de TAP. Sin embargo, no hay ninguna funcionalidad de accesibilidad y, por lo tanto, el narrador de Windows y el teclado no pueden interactuar con `ListView` .

> [!NOTE]
> Los `Accessible` `Inaccessible` modos de selección y son mutuamente excluyentes y tendrá que elegir entre un accesible [`ListView`](xref:Xamarin.Forms.ListView) o un `ListView` que pueda responder a los gestos de TAP.

Además, [ `GetSelectionMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. GetSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView})) que se puede usar para devolver el objeto actual [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) .

El resultado es que [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) se aplica un especificado a [`ListView`](xref:Xamarin.Forms.ListView) , que controla si los elementos de `ListView` pueden responder a los gestos de punteo y, por tanto, si el nativo `ListView` activa el `ItemClick` `Tapped` evento o.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)