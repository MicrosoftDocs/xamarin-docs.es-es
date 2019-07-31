---
title: Estilo de encabezado de grupo de ListView en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si las celdas de encabezado de ListView flotan durante el desplazamiento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648318"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo de encabezado de grupo de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS [`ListView`](xref:Xamarin.Forms.ListView) determina si las celdas de encabezado flotan durante el desplazamiento. Se consume en XAML estableciendo la `ListView.GroupHeaderStyle` propiedad Bindable en un valor de la `GroupHeaderStyle` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `ListView.SetGroupHeaderStyle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para [`ListView`](xref:Xamarin.Forms.ListView) controlar si las celdas de encabezado flotan durante el desplazamiento. La `GroupHeaderStyle` enumeración proporciona dos valores posibles:

- `Plain`: indica que las celdas de encabezado flotan cuando se desplaza el [`ListView`](xref:Xamarin.Forms.ListView) valor (predeterminado).
- `Grouped`: indica que las celdas de encabezado no flotan [`ListView`](xref:Xamarin.Forms.ListView) cuando se desplaza.

Además, el `ListView.GetGroupHeaderStyle` método se puede utilizar para devolver el `GroupHeaderStyle` [`ListView`](xref:Xamarin.Forms.ListView)que se aplica a.

El resultado es que un valor `GroupHeaderStyle` especificado se aplica a, [`ListView`](xref:Xamarin.Forms.ListView)que controla si las celdas de encabezado flotan durante el desplazamiento:

[ ![Captura de pantalla de las celdas de encabezado de ListView flotantes y no flotantes, en la ListView de iOS](listview-group-header-style-images/group-header-styles.png "con celdas de encabezado flotante y no flotantes") ] (listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celdas de encabezado flotante y no flotante")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
