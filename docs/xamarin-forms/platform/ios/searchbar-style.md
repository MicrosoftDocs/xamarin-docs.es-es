---
title: Estilo barra en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si un barra tiene un fondo.
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 7d95a90e96f868b6d8368054659f978555bc28ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532956"
---
# <a name="searchbar-style-on-ios"></a>Estilo barra en iOS

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS determina [`SearchBar`](xref:Xamarin.Forms.SearchBar) si tiene un fondo. Se consume en XAML estableciendo la `SearchBar.SearchBarStyle` propiedad Bindable en un valor de la `UISearchBarStyle` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

El `SearchBar.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `SearchBar.SetSearchBarStyle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar [`SearchBar`](xref:Xamarin.Forms.SearchBar) si tiene un fondo. La `UISearchBarStyle` enumeración proporciona tres valores posibles:

- `Default`indica que [`SearchBar`](xref:Xamarin.Forms.SearchBar) tiene el estilo predeterminado. Este es el valor predeterminado de la `SearchBar.SearchBarStyle` propiedad enlazable.
- `Prominent`indica que [`SearchBar`](xref:Xamarin.Forms.SearchBar) tiene un fondo translúcido y el campo de búsqueda es opaco.
- `Minimal`indica que no [`SearchBar`](xref:Xamarin.Forms.SearchBar) tiene ningún fondo y que el campo de búsqueda es translúcido.

Además, el `SearchBar.GetSearchBarStyle` método se puede utilizar para devolver el `UISearchBarStyle` que se aplica a. `SearchBar`

El resultado es que un miembro `UISearchBarStyle` especificado se aplica a un [`SearchBar`](xref:Xamarin.Forms.SearchBar), que controla si `SearchBar` tiene un fondo:

![Captura de pantalla de los estilos de barra, en iOS](searchbar-style-images/searchbar-styles.png "Estilos de barra en iOS")

Las capturas de pantallas `UISearchBarStyle` siguientes muestran los [`SearchBar`](xref:Xamarin.Forms.SearchBar) miembros aplicados a `BackgroundColor` los objetos que tienen su propiedad establecida:

![Captura de pantalla de estilos barra con color de fondo, en iOS](searchbar-style-images/searchbar-background-styles.png "Estilos de barra con color de fondo en iOS")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
