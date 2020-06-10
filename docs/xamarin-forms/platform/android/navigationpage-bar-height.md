---
title: "NavigationPage de alto de la barra en Android" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que establece el alto de la barra de navegación en un NavigationPage.
MS. Prod: Xamarin ms. AssetID: C8A73B64-FE70-408A-A72E-8AF147F0C52C ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-height-on-android"></a>Alto de la barra de NavigationPage en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma Android establece el alto de la barra de navegación en un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Se consume en XAML estableciendo la [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propiedad Bindable en un valor entero:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

El `NavigationPage.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android de compatibilidad de aplicaciones. [ `NavigationPage.SetBarHeight` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage}, System. Int32)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres, se usa para establecer el alto de la barra de navegación en [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Además, [ `NavigationPage.GetBarHeight` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage})) que se puede usar para devolver el alto de la barra de navegación en `NavigationPage` .

El resultado es que se puede establecer el alto de la barra de navegación en un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

![](navigationpage-bar-height-images/navigationpage-barheight.png "NavigationPage navigation bar height")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
