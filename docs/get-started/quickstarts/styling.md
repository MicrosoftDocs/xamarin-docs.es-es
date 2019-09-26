---
title: Estilo de una aplicación multiplataforma de Xamarin.Forms
description: En este artículo se explica cómo aplicar un estilo a una aplicación de Xamarin. Forms multiplataforma con estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 688b0e87bb6281923d3099c0d269b1c2554b6c7a
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "70756757"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Aplicar estilo a una aplicación de Xamarin. Forms multiplataforma

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

En esta guía de inicio rápido, obtendrá información sobre cómo:

- Aplicar estilo a una aplicación de Xamarin. Forms mediante estilos XAML.

En la guía de inicio rápido se explica cómo aplicar un estilo a una aplicación de Xamarin. Forms multiplataforma con estilos XAML. A continuación se muestra la aplicación final:

[![](styling-images/screenshots1-sml.png "")](styling-images/screenshots1.png#lightbox "Página notas")
[![](styling-images/screenshots2-sml.png "Página de entrada de nota") de página de notas](styling-images/screenshots2.png#lightbox "Página de entrada de nota")

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar esta guía de inicio rápido, debe completar correctamente el [Inicio rápido anterior](database.md) . También puede descargar el [ejemplo de inicio rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) y usarlo como punto de partida para esta guía de inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución notas.

2. En **Explorador de soluciones**, en el proyecto **notas** , haga doble clic en **app. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define un [`Thickness`](xref:Xamarin.Forms.Thickness) valor, una serie de [`Color`](xref:Xamarin.Forms.Color) valores [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y estilos implícitos para y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Tenga en cuenta que estos estilos, que están en el nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **app. Xaml** presionando **Ctrl + S**y cierre el archivo.

3. En **Explorador de soluciones**, en el proyecto **notas** , haga doble clic en **NotesPage. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un [`ListView`](xref:Xamarin.Forms.ListView) estilo implícito para al nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página y establece la `ListView.Margin` propiedad en un valor definido en el nivel `ResourceDictionary`de la aplicación. Tenga en cuenta `ListView` que el estilo implícito se agregó al nivel `ResourceDictionary`de página, porque solo lo consume `NotesPage`. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage. Xaml** presionando **Ctrl + S**y cierre el archivo.

4. En **Explorador de soluciones**, en el proyecto **notas** , haga doble clic en **NoteEntryPage. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Este código agrega estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) las [`Button`](xref:Xamarin.Forms.Button) vistas y al nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página, y establece la `StackLayout.Margin` propiedad en un valor definido en el nivel `ResourceDictionary`de la aplicación. Tenga en cuenta `Editor` que `Button` los estilos implícitos y se agregaron al `ResourceDictionary`nivel de página, porque solo los `NoteEntryPage`consumen. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NoteEntryPage. Xaml** presionando **Ctrl + S**y cierre el archivo.

5. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo de la guía de inicio rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto de notas.

2. En el **Panel de solución**, en el proyecto **notas** , haga doble clic en **app. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define un [`Thickness`](xref:Xamarin.Forms.Thickness) valor, una serie de [`Color`](xref:Xamarin.Forms.Color) valores [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y estilos implícitos para y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Tenga en cuenta que estos estilos, que están en el nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **app. Xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

3. En el **Panel de solución**, en el proyecto **notas** , haga doble clic en **NotesPage. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un [`ListView`](xref:Xamarin.Forms.ListView) estilo implícito para al nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página y establece la `ListView.Margin` propiedad en un valor definido en el nivel `ResourceDictionary`de la aplicación. Tenga en cuenta `ListView` que el estilo implícito se agregó al nivel `ResourceDictionary`de página, porque solo lo consume `NotesPage`. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage. Xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

4. En el **Panel de solución**, en el proyecto **notas** , haga doble clic en **NoteEntryPage. Xaml** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Este código agrega estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) las [`Button`](xref:Xamarin.Forms.Button) vistas y al nivel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página, y establece la `StackLayout.Margin` propiedad en un valor definido en el nivel `ResourceDictionary`de la aplicación. Tenga en cuenta `Editor` que `Button` los estilos implícitos y se agregaron al `ResourceDictionary`nivel de página, porque solo los `NoteEntryPage`consumen. Para obtener más información sobre el estilo XAML, vea [aplicar](deepdive.md#styling) un análisis detallado en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NoteEntryPage. Xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

5. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo de la guía de inicio rápido anterior.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, aprendió a:

- Aplicar estilo a una aplicación de Xamarin. Forms mediante estilos XAML.

Para obtener más información sobre los aspectos básicos del desarrollo de aplicaciones con Xamarin. Forms, continúe con el tutorial de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](deepdive.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Análisis detallado de Xamarin. Forms](deepdive.md)
