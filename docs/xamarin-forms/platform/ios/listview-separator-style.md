---
title: Estilo de separador de ListView en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si el separador entre las celdas de un control ListView usa el ancho completo del control ListView.
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 921ef905eed17b6bfe5cb2ea401f7da92ff744af
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648113"
---
# <a name="listview-separator-style-on-ios"></a>Estilo de separador de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico `ListView`de la plataforma iOS determina si el separador entre [`ListView`](xref:Xamarin.Forms.ListView) las celdas de un usa el ancho completo de. Se consume en XAML estableciendo el [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) propiedad adjunta a un valor de la [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
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

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si el separador entre las celdas de la [ `ListView` ](xref:Xamarin.Forms.ListView) usa completo ancho de la `ListView`, con el [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeración que proporciona dos valores posibles:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) : indica el comportamiento de separador de iOS de forma predeterminada. Este es el comportamiento predeterminado en Xamarin.Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) : indica que se dibujarán los separadores de uno de los bordes de la `ListView` a otro.

El resultado es que un determinado [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valor se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla el ancho del separador entre las celdas:

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle específico de plataforma")

> [!NOTE]
> Una vez que se ha establecido el estilo del separador en `FullWidth`, no se puede cambiar a `Default` en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
