---
title: "TabbedPage: animaciones de transición de página en Android" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que deshabilita las animaciones de transición al navegar por las páginas de un TabbedPage ".
MS. Prod: Xamarin ms. AssetID: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="tabbedpage-page-transition-animations-on-android"></a>Animaciones de transición de páginas de TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para deshabilitar animaciones de transición al navegar por las páginas, ya sea mediante programación o cuando se usa la barra de pestañas, en [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Se consume en XAML estableciendo la `TabbedPage.IsSmoothScrollEnabled` propiedad Bindable en `false` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

El `TabbedPage.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. El `TabbedPage.SetIsSmoothScrollEnabled` método, en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si se mostrarán animaciones de transición al navegar entre las páginas de un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Además, la `TabbedPage` clase del espacio de `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` nombres también tiene los siguientes métodos:

- `IsSmoothScrollEnabled`, que se usa para recuperar si se mostrarán animaciones de transición al navegar entre las páginas de un `TabbedPage` .
- `EnableSmoothScroll`, que se usa para habilitar animaciones de transición al navegar entre las páginas de un `TabbedPage` .
- `DisableSmoothScroll`, que se usa para deshabilitar las animaciones de transición al navegar entre las páginas de un `TabbedPage` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
