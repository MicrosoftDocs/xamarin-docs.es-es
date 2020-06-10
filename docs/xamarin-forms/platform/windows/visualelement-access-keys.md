---
title: "VisualElement Access Keys on Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que especifica una clave de acceso para un VisualElement.
MS. Prod: Xamarin ms. AssetID: 771AF785-76B8-4372-89F5-E4D521D21E0C ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-access-keys-on-windows"></a>Claves de acceso de VisualElement en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Las claves de acceso son métodos abreviados de teclado que mejoran la facilidad de uso y la accesibilidad de las aplicaciones en el Plataforma universal de Windows (UWP) al proporcionar una manera intuitiva de que los usuarios naveguen rápidamente e interactúen con la interfaz de usuario visible de la aplicación a través de un teclado en lugar de hacerlo a través de un toque o un mouse. Son combinaciones de la tecla Alt y una o varias claves alfanuméricas, normalmente presionadas secuencialmente. Los métodos abreviados de teclado se admiten automáticamente para las teclas de acceso que utilizan un solo carácter alfanumérico.

Las sugerencias de teclas de acceso son notificaciones flotantes que se muestran junto a los controles que incluyen claves de acceso. Cada sugerencia de clave de acceso contiene las claves alfanuméricas que activan el control asociado. Cuando el usuario presiona la tecla Alt, se muestran las sugerencias de teclas de acceso.

Este UWP específico de la plataforma se usa para especificar una clave de acceso para [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Se utiliza en XAML estableciendo la [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propiedad adjunta en un valor alfanumérico y estableciendo opcionalmente la [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propiedad adjunta en un valor de la [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumeración, la [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) propiedad adjunta en un `double` y la [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propiedad adjunta en un `double` :

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

El `VisualElement.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. [ `VisualElement.SetAccessKey` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, System. String)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer el valor de la clave de acceso para `VisualElement` . [ `VisualElement.SetAccessKeyPlacement` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, Xamarin.Forms . AccessKeyPlacement)), especifica opcionalmente la posición que se va a usar para mostrar la sugerencia de clave de acceso, con la [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumeración que proporciona los siguientes valores posibles:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto): indica que el sistema operativo determinará la ubicación de la información sobre las teclas de acceso.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top): indica que la sugerencia de tecla de acceso aparecerá encima del borde superior de `VisualElement` .
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom): indica que la sugerencia de tecla de acceso aparecerá debajo del borde inferior de `VisualElement` .
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right): indica que la sugerencia de tecla de acceso aparecerá a la derecha del borde derecho del `VisualElement` .
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left): indica que la sugerencia de tecla de acceso aparecerá a la izquierda del borde izquierdo de `VisualElement` .
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center): indica que la sugerencia de tecla de acceso aparecerá superpuesta en el centro del `VisualElement` .

> [!NOTE]
> Normalmente, la [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) Ubicación de la sugerencia de teclas es suficiente, lo que incluye la compatibilidad con las interfaces de usuario adaptables.

[ `VisualElement.SetAccessKeyHorizontalOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, System. Double)) y [ `VisualElement.SetAccessKeyVerticalOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Los métodos VisualElement}, System. Double)) se pueden usar para un control más granular de la ubicación de la información sobre las teclas de acceso. El argumento para el `SetAccessKeyHorizontalOffset` método indica la distancia a la que se debe desplace la punta de la tecla de acceso a la izquierda o a la derecha, y el argumento al `SetAccessKeyVerticalOffset` método indica hasta dónde se mueve la punta de la tecla de acceso hacia arriba o hacia abajo.

>[!NOTE]
> No se pueden establecer los desplazamientos de la sugerencia de clave de acceso cuando se establece la posición de la clave de acceso `Auto` .

Además, [ `GetAccessKey` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyPlacement` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyHorizontalOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})) y [ `GetAccessKeyVerticalOffset` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})). los métodos se pueden usar para recuperar un valor de clave de acceso y su ubicación.

El resultado es que las sugerencias de teclas de acceso se pueden mostrar junto a cualquier [`VisualElement`](xref:Xamarin.Forms.VisualElement) instancia que defina las teclas de acceso presionando la tecla Alt:

![Claves de acceso VisualElement específicas de la plataforma](visualelement-access-keys-images/visualelement-accesskeys.png "Claves de acceso VisualElement específicas de la plataforma")

Cuando un usuario activa una tecla de acceso, presione la tecla Alt seguida de la tecla de acceso para que se ejecute la acción predeterminada de `VisualElement` . Por ejemplo, cuando un usuario activa la tecla de acceso en un [`Switch`](xref:Xamarin.Forms.Switch) , `Switch` se alterna. Cuando un usuario activa la tecla de acceso en un [`Entry`](xref:Xamarin.Forms.Entry) , `Entry` obtiene el foco. Cuando un usuario activa la tecla de acceso en una [`Button`](xref:Xamarin.Forms.Button) , se ejecuta el controlador de eventos para el [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento.

Para obtener más información acerca de las claves de acceso, consulte [claves de acceso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
