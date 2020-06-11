---
Título: "VisualElement modo de color heredado en Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que deshabilita el Xamarin.Forms modo de color heredado ".
MS. Prod: Xamarin ms. AssetID: B8759309-07C7-4DCA-A18A-C1A198A7951B ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-legacy-color-mode-on-windows"></a>Modo de color heredado de VisualElement en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algunas de las Xamarin.Forms vistas presentan un modo de color heredado. En este modo, cuando la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de la vista está establecida en `false` , la vista invalidará los colores establecidos por el usuario con los colores nativos predeterminados para el Estado deshabilitado. Por compatibilidad con versiones anteriores, este modo de color heredado sigue siendo el comportamiento predeterminado para las vistas admitidas.

Esta Plataforma universal de Windows específica de la plataforma deshabilita este modo de color heredado, de modo que los colores establecidos en una vista por parte del usuario permanecen incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo la [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta en `false` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en Windows. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si el modo de color heredado está deshabilitado. Además, [ `VisualElement.GetIsLegacyColorModeEnabled` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})) que se puede usar para devolver si el modo de color heredado está deshabilitado.

El resultado es que el modo de color heredado se puede deshabilitar, de modo que los colores establecidos en una vista por parte del usuario permanezcan incluso cuando la vista esté deshabilitada:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> Al establecer [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) en una vista, el modo de color heredado se omite por completo. Para obtener más información sobre los Estados visuales, consulte [el Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
