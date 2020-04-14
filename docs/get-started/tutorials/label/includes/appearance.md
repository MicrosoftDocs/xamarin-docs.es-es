---
ms.openlocfilehash: 4fc6c50b5aa2ce502b4157ca2b15f0d33a68ecd1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "60896733"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Label`](xref:Xamarin.Forms.Label) para cambiar su apariencia visual:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Este código establece las propiedades que cambian la apariencia visual del objeto [`Label`](xref:Xamarin.Forms.Label). La propiedad [`TextColor`](xref:Xamarin.Forms.Label.TextColor) establece el color del texto del objeto `Button`. La propiedad [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) establece la fuente de la etiqueta en cursiva y la propiedad [`FontSize`](xref:Xamarin.Forms.Label.FontSize) establece el tamaño de fuente. Además, se aplica una decoración de texto subrayado en el objeto `Label` configurando la propiedad [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations), y se centra horizontalmente configurando la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) en [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Observe que el aspecto del objeto [`Label`](xref:Xamarin.Forms.Label) ha cambiado:

    [![Captura de pantalla de una etiqueta con una apariencia visual modificada, en iOS y Android](../images/change-label-appearance.png "Etiqueta con apariencia cambiada")](../images/change-label-appearance-large.png#lightbox "Etiqueta con apariencia cambiada")

    Para obtener más información sobre la configuración del aspecto de [`Label`](xref:Xamarin.Forms.Label), consulte la guía [Etiqueta de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Label`](xref:Xamarin.Forms.Label) para cambiar su apariencia visual:

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    Este código establece las propiedades que cambian la apariencia visual del objeto [`Label`](xref:Xamarin.Forms.Label). La propiedad [`TextColor`](xref:Xamarin.Forms.Label.TextColor) establece el color del texto del objeto `Button`. La propiedad [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) establece la fuente de la etiqueta en cursiva y la propiedad [`FontSize`](xref:Xamarin.Forms.Label.FontSize) establece el tamaño de fuente. Además, se aplica una decoración de texto subrayado en el objeto `Label` configurando la propiedad [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations), y se centra horizontalmente configurando la propiedad [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) en [`Center`](xref:Xamarin.Forms.LayoutOptions.Center).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Observe que el aspecto del objeto [`Label`](xref:Xamarin.Forms.Label) ha cambiado:

    [![Captura de pantalla de una etiqueta con una apariencia visual modificada, en iOS y Android](../images/change-label-appearance.png "Etiqueta con apariencia cambiada")](../images/change-label-appearance-large.png#lightbox "Etiqueta con apariencia cambiada")

    Para obtener más información sobre la configuración del aspecto de [`Label`](xref:Xamarin.Forms.Label), consulte la guía [Etiqueta de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
