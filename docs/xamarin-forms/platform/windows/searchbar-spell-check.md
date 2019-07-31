---
title: " Spell Check barra en Windows"
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que permite a un barra interactuar con el motor de revisión ortográfica.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 863748a7af057d2ea3e53719c332cd555ff3b698
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656860"
---
# <a name="searchbar-spell-check-on-windows"></a>Spell Check barra en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma universal de Windows específica de la plataforma permite [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un interactuar con el motor de revisión ortográfica. Se consume en XAML estableciendo el [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) propiedad adjunta un `boolean` valor:

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

El `SearchBar.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se activa el corrector ortográfico y desactiva. Además, el `SearchBar.SetIsSpellCheckEnabled` método puede utilizarse para activar o desactivar el corrector ortográfico mediante una llamada a la [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método devuelva si el corrector ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

El resultado es que el texto escrito en el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) puede spell comprobarse, con ortografía incorrecta que se indica al usuario:

![SearchBar spell check específicas de la plataforma](searchbar-spell-check-images/searchbar-spellcheck.png "SearchBar spell check específicas de la plataforma")

> [!NOTE]
> El `SearchBar` clase en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) también dispone de espacio de nombres [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) y [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que pueden usarse para habilitar y deshabilitar el corrector ortográfico en el `SearchBar`, respectivamente.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
