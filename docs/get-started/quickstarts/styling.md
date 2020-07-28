---
title: Estilo de una aplicación de Xamarin.Forms multiplataforma
description: En este artículo se explica cómo diseñar una aplicación Xamarin.Forms multiplataforma con estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05560ae7c3d255140c0782e5f442dd2356d86cad
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937428"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Estilo de una aplicación de Xamarin.Forms multiplataforma

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

En este inicio rápido aprenderá a:

- Aplicar estilo a una aplicación Xamarin.Forms mediante estilos XAML.

En el inicio rápido se describe cómo aplicar estilo a una aplicación Xamarin.Forms multiplataforma con estilos XAML. A continuación se muestra la aplicación final:

[![Página Notas](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox "Página Notas")
[![Página de entrada de nota](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox "Página de entrada de nota")

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](database.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución Notes.

2. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **App.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    En este código se define un valor [`Thickness`](xref:Xamarin.Forms.Thickness), una serie de valores [`Color`](xref:Xamarin.Forms.Color) y estilos implícitos para los objetos [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Tenga en cuenta que estos estilos, que están en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **App.xaml** y cierre el archivo.

3. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un estilo implícito para [`ListView`](xref:Xamarin.Forms.ListView) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `ListView.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `ListView` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NotesPage`. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml** y cierre el archivo.

4. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **NoteEntryPage.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    Este código agrega estilos implícitos para las vistas [`Editor`](xref:Xamarin.Forms.Editor) y [`Button`](xref:Xamarin.Forms.Button) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `StackLayout.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que los estilos implícitos `Editor` y `Button` sen ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo los usa `NoteEntryPage`. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml** y cierre el archivo.

5. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo con respecto al inicio rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto Notes.

2. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **App.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    En este código se define un valor [`Thickness`](xref:Xamarin.Forms.Thickness), una serie de valores [`Color`](xref:Xamarin.Forms.Color) y estilos implícitos para los objetos [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Tenga en cuenta que estos estilos, que están en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **App.xaml**, seleccione **Archivo > Guardar** (o presione **&#8984; + S**) y cierre el archivo.

3. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código agrega un estilo implícito para [`ListView`](xref:Xamarin.Forms.ListView) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `ListView.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `ListView` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NotesPage`. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

4. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **NoteEntryPage.xaml** para abrirlo. Después, reemplace el código existente con el siguiente:

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    Este código agrega estilos implícitos para las vistas [`Editor`](xref:Xamarin.Forms.Editor) y [`Button`](xref:Xamarin.Forms.Button) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `StackLayout.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que los estilos implícitos `Editor` y `Button` sen ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo los usa `NoteEntryPage`. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NoteEntryPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

5. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. En cada página, observe cómo ha cambiado el estilo con respecto al inicio rápido anterior.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Aplicar estilo a una aplicación Xamarin.Forms mediante estilos XAML.

Para obtener más información sobre los aspectos básicos del desarrollo de aplicaciones con Xamarin.Forms, continúe con el análisis detallado de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](deepdive.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
