---
ms.openlocfilehash: 703d99446d3a43411b681677bf9d3c9ab01ea83f
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689647"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Observe que el aspecto del objeto [`Button`](xref:Xamarin.Forms.Button) ha cambiado:

    [![Captura de pantalla de un botón con una apariencia visual modificada, en iOS y Android](../images/change-button-appearance.png "Botón con apariencia modificada")](../images/change-button-appearance-large.png#lightbox "Botón con apariencia modificada")

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre la configuración del aspecto de [`Button`](xref:Xamarin.Forms.Button), consulte [Apariencia de los botones](~/xamarin-forms/user-interface/button.md#button-appearance) en la [Guía sobre los botones de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio para Mac, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Observe que el aspecto del objeto [`Button`](xref:Xamarin.Forms.Button) ha cambiado:

    [![Captura de pantalla de un botón con una apariencia visual modificada, en iOS y Android](../images/change-button-appearance.png "Botón con apariencia modificada")](../images/change-button-appearance-large.png#lightbox "Botón con apariencia modificada")

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre la configuración del aspecto de [`Button`](xref:Xamarin.Forms.Button), consulte [Apariencia de los botones](~/xamarin-forms/user-interface/button.md#button-appearance) en la [Guía sobre los botones de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
