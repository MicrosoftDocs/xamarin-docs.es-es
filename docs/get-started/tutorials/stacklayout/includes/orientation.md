---
ms.openlocfilehash: 5a464196c08220158432219d96bf82578789d8e5
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "67560023"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración de [`StackLayout`](xref:Xamarin.Forms.StackLayout) de modo que alinee sus elementos secundarios en horizontal, en lugar de en vertical:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código establece la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) en [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de vistas secundarias en horizontal en un elemento StackLayout, en iOS y Android](../images/orientation.png "StackLayout que contiene instancias de etiqueta orientadas en horizontal")](../images/orientation-large.png#lightbox "StackLayout que contiene instancias de etiqueta orientadas en horizontal")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración de [`StackLayout`](xref:Xamarin.Forms.StackLayout) de modo que alinee sus elementos secundarios en horizontal, en lugar de en vertical:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código establece la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) en [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de vistas secundarias en horizontal en un elemento StackLayout, en iOS y Android](../images/orientation.png "StackLayout que contiene instancias de etiqueta orientadas en horizontal")](../images/orientation-large.png#lightbox "StackLayout que contiene instancias de etiqueta orientadas en horizontal")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.

-----

> [!div class="nextstepaction"]
> [Encontré un problema](https://github.com/MicrosoftDocs/xamarin-docs/issues/new?title=StackLayout+Tutorial+Step+2+Feedback&template=tutorial_template.md)
