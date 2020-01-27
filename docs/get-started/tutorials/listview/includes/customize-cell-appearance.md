---
ms.openlocfilehash: 3c88b71cea834f5e6ef20d43332904c052c6e3a6
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "61037649"
---
Anteriormente, [`ListView`](xref:Xamarin.Forms.ListView) se ha rellenado con datos mediante el enlace de datos. Aun así, pese al enlace de datos con una colección, donde cada objeto de la colección ha definido varios elementos de datos, solo se ha mostrado un elemento de datos por objeto (la propiedad `Name` del objeto `Monkey`).

En este ejercicio, modificará el proyecto **ListViewTutorial** de forma que [`ListView`](xref:Xamarin.Forms.ListView) muestre varios elementos de datos en cada fila.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`ListView`](xref:Xamarin.Forms.Image) para personalizar la apariencia de cada fila:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
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
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Este código establece la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada fila de [`ListView`](xref:Xamarin.Forms.ListView). El elemento secundario de `DataTemplate` debe ser de tipo [`Cell`](xref:Xamarin.Forms.Cell), o bien derivarse de él. Este código usa [`ViewCell`](xref:Xamarin.Forms.ViewCell), que se deriva de `Cell`, para crear un diseño personalizado para cada fila de `ListView`. Aquí, una [`Grid`](xref:Xamarin.Forms.Grid), que contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y dos objetos [`Label`](xref:Xamarin.Forms.Label), administra el diseño dentro de `ViewCell`. El objeto `Image` enlaza los datos de su propiedad [`Source`](xref:Xamarin.Forms.Image.Source) con la propiedad `ImageUrl` de cada objeto `Monkey`, mientras que los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name` y `Location` de cada objeto `Monkey`.

    De forma predeterminada, todas las filas de un elemento [`ListView`](xref:Xamarin.Forms.ListView) tienen la misma altura. Pero este código establece la propiedad [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) en `true` para que la altura de la fila se pueda ajustar a su contenido. De esta forma, se adaptan las propiedades `Name` y `Location` de cada objeto `Monkey` que tiene cadenas de longitud variable.

    Para obtener más información sobre la apariencia de las celdas de un elemento [`ListView`](xref:Xamarin.Forms.ListView), consulte [Personalizar la apariencia de una celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un elemento ListView cuyos elementos están basados en una plantilla de datos](../images/customize-cell-appearance.png "ListView que muestra datos basados en plantilla")](../images/customize-cell-appearance-large.png#lightbox "ListView que muestra datos basados en plantilla")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`ListView`](xref:Xamarin.Forms.Image) para personalizar la apariencia de cada fila:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
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
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Este código establece la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define la apariencia de cada fila de [`ListView`](xref:Xamarin.Forms.ListView). El elemento secundario de `DataTemplate` debe ser de tipo [`Cell`](xref:Xamarin.Forms.Cell), o bien derivarse de él. Este código usa [`ViewCell`](xref:Xamarin.Forms.ViewCell), que se deriva de `Cell`, para crear un diseño personalizado para cada fila de `ListView`. Aquí, una [`Grid`](xref:Xamarin.Forms.Grid), que contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y dos objetos [`Label`](xref:Xamarin.Forms.Label), administra el diseño dentro de `ViewCell`. El objeto `Image` enlaza los datos de su propiedad [`Source`](xref:Xamarin.Forms.Image.Source) con la propiedad `ImageUrl` de cada objeto `Monkey`, mientras que los objetos `Label` enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) con las propiedades `Name` y `Location` de cada objeto `Monkey`.

    De forma predeterminada, todas las filas de un elemento [`ListView`](xref:Xamarin.Forms.ListView) tienen la misma altura. Pero este código establece la propiedad [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) en `true` para que la altura de la fila se pueda ajustar a su contenido. De esta forma, se adaptan las propiedades `Name` y `Location` de cada objeto `Monkey` que tiene cadenas de longitud variable.

    Para obtener más información sobre la apariencia de las celdas de un elemento [`ListView`](xref:Xamarin.Forms.ListView), consulte [Personalizar la apariencia de una celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un elemento ListView cuyos elementos están basados en una plantilla de datos](../images/customize-cell-appearance.png "ListView que muestra datos basados en plantilla")](../images/customize-cell-appearance-large.png#lightbox "ListView que muestra datos basados en plantilla")
