---
Título: "dirección de extracción RefreshView en Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma de Windows que permite cambiar la dirección de extracción de un RefreshView.
MS. Prod: Xamarin ms. AssetID: 407A862B-281E-4384-9696-C0655830B84D ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/20/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="refreshview-pull-direction-on-windows"></a>Dirección de extracción de RefreshView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma permite cambiar la dirección de extracción de un para que `RefreshView` coincida con la orientación del control desplazable que muestra los datos. Se consume en XAML estableciendo la `RefreshView.RefreshPullDirection` propiedad Bindable en un valor de la `RefreshPullDirection` enumeración:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

El `RefreshView.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. El `RefreshView.SetRefreshPullDirection` método, en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer la dirección de extracción de `RefreshView` , con la `RefreshPullDirection` enumeración que proporciona cuatro valores posibles:

- `LeftToRight`indica que una extracción de izquierda a derecha inicia una actualización.
- `TopToBottom`indica que una extracción de la parte superior a la inferior inicia una actualización y es la dirección de extracción predeterminada de un `RefreshView` .
- `RightToLeft`indica que una extracción de derecha a izquierda inicia una actualización.
- `BottomToTop`indica que una extracción de la parte inferior a la superior inicia una actualización.

Además, el `GetRefreshPullDirection` método se puede utilizar para devolver el actual `RefreshPullDirection` del `RefreshView` .

El resultado es que `RefreshPullDirection` se aplica un especificado a `RefreshView` , para establecer la dirección de extracción de forma que coincida con la orientación del control desplazable que muestra los datos. En la captura de pantalla siguiente se muestra un `RefreshView` con una `LeftToRight` dirección de extracción:

[![Captura de pantalla de un RefreshView con una dirección de extracción de izquierda a derecha, en UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView con dirección de extracción de izquierda a derecha")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView con dirección de extracción de izquierda a derecha")

> [!NOTE]
> Al cambiar la dirección de extracción, la posición inicial del círculo de progreso gira automáticamente para que la flecha comience en la posición adecuada para la dirección de extracción.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
