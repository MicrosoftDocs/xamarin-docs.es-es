---
title: Color de fondo de celda en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el valor específico de la plataforma iOS que establece el color de fondo predeterminado de las celdas en iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68651376"
---
# <a name="cell-background-color-on-ios"></a>Color de fondo de celda en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este valor específico de la plataforma iOS establece el color de [`Cell`](xref:Xamarin.Forms.Cell) fondo predeterminado de las instancias. Se consume en XAML estableciendo la `Cell.DefaultBackgroundColor` propiedad Bindable [`Color`](xref:Xamarin.Forms.Color)en:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Cell.SetDefaultBackgroundColor` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, establece el color de fondo de [`Color`](xref:Xamarin.Forms.Color)la celda en un especificado. Además, se puede `Cell.DefaultBackgroundColor` utilizar el método para recuperar el color de fondo de la celda actual.

El resultado es que el color de fondo de [`Cell`](xref:Xamarin.Forms.Cell) un se puede establecer en un [`Color`](xref:Xamarin.Forms.Color)específico:

[![Captura de pantalla de las celdas de encabezado de verde azulado, en iOS](cell-background-color-images/group-header-cell-color.png "ListView con celdas de encabezado de verde azulado")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con celdas de encabezado de verde azulado")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
