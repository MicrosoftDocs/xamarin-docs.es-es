---
ms.openlocfilehash: 6118d4ee8d2fa56a3b14d3d280db4991a36874fc
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689774"
---
Anteriormente, [`CollectionView`](xref:Xamarin.Forms.CollectionView) se ha rellenado con datos mediante el enlace de datos. Aun así, pese al enlace de datos con una colección, donde cada objeto de la colección ha definido varios elementos de datos, solo se ha mostrado un elemento de datos por objeto (la propiedad `Name` del objeto `Monkey`).

En este ejercicio, modificará el proyecto **CollectionViewTutorial** de forma que [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestre varios elementos de datos en cada fila.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`CollectionView`](xref:Xamarin.Forms.CollectionView) para personalizar la apariencia de cada elemento de datos:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Este código establece la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView). El elemento secundario de `DataTemplate` es [`Grid`](xref:Xamarin.Forms.Grid), que contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y dos objetos [`Label`](xref:Xamarin.Forms.Label). El objeto `Image` enlaza los datos de su propiedad [`Source`](xref:Xamarin.Forms.Image.Source) con la propiedad `ImageUrl` de cada objeto `Monkey`, mientras que los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name` y `Location` de cada objeto `Monkey`.

    Para obtener más información sobre la apariencia del elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView), consulte [Definir la apariencia del elemento](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView cuyos elementos están basados en una plantilla de datos](../images/customize-item-appearance.png "CollectionView que muestra datos basados en plantilla")](../images/customize-item-appearance-large.png#lightbox "CollectionView que muestra datos basados en plantilla")

    En Visual Studio, detenga la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`CollectionView`](xref:Xamarin.Forms.CollectionView) para personalizar la apariencia de cada elemento de datos:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Este código establece la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView). El elemento secundario de `DataTemplate` es [`Grid`](xref:Xamarin.Forms.Grid), que contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y dos objetos [`Label`](xref:Xamarin.Forms.Label). El objeto `Image` enlaza los datos de su propiedad [`Source`](xref:Xamarin.Forms.Image.Source) con la propiedad `ImageUrl` de cada objeto `Monkey`, mientras que los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name` y `Location` de cada objeto `Monkey`.

    Para obtener más información sobre la apariencia del elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView), consulte [Definir la apariencia del elemento](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView cuyos elementos están basados en una plantilla de datos](../images/customize-item-appearance.png "CollectionView que muestra datos basados en plantilla")](../images/customize-item-appearance-large.png#lightbox "CollectionView que muestra datos basados en plantilla")

    En Visual Studio para Mac, detenga la aplicación.
