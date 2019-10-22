---
title: Estilo de encabezado de grupo de ListView en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si las celdas de encabezado de ListView flotan durante el desplazamiento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648318"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo de encabezado de grupo de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS determina si [`ListView`](xref:Xamarin.Forms.ListView) las celdas de encabezado flotan durante el desplazamiento. Se consume en XAML estableciendo la propiedad `ListView.GroupHeaderStyle` enlazable en un valor de la enumeración `GroupHeaderStyle`:

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

Como alternativa, se puede utilizar para C# usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

El método `ListView.On<iOS>` especifica que este específico de la plataforma solo se ejecutará en iOS. El método `ListView.SetGroupHeaderStyle`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , se utiliza para controlar si [`ListView`](xref:Xamarin.Forms.ListView) las celdas de encabezado flotan durante el desplazamiento. La enumeración `GroupHeaderStyle` proporciona dos valores posibles:

- `Plain`: indica que las celdas de encabezado flotan cuando se desplaza el [`ListView`](xref:Xamarin.Forms.ListView) (valor predeterminado).
- `Grouped`: indica que las celdas de encabezado no flotan cuando se desplaza el [`ListView`](xref:Xamarin.Forms.ListView) .

Además, se puede utilizar el método `ListView.GetGroupHeaderStyle` para devolver el `GroupHeaderStyle` que se aplica a la [`ListView`](xref:Xamarin.Forms.ListView).

El resultado es que un valor de `GroupHeaderStyle` especificado se aplica a la [`ListView`](xref:Xamarin.Forms.ListView), que controla si las celdas de encabezado flotan durante el desplazamiento:

[![Captura de pantalla de las celdas de encabezado de ListView flotantes y no flotantes, en iOS](listview-group-header-style-images/group-header-styles.png "ListView con celdas de encabezado flotante y no flotante")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celdas de encabezado flotante y no flotante")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
