---
title: Claves de acceso de VisualElement en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que especifica una clave de acceso para un VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ce1d682678c17bb24e7a4fd528c84554465c4fa6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656835"
---
# <a name="visualelement-access-keys-on-windows"></a>Claves de acceso de VisualElement en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Las claves de acceso son métodos abreviados de teclado que mejoran la facilidad de uso y la accesibilidad de las aplicaciones en el Plataforma universal de Windows (UWP) al proporcionar una manera intuitiva de que los usuarios naveguen rápidamente e interactúen con la interfaz de usuario visible de la aplicación a través de un teclado en lugar de hacerlo a través de un toque. o un mouse. Son combinaciones de la tecla Alt y uno o más teclas alfanuméricas, normalmente presionadas de manera secuencial. Métodos abreviados de teclado automáticamente son compatibles con las claves de acceso que utilizan un carácter alfanumérico individual.

Sugerencias de teclas de acceso son flotantes distintivos que se muestra al lado de los controles que incluyen las claves de acceso. Cada sugerencia de clave de acceso contiene las teclas alfanuméricas que activación el control asociado. Cuando un usuario presiona la tecla Alt, se muestran las sugerencias de teclas de acceso.

Este UWP específico de la plataforma se usa para especificar una clave de acceso [`VisualElement`](xref:Xamarin.Forms.VisualElement)para. Se consume en XAML estableciendo el [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propiedad adjunta a un valor alfanumérico y, opcionalmente, establezca el [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propiedad adjunta a un valor de la [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeración, el [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) propiedad adjunta un `double`y el [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propiedad adjunta a un `double`:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

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

El `VisualElement.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer el valor de clave de acceso para el `VisualElement`. El [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) método, opcionalmente, especifica la posición que se usará para mostrar la sugerencia de clave de acceso, con el [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeración que proporciona los siguientes valores posibles:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) : indica que la selección de ubicación de acceso keytips vendrá determinado por el sistema operativo.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) : indica que la sugerencia de clave de acceso aparecerá por encima del borde superior de la `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) : indica que la sugerencia de clave de acceso aparecerá debajo de la parte inferior de la `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) : indica que aparecerá la sugerencia de clave de acceso a la derecha del borde derecho de la `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) : indica que aparecerá la sugerencia de clave de acceso a la izquierda del borde izquierdo de la `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) : indica que la sugerencia de clave de acceso aparecen superpuesta en el centro de la `VisualElement`.

> [!NOTE]
> Normalmente, el [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) colocación keytips es suficiente, lo que incluye compatibilidad con interfaces de usuario adaptable.

El [ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) y [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) pueden usarse métodos de control más detallado de la ubicación de acceso keytips. El argumento para el `SetAccessKeyHorizontalOffset` método indica el momento para mover la punta de clave de acceso hacia la izquierda o derecha y el argumento para el `SetAccessKeyVerticalOffset` método indica la distancia que hay que mover la sugerencia de clave de acceso hacia arriba o hacia abajo.

>[!NOTE]
> Desplazamientos de sugerencia de clave de acceso no pueden establecerse cuando se establece la posición de clave de acceso `Auto`.

Además, el [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), y [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) pueden usarse métodos para recuperar el acceso de un valor y su ubicación de la clave.

El resultado es que se pueden mostrar sugerencias de teclas de acceso junto a cualquiera [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instancias que definen el acceso de las claves, presionando la tecla Alt:

![Específico de la plataforma de las claves de acceso de VisualElement](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement acceso claves específicas de la plataforma")

Cuando un usuario activa una tecla de acceso presionando la tecla Alt, seguida por el acceso de clave, la acción predeterminada para el `VisualElement` se ejecutará. Por ejemplo, cuando un usuario activa la clave de acceso en un [ `Switch` ](xref:Xamarin.Forms.Switch), el `Switch` se alterna. Cuando un usuario activa la clave de acceso en un [ `Entry` ](xref:Xamarin.Forms.Entry), el `Entry` recibe el foco. Cuando un usuario activa la clave de acceso en un [ `Button` ](xref:Xamarin.Forms.Button), el controlador de eventos para el [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) se ejecuta el evento.

Para obtener más información acerca de las claves de acceso, consulte [claves de acceso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
