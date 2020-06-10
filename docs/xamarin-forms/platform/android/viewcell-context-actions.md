---
title: "ViewCell context Actions on Android" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que habilita el modo heredado de acciones de contexto de ViewCell.
MS. Prod: Xamarin ms. AssetID: 8BD71B10-5037-443F-9975-B941CE393E5A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/24/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="viewcell-context-actions-on-android"></a>Acciones de contexto ViewCell en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

De forma predeterminada Xamarin.Forms , en 4,3, cuando un [`ViewCell`](xref:Xamarin.Forms.ViewCell) en una aplicación de Android define acciones de contexto para cada elemento en [`ListView`](xref:Xamarin.Forms.ListView) , el menú acciones de contexto se actualiza cuando se cambia el elemento seleccionado en el `ListView` . Sin embargo, en las versiones anteriores del Xamarin.Forms menú acciones de contexto no se actualizaba y este comportamiento se denomina `ViewCell` modo heredado. Este modo heredado puede producir un comportamiento incorrecto si `ListView` usa un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para establecer su a `ItemTemplate` partir de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que definen diferentes acciones de contexto.

Esta opción específica de la plataforma Android habilita el [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo heredado del menú acciones de contexto, por compatibilidad con versiones anteriores, para que el menú acciones de contexto no se actualice cuando el elemento seleccionado en un [`ListView`](xref:Xamarin.Forms.ListView) cambie. Se consume en XAML estableciendo la `ViewCell.IsContextActionsLegacyModeEnabled` propiedad Bindable en `true` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

El `ViewCell.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. El `ViewCell.SetIsContextActionsLegacyModeEnabled` método, en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para habilitar el [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo heredado del menú acciones de contexto, de modo que el menú acciones de contexto no se actualice cuando el elemento seleccionado en un [`ListView`](xref:Xamarin.Forms.ListView) cambie. Además, se `ViewCell.GetIsContextActionsLegacyModeEnabled` puede usar el método para devolver si está habilitado el modo heredado de acciones de contexto.

Las siguientes capturas de pantallas muestran [`ViewCell`](xref:Xamarin.Forms.ViewCell) las acciones de contexto modo heredado habilitado:

![Captura de pantalla del modo heredado de ViewCell habilitado, en Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modo heredado de ViewCell habilitado")

En este modo, los elementos de menú de acción de contexto mostrados son idénticos para la celda 1 y la celda 2, a pesar de que se estén definiendo distintos elementos de menú contextual para la celda 2.

Las capturas de pantallas siguientes muestran las [`ViewCell`](xref:Xamarin.Forms.ViewCell) acciones de contexto modo heredado deshabilitado, que es el Xamarin.Forms comportamiento predeterminado:

![Captura de pantalla del modo heredado de ViewCell deshabilitado, en Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modo heredado de ViewCell deshabilitado")

En este modo, se muestran los elementos de menú de acción de contexto correctos para las celdas 1 y 2.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
