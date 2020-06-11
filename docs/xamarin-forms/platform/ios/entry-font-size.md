---
Título: "tamaño de fuente de entrada en iOS" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo usar la plataforma específica de iOS que escala el tamaño de fuente de una entrada ".
MS. Prod: Xamarin ms. AssetID: E8881D4E-902B-4397-A43E-916B2885EC87 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="entry-font-size-on-ios"></a>Tamaño de fuente de entrada en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma específica de iOS se usa para escalar el tamaño de fuente de un [`Entry`](xref:Xamarin.Forms.Entry) para asegurarse de que el texto introducido quepa en el control. Se consume en XAML estableciendo la [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

El `Entry.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. [ `Entry.EnableAdjustsFontSizeToFitWidth` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. EnableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})), en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para escalar el tamaño de fuente del texto de entrada para asegurarse de que quepa en el [`Entry`](xref:Xamarin.Forms.Entry) . Además, la [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) clase del espacio de `Xamarin.Forms.PlatformConfiguration.iOSSpecific` nombres también tiene [ `DisableAdjustsFontSizeToFitWidth` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. DisableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})) que deshabilita este específico de la plataforma y un [ `SetAdjustsFontSizeToFitWidth` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}, System. Boolean)) que se puede usar para alternar el escalado de tamaño de fuente llamando a [ `AdjustsFontSizeToFitWidth` ] (XREF: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. AdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})):

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

El resultado es que se escala el tamaño de fuente del [`Entry`](xref:Xamarin.Forms.Entry) control para asegurarse de que el texto introducido quepa en el control:

![](entry-font-size-images/entry-font-size.png "Adjust Entry Font Size Platform-Specific")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
