---
title: "escalado de accesibilidad para tamaños de fuente con nombre en iOS" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma iOS que deshabilita el escalado de accesibilidad para los tamaños de fuente con nombre. "
MS. Prod: Xamarin ms. AssetID: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/28/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Escalado de accesibilidad para tamaños de fuente con nombre en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS deshabilita el escalado de accesibilidad para tamaños de fuente con nombre. Se consume en XAML estableciendo la `Application.EnableAccessibilityScalingForNamedFontSizes` propiedad Bindable en `false` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

El `Application.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Application.SetEnableAccessibilityScalingForNamedFontSizes` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para deshabilitar los tamaños de fuente con nombre que se escalan mediante la configuración de accesibilidad de iOS. Además, se `Application.GetEnableAccessibilityScalingForNamedFontSizes` puede usar el método para devolver si los tamaños de fuente con nombre se escalan mediante la configuración de accesibilidad de iOS.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
