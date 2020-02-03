---
title: " Spell Check barra en Windows"
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que permite a un barra interactuar con el motor de revisión ortográfica.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: da75d42e0e0bcf38dd2ff50999b705125dc4e2b5
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723619"
---
# <a name="searchbar-spell-check-on-windows"></a>Spell Check barra en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma permite a un [`SearchBar`](xref:Xamarin.Forms.SearchBar) interactuar con el motor de revisión ortográfica. Se utiliza en XAML estableciendo el [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) propiedad adjunta en un valor `boolean`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

El método `SearchBar.On<Windows>` especifica que este específico de la plataforma solo se ejecutará en el Plataforma universal de Windows. El método [`SearchBar.SetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) , en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , activa y desactiva el corrector ortográfico. Además, el método `SearchBar.SetIsSpellCheckEnabled` se puede utilizar para alternar el corrector ortográfico llamando al método [`SearchBar.GetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) para devolver si está habilitado el corrector ortográfico:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

El resultado es que el texto que se escribe en el [`SearchBar`](xref:Xamarin.Forms.SearchBar) se puede comprobar ortográficamente, con ortografías incorrectas que se indican al usuario:

![Barra spell check: específico de la plataforma](searchbar-spell-check-images/searchbar-spellcheck.png "Barra spell check: específico de la plataforma")

> [!NOTE]
> La clase `SearchBar` del espacio de nombres [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) también tiene los métodos [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) y [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) que se pueden usar para habilitar y deshabilitar el corrector ortográfico en el `SearchBar`, respectivamente.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
