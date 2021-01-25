---
ms.openlocfilehash: 9ff398b53e88f38589733b1caac623e564e1ed39
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634845"
---

Xamarin.Forms tiene un elemento emergente modal, conocido como una hoja de acciones, que se puede usar para guiar a los usuarios en la realización de una tarea. En este ejercicio, usará el método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) de la clase [`Page`](xref:Xamarin.Forms.Page) para mostrar una hoja de acciones que guía a los usuarios en la realización de una tarea.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, agregue una nueva declaración [`Button`](xref:Xamarin.Forms.Button) que mostrará una hoja de acciones:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

     La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`. Además, el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) se establece en un controlador de eventos denominado `OnDisplayActionSheetButtonClicked` que se creará en el paso siguiente.

1. En el **Explorador de soluciones**, en el proyecto **PopupsTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnDisplayActionSheetButtonClicked` a la clase:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método `OnDisplayActionSheetButtonClicked`. Este método llama al método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) para presentar al usuario un conjunto de alternativas relacionadas con cómo realizar una tarea. Una vez que el usuario seleccione una de las alternativas, la selección se devuelve como una `string`.

    > [!IMPORTANT]
    > El método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) es asincrónico y siempre se debe esperar por él con la palabra clave `await`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Después, pulse el [`Button`](xref:Xamarin.Forms.Button) que ha agregado a la [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Captura de pantalla de una hoja de acción en iOS y Android](../images/actionsheet.png "Hoja de acciones que guía a los usuarios a través de una tarea")](../images/actionsheet-large.png#lightbox "Hoja de acciones que guía a los usuarios a través de una tarea")

    Tenga en cuenta que, tras seleccionar una alternativa en el cuadro de diálogo de la hoja de acciones, la selección aparece en la ventana **Salida** de Visual Studio. Si esta ventana no está visible, puede mostrarse seleccionando la opción de menú **Ver > Salida**.

    En Visual Studio, detenga la aplicación.

    Para más información sobre cómo mostrar hojas de acciones, consulte [Guiar a los usuarios a través de tareas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) en la guía [Mostrar elementos emergentes](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, agregue una nueva declaración [`Button`](xref:Xamarin.Forms.Button) que mostrará una hoja de acciones:

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

    La propiedad [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifica el texto que aparece en el elemento `Button`. Además, el evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) se establece en un controlador de eventos denominado `OnDisplayActionSheetButtonClicked` que se creará en el paso siguiente.

1. En el **Panel de solución**, en el proyecto **PopupsTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnDisplayActionSheetButtonClicked` a la clase:

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método `OnDisplayActionSheetButtonClicked`. Este método llama al método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) para presentar al usuario un conjunto de alternativas relacionadas con cómo realizar una tarea. Una vez que el usuario seleccione una de las alternativas, la selección se devuelve como una `string`.

    > [!IMPORTANT]
    > El método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) es asincrónico y siempre se debe esperar por él con la palabra clave `await`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Después, pulse el [`Button`](xref:Xamarin.Forms.Button) que ha agregado a la [`ContentPage`](xref:Xamarin.Forms.ContentPage):

    [![Captura de pantalla de una hoja de acción en iOS y Android](../images/actionsheet.png "Hoja de acciones que guía a los usuarios a través de una tarea")](../images/actionsheet-large.png#lightbox "Hoja de acciones que guía a los usuarios a través de una tarea")

    Tenga en cuenta que, tras seleccionar una alternativa en el cuadro de diálogo de la hoja de acciones, la selección aparece en la ventana **Salida de la aplicación** de Visual Studio para Mac. Si esta ventana no está visible, puede mostrarse seleccionando la opción de menú **Ver > Otras ventanas > Salida de la aplicación**.

    En Visual Studio para Mac, detenga la aplicación.

    Para más información sobre cómo mostrar hojas de acciones, consulte [Guiar a los usuarios a través de tareas](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) en la guía [Mostrar elementos emergentes](~/xamarin-forms/user-interface/pop-ups.md).
