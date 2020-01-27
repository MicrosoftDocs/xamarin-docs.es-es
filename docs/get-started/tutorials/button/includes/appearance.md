---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "61372970"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Button`](xref:Xamarin.Forms.Button) para cambiar su apariencia visual:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Este código establece las propiedades que cambian la apariencia visual del objeto [`Button`](xref:Xamarin.Forms.Button). La propiedad [`TextColor`](xref:Xamarin.Forms.Button.TextColor) establece el color del texto del objeto `Button` y la propiedad [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) establece el color de fondo del texto. La propiedad [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) establece el color de un área alrededor del objeto `Button` y la propiedad [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) establece el ancho del borde. De forma predeterminada, el objeto `Button` es rectangular, pero puede presentarse esquinas redondeadas si se configura la propiedad [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) con un valor adecuado. Además, el tamaño del objeto `Button` se puede cambiar configurando las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Observe que el aspecto del objeto [`Button`](xref:Xamarin.Forms.Button) ha cambiado:

    [![Captura de pantalla de un botón con una apariencia visual modificada, en iOS y Android](../images/change-button-appearance.png "Botón con apariencia modificada")](../images/change-button-appearance-large.png#lightbox "Botón con apariencia modificada")

    Para obtener más información sobre la configuración del aspecto de [`Button`](xref:Xamarin.Forms.Button), consulte [Apariencia de los botones](~/xamarin-forms/user-interface/button.md#button-appearance) en la [Guía sobre los botones de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Button`](xref:Xamarin.Forms.Button) para cambiar su apariencia visual:

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Este código establece las propiedades que cambian la apariencia visual del objeto [`Button`](xref:Xamarin.Forms.Button). La propiedad [`TextColor`](xref:Xamarin.Forms.Button.TextColor) establece el color del texto del objeto `Button` y la propiedad [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) establece el color de fondo del texto. La propiedad [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) establece el color de un área alrededor del objeto `Button` y la propiedad [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) establece el ancho del borde. De forma predeterminada, el objeto `Button` es rectangular, pero puede presentarse esquinas redondeadas si se configura la propiedad [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) con un valor adecuado. Además, el tamaño del objeto `Button` se puede cambiar configurando las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Observe que el aspecto del objeto [`Button`](xref:Xamarin.Forms.Button) ha cambiado:

    [![Captura de pantalla de un botón con una apariencia visual modificada, en iOS y Android](../images/change-button-appearance.png "Botón con apariencia modificada")](../images/change-button-appearance-large.png#lightbox "Botón con apariencia modificada")

    Para obtener más información sobre la configuración del aspecto de [`Button`](xref:Xamarin.Forms.Button), consulte [Apariencia de los botones](~/xamarin-forms/user-interface/button.md#button-appearance) en la [Guía sobre los botones de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
