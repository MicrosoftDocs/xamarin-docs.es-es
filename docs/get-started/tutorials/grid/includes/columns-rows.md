---
ms.openlocfilehash: d87289e481b69592b68627d053e937856d3d6067
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "61376015"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Grid`](xref:Xamarin.Forms.Grid) para definir columnas y filas, y coloque el contenido en las columnas y filas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Este código define las columnas y filas de [`Grid`](xref:Xamarin.Forms.Grid) y coloca las instancias de [`Label`](xref:Xamarin.Forms.Label) en columnas y filas específicas. Los datos de las columna y las filas se almacenan en las propiedades [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) y [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), que son colecciones de objetos [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) y [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), respectivamente. El ancho de cada `ColumnDefinition` se establece mediante la propiedad [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) y el alto de cada `RowDefinition`, mediante la propiedad [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Los valores de anchura y altura válidos son:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), que cambia el tamaño de la columna o la fila para que se ajuste al contenido.
    - Valores proporcionales, que cambian el tamaño de las columnas y las filas como una proporción del espacio restante. Los valores proporcionales se indican con un `*` al final.
    - Valores absolutos, que cambian el tamaño de las columnas o las filas con valores específicos y fijos.

    Por lo tanto, en el código anterior, cada columna tiene un ancho de la mitad de [`Grid`](xref:Xamarin.Forms.Grid), mientras que cada fila tiene un alto de 50 unidades independientes del dispositivo.

    La posición de cada [`Label`](xref:Xamarin.Forms.Label) en la [`Grid`](xref:Xamarin.Forms.Grid) se especifica con las propiedades adjuntas [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) y [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty), mediante un índice de base cero. Por lo tanto, la primera columna es la 0 y la primera fila es la 0. La primera `Label` carece de estas propiedades adjuntas porque cualquier vista secundaria que no las establezca se representará automáticamente en la columna 0, fila 0.

    > [!NOTE]
    > El espaciado entre columnas y filas en una [`Grid`](xref:Xamarin.Forms.Grid) se puede establecer con las propiedades [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) y [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Para obtener más información, consulte [Espaciado](~/xamarin-forms/user-interface/layouts/grid.md#spacing) en la guía [Cuadrícula de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una cuadrícula que tiene contenido que aparece en columnas y filas, en iOS y Android](../images/columns-rows.png "Cuadrícula con contenido en columnas y filas")](../images/columns-rows-large.png#lightbox "Cuadrícula con contenido en columnas y filas")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Grid`](xref:Xamarin.Forms.Grid) para definir columnas y filas, y coloque el contenido en las columnas y filas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Este código define las columnas y filas de [`Grid`](xref:Xamarin.Forms.Grid) y coloca las instancias de [`Label`](xref:Xamarin.Forms.Label) en columnas y filas específicas. Los datos de las columna y las filas se almacenan en las propiedades [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) y [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), que son colecciones de objetos [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) y [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), respectivamente. El ancho de cada `ColumnDefinition` se establece mediante la propiedad [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) y el alto de cada `RowDefinition`, mediante la propiedad [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). Los valores de anchura y altura válidos son:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), que cambia el tamaño de la columna o la fila para que se ajuste al contenido.
    - Valores proporcionales, que cambian el tamaño de las columnas y las filas como una proporción del espacio restante. Los valores proporcionales se indican con un `*` al final.
    - Valores absolutos, que cambian el tamaño de las columnas o las filas con valores específicos y fijos.

    Por lo tanto, en el código anterior, cada columna tiene un ancho de la mitad de [`Grid`](xref:Xamarin.Forms.Grid), mientras que cada fila tiene un alto de 50 unidades independientes del dispositivo.

    La posición de cada [`Label`](xref:Xamarin.Forms.Label) en la [`Grid`](xref:Xamarin.Forms.Grid) se especifica con las propiedades adjuntas [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) y [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty), mediante un índice de base cero. Por lo tanto, la primera columna es la 0 y la primera fila es la 0. La primera `Label` carece de estas propiedades adjuntas porque cualquier vista secundaria que no las establezca se representará automáticamente en la columna 0, fila 0.

    > [!NOTE]
    > El espaciado entre columnas y filas en una [`Grid`](xref:Xamarin.Forms.Grid) se puede establecer con las propiedades [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) y [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Para obtener más información, consulte [Espaciado](~/xamarin-forms/user-interface/layouts/grid.md#spacing) en la guía [Cuadrícula de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una cuadrícula que tiene contenido que aparece en columnas y filas, en iOS y Android](../images/columns-rows.png "Cuadrícula con contenido en columnas y filas")](../images/columns-rows-large.png#lightbox "Cuadrícula con contenido en columnas y filas")
