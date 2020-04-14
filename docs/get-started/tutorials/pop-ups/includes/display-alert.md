---
ms.openlocfilehash: 875f00b379879aa131d37018f89e475170e5320e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277460"
---
Xamarin.Forms tienen una ventana emergente modal, conocida como alerta, para mostrar alertas al usuario o realizar preguntas sencillas. En este ejercicio, usará el método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*), de la clase [`Page`](xref:Xamarin.Forms.Page), para mostrar una alerta al usuario y formular una pregunta sencilla.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una nueva aplicación de Xamarin.Forms en blanco denominada **PopupsTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este tutorial requieren que la solución se denomine **PopupsTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **PopupsTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de dos objetos [`Button`](xref:Xamarin.Forms.Button) de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Las propiedades de [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifican el texto que aparece en cada `Button` y los eventos [`Clicked`](xref:Xamarin.Forms.Button.Clicked) se establecen en los controladores de eventos que se crearán en el siguiente paso.

1. En el **Explorador de soluciones**, en el proyecto **PopupsTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnDisplayAlertButtonClicked` y `OnDisplayAlertQuestionButtonClicked` a la clase:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método de controlador de evento correspondiente. El método `OnDisplayAlertButtonClicked` llamada al método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para mostrar una alerta modal con un botón Cancelar único. Una vez que se descarta la alerta, el usuario puede seguir interactuando con la aplicación.

    El método `OnDisplayAlertQuestionButtonClicked` llama a una sobrecarga del método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para mostrar una alerta modal con un botón Aceptar y un botón Cancelar. Una vez que el usuario seleccione uno de los botones, la selección se devuelve como un elemento `boolean`.

    > [!IMPORTANT]
    > El método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) es asincrónico y siempre se debe esperar por él con la palabra clave `await`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Después, pulse el primer elemento [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de pantalla de una alerta en iOS y Android](../images/alert.png "Alerta")](../images/alert-large.png#lightbox "Alerta")

    Después de descartar la alerta, pulse el segundo elemento [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de pantalla de una alerta que formula una pregunta, en iOS y Android](../images/alert-question.png "Alerta que formula una pregunta")](../images/alert-question-large.png#lightbox "Alerta que formula una pregunta")

    Observe que, después de seleccionar una respuesta a la pregunta, la respuesta se muestra en la ventana **Salida** de Visual Studio.

    Para más información sobre cómo mostrar alertas, consulte [Visualización de una alerta](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) en la guía [Mostrar elementos emergentes](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una nueva aplicación de Xamarin.Forms en blanco denominada **PopupsTutorial**. Asegúrese de que la aplicación use .NET Standard como mecanismo de código compartido.

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este tutorial requieren que la solución se denomine **PopupsTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **PopupsTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de dos objetos [`Button`](xref:Xamarin.Forms.Button) de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Las propiedades de [`Button.Text`](xref:Xamarin.Forms.Button.Text) especifican el texto que aparece en cada `Button` y los eventos [`Clicked`](xref:Xamarin.Forms.Button.Clicked) se establecen en los controladores de eventos que se crearán en el siguiente paso.

1. En el **Panel de solución**, en el proyecto **PopupsTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue los controladores de eventos `OnDisplayAlertButtonClicked` y `OnDisplayAlertQuestionButtonClicked` a la clase:

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), se ejecuta el método de controlador de evento correspondiente. El método `OnDisplayAlertButtonClicked` llamada al método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para mostrar una alerta modal con un botón Cancelar único. Una vez que se descarta la alerta, el usuario puede seguir interactuando con la aplicación.

    El método `OnDisplayAlertQuestionButtonClicked` llama a una sobrecarga del método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) para mostrar una alerta modal con un botón Aceptar y un botón Cancelar. Una vez que el usuario seleccione uno de los botones, la selección se devuelve como un elemento `boolean`.

    > [!IMPORTANT]
    > El método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) es asincrónico y siempre se debe esperar por él con la palabra clave `await`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Después, pulse el primer elemento [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de pantalla de una alerta en iOS y Android](../images/alert.png "Alerta")](../images/alert-large.png#lightbox "Alerta")

    Después de descartar la alerta, pulse el segundo elemento [`Button`](xref:Xamarin.Forms.Button):

    [![Captura de pantalla de una alerta que formula una pregunta, en iOS y Android](../images/alert-question.png "Alerta que formula una pregunta")](../images/alert-question-large.png#lightbox "Alerta que formula una pregunta")

    Observe que, después de seleccionar una respuesta a la pregunta, la respuesta se muestra en la ventana **Salida** de Visual Studio para Mac.

    Para más información sobre cómo mostrar alertas, consulte [Visualización de una alerta](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) en la guía [Mostrar elementos emergentes](~/xamarin-forms/user-interface/pop-ups.md).
