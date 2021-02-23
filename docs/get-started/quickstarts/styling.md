---
title: Estilo de una aplicación de Xamarin.Forms multiplataforma
description: En este artículo se explica cómo aplicar un estilo a una aplicación de Xamarin.Forms Shell multiplataforma con estilos XAML y cómo usar la Recarga activa de XAML para ver estos cambios.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 1C6ED8AF-41B4-4D6C-9BD8-C72D3F05E541
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0424f3c9cdcde98cef4c414ada33046cd8ce7ee6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818139"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Estilo de una aplicación de Xamarin.Forms multiplataforma

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

En este inicio rápido aprenderá a:

- Aplicar estilo a una aplicación de Xamarin.Forms Shell mediante estilos XAML.
- Usar la Recarga activa de XAML para ver cambios en la UI sin tener que recompilar su aplicación.

En el inicio rápido se describe cómo aplicar estilo a una aplicación Xamarin.Forms multiplataforma con estilos XAML. Además, la guía de inicio rápido usa la Recarga activa de XAML para actualizar la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. Para más información sobre la Recarga activa de XAML, consulte [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md).

A continuación se muestra la aplicación final:

[![Página Notas](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox)
[![Página de entrada de nota](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox)
[![Página de información](styling-images/screenshots3-sml.png)](styling-images/screenshots3.png#lightbox)

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](database.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución Notes.

2. Compile y ejecute el proyecto en la plataforma que prefiera. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    Deje que la aplicación se ejecute y vuelva a Visual Studio.

3. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **App.xaml**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->         
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    En este código se define un valor [`Thickness`](xref:Xamarin.Forms.Thickness), una serie de valores [`Color`](xref:Xamarin.Forms.Color) y estilos implícitos para los tipos [`ContentPage`](xref:Xamarin.Forms.ContentPage) y [`Button`](xref:Xamarin.Forms.Button). Tenga en cuenta que estos estilos, que están en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **App.xaml**. La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de fondo de cada página.

4. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **AppShell.xaml**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    Este código agrega dos estilos al diccionario de recursos de `Shell`, los que definen una serie de valores [`Color`](xref:Xamarin.Forms.Color) que usa la aplicación.

    Presione **CTRL+S** para guardar los cambios en **AppShell.xaml**. La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de fondo del cromo de Shell.

5. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **NotesPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Este código agrega un estilo implícito para [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define la apariencia de cada elemento seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página y establece la propiedad `CollectionView.Margin` y `Label.TextColor` en los valores definidos en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `StackLayout` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NotesPage`.

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml**. La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de los elementos seleccionados en [`CollectionView`](xref:Xamarin.Forms.CollectionView).

6. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **NoteEntryPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>         
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid ColumnDefinitions="*,*">
                <!-- Layout children in two columns -->
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código agrega un estilo implícito para [`Editor`](xref:Xamarin.Forms.Editor) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `StackLayout.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `Editor` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NoteEntryPage`.

7. En la aplicación en ejecución, navegue hasta `NoteEntryPage`.

8. En Visual Studio, para guardar los cambios en **NoteEntryPage.xaml**, elija **Archivo > Guardar** (o bien presione **&#8984; + S**).

    La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación, sin tener que recompilarla. En concreto, cambiará el color de fondo de [`Editor`](xref:Xamarin.Forms.Editor) en la aplicación en ejecución, así como la apariencia de los objetos [`Button`](xref:Xamarin.Forms.Button).

9. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **AboutPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->       
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Este código establece las propiedades `Image.BackgroundColor` y `StackLayout.Margin` en los valores definidos en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de la aplicación.

10. En la aplicación en ejecución, navegue hasta `AboutPage`.

11. En Visual Studio, presione **CTRL+S** para guardar los cambios en **AboutPage.xaml**.

    La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación, sin tener que recompilarla. En concreto, cambiará el color de fondo de [`Image`](xref:Xamarin.Forms.Editor) en la aplicación en ejecución.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto Notes.

2. Compile y ejecute el proyecto en la plataforma que prefiera. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    Deje que la aplicación se ejecute y vuelva a Visual Studio para Mac.

3. En el **Panel de solución**, en el proyecto **Notes**, abra **App.xaml**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->                 
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>  
        </Application.Resources>
    </Application>
    ```

    En este código se define un valor [`Thickness`](xref:Xamarin.Forms.Thickness), una serie de valores [`Color`](xref:Xamarin.Forms.Color) y estilos implícitos para los tipos [`ContentPage`](xref:Xamarin.Forms.ContentPage) y [`Button`](xref:Xamarin.Forms.Button). Tenga en cuenta que estos estilos, que están en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación, se pueden consumir en toda la aplicación. Para obtener más información sobre los estilos XAML, vea [Aplicación de estilos](deepdive.md#styling) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **App.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**). La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de fondo de cada página.

4. En el **Panel de solución**, en el proyecto **Notes**, abra **AppShell.xaml**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    Este código agrega dos estilos al diccionario de recursos de `Shell`, los que definen una serie de valores [`Color`](xref:Xamarin.Forms.Color) que usa la aplicación.

    Para guardar los cambios en **AppShell.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**). La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de fondo del cromo de Shell.

5. En el **Panel de solución**, en el proyecto **Notes**, abra **NotesPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Este código agrega un estilo implícito para [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define la apariencia de cada elemento seleccionado en [`CollectionView`](xref:Xamarin.Forms.CollectionView) en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página y establece la propiedad `CollectionView.Margin` y `Label.TextColor` en los valores definidos en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `StackLayout` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NotesPage`.

    Para guardar los cambios en **NotesPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**). La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación en ejecución, sin tener que recompilar la aplicación. En concreto, cambiará el color de los elementos seleccionados en [`CollectionView`](xref:Xamarin.Forms.CollectionView).

6. En el **Panel de solución**, en el proyecto **Notes**, abra **NoteEntryPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>       
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código agrega estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página, y establece la propiedad `StackLayout.Margin` en un valor definido en el objeto `ResourceDictionary` de nivel de la aplicación. Tenga en cuenta que el estilo implícito `Editor` se ha agregado al objeto `ResourceDictionary` de nivel de página, porque solo lo usa `NoteEntryPage`.

7. En la aplicación en ejecución, navegue hasta `NoteEntryPage`.

8. En Visual Studio para Mac, para guardar los cambios en **NoteEntryPage.xaml**, elija **Archivo > Guardar** (o bien presione **&#8984; + S**).

    La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación, sin tener que recompilarla. En concreto, cambiará el color de fondo de [`Editor`](xref:Xamarin.Forms.Editor) en la aplicación en ejecución, así como la apariencia de los objetos [`Button`](xref:Xamarin.Forms.Button).

9. En el **Panel de solución**, en el proyecto **Notes**, abra **AboutPage.xaml** en la carpeta **Vistas**. Después, reemplace el código existente con el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Este código establece las propiedades `Image.BackgroundColor` y `StackLayout.Margin` en los valores definidos en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de la aplicación.

10. En la aplicación en ejecución, navegue hasta `AboutPage`.

11. En Visual Studio para Mac, para guardar los cambios en **AboutPage.xaml**, elija **Archivo > Guardar** (o bien presione **&#8984; + S**).

    La Recarga activa de XAML actualizará la interfaz de usuario de la aplicación, sin tener que recompilarla. En concreto, cambiará el color de fondo de [`Image`](xref:Xamarin.Forms.Editor) en la aplicación en ejecución.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Aplicar estilo a una aplicación de Xamarin.Forms Shell mediante estilos XAML.
- Usar la Recarga activa de XAML para ver cambios en la UI sin tener que recompilar su aplicación.

Para más información sobre los aspectos fundamentales del desarrollo de aplicaciones con Xamarin.Forms Shell, continúe con el análisis detallado de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](deepdive.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
