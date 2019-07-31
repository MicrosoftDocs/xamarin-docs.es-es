---
title: Desplazamiento rápido de ListView en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que permite el desplazamiento rápido a través de los datos de un control ListView.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ce51483da9599cf049cf005ae18b35d110aa325b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649990"
---
# <a name="listview-fast-scrolling-on-android"></a>Desplazamiento rápido de ListView en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para habilitar el desplazamiento rápido a través de los datos [`ListView`](xref:Xamarin.Forms.ListView)de un. Se consume en XAML estableciendo el `ListView.IsFastScrollEnabled` propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

El `ListView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `ListView.SetIsFastScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Además, el `SetIsFastScrollEnabled` método puede utilizarse para activar o desactivar el desplazamiento rápido mediante una llamada a la `IsFastScrollEnabled` método para devolver si se habilita el desplazamiento rápido:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

El resultado es ese desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView) puede habilitarse, que cambia el tamaño de la miniatura de desplazamiento:

[![](listview-fast-scrolling-images/fastscroll.png "Específico de plataforma ListView FastScroll")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll específico de plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
