---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372979"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Button`](xref:Xamarin.Forms.Button) para que establezca un controlador para el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Este código establece el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente.

1. En el **Explorador de soluciones**, en el proyecto **ButtonTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnButtonClicked` a la clase:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método `OnButtonClicked`. El argumento `sender` es el objeto `Button` responsable de la activación del evento `Clicked` y puede usarse para acceder al objeto `Button`. Este controlador de eventos actualiza el texto mostrado por `Button`.

    > [!NOTE]
    > Además del evento `Clicked`, `Button` también define los eventos [`Pressed`](xref:Xamarin.Forms.Button.Pressed) y [`Released`](xref:Xamarin.Forms.Button.Released). Para obtener más información, consulte la sección [Presionar y soltar el botón](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) en la guía del [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Haga clic en [`Button`](xref:Xamarin.Forms.Button) y observe que el texto muestra cambios:

    [![Captura de pantalla del cambio de texto del botón tras recibir un clic, en iOS y Android](../images/handle-button-click.png "Control de los clics de botón")](../images/handle-button-click-large.png#lightbox "Control de los clics de botón")

    Para obtener más información sobre cómo controlar los clics de botón, consulte la sección sobre el [control de clics de botón](~/xamarin-forms/user-interface/button.md#handling-button-clicks) en la guía del [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Button`](xref:Xamarin.Forms.Button) para que establezca un controlador para el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked):

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Este código establece el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente.

1. En el **Panel de solución**, en el proyecto **ButtonTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnButtonClicked` a la clase:

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método `OnButtonClicked`. El argumento `sender` es el objeto `Button` responsable de la activación del evento `Clicked` y puede usarse para acceder al objeto `Button`. Este controlador de eventos actualiza el texto mostrado por `Button`.

    > [!NOTE]
    > Además del evento `Clicked`, `Button` también define los eventos [`Pressed`](xref:Xamarin.Forms.Button.Pressed) y [`Released`](xref:Xamarin.Forms.Button.Released). Para obtener más información, consulte la sección [Presionar y soltar el botón](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) en la guía del [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Haga clic en [`Button`](xref:Xamarin.Forms.Button) y observe que el texto muestra cambios:

    [![Captura de pantalla del cambio de texto del botón tras recibir un clic, en iOS y Android](../images/handle-button-click.png "Control de los clics de botón")](../images/handle-button-click-large.png#lightbox "Control de los clics de botón")

    Para obtener más información sobre cómo controlar los clics de botón, consulte la sección sobre el [control de clics de botón](~/xamarin-forms/user-interface/button.md#handling-button-clicks) en la guía del [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
