---
title: "Slide Thumb TAP on iOS" Description: "specific de la plataforma permite consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores ni efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite establecer la propiedad Slider. Value al puntear en la barra deslizante ".
MS. Prod: Xamarin ms. AssetID: D0915D37-9A59-4728-BB6A-FE094A661275 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="slider-thumb-tap-on-ios"></a>Puntear en el control deslizante de iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS permite [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) establecer la propiedad pulsando en una posición en la [`Slider`](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar el `Slider` control. Se consume en XAML estableciendo la [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propiedad Bindable en `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

El `Slider.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Slider.SetUpdateOnTap` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. SetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si una pulsación en la `Slider` barra establecerá la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propiedad. Además, [ `Slider.GetUpdateOnTap` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. GetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider})) que se puede usar para devolver si una pulsación en la `Slider` barra establecerá la `Slider.Value` propiedad.

El resultado es que una pulsación en la [`Slider`](xref:Xamarin.Forms.Slider) barra puede trasladar el `Slider` control de posición y establecer la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propiedad:

![](slider-thumb-images/slider-updateontap.png "Slider Update on Tap enabled")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
