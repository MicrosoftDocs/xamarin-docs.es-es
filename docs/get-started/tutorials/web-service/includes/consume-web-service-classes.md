---
ms.openlocfilehash: a642e34c5f49e63503b34c44f536f0428993335b
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634967"
---
En este ejercicio, creará una interfaz de usuario para usar la clase `RestService`, que recupera datos de repositorio para .NET de la API web de GitHub. Una vista [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará los datos recuperados.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **WebServiceTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) y una vista [`CollectionView`](xref:Xamarin.Forms.CollectionView). El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. La vista `CollectionView` establece la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en un valor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada elemento en `CollectionView`. El elemento secundario de `DataTemplate` es un valor [`StackLayout`](xref:Xamarin.Forms.StackLayout), que contiene tres objetos [`Label`](xref:Xamarin.Forms.Label). Los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name`, `Description` y `GitHubHomeUrl` de cada objeto `Repository`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Además, el objeto [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto con el nombre que se le ha asignado.

1. En el **Explorador de soluciones**, en el proyecto **WebServiceTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
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
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    El método `OnButtonClicked`, que se ejecuta cuando se pulsa el objeto [`Button`](xref:Xamarin.Forms.Button), invoca el método `RestService.GetRepositoriesAsync` para recuperar datos de repositorio para .NET de la API web de GitHub. El método `GetRepositoriesAsync` requiere un argumento `string` que representa el URI de la API web que se invoca; el campo `Constants.GitHubReposEndpoint` devuelve dicho argumento.

    Después de recuperar los datos solicitados, la propiedad [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) se establece en los datos recuperados.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Pulse el objeto [`Button`](xref:Xamarin.Forms.Button) a fin de recuperar datos de repositorio para .NET de GitHub:

    [![Captura de pantalla de una serie de repositorios para .NET de GitHub, en iOS y Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre cómo usar servicios web basados en REST en Xamarin.Forms, vea [Consumo de servicios web RESTful (guía)](~/xamarin-forms/data-cloud/web-services/rest.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **WebServiceTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en un objeto [`Button`](xref:Xamarin.Forms.Button) y una vista [`CollectionView`](xref:Xamarin.Forms.CollectionView). El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. La vista `CollectionView` establece la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en un valor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada elemento en `CollectionView`. El elemento secundario de `DataTemplate` es un valor [`StackLayout`](xref:Xamarin.Forms.StackLayout), que contiene tres objetos [`Label`](xref:Xamarin.Forms.Label). Los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name`, `Description` y `GitHubHomeUrl` de cada objeto `Repository`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    Además, el objeto [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un nombre que se especifica con el atributo `x:Name`. Esto permite al archivo de código subyacente tener acceso al objeto con el nombre que se le ha asignado.

1. En el **Panel de solución**, en el proyecto **WebServiceTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
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
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    El método `OnButtonClicked`, que se ejecuta cuando se pulsa el objeto [`Button`](xref:Xamarin.Forms.Button), invoca el método `RestService.GetRepositoriesAsync` para recuperar datos de repositorio para .NET de la API web de GitHub. El método `GetRepositoriesAsync` requiere un argumento `string` que representa el URI de la API web que se invoca; el campo `Constants.GitHubReposEndpoint` devuelve dicho argumento.

    Después de recuperar los datos solicitados, la propiedad [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) se establece en los datos recuperados.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Pulse el objeto [`Button`](xref:Xamarin.Forms.Button) a fin de recuperar datos de repositorio para .NET de GitHub:

    [![Captura de pantalla de una serie de repositorios para .NET de GitHub, en iOS y Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre cómo usar servicios web basados en REST en Xamarin.Forms, vea [Consumo de servicios web RESTful (guía)](~/xamarin-forms/data-cloud/web-services/rest.md).
