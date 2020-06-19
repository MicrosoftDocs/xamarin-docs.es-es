---
title: Estilo de encabezado de grupo de ListView en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si las celdas de encabezado de ListView flotan durante el desplazamiento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46e8dec3d5644defdeb8a2265a73815adfde92d8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136043"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo de encabezado de grupo de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS determina si [`ListView`](xref:Xamarin.Forms.ListView) las celdas de encabezado flotan durante el desplazamiento. Se consume en XAML estableciendo la `ListView.GroupHeaderStyle` propiedad Bindable en un valor de la `GroupHeaderStyle` enumeración:

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

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

El `ListView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `ListView.SetGroupHeaderStyle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si [`ListView`](xref:Xamarin.Forms.ListView) las celdas de encabezado flotan durante el desplazamiento. La `GroupHeaderStyle` enumeración proporciona dos valores posibles:

- `Plain`: indica que las celdas de encabezado flotan cuando [`ListView`](xref:Xamarin.Forms.ListView) se desplaza el valor (predeterminado).
- `Grouped`: indica que las celdas de encabezado no flotan cuando [`ListView`](xref:Xamarin.Forms.ListView) se desplaza.

Además, el `ListView.GetGroupHeaderStyle` método se puede utilizar para devolver el `GroupHeaderStyle` que se aplica a [`ListView`](xref:Xamarin.Forms.ListView) .

El resultado es que un `GroupHeaderStyle` valor especificado se aplica a [`ListView`](xref:Xamarin.Forms.ListView) , que controla si las celdas de encabezado flotan durante el desplazamiento:

[![Captura de pantalla de las celdas de encabezado de ListView flotantes y no flotantes, en iOS](listview-group-header-style-images/group-header-styles.png "ListView con celdas de encabezado flotante y no flotante")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celdas de encabezado flotante y no flotante")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
