---
title: Acciones de contexto ViewCell en Android
description: 'Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma Android: específica que habilita el modo heredado de acciones de contexto ViewCell.'
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: b040c7c5b7f132b0832469efba91dd89d6b2ddbd
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697750"
---
# <a name="viewcell-context-actions-on-android"></a>Acciones de contexto ViewCell en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

De forma predeterminada, de Xamarin. Forms 4,3, cuando un [`ViewCell`](xref:Xamarin.Forms.ViewCell) en una aplicación de Android define acciones de contexto para cada elemento de un [`ListView`](xref:Xamarin.Forms.ListView), el menú acciones de contexto se actualiza cuando cambia el elemento seleccionado en el `ListView`. Sin embargo, en las versiones anteriores de Xamarin. Forms, el menú acciones de contexto no se actualizaba y este comportamiento se denomina modo heredado `ViewCell`. Este modo heredado puede producir un comportamiento incorrecto si un `ListView` utiliza un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para establecer su `ItemTemplate` a partir de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que definen diferentes acciones de contexto.

Este elemento específico de la plataforma Android habilita el modo heredado del menú acciones de [`ViewCell`](xref:Xamarin.Forms.ViewCell) contexto, por compatibilidad con versiones anteriores, para que el menú acciones de contexto no se actualice cuando cambie el elemento seleccionado en un [`ListView`](xref:Xamarin.Forms.ListView) . Se consume en XAML estableciendo la propiedad `ViewCell.IsContextActionsLegacyModeEnabled` enlazable en `true`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede utilizar para C# usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

El método `ViewCell.On<Android>` especifica que este específico de la plataforma solo se ejecutará en Android. El método `ViewCell.SetIsContextActionsLegacyModeEnabled`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , se usa para habilitar el modo heredado del menú acciones de contexto de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , de modo que el menú acciones de contexto no se actualice cuando el elemento seleccionado en una [`ListView`](xref:Xamarin.Forms.ListView) cambie. Además, se puede usar el método `ViewCell.GetIsContextActionsLegacyModeEnabled` para devolver si está habilitado el modo heredado de acciones de contexto.

Las capturas de pantallas siguientes muestran [`ViewCell`](xref:Xamarin.Forms.ViewCell) el modo heredado de acciones de contexto habilitado:

![Captura de pantalla del modo heredado de ViewCell habilitado, en Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modo heredado de ViewCell habilitado")

En este modo, los elementos de menú de acción de contexto mostrados son idénticos para la celda 1 y la celda 2, a pesar de que se estén definiendo distintos elementos de menú contextual para la celda 2.

Las capturas de pantallas siguientes muestran [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo de operaciones de contexto heredado deshabilitado, que es el comportamiento predeterminado de Xamarin. Forms:

![Captura de pantalla del modo heredado de ViewCell deshabilitado, en Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modo heredado de ViewCell deshabilitado")

En este modo, se muestran los elementos de menú de acción de contexto correctos para las celdas 1 y 2.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
