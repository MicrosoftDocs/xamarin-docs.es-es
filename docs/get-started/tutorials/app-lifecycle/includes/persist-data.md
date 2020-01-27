---
ms.openlocfilehash: 16ceaba572ca932777bb366d9f7c58f6dcb24f70
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "67841442"
---
La subclase [`Application`](xref:Xamarin.Forms.Application) tiene un diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) estático que se puede usar para almacenar datos a lo largo de los cambios de estado del ciclo de vida. Este diccionario usa una clave `string` y almacena un valor `object`. El diccionario se guarda automáticamente en el dispositivo y se vuelve a rellenar cuando se reinicia la aplicación.

> [!IMPORTANT]
> El diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) solo puede serializar tipos primitivos para el almacenamiento.

En este ejercicio, modificará la aplicación para conservar el texto de un objeto [`Entry`](xref:Xamarin.Forms.Entry) tras el procesamiento en segundo plano y restaurar el texto al objeto `Entry` cuando se reinicie la aplicación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **AppLifecycleTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Este código define una propiedad `DisplayText` y una constante `displayText`. Cuando la aplicación se pasa a segundo plano o se termina, la invalidación del método `OnSleep` agrega el valor de propiedad `DisplayText` al diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) con una clave de `displayText`. Después, cuando se inicia la aplicación, siempre y cuando el diccionario `Properties` contenga la clave `displayText`, el valor de la clave se restaura a la propiedad `DisplayText`.

    > [!IMPORTANT]
    > Para evitar errores inesperados, compruebe que el diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) tenga la clave antes de obtener acceso.

    No es necesario restaurar datos del diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) en la sobrecarga del método `OnResume`. Esto se debe a que, cuando una aplicación pasa a segundo plano, dicha aplicación y su estado todavía están en la memoria.

1. En el **Explorador de soluciones**, en el proyecto **AppLifecycleTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Entry`](xref:Xamarin.Forms.Entry) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) especifica el texto de marcador de posición que se muestra cuando el objeto `Entry` aparece por primera vez, y se registra un controlador de eventos denominado `OnEntryCompleted` con el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Además, el objeto `Entry` tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto `Entry` con el nombre que se le ha asignado.

1. En el **Explorador de soluciones**, en el proyecto **AppLifecycleTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue una invalidación del método `OnAppearing` y el controlador de eventos `OnEntryCompleted` a la clase:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    El método `OnAppearing` recupera el valor de la propiedad `App.DisplayText` y lo establece como valor de propiedad [`Text`](xref:Xamarin.Forms.Entry.Text) del objeto [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > La invalidación del método `OnAppearing` se ejecuta después de que se diseñe [`ContentPage`](xref:Xamarin.Forms.ContentPage), pero justo antes de que sea visible. Por lo tanto, se trata de un lugar adecuado para establecer el contenido de las vistas de Xamarin.Forms.

    Cuando se finaliza el texto en el objeto [`Entry`](xref:Xamarin.Forms.Entry), al pulsar la tecla ENTRAR, el método `OnEntryCompleted` se ejecuta y el texto `Entry` se almacena en la propiedad `App.DisplayText`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido.

    Escriba algo de texto en el objeto [`Entry`](xref:Xamarin.Forms.Entry) y pulse la tecla ENTRAR. Después, ejecute en segundo plano la aplicación. Para ello, pulse el botón de inicio para invocar el método `OnSleep`.

    Por último, inicie la aplicación de nuevo desde Visual Studio y el texto que especificó anteriormente en el objeto [`Entry`](xref:Xamarin.Forms.Entry) se restaurará:

    [![Captura de pantalla de una entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida, en iOS y Android](../images/persist-data.png "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")](../images/persist-data-large.png#lightbox "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")

    Para obtener más información sobre cómo conservar datos en el diccionario Properties, vea [Diccionario Properties](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) en la guía [Clase App de Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **AppLifecycleTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Este código define una propiedad `DisplayText` y una constante `displayText`. Cuando la aplicación se pasa a segundo plano o se termina, la invalidación del método `OnSleep` agrega el valor de propiedad `DisplayText` al diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) con una clave de `displayText`. Después, cuando se inicia la aplicación, siempre y cuando el diccionario `Properties` contenga la clave `displayText`, el valor de la clave se restaura a la propiedad `DisplayText`.

    > [!IMPORTANT]
    > Para evitar errores inesperados, compruebe que el diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) tenga la clave antes de obtener acceso.

    No es necesario restaurar datos del diccionario [`Properties`](xref:Xamarin.Forms.Application.Properties) en la sobrecarga del método `OnResume`. Esto se debe a que, cuando una aplicación pasa a segundo plano, dicha aplicación y su estado todavía están en la memoria.

1. En el **Panel de solución**, en el proyecto **AppLifecycleTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Entry`](xref:Xamarin.Forms.Entry) en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propiedad [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) especifica el texto de marcador de posición que se muestra cuando el objeto `Entry` aparece por primera vez, y se registra un controlador de eventos denominado `OnEntryCompleted` con el evento [`Completed`](xref:Xamarin.Forms.Entry.Completed). Además, el objeto `Entry` tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto `Entry` con el nombre que se le ha asignado.

1. En el **Panel de solución**, en el proyecto **AppLifecycleTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue una invalidación del método `OnAppearing` y el controlador de eventos `OnEntryCompleted` a la clase:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    El método `OnAppearing` recupera el valor de la propiedad `App.DisplayText` y lo establece como valor de propiedad [`Text`](xref:Xamarin.Forms.Entry.Text) del objeto [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > La invalidación del método `OnAppearing` se ejecuta después de que se diseñe [`ContentPage`](xref:Xamarin.Forms.ContentPage), pero justo antes de que sea visible. Por lo tanto, se trata de un lugar adecuado para establecer el contenido de las vistas de Xamarin.Forms.

    Cuando se finaliza el texto en el objeto [`Entry`](xref:Xamarin.Forms.Entry), al pulsar la tecla ENTRAR, el método `OnEntryCompleted` se ejecuta y el texto `Entry` se almacena en la propiedad `App.DisplayText`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido.

    Escriba algo de texto en el objeto [`Entry`](xref:Xamarin.Forms.Entry) y pulse la tecla ENTRAR. Después, ejecute en segundo plano la aplicación. Para ello, pulse el botón de inicio para invocar el método `OnSleep`.

    Por último, inicie la aplicación de nuevo desde Visual Studio para Mac y el texto que especificó anteriormente en el objeto [`Entry`](xref:Xamarin.Forms.Entry) se restaurará:

    [![Captura de pantalla de una entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida, en iOS y Android](../images/persist-data.png "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")](../images/persist-data-large.png#lightbox "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")

    Para obtener más información sobre cómo conservar datos en el diccionario Properties, vea [Diccionario Properties](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) en la guía [Clase App de Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).
