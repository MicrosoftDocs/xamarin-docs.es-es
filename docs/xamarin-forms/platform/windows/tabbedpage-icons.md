---
title: Iconos de TabbedPage en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que permite mostrar iconos de página en una barra de herramientas de TabbedPage.
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d755f2242b1af94ce5729b1004eb867217876ac6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656854"
---
# <a name="tabbedpage-icons-on-windows"></a>Iconos de TabbedPage en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma universal de Windows específica de la plataforma permite mostrar iconos de página en una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de herramientas y proporciona la capacidad de especificar el tamaño del icono opcionalmente. Se consume en XAML estableciendo el [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) propiedad adjunta `true`y, opcionalmente, estableciendo el [ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) propiedad adjunta un [ `Size` ](xref:Xamarin.Forms.Size) valor:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

El `TabbedPage.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para activar o desactivar los iconos de encabezado. El [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) método, opcionalmente, especifica el tamaño del icono de encabezado con un [ `Size` ](xref:Xamarin.Forms.Size) valor.

Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` espacio de nombres también tiene un [ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) método que permite a los iconos de encabezado, un [ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) método que deshabilita los iconos de encabezado, y un [ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) método que devuelve un `boolean` valor que indica si se habilitan los iconos de encabezado.

El resultado es esa página se pueden mostrar los iconos en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas, con el tamaño de icono que se va a establecer opcionalmente al tamaño que desee:

![Específico de plataforma iconos habilitados TabbedPage](tabbedpage-icons-images/tabbedpage-icons.png "TabbedPage iconos habilitados específico de plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
