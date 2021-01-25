---
ms.openlocfilehash: 89b56e3733d1c75ed616f79508d555ccb0c51f00
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634912"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Grid`](xref:Xamarin.Forms.Grid) para definir columnas y filas y coloque el contenido que abarca las columnas y filas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Este código define las columnas y filas de [`Grid`](xref:Xamarin.Forms.Grid) y coloca las instancias de [`Label`](xref:Xamarin.Forms.Label) en columnas y filas específicas. El primer `Label` establece la propiedad adjunta [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) para que su texto abarque varias columnas. La propiedad `ColumnSpan` está establecida en 2, que representa el número de columnas que abarcará `Label`. El segundo `Label` establece la propiedad adjunta [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) para que su texto abarque varias filas. La propiedad `RowSpan` está establecida en 2, que representa el número de filas que abarcará `Label`.

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de una cuadrícula que tiene contenido que abarca varias columnas y filas, en iOS y Android](../images/span-columns-rows.png "Cuadrícula con contenido que abarca filas y columnas")](../images/span-columns-rows-large.png#lightbox "Cuadrícula con contenido que abarca filas y columnas")

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre la extensión de columnas y filas, vea la sección [Filas y columnas](~/xamarin-forms/user-interface/layouts/grid.md#rows-and-columns) de la guía de [Xamarin.Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Grid`](xref:Xamarin.Forms.Grid) para definir columnas y filas y coloque el contenido que abarca las columnas y filas:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Este código define las columnas y filas de [`Grid`](xref:Xamarin.Forms.Grid) y coloca las instancias de [`Label`](xref:Xamarin.Forms.Label) en columnas y filas específicas. El primer `Label` establece la propiedad adjunta [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) para que su texto abarque varias columnas. La propiedad `ColumnSpan` está establecida en 2, que representa el número de columnas que abarcará `Label`. El segundo `Label` establece la propiedad adjunta [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) para que su texto abarque varias filas. La propiedad `RowSpan` está establecida en 2, que representa el número de filas que abarcará `Label`.

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio para Mac, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de una cuadrícula que tiene contenido que abarca varias columnas y filas, en iOS y Android](../images/span-columns-rows.png "Cuadrícula con contenido que abarca filas y columnas")](../images/span-columns-rows-large.png#lightbox "Cuadrícula con contenido que abarca filas y columnas")

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre la extensión de columnas y filas, vea la sección [Filas y columnas](~/xamarin-forms/user-interface/layouts/grid.md#rows-and-columns) de la guía de [Xamarin.Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md).
