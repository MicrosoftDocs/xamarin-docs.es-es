---
title: Títulos de páginas grandes en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma de iOS específica que muestra el título de la página como un título grande en la barra de navegación de un NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ab9becf2f7363674346abf004c1748cb06eb0d31
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655421"
---
# <a name="large-page-titles-on-ios"></a>Títulos de páginas grandes en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para mostrar el título de la página como un título grande en la barra de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)navegación de un, para los dispositivos que usan iOS 11 o superior. Un gran título está alineado a la izquierda y usa una fuente mayor y realiza la transición a un título estándar como el usuario comienza a desplazarse por el contenido, por lo que se usan de forma eficaz el espacio en pantalla. Sin embargo, en orientación horizontal, el título devolverá al centro de la barra de navegación para optimizar el diseño del contenido. Se consume en XAML estableciendo el `NavigationPage.PrefersLargeTitles` propiedad adjunta un `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa puede usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

El `NavigationPage.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `NavigationPage.SetPrefersLargeTitle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) controla el espacio de nombres, si están habilitados los títulos de gran tamaño.

Siempre que los títulos de gran tamaño están habilitados en el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), todas las páginas de la pila de navegación mostrará los títulos de gran tamaño. Este comportamiento puede invalidarse en páginas estableciendo el `Page.LargeTitleDisplay` propiedad adjunta a un valor de la `LargeTitleDisplayMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, se puede invalidar el comportamiento de la página de C# con la API fluida:

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

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetLargeTitleDisplay` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla el comportamiento de título grande en la [ `Page` ](xref:Xamarin.Forms.Page), con el `LargeTitleDisplayMode` enumeración que proporciona tres posibles valores:

- `Always` : fuerza la barra de navegación y la fuente tamaño usando el formato grande.
- `Automatic` : use el mismo estilo (grande o pequeño) que el elemento anterior en la pila de navegación.
- `Never` – forzar el uso de la barra de navegación de formato estándar, pequeñas.

Además, el `SetLargeTitleDisplay` método puede utilizarse para activar o desactivar los valores de enumeración mediante una llamada a la `LargeTitleDisplay` método, que devuelve el valor actual `LargeTitleDisplayMode`:

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

El resultado es que un determinado `LargeTitleDisplayMode` se aplica a la [ `Page` ](xref:Xamarin.Forms.Page), que controla el comportamiento de título grande:

![](page-large-title-images/large-title.png "Específico de la plataforma efecto de desenfoque")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
