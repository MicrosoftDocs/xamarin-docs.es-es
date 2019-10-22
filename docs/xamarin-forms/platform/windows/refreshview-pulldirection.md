---
title: Dirección de extracción de RefreshView en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma de Windows que permite cambiar la dirección de extracción de un RefreshView.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697654"
---
# <a name="refreshview-pull-direction-on-windows"></a>Dirección de extracción de RefreshView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma permite cambiar la dirección de extracción de una `RefreshView` para que coincida con la orientación del control desplazable que muestra los datos. Se consume en XAML estableciendo la propiedad `RefreshView.RefreshPullDirection` enlazable en un valor de la enumeración `RefreshPullDirection`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

Como alternativa, se puede utilizar para C# usar la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

El método `RefreshView.On<Windows>` especifica que este específico de la plataforma solo se ejecutará en el Plataforma universal de Windows. El método `RefreshView.SetRefreshPullDirection`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , se usa para establecer la dirección de extracción del `RefreshView`, con la enumeración `RefreshPullDirection` que proporciona cuatro valores posibles:

- `LeftToRight` indica que una extracción de izquierda a derecha inicia una actualización.
- `TopToBottom` indica que una extracción de la parte superior a la inferior inicia una actualización y es la dirección de extracción predeterminada de un `RefreshView`.
- `RightToLeft` indica que una extracción de derecha a izquierda inicia una actualización.
- `BottomToTop` indica que una extracción de la parte inferior a la superior inicia una actualización.

Además, se puede utilizar el método `GetRefreshPullDirection` para devolver el `RefreshPullDirection` actual de la `RefreshView`.

El resultado es que se aplica un `RefreshPullDirection` especificado al `RefreshView`, para establecer la dirección de extracción de forma que coincida con la orientación del control desplazable que muestra los datos. En la captura de pantalla siguiente se muestra un `RefreshView` con un `LeftToRight` dirección de extracción:

[![Captura de pantalla de un RefreshView con una dirección de extracción de izquierda a derecha, en UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView con dirección de extracción de izquierda a derecha")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView con dirección de extracción de izquierda a derecha")

> [!NOTE]
> Al cambiar la dirección de extracción, la posición inicial del círculo de progreso gira automáticamente para que la flecha comience en la posición adecuada para la dirección de extracción.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
