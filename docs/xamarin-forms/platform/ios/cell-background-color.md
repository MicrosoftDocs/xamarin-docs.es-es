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
ms.openlocfilehash: 90282262926fef663183be247e37d64dd1be9124
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138575"
---
# <a name="cell-background-color-on-ios"></a>Color de fondo de celda en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este valor específico de la plataforma iOS establece el color de fondo predeterminado de [`Cell`](xref:Xamarin.Forms.Cell) las instancias. Se consume en XAML estableciendo la `Cell.DefaultBackgroundColor` propiedad Bindable en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
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

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

El `ListView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Cell.SetDefaultBackgroundColor` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, establece el color de fondo de la celda en un especificado [`Color`](xref:Xamarin.Forms.Color) . Además, `Cell.DefaultBackgroundColor` se puede utilizar el método para recuperar el color de fondo de la celda actual.

El resultado es que el color de fondo de un [`Cell`](xref:Xamarin.Forms.Cell) se puede establecer en un específico [`Color`](xref:Xamarin.Forms.Color) :

[![Captura de pantalla de las celdas de encabezado de verde azulado, en iOS](cell-background-color-images/group-header-cell-color.png "ListView con celdas de encabezado de verde azulado")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con celdas de encabezado de verde azulado")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
