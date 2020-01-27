---
ms.openlocfilehash: c92a97b336e89214bbd95021ad8fb9a56f64cc8c
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "67659907"
---
En este ejercicio se creará una interfaz de usuario para consumir la clase `RestService`, que a su vez recupera datos de la API web [OpenWeatherMap](https://openweathermap.org/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **WebServiceTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de un elemento [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) y una serie de instancias de [`Label`](xref:Xamarin.Forms.Label) en un elemento [`Grid`](xref:Xamarin.Forms.Grid). El elemento `Entry` se rellena previamente con "Seattle" estableciendo su propiedad [`Text`](xref:Xamarin.Forms.Entry.Text). El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. La mitad de la instancias de `Label` muestran texto estático y las instancias restantes establecen un enlace de datos a las propiedades de `WeatherData`. En tiempo de ejecución, las instancias de `Label` que utilizan el enlace de datos buscarán en sus respectivas propiedades [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) el objeto `WeatherData` que se utilizará en sus expresiones de enlace. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Además, el objeto [`Entry`](xref:Xamarin.Forms.Entry) tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto con el nombre que se le ha asignado.

1. En el **Explorador de soluciones**, en el proyecto **WebServiceTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    El método `OnButtonClicked`, que se ejecuta cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), invoca el método `RestService.GetWeatherDataAsync` para recuperar datos meteorológicos de la ciudad cuyo nombre se ha indicado en el elemento [`Entry`](xref:Xamarin.Forms.Entry). El método `GetWeatherDataAsync` requiere un argumento `string` que representa el URI de la API web que se invoca, dicho argumento lo produce el método `GenerateRequestUri`. Este método toma la dirección de punto de conexión almacenada en la constante `OpenWeatherMapEndpoint` y le agrega parámetros de consulta que especifican:

    - Ciudad para la que se solicitan datos meteorológicos.
    - Unidades en las que se devolverán los datos meteorológicos.
    - Clave de API personal.

    Después de recuperar los datos meteorológicos solicitados, el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en el objeto `WeatherData`. Para obtener más información sobre la propiedad `BindingContext`, vea [Enlaces con un contexto de enlace](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) en la guía de [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) se hereda a través del árbol visual. Por lo tanto, como ya se ha establecido en el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), los objetos secundarios del elemento `ContentPage` heredan su valor, incluyendo las instancias de [`Label`](xref:Xamarin.Forms.Label).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Pulse [`Button`](xref:Xamarin.Forms.Button) para recuperar los datos meteorológicos actuales para Seattle:

    [![Captura de pantalla de datos meteorológicos de Seattle, en iOS y Android](../images/consume-web-service.png "Datos meteorológicos de Seattle")](../images/consume-web-service-large.png#lightbox "Datos meteorológicos de Seattle")

    > [!IMPORTANT]
    > Debe establecerse la clave de API de OpenWeatherMap personal como el valor de la constante `OpenWeatherMapAPIKey` en la clase `Constants`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **WebServiceTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de un elemento [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) y una serie de instancias de [`Label`](xref:Xamarin.Forms.Label) en un elemento [`Grid`](xref:Xamarin.Forms.Grid). El elemento `Entry` se rellena previamente con "Seattle" estableciendo su propiedad [`Text`](xref:Xamarin.Forms.Entry.Text). El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. La mitad de la instancias de `Label` muestran texto estático y las instancias restantes establecen un enlace de datos a las propiedades de `WeatherData`. En tiempo de ejecución, las instancias de `Label` que utilizan el enlace de datos buscarán en sus respectivas propiedades [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) el objeto `WeatherData` que se utilizará en sus expresiones de enlace. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Además, el objeto [`Entry`](xref:Xamarin.Forms.Entry) tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto con el nombre que se le ha asignado.

    Para obtener más información sobre cómo usar servicios web basados en REST en Xamarin.Forms, vea [Consumo de servicios web RESTful (guía)](~/xamarin-forms/data-cloud/web-services/rest.md).

1. En el **Panel de solución**, en el proyecto **WebServiceTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    El método `OnButtonClicked`, que se ejecuta cuando se pulsa [`Button`](xref:Xamarin.Forms.Button), invoca el método `RestService.GetWeatherDataAsync` para recuperar datos meteorológicos de la ciudad cuyo nombre se ha indicado en el elemento [`Entry`](xref:Xamarin.Forms.Entry). El método `GetWeatherDataAsync` requiere un argumento `string` que representa el URI de la API web que se invoca, dicho argumento lo produce el método `GenerateRequestUri`. Este método toma la dirección de punto de conexión almacenada en la constante `OpenWeatherMapEndpoint` y le agrega parámetros de consulta que especifican:

    - Ciudad para la que se solicitan datos meteorológicos.
    - Unidades en las que se devolverán los datos meteorológicos.
    - Clave de API personal.

    Después de recuperar los datos meteorológicos solicitados, el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en el objeto `WeatherData`. Para obtener más información sobre la propiedad `BindingContext`, vea [Enlaces con un contexto de enlace](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) en la guía de [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) se hereda a través del árbol visual. Por lo tanto, como ya se ha establecido en el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), los objetos secundarios del elemento `ContentPage` heredan su valor, incluyendo las instancias de [`Label`](xref:Xamarin.Forms.Label).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Pulse [`Button`](xref:Xamarin.Forms.Button) para recuperar los datos meteorológicos actuales para Seattle:

    [![Captura de pantalla de datos meteorológicos de Seattle, en iOS y Android](../images/consume-web-service.png "Datos meteorológicos de Seattle")](../images/consume-web-service-large.png#lightbox "Datos meteorológicos de Seattle")

    > [!IMPORTANT]
    > Debe establecerse la clave de API de OpenWeatherMap personal como el valor de la constante `OpenWeatherMapAPIKey` en la clase `Constants`.

    Para obtener más información sobre cómo usar servicios web basados en REST en Xamarin.Forms, vea [Consumo de servicios web RESTful (guía)](~/xamarin-forms/data-cloud/web-services/rest.md).
