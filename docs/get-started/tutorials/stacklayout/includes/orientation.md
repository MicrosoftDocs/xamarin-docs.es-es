---
ms.openlocfilehash: f205e97c11c6ae1c75b101ada57c10ef49a9c146
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634784"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de vistas secundarias en horizontal en un elemento StackLayout, en iOS y Android](../images/orientation.png "StackLayout que contiene instancias de etiqueta orientadas en horizontal")](../images/orientation-large.png#lightbox "StackLayout que contiene instancias de etiqueta orientadas en horizontal")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio para Mac, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de vistas secundarias en horizontal en un elemento StackLayout, en iOS y Android](../images/orientation.png "StackLayout que contiene instancias de etiqueta orientadas en horizontal")](../images/orientation-large.png#lightbox "StackLayout que contiene instancias de etiqueta orientadas en horizontal")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.
