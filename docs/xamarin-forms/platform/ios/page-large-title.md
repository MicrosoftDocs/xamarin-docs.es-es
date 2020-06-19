---
title: Títulos de páginas grandes en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma de iOS específica que muestra el título de la página como un título grande en la barra de navegación de un NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0db20620870340386ccd0cedf7f98cb2975527ba
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128056"
---
# <a name="large-page-titles-on-ios"></a>Títulos de páginas grandes en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para mostrar el título de la página como un título grande en la barra de navegación de un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , para los dispositivos que usan iOS 11 o superior. Un título grande se alinea a la izquierda y usa una fuente mayor, y pasa a un título estándar a medida que el usuario comienza a desplazar el contenido, de modo que el estado real de la pantalla se utiliza de forma eficaz. Sin embargo, en la orientación horizontal, el título volverá al centro de la barra de navegación para optimizar el diseño del contenido. Se consume en XAML estableciendo la `NavigationPage.PrefersLargeTitles` propiedad adjunta en un `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

El `NavigationPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `NavigationPage.SetPrefersLargeTitle` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla si los títulos grandes están habilitados.

Siempre que los títulos grandes estén habilitados en [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , todas las páginas de la pila de navegación mostrarán títulos grandes. Este comportamiento se puede invalidar en las páginas si se establece la `Page.LargeTitleDisplay` propiedad adjunta en un valor de la `LargeTitleDisplayMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, el comportamiento de la página se puede invalidar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

El `Page.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Page.SetLargeTitleDisplay` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla el comportamiento del título grande en [`Page`](xref:Xamarin.Forms.Page) , con la `LargeTitleDisplayMode` enumeración que proporciona tres valores posibles:

- `Always`: fuerce la barra de navegación y el tamaño de fuente para usar el formato grande.
- `Automatic`: Use el mismo estilo (grande o pequeño) que el elemento anterior en la pila de navegación.
- `Never`: fuerza el uso de la barra de navegación normal de formato pequeño.

Además, el `SetLargeTitleDisplay` método se puede utilizar para alternar los valores de enumeración llamando al `LargeTitleDisplay` método, que devuelve la actual `LargeTitleDisplayMode` :

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

El resultado es que `LargeTitleDisplayMode` se aplica un especificado a [`Page`](xref:Xamarin.Forms.Page) , que controla el comportamiento del título grande:

![](page-large-title-images/large-title.png "Blur Effect Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
