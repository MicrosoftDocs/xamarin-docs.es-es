---
ms.openlocfilehash: d9834c2d2974787de989b640449fddae6c1ce39b
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919336"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Entry`](xref:Xamarin.Forms.Entry) para que establezca un controlador para los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Este código establece el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) en un controlador de eventos denominado `OnEntryTextChanged`, y el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed), en un controlador de eventos llamado `OnEntryCompleted`. Ambos controladores de eventos se crearán en el paso siguiente.

1. En el **Explorador de soluciones**, en el proyecto **EntryTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnEntryTextChanged` y `OnEntryCompleted` a la clase:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Cuando cambia el texto en [`Entry`](xref:Xamarin.Forms.Entry), se ejecuta el método `OnEntryTextChanged`. El argumento `sender` es el objeto `Entry` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Entry`. El argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) proporciona los valores de texto anteriores y nuevos (antes y después del cambio de texto).

    Al finalizar el texto en [`Entry`](xref:Xamarin.Forms.Entry) con la tecla de retorno, se ejecuta el método `OnEntryCompleted`. El argumento `sender` es el objeto `Entry` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Entry`.

    > [!IMPORTANT]
    > El texto especificado en [`Entry`](xref:Xamarin.Forms.Entry) se almacena en la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una entrada que contiene texto, en iOS y Android](../images/text-changes.png "Entrada con texto")](../images/text-changes-large.png#lightbox "Entrada con texto")

    Establezca puntos de interrupción en los dos controladores de eventos, escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe cómo se desencadenan los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Para obtener más información sobre los eventos de [`Entry`](xref:Xamarin.Forms.Entry), consulte [Eventos e interactividad](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) en la guía [Entrada de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Entry`](xref:Xamarin.Forms.Entry) para que establezca un controlador para los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Entry.Completed):

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Este código establece el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) en un controlador de eventos denominado `OnEntryTextChanged`, y el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed), en un controlador de eventos llamado `OnEntryCompleted`. Ambos controladores de eventos se crearán en el paso siguiente.

1. En el **Panel de solución**, en el proyecto **EntryTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnEntryTextChanged` y `OnEntryCompleted` a la clase:

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Cuando cambia el texto en [`Entry`](xref:Xamarin.Forms.Entry), se ejecuta el método `OnEntryTextChanged`. El argumento `sender` es el objeto `Entry` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Entry`. El argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) proporciona los valores de texto anteriores y nuevos (antes y después del cambio de texto).

    Al finalizar el texto en [`Entry`](xref:Xamarin.Forms.Entry) con la tecla de retorno, se ejecuta el método `OnEntryCompleted`. El argumento `sender` es el objeto `Entry` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Entry`.

    > [!IMPORTANT]
    > El texto especificado en [`Entry`](xref:Xamarin.Forms.Entry) se almacena en la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una entrada que contiene texto, en iOS y Android](../images/text-changes.png "Entrada con texto")](../images/text-changes-large.png#lightbox "Entrada con texto")

    Establezca puntos de interrupción en los dos controladores de eventos, escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe cómo se desencadenan los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Para obtener más información sobre los eventos de [`Entry`](xref:Xamarin.Forms.Entry), vea [Eventos e interactividad](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) en la guía [Entrada de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
