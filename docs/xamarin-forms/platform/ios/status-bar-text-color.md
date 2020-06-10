---
Título: "modo de color de texto de la barra NavigationPage en iOS" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si el color del texto de la barra de estado de un NavigationPage coincide con la luminosidad de la barra de navegación.
MS. Prod: Xamarin ms. AssetID: 03698A44-39F1-4030-9AF5-F10A6713828A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-text-color-mode-on-ios"></a>Modo de color de texto de la barra NavigationPage en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma determina si el color del texto de la barra de estado en un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Se consume en XAML estableciendo la [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) propiedad adjunta en un valor de la [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

El `NavigationPage.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `NavigationPage.SetStatusBarTextColorMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, Xamarin.Forms . PlatformConfiguration. iOSSpecific. StatusBarTextColorMode)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres controla si el color del texto de la barra de estado en [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación, y la [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración proporciona dos valores posibles:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust): indica que no se debe ajustar el color del texto de la barra de estado.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity): indica que el color del texto de la barra de Estado debe coincidir con la luminosidad de la barra de navegación.

Además, [ `GetStatusBarTextColorMode` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) que se puede usar para recuperar el valor actual de la [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración que se aplica a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

El resultado es que el color del texto de la barra de estado de un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) se puede ajustar para que coincida con la luminosidad de la barra de navegación. En este ejemplo, el color del texto de la barra de estado cambia a medida que el usuario cambia entre las [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) páginas y de un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

![](status-bar-text-color-images/status-bar-text-color-mode.png "Status Bar Text Color Mode Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
