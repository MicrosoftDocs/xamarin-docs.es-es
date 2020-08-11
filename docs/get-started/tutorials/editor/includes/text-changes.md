---
ms.openlocfilehash: f1671f4f40e3eb44465431d39994cf5e623665a1
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919353"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Editor`](xref:Xamarin.Forms.Editor) para que establezca un controlador para los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Este código establece el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) en un controlador de eventos denominado `OnEditorTextChanged`, y el evento [`Completed`](xref:Xamarin.Forms.Editor.Completed), en un controlador de eventos llamado `OnEditorCompleted`. Ambos controladores de eventos se crearán en el paso siguiente.

1. En el **Explorador de soluciones**, en el proyecto **EditorTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnEditorTextChanged` y `OnEditorCompleted` a la clase:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Cuando cambia el texto en [`Editor`](xref:Xamarin.Forms.Editor), se ejecuta el método `OnEditorTextChanged`. El argumento `sender` es el objeto `Editor` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Editor`. El argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) proporciona los valores de texto anteriores y nuevos (antes y después del cambio de texto).

    Cuando se completa la edición, se ejecuta el método `OnEditorCompleted`. Para hacerlo, es necesario quitar el enfoque de [`Editor`](xref:Xamarin.Forms.Editor) o presionar el botón "Aceptar" en iOS. El argumento `sender` es el objeto `Editor` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Editor`.

    > [!IMPORTANT]
    > El texto especificado en [`Editor`](xref:Xamarin.Forms.Editor) se almacena en la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla del editor que contiene texto, en iOS y Android](../images/text-changes.png "Editor con texto")](../images/text-changes-large.png#lightbox "Editor con texto")

    Establezca puntos de interrupción en los dos controladores de eventos, escriba texto en [`Editor`](xref:Xamarin.Forms.Editor) y observe cómo se activa el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Quite el enfoque de `Editor` para observar cómo se activa el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Para obtener más información sobre los eventos de [`Editor`](xref:Xamarin.Forms.Editor), consulte [Eventos e interactividad](~/xamarin-forms/user-interface/text/editor.md#events-and-interactivity) en la guía [Editor de Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Editor`](xref:Xamarin.Forms.Editor) para que establezca un controlador para los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Editor.Completed):

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Este código establece el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) en un controlador de eventos denominado `OnEditorTextChanged`, y el evento [`Completed`](xref:Xamarin.Forms.Editor.Completed), en un controlador de eventos llamado `OnEditorCompleted`. Ambos controladores de eventos se crearán en el paso siguiente.

1. En el **Panel de solución**, en el proyecto **EditorTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnEditorTextChanged` y `OnEditorCompleted` a la clase:

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Cuando cambia el texto en [`Editor`](xref:Xamarin.Forms.Editor), se ejecuta el método `OnEditorTextChanged`. El argumento `sender` es el objeto `Editor` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Editor`. El argumento [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) proporciona los valores de texto anteriores y nuevos (antes y después del cambio de texto).

    Cuando se completa la edición, se ejecuta el método `OnEditorCompleted`. Para hacerlo, es necesario quitar el enfoque de [`Editor`](xref:Xamarin.Forms.Editor) o presionar el botón "Aceptar" en iOS. El argumento `sender` es el objeto `Editor` responsable de la activación del evento `TextChanged` y puede usarse para acceder al objeto `Editor`.

    > [!IMPORTANT]
    > El texto especificado en [`Editor`](xref:Xamarin.Forms.Editor) se almacena en la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla del editor que contiene texto, en iOS y Android](../images/text-changes.png "Editor con texto")](../images/text-changes-large.png#lightbox "Editor con texto")

    Establezca puntos de interrupción en los dos controladores de eventos, escriba texto en [`Editor`](xref:Xamarin.Forms.Editor) y observe cómo se activa el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Quite el enfoque de `Editor` para observar cómo se activa el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Para obtener más información sobre los eventos de [`Editor`](xref:Xamarin.Forms.Editor), consulte [Eventos e interactividad](~/xamarin-forms/user-interface/text/editor.md#events-and-interactivity) en la guía [Editor de Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
