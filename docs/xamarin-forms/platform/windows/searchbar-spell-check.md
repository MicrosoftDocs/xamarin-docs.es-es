---
title: "barra Spell Check en Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores ni efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que permite a un barra interactuar con el motor de revisión ortográfica ".
MS. Prod: Xamarin ms. AssetID: 7974C91F-7502-4DB3-B0E9-C45E943DDA26 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="searchbar-spell-check-on-windows"></a>Spell Check barra en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma permite [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un interactuar con el motor de revisión ortográfica. Se consume en XAML estableciendo la [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

El `SearchBar.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. [ `SearchBar.SetIsSpellCheckEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. barra. SetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Barra}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, activa y desactiva el corrector ortográfico. Además, `SearchBar.SetIsSpellCheckEnabled` se puede usar el método para alternar el corrector ortográfico llamando a [ `SearchBar.GetIsSpellCheckEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. barra. GetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Barra})) para devolver si está habilitado el corrector ortográfico:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

El resultado es que el texto que se escribe en la [`SearchBar`](xref:Xamarin.Forms.SearchBar) se puede comprobar ortográficamente, con la indicación de que se ha indicado una ortografía incorrecta para el usuario:

![Barra spell check: específico de la plataforma](searchbar-spell-check-images/searchbar-spellcheck.png "Barra spell check: específico de la plataforma")

> [!NOTE]
> La `SearchBar` clase del [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres también [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) tiene [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos y que se pueden utilizar para habilitar y deshabilitar el corrector ortográfico en `SearchBar` , respectivamente.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
