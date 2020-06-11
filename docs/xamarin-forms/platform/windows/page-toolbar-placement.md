---
title: "ubicación de la barra de herramientas de página en Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores ni efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que cambia la posición de una barra de herramientas en una página.
MS. Prod: Xamarin ms. AssetID: 99F29E95-0C36-4A3B-BDE8-7E9F119E844E ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="page-toolbar-placement-on-windows"></a>Colocación de la barra de herramientas de página en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma se usa para cambiar la posición de una barra de herramientas en un [`Page`](xref:Xamarin.Forms.Page) y se consume en XAML estableciendo la [`Page.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) propiedad adjunta en un valor de la [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeración:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

El `Page.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en Windows. [ `Page.SetToolbarPlacement` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Page. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Page}, Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ToolbarPlacement)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer la ubicación de la barra de herramientas, con la [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeración que proporciona tres valores: [`Default`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default) , [`Top`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top) y [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom) .

El resultado es que la ubicación especificada de la barra de herramientas se aplica a la [`Page`](xref:Xamarin.Forms.Page) instancia:

[![](page-toolbar-placement-images/toolbar-placement.png "Toolbar Placement Platform-Specific")](page-toolbar-placement-images/toolbar-placement-large.png#lightbox "Toolbar Placement Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
