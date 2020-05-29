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
ms.openlocfilehash: 594e436c9db7c123fea4f9aa262c9d27af765b07
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136011"
---
# <a name="listview-row-animations-on-ios"></a>Animaciones de filas de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS indica si se deshabilitan las animaciones de filas cuando [`ListView`](xref:Xamarin.Forms.ListView) se actualiza la colección de elementos. Se consume en XAML estableciendo la `ListView.RowAnimationsEnabled` propiedad Bindable en `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

El `ListView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `ListView.SetRowAnimationsEnabled` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si se deshabilitan las animaciones de filas cuando [`ListView`](xref:Xamarin.Forms.ListView) se actualiza la colección de elementos. Además, se `ListView.GetRowAnimationsEnabled` puede utilizar el método para devolver si las animaciones de fila están deshabilitadas en `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)las animaciones de fila están habilitadas de forma predeterminada. Por lo tanto, una animación se produce cuando se inserta una nueva fila en `ListView` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
