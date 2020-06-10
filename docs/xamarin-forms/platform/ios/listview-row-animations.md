---
title: "animaciones de filas de ListView en iOS" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma iOS que controla si las animaciones de filas se deshabilitan cuando se actualiza la colección de elementos de ListView. "
MS. Prod: Xamarin ms. AssetID: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/21/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-row-animations-on-ios"></a>Animaciones de filas de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este control específico de la plataforma iOS indica si se deshabilitan las animaciones de filas cuando [`ListView`](xref:Xamarin.Forms.ListView) se actualiza la colección de elementos. Se consume en XAML estableciendo la `ListView.RowAnimationsEnabled` propiedad Bindable en `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

El `ListView.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `ListView.SetRowAnimationsEnabled` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si se deshabilitan las animaciones de filas cuando [`ListView`](xref:Xamarin.Forms.ListView) se actualiza la colección de elementos. Además, se `ListView.GetRowAnimationsEnabled` puede utilizar el método para devolver si las animaciones de fila están deshabilitadas en `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)las animaciones de fila están habilitadas de forma predeterminada. Por lo tanto, una animación se produce cuando se inserta una nueva fila en `ListView` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
