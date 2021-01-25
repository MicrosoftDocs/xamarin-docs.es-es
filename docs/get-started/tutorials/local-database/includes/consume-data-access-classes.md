---
ms.openlocfilehash: 6e546d85c87962b022f72c8c16cb0ece14e9bde6
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690115"
---
En este ejercicio, creará una interfaz de usuario para consumir las clases de acceso a datos creadas anteriormente.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **LocalDatabaseTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de dos instancias de [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) y un elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tiene su conjunto de propiedades [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica el texto de marcador de posición que se muestra antes de la entrada del usuario. El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. El elemento `CollectionView` establece su propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa `StackLayout` y dos objetos [`Label`](xref:Xamarin.Forms.Label) para definir la apariencia de cada fila en `CollectionView`. Los objetos `Label` enlazan sus propiedades `Text` a `Name`, así como las propiedades `Age` de cada objeto `Person`, respectivamente.

    Además, las instancias de [`Entry`](xref:Xamarin.Forms.Entry) y el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) tienen los nombres especificados con el atributo `x:Name`. Esto permite al archivo de código subyacente acceder a estos objetos mediante los nombre asignados.

1. En el **Explorador de soluciones**, en el proyecto **LocalDatabaseTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue la invalidación `OnAppearing` y el controlador de eventos `OnButtonClicked` a la clase:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    El método `OnAppearing` rellena el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todos los datos almacenados en la base de datos. El método `OnButtonClicked`, que se ejecuta cuando se pulsa el elemento [`Button`](xref:Xamarin.Forms.Button), guarda los datos especificados en la base de datos antes de borrar ambas instancias de [`Entry`](xref:Xamarin.Forms.Entry) y actualizar los datos de `CollectionView`.

    > [!NOTE]
    > La invalidación del método `OnAppearing` se ejecuta después de que se diseñe [`ContentPage`](xref:Xamarin.Forms.ContentPage), pero justo antes de que sea visible. Por lo tanto, se trata de un lugar adecuado para establecer el contenido de las vistas de Xamarin.Forms.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido.

    Escriba varios elementos de datos y pulse el [`Button`](xref:Xamarin.Forms.Button) de cada uno de ellos. De esta forma, se guardarán los datos en la base de datos y se volverá a rellenar el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todos los datos de la base de datos:

    [![Captura de pantalla de la persistencia de datos de la base de datos local de SQLite.NET, en iOS y Android](../images/consume-data-access-classes.png "Persistencia de datos de la base de datos local")](../images/consume-data-access-classes-large.png#lightbox "Persistencia de datos de la base de datos local")

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre las bases de datos locales en Xamarin.Forms, consulte la guía [Bases de datos locales de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **LocalDatabaseTutorial**, haga doble clic en **MainPage.xaml** para abrirlo. Después, en **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LocalDatabaseTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="nameEntry"
                   Placeholder="Enter name" />
            <Entry x:Name="ageEntry"
                   Placeholder="Enter age" />
            <Button Text="Add to Database"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Age}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de dos instancias de [`Entry`](xref:Xamarin.Forms.Entry), un elemento [`Button`](xref:Xamarin.Forms.Button) y un elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cada `Entry` tiene su conjunto de propiedades [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder), que especifica el texto de marcador de posición que se muestra antes de la entrada del usuario. El elemento `Button` establece su evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) en un controlador de eventos denominado `OnButtonClicked` que se creará en el paso siguiente. El elemento `CollectionView` establece su propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que usa `StackLayout` y dos objetos [`Label`](xref:Xamarin.Forms.Label) para definir la apariencia de cada fila en `CollectionView`. Los objetos `Label` enlazan sus propiedades `Text` a `Name`, así como las propiedades `Age` de cada objeto `Person`, respectivamente.

    Además, las instancias de [`Entry`](xref:Xamarin.Forms.Entry) y el elemento [`ListView`](xref:Xamarin.Forms.ListView) tienen los nombres especificados con el atributo `x:Name`. Esto permite al archivo de código subyacente acceder a estos objetos mediante los nombre asignados.

1. En el **Panel de solución**, en el proyecto **LocalDatabaseTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue la invalidación `OnAppearing` y el controlador de eventos `OnButtonClicked` a la clase:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();
        collectionView.ItemsSource = await App.Database.GetPeopleAsync();
    }

    async void OnButtonClicked(object sender, EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(nameEntry.Text) && !string.IsNullOrWhiteSpace(ageEntry.Text))
        {
            await App.Database.SavePersonAsync(new Person
            {
                Name = nameEntry.Text,
                Age = int.Parse(ageEntry.Text)
            });

            nameEntry.Text = ageEntry.Text = string.Empty;
            collectionView.ItemsSource = await App.Database.GetPeopleAsync();
        }
    }
    ```

    El método `OnAppearing` rellena el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todos los datos almacenados en la base de datos. El método `OnButtonClicked`, que se ejecuta cuando se pulsa el elemento [`Button`](xref:Xamarin.Forms.Button), guarda los datos especificados en la base de datos antes de borrar ambas instancias de [`Entry`](xref:Xamarin.Forms.Entry) y actualizar los datos de `CollectionView`.

    > [!NOTE]
    > La invalidación del método `OnAppearing` se ejecuta después de que se diseñe [`ContentPage`](xref:Xamarin.Forms.ContentPage), pero justo antes de que sea visible. Por lo tanto, se trata de un lugar adecuado para establecer el contenido de las vistas de Xamarin.Forms.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido.

    Escriba varios elementos de datos y pulse el [`Button`](xref:Xamarin.Forms.Button) de cada uno de ellos. De esta forma, se guardarán los datos en la base de datos y se volverá a rellenar el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todos los datos de la base de datos:

    [![Captura de pantalla de la persistencia de datos de la base de datos local de SQLite.NET, en iOS y Android](../images/consume-data-access-classes.png "Persistencia de datos de la base de datos local")](../images/consume-data-access-classes-large.png#lightbox "Persistencia de datos de la base de datos local")

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre las bases de datos locales en Xamarin.Forms, consulte la guía [Bases de datos locales de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md).
