---
title: ListView (SelectionMode) en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que controla si los elementos de un control ListView pueden responder a los gestos de TAP.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f6a90a8a0397db99a245f706450e7dc83097a45e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656896"
---
# <a name="listview-selectionmode-on-windows"></a>ListView (SelectionMode) en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

En la plataforma Universal de Windows, de forma predeterminada el Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` eventos para responder a interacción, en lugar de nativo `Tapped` eventos. Esto proporciona la funcionalidad de accesibilidad para que el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, también presenta los gestos de tap dentro de la `ListView` no funciona.

Esta plataforma universal de Windows controles específicos de la plataforma si los elementos [`ListView`](xref:Xamarin.Forms.ListView) de un pueden responder a los gestos de TAP y, `ListView` por tanto, `Tapped` si el nativo desencadena el `ItemClick` evento o. Se consume en XAML estableciendo el [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propiedad adjunta a un valor de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

El `ListView.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si los elementos de un [ `ListView` ](xref:Xamarin.Forms.ListView) puede responder pulse gestos, con el [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración que proporciona dos valores posibles:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica que el `ListView` desencadenará nativo `ItemClick` eventos para controlar la interacción y, por lo tanto, proporcionar una funcionalidad de accesibilidad. Por lo tanto, el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, los elementos de la `ListView` no puede responder para puntear gestos. Este es el comportamiento predeterminado para `ListView` instancias en la plataforma Universal de Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica que el `ListView` desencadenará nativo `Tapped` eventos para controlar la interacción. Por lo tanto, los elementos de la `ListView` pulse gestos puede responder. Sin embargo, no hay ninguna funcionalidad de accesibilidad y, por tanto, el Narrador de Windows y el teclado no pueden interactuar con el `ListView`.

> [!NOTE]
> El `Accessible` y `Inaccessible` modos de selección son mutuamente excluyentes y tendrá que elegir entre una accesible [ `ListView` ](xref:Xamarin.Forms.ListView) o un `ListView` que puede responder a los gestos de pulse.

Además, el [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método puede utilizarse para devolver actual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

El resultado es que un determinado [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla si los elementos de la `ListView` pulse gestos, puede responder y, por tanto, si nativo `ListView` se activa el `ItemClick` o `Tapped` eventos.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
