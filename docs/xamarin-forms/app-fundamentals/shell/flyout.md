---
title: Control flotante de Xamarin.Forms Shell
description: Un control flotante es el menú raíz opcional de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lateral de la pantalla. El control flotante consta de un encabezado opcional, elementos de control flotante, elementos de menú opcionales y un pie de página opcional.
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 82a8297d393e82119156509f20a7306d2ea34540
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757815"
---
# <a name="xamarinforms-shell-flyout"></a>Control flotante de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La experiencia de navegación proporcionada por Xamarin.Forms Shell se basa en controles flotantes y pestañas. Un control flotante es el menú raíz opcional de una aplicación de Shell y es totalmente personalizable. Está disponible mediante un icono o al deslizar el dedo desde el lateral de la pantalla. El control flotante consta de un encabezado opcional, elementos flotantes, elementos de menú opcionales y un pie de página opcional:

![Captura de pantalla de un control flotante anotado de Shell](flyout-images/flyout-annotated.png)

## <a name="flyout-items"></a>Elementos del control flotante

Se pueden agregar uno o varios elementos de control flotante al control flotante, y cada uno de ellos se representa con un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem). Cada objeto `FlyoutItem` debe ser un elemento secundario del objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Cuando no existe un encabezado de control flotante, los elementos de control flotante aparecen en la parte superior del control flotante.

En el ejemplo siguiente, se crea un control flotante que contiene dos elementos de control flotante:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <FlyoutItem Title="Cats"
                Icon="cat.png">
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
    </FlyoutItem>
</Shell>
```

La propiedad [`FlyoutItem.Title`](xref:Xamarin.Forms.BaseShellItem.Title), de tipo `string`, define el título del elemento de control flotante. La propiedad [`FlyoutItem.Icon`](xref:Xamarin.Forms.BaseShellItem.Icon), de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), define el icono del elemento de control flotante:

[![Captura de pantalla de una aplicación de dos páginas de Shell con elementos de control flotante, en iOS y Android](flyout-images/two-page-app-flyout.png)](flyout-images/two-page-app-flyout-large.png#lightbox)

En este ejemplo, solo se puede acceder a cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) mediante elementos de control flotante, no de pestañas. Esto se debe a que, de forma predeterminada, las pestañas solo se mostrarán si el elemento de control flotante contiene más de una pestaña.

> [!IMPORTANT]
> En una aplicación de Shell, las páginas se crean a petición en respuesta a la navegación. Esto se logra mediante el uso de la extensión de marcado [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) para establecer la propiedad [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Shell tiene operadores de conversión implícita que permiten simplificar la jerarquía visual de Shell, sin introducir vistas adicionales en el árbol visual. Esto es posible porque un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases solo puede contener objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o un objeto [`TabBar`](xref:Xamarin.Forms.TabBar), que solo pueden contener objetos [`Tab`](xref:Xamarin.Forms.Tab), que solo pueden contener objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent). Se pueden usar estos operadores de conversión implícita para quitar los objetos `FlyoutItem` y `Tab` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
   <ShellContent Title="Cats"
                 Icon="cat.png"
                 ContentTemplate="{DataTemplate views:CatsPage}" />
   <ShellContent Title="Dogs"
                 Icon="dog.png"
                 ContentTemplate="{DataTemplate views:DogsPage}" />
</Shell>
```

Esta conversión implícita encapsula automáticamente cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en objetos [`Tab`](xref:Xamarin.Forms.Tab), que se encapsulan en objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem).

> [!NOTE]
> Todos los [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) objetos de un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases se agregan automáticamente a la colección `Shell.FlyoutItems`, que define la lista de elementos que se mostrarán en el control flotante.

### <a name="flyout-display-options"></a>Opciones de presentación de control flotante

La propiedad [`FlyoutItem.FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) configura cómo se muestran los elementos de un control flotante y sus elementos secundarios en el control flotante. Esta propiedad se debe establecer en un miembro de enumeración [`FlyoutDisplayOptions`](xref:Xamarin.Forms.FlyoutDisplayOptions):

- `AsSingleItem`, indica que el elemento será visible como un único elemento. Se trata del valor predeterminado de la propiedad [`FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions).
- `AsMultipleItems`, indica que el elemento y sus elementos secundarios serán visibles en el control flotante como un grupo de elementos.

Se puede mostrar un elemento de control flotante para cada objeto [`Tab`](xref:Xamarin.Forms.Tab) dentro de un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) estableciendo la propiedad [`FlyoutItem.FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) en `AsMultipleItems`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

En este ejemplo, se crean elementos de control flotante para el objeto [`Tab`](xref:Xamarin.Forms.Tab), que es elemento secundario del objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), y los objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent), que son elementos secundarios del objeto `FlyoutItem`. Esto ocurre porque cada objeto `ShellContent` que es secundario del objeto `FlyoutItem` se encapsula automáticamente en un objeto `Tab`. Además, se crea un elemento de control flotante para el objeto `ShellContent` final, que se encapsula automáticamente en un objeto `Tab` y, luego, en un objeto `FlyoutItem`.

> [!NOTE]
> Las pestañas se muestran cuando un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) contiene más de un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent).

El resultado son los siguientes elementos de control flotante:

[![Captura de pantalla de un control flotante que contiene objetos FlyoutItem, en iOS y Android](flyout-images/flyout-reduced.png "Control flotante de Shell con objetos FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Control flotante de Shell con objetos FlyoutItem")

### <a name="define-flyoutitem-appearance"></a>Definición de la apariencia de FlyoutItem

Se puede personalizar la apariencia de cada [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) estableciendo la propiedad adjunta [`Shell.ItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid ColumnDefinitions="0.2*,0.8*">
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

En este ejemplo se muestra el título de cada objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) en cursiva:

[![Captura de pantalla de objetos FlyoutItem con plantilla, en iOS y Android](flyout-images/flyoutitem-templated.png)](flyout-images/flyoutitem-templated-large.png#lightbox)

Como [`Shell.ItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) es una propiedad adjunta, se pueden asociar otras plantillas a objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) específicos.

> [!NOTE]
> Shell proporciona las propiedades [`Title`](xref:Xamarin.Forms.BaseShellItem.Title) y [`FlyoutIcon`](xref:Xamarin.Forms.BaseShellItem.FlyoutIcon) para el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del objeto `ItemTemplate`.

Shell incluye también tres clases de estilo, que se aplican automáticamente a objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem). Para obtener más información, consulte [Objetos FlyoutItem y MenuItem de estilo](#style-flyoutitem-and-menuitem-objects).

### <a name="default-template-for-flyoutitems"></a>Plantilla predeterminada para objetos FlyoutItem

Aquí se muestra la plantilla[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) predeterminada usada con cada [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem):

```xaml
<DataTemplate x:Key="FlyoutTemplate">
    <Grid x:Name="FlyoutItemLayout"
          HeightRequest="{x:OnPlatform Android=50}"
          ColumnSpacing="{x:OnPlatform UWP=0}"
          RowSpacing="{x:OnPlatform UWP=0}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor"
                                    Value="{x:OnPlatform Android=#F2F2F2, iOS=#F2F2F2}" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50, UWP=Auto}" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Image x:Name="FlyoutItemImage"
               Source="{Binding FlyoutIcon}"
               VerticalOptions="Center"
               HorizontalOptions="{x:OnPlatform Default=Center, UWP=Start}"
               HeightRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}"
               WidthRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}">
            <Image.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="UWP"
                            Value="12,0,12,0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Image.Margin>
        </Image>
        <Label x:Name="FlyoutItemLabel"
               Grid.Column="1"
               Text="{Binding Title}"
               FontSize="{x:OnPlatform Android=14, iOS=Small}"
               HorizontalOptions="{x:OnPlatform UWP=Start}"
               HorizontalTextAlignment="{x:OnPlatform UWP=Start}"
               FontAttributes="{x:OnPlatform iOS=Bold}"
               VerticalTextAlignment="Center">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

Esta plantilla se puede usar como base para realizar modificaciones en el diseño de control flotante existente y, asimismo, muestra los estados visuales que se implementan para los elementos de control flotante.

Además, los elementos [`Grid`](xref:Xamarin.Forms.Grid), [`Image`](xref:Xamarin.Forms.Image) y [`Label`](xref:Xamarin.Forms.Label) tienen todos valores `x:Name` y, por tanto, pueden ser destinos en Visual State Manager. Para más información, vea [Establecer el estado en varios elementos](~/xamarin-forms/user-interface/visual-state-manager.md#set-state-on-multiple-elements).

> [!NOTE]
> Esta misma plantilla también se puede usar con los objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem).

### <a name="replace-flyout-content"></a>Reemplazo del contenido de un control flotante

Los elementos de un control flotante, que representan su contenido, pueden reemplazarse opcionalmente por contenido propio estableciendo la propiedad enlazable `Shell.FlyoutContent` en un valor `object`:

```xaml
<Shell ...
       x:Name="shell">
    ...
    <Shell.FlyoutContent>
        <CollectionView BindingContext="{x:Reference shell}"
                        IsGrouped="True"
                        ItemsSource="{Binding FlyoutItems}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Label Text="{Binding Title}"
                           TextColor="White"
                           FontSize="Large" />
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </Shell.FlyoutContent>
</Shell>
```

En este ejemplo, el contenido del control flotante se reemplaza por un valor [`CollectionView`](xref:Xamarin.Forms.CollectionView) que muestra el título de cada elemento en la colección `FlyoutItems`.

> [!NOTE]
> La propiedad `FlyoutItems`, en la clase [`Shell`](xref:Xamarin.Forms.Shell), es una colección de solo lectura de elementos de control flotante.

También se puede definir el contenido del control flotante estableciendo la propiedad enlazable `Shell.FlyoutContentTemplate` en un valor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...
       x:Name="shell">
    ...
    <Shell.FlyoutContentTemplate>
        <DataTemplate>
            <CollectionView BindingContext="{x:Reference shell}"
                            IsGrouped="True"
                            ItemsSource="{Binding FlyoutItems}">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Label Text="{Binding Title}"
                               TextColor="White"
                               FontSize="Large" />
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </DataTemplate>
    </Shell.FlyoutContentTemplate>
</Shell>
```

> [!IMPORTANT]
> Opcionalmente, se puede mostrar un encabezado de control flotante encima del contenido del control. También se puede mostrar un pie de página de control flotante debajo del contenido del control. Si el contenido del control flotante es desplazable, el shell intentará respetar el comportamiento de desplazamiento del encabezado de control flotante.

## <a name="menu-items"></a>Elementos de menú

Los elementos del menú se pueden agregar opcionalmente a la ventana flotante, y cada elemento de menú se representa mediante un objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem). La posición de los objetos `MenuItem` en el control flotante depende de su orden de declaración en la jerarquía visual del Shell. Por lo tanto, los objetos `MenuItem` declarados antes de los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) aparecerán antes de los objetos `FlyoutItem` en el control flotante y los objetos `MenuItem` declarados después de los objetos `FlyoutItem` aparecerán después de los objetos `FlyoutItem` en el control flotante.

La clase [`MenuItem`](xref:Xamarin.Forms.MenuItem) tiene un evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) y una propiedad [`Command`](xref:Xamarin.Forms.MenuItem.Command). Por lo tanto, los objetos `MenuItem` permiten escenarios que ejecutan una acción en respuesta al elemento `MenuItem` que se pulsa.

Se pueden agregar objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem) al control flotante, tal como se muestra en el ejemplo siguiente:

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

En este ejemplo se agrega un objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem) al control flotante, debajo de todos los elementos de control flotante:

[![Captura de pantalla de un control flotante que contiene un objeto MenuItem, en iOS y Android](flyout-images/flyout.png)](flyout-images/flyout-large.png#lightbox)

El objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem) ejecuta un elemento `ICommand` llamado `HelpCommand`, que abre la dirección URL especificada por la propiedad `CommandParameter` en el explorador web del sistema.

> [!NOTE]
> El elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada [`MenuItem`](xref:Xamarin.Forms.MenuItem) se hereda del objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases.

### <a name="define-menuitem-appearance"></a>Definición de la apariencia de MenuItem

Se puede personalizar la apariencia de cada [`MenuItem`](xref:Xamarin.Forms.MenuItem) estableciendo la propiedad adjunta [`Shell.MenuItemTemplate`](xref:Xamarin.Forms.Shell.MenuItemTemplate) en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid ColumnDefinitions="0.2*,0.8*">
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

En este ejemplo se adjunta el elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a cada objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem) y se muestra el título del objeto `MenuItem` en cursiva:

[![Captura de pantalla de objetos MenuItem con plantilla, en iOS y Android](flyout-images/menuitem-templated.png)](flyout-images/menuitem-templated-large.png#lightbox)

Como [`Shell.MenuItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) es una propiedad adjunta, se pueden asociar otras plantillas a objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem) específicos.

> [!NOTE]
> Shell proporciona las propiedades [`Text`](xref:Xamarin.Forms.MenuItem.Text) y [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) para el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del objeto [`MenuItemTemplate`](xref:Xamarin.Forms.Shell.MenuItemTemplate). También se puede usar `Title` en lugar de `Text` y `Icon` en lugar de `IconImageSource`, lo que le permitirá volver a usar la misma plantilla para los elementos de menú y los de control flotante.

La plantilla predeterminada para objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) también se puede usar con objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem). Para más información, vea [Plantilla predeterminada para objetos FlyoutItem](#default-template-for-flyoutitems).

## <a name="style-flyoutitem-and-menuitem-objects"></a>Objetos FlyoutItem y MenuItem de estilo

Shell incluye tres clases de estilo, que se aplican automáticamente a los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) y [`MenuItem`](xref:Xamarin.Forms.MenuItem). Los nombres de esas clases de estilo son `FlyoutItemLabelStyle`, `FlyoutItemImageStyle` y `FlyoutItemLayoutStyle`.

En el siguiente código XAML se muestra un ejemplo de definición de estilos para estas clases de estilo:

```xaml
<Style TargetType="Label"
       Class="FlyoutItemLabelStyle">
    <Setter Property="TextColor"
            Value="Black" />
    <Setter Property="HeightRequest"
            Value="100" />
</Style>

<Style TargetType="Image"
       Class="FlyoutItemImageStyle">
    <Setter Property="Aspect"
            Value="Fill" />
</Style>

<Style TargetType="Layout"
       Class="FlyoutItemLayoutStyle"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Teal" />
</Style>
```

Estos estilos se aplicarán automáticamente a objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) y [`MenuItem`](xref:Xamarin.Forms.MenuItem), sin tener que establecer sus propiedades [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) en los nombres de clase de estilo.

Además, se pueden definir y aplicar clases de estilo personalizadas a objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) y [`MenuItem`](xref:Xamarin.Forms.MenuItem). Para obtener más información sobre las clases de estilo, vea [Clases de estilo de Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

## <a name="flyout-header"></a>Encabezado de control flotante

El encabezado de control flotante es el contenido que aparece opcionalmente en la parte superior del control flotante y su apariencia se define mediante un elemento `object` que se puede establecer con la propiedad enlazable [`Shell.FlyoutHeader`](xref:Xamarin.Forms.Shell.FlyoutHeader):

```xaml
<Shell ...>
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
</Shell>
```

El tipo `FlyoutHeader` se muestra en el ejemplo siguiente:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

El resultado es el siguiente encabezado de control flotante:

![Captura de pantalla del encabezado de control flotante](flyout-images/flyout-header.png)

También se puede definir la apariencia del encabezado de control flotante estableciendo la propiedad [`Shell.FlyoutHeaderTemplate`](xref:Xamarin.Forms.Shell.FlyoutHeaderTemplate) en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    <Shell.FlyoutHeaderTemplate>
        <DataTemplate>
            <Grid BackgroundColor="Black"
                  HeightRequest="200">
                <Image Aspect="AspectFill"
                       Source="xamarinstore.jpg"
                       Opacity="0.6" />
                <Label Text="Animals"
                       TextColor="White"
                       FontAttributes="Bold"
                       HorizontalTextAlignment="Center"
                       VerticalTextAlignment="Center" />
            </Grid>            
        </DataTemplate>
    </Shell.FlyoutHeaderTemplate>
</Shell>
```

De forma predeterminada, el encabezado de control flotante se corregirá en el control flotante mientras el contenido que va a continuación se desplazará si hay suficientes elementos. Sin embargo, este comportamiento se puede cambiar si se establece la propiedad enlazable [`Shell.FlyoutHeaderBehavior`](xref:Xamarin.Forms.Shell.FlyoutHeaderBehavior) en uno de los miembros de la enumeración [`FlyoutHeaderBehavior`](xref:Xamarin.Forms.FlyoutHeaderBehavior):

- `Default`: indica que se usará el comportamiento predeterminado para la plataforma. Se trata del valor predeterminado de la propiedad [`FlyoutHeaderBehavior`](xref:Xamarin.Forms.Shell.FlyoutHeaderBehavior).
- `Fixed`: indica que el encabezado de control flotante permanece sin cambios y visible en todo momento.
- `Scroll`: indica que el encabezado de control flotante se desplaza fuera de la vista cuando el usuario desplaza los elementos.
- `CollapseOnScroll`: indica que el encabezado de control flotante se contrae solo en un título, a medida que el usuario desplaza los elementos.

En el ejemplo siguiente se muestra cómo contraer el encabezado de control flotante a medida que el usuario se desplaza:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-footer"></a>Pie de página de control flotante

El pie de página de control flotante es el contenido que aparece opcionalmente en la parte inferior del control flotante y su apariencia se define mediante un elemento `object` que se puede establecer con la propiedad enlazable`Shell.FlyoutFooter`:

```xaml
<Shell ...>
    <Shell.FlyoutFooter>
        <controls:FlyoutFooter />
    </Shell.FlyoutFooter>
</Shell>
```

El tipo `FlyoutFooter` se muestra en el ejemplo siguiente:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="Xaminals.Controls.FlyoutFooter">
    <StackLayout>
        <Label Text="Xaminals"
               TextColor="GhostWhite"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Static sys:DateTime.Now}, StringFormat='{0:MMMM dd, yyyy}'}"
               TextColor="GhostWhite"
               HorizontalOptions="Center" />
    </StackLayout>
</ContentView>
```

El resultado es el siguiente pie de página de control flotante:

![Captura de pantalla del pie de página de control flotante](flyout-images/flyout-footer.png "Pie de página de control flotante")

Como alternativa, la apariencia del pie de página de control flotante se puede definir estableciendo la propiedad `Shell.FlyoutFooterTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    <Shell.FlyoutFooterTemplate>
        <DataTemplate>
            <StackLayout>
                <Label Text="Xaminals"
                       TextColor="GhostWhite"
                       FontAttributes="Bold"
                       HorizontalOptions="Center" />
                <Label Text="{Binding Source={x:Static sys:DateTime.Now}, StringFormat='{0:MMMM dd, yyyy}'}"
                       TextColor="GhostWhite"
                       HorizontalOptions="Center" />
            </StackLayout>
        </DataTemplate>
    </Shell.FlyoutFooterTemplate>
</Shell>
```

El pie de página de control flotante se fija en la parte inferior del control flotante y puede tener cualquier altura. Además, el pie de página nunca oculta ningún elemento de menú.

## <a name="flyout-width-and-height"></a>Anchura y altura de un control flotante

La anchura y altura de un control flotante se pueden personalizar estableciendo las propiedades adjuntas `Shell.FlyoutWidth` y `Shell.FlyoutHeight` en valores `double`:

```xaml
<Shell ...
       FlyoutWidth="400"
       FlyoutHeight="200">
    ...
</Shell>
```

De esta forma, se permiten escenarios como la expansión del control flotante en toda la pantalla o la reducción de su altura para que no tape la barra de pestañas.

## <a name="flyout-icon"></a>Icono de control flotante

De forma predeterminada, las aplicaciones de Shell tienen un icono de tres barras que, cuando se presiona, abre el control flotante. Este icono se puede cambiar si se establece la propiedad enlazable [`Shell.FlyoutIcon`](xref:Xamarin.Forms.Shell.FlyoutIcon), de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), en un icono adecuado:

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-background"></a>Imagen de fondo del control flotante

El color de fondo del control flotante se puede establecer con la propiedad enlazable [`Shell.FlyoutBackgroundColor`](xref:Xamarin.Forms.Shell.FlyoutBackgroundColor):

```xaml
<Shell ...
       FlyoutBackgroundColor="AliceBlue">
    ...
</Shell>
```

> [!NOTE]
> El objeto [`Shell.FlyoutBackgroundColor`](xref:Xamarin.Forms.Shell.FlyoutBackgroundColor) también se puede establecer con una hoja de estilo CSS. Para obtener más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

También se puede especificar el fondo del control flotante estableciendo la propiedad enlazable [`Shell.FlyoutBackground`](xref:Xamarin.Forms.Shell.FlyoutBackground) en [`Brush`](xref:Xamarin.Forms.Brush):

```xaml
<Shell ...
       FlyoutBackground="LightGray">
    ...
</Shell>
```

En este ejemplo, el fondo del control flotante se pinta con un objeto [`SolidColorBrush`](xref:Xamarin.Forms.SolidColorBrush) gris claro.

En el ejemplo siguiente se muestra cómo establecer el fondo del control flotante en un objeto [`LinearGradientBrush`](xref:Xamarin.Forms.LinearGradientBrush):

```xaml
<Shell ...>
    <Shell.FlyoutBackground>
        <LinearGradientBrush StartPoint="0,0"
                             EndPoint="1,1">
            <GradientStop Color="#8A2387"
                          Offset="0.1" />
            <GradientStop Color="#E94057"
                          Offset="0.6" />
            <GradientStop Color="#F27121"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Shell.FlyoutBackground>
    ...
</Shell>
```

Para más información acerca de los pinceles, vea [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md).

## <a name="flyout-background-image"></a>Imagen de fondo del control flotante

El control flotante puede tener una imagen de fondo opcional, que aparece debajo del encabezado del control flotante y detrás de los elementos del control flotante, los elementos del menú y el pie de página del control flotante. La imagen de fondo se puede especificar si se establece la propiedad enlazable [`FlyoutBackgroundImage`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImage), de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), en un archivo, un recurso insertado, un URI o un flujo.

La relación de aspecto de la imagen de fondo se puede configurar si se establece la propiedad enlazable [`FlyoutBackgroundImageAspect`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImageAspect), de tipo [`Aspect`](xref:Xamarin.Forms.Aspect), en uno de los miembros de la enumeración `Aspect`:

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): recorta la imagen para que rellene el área de visualización y conserve la relación de aspecto.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): aplica el formato letterbox a la imagen si es necesario para que la imagen quepa en el área de visualización, con un espacio en blanco agregado a la parte superior o inferior o a los laterales, en función de si la imagen es ancha o alta. Se trata del valor predeterminado de la propiedad [`FlyoutBackgroundImageAspect`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImageAspect).
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): ajusta la imagen para rellenar completa y exactamente el área de visualización. Esto puede producir que la imagen se distorsione.

En el ejemplo siguiente se muestra cómo configurar estas propiedades:

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

Esto se traduce en que aparece una imagen de fondo en el control flotante, debajo de su encabezado:

![Captura de pantalla de la imagen de fondo de un control flotante](flyout-images/flyout-backgroundimage.png)

## <a name="flyout-backdrop"></a>Fondo del control flotante

El fondo del control flotante, que es la apariencia de la superposición del control flotante, se puede especificar estableciendo la propiedad adjunta [`Shell.FlyoutBackdrop`](xref:Xamarin.Forms.Shell.FlyoutBackdrop) en un objeto [`Brush`](xref:Xamarin.Forms.Brush):

```xaml
<Shell ...
       FlyoutBackdrop="Silver">
    ...
</Shell>
```

En este ejemplo, el fondo del control flotante se pinta con un objeto [`SolidColorBrush`](xref:Xamarin.Forms.SolidColorBrush) color plata.

> [!IMPORTANT]
> La propiedad adjunta [`FlyoutBackdrop`](xref:Xamarin.Forms.Shell.FlyoutBackdrop) se puede establecer en cualquier elemento de Shell, pero solo se aplicará cuando se establezca en objetos `Shell`, `FlyoutItem` o `TabBar`.

En el ejemplo siguiente se muestra cómo establecer el fondo del control flotante en un objeto [`LinearGradientBrush`](xref:Xamarin.Forms.LinearGradientBrush):

```xaml
<Shell ...>
    <Shell.FlyoutBackdrop>
        <LinearGradientBrush StartPoint="0,0"
                             EndPoint="1,1">
            <GradientStop Color="#8A2387"
                          Offset="0.1" />
            <GradientStop Color="#E94057"
                          Offset="0.6" />
            <GradientStop Color="#F27121"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Shell.FlyoutBackdrop>
    ...
</Shell>
```

Para más información acerca de los pinceles, vea [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md).

## <a name="flyout-behavior"></a>Comportamiento del control flotante

Al control flotante se tiene acceso mediante el icono de tres barras o al deslizar el dedo desde el lado de la pantalla. Sin embargo, este comportamiento se puede cambiar si se establece la propiedad adjunta [`Shell.FlyoutBehavior`](xref:Xamarin.Forms.Shell.FlyoutBehavior) en uno de los miembros de la enumeración [`FlyoutBehavior`](xref:Xamarin.Forms.FlyoutBehavior):

- `Disabled`: indica que el usuario no puede abrir el control flotante.
- `Flyout`: indica que el usuario puede abrir y cerrar el control flotante. Se trata del valor predeterminado de la propiedad [`FlyoutBehavior`](xref:Xamarin.Forms.Shell.FlyoutBehavior).
- `Locked`: indica que el usuario no puede cerrar el control flotante, y que este no solapa el contenido.

En el siguiente ejemplo se muestra cómo deshabilitar el control flotante.

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> La propiedad adjunta `FlyoutBehavior` se puede establecer en los objetos `Shell`, `FlyoutItem`, `ShellContent` y en objetos de página, para invalidar el comportamiento predeterminado del control flotante.

## <a name="flyout-vertical-scroll"></a>Desplazamiento vertical de control flotante

De forma predeterminada, un control flotante se puede desplazar verticalmente cuando los elementos del control flotante no caben en este. Este comportamiento se puede cambiar si se establece la propiedad enlazable [`Shell.FlyoutVerticalScrollMode`](xref:Xamarin.Forms.Shell.FlyoutVerticalScrollMode) en uno de los miembros de la enumeración [`ScrollMode`](xref:Xamarin.Forms.ScrollMode):

- `Disabled`: indica que se deshabilitará el desplazamiento vertical.
- `Enabled` indica que se habilitará el desplazamiento vertical.
- `Auto`: indica que se habilitará el desplazamiento vertical si los elementos de control flotante no caben en el control flotante. Se trata del valor predeterminado de la propiedad [`FlyoutVerticalScrollMode`](xref:Xamarin.Forms.Shell.FlyoutVerticalScrollMode).

En el siguiente ejemplo se muestra cómo deshabilitar el desplazamiento vertical:

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled">
    ...
</Shell>
```

## <a name="flyoutitem-tab-order"></a>Orden de tabulación de FlyoutItem

De forma predeterminada, el orden de tabulación de los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) es el mismo orden en que se enumeran en XAML o se agregan mediante programación a una colección secundaria. Este es el orden en que se navegará por los objetos `FlyoutItem` con un teclado y, con frecuencia, este orden predeterminado es el mejor orden posible.

Se puede cambiar el orden de tabulación predeterminado cambiando la propiedad [`FlyoutItem.TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex), que indica el orden en que los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) reciben el foco cuando el usuario navega por los elementos presionando la tecla de tabulación. El valor predeterminado de la propiedad es 0, y se puede establecer en cualquier valor `int`.

Las reglas siguientes aplican cuando se usa el orden de tabulación predeterminado o se establece la propiedad [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex):

- Los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) con un objeto [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) igual a 0 se agregan al orden de tabulación según su orden de declaración en XAML o sus colecciones secundarias.
- Los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) con un valor de [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) mayor que 0 se agregan al orden de tabulación en función de su valor `TabIndex`.
- Los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) con un valor de [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) menor que 0 se agregan al orden de tabulación y aparecen antes que cualquier valor 0.
- Los conflictos relacionados con un objeto [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) se resuelven por orden de declaración.

Tras definirse un orden de tabulación, al presionar la tecla TAB, se desplazará cíclicamente el foco a través de los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) en orden ascendente de [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex), para volver al principio una vez alcanzado el último objeto.

Además de establecer el orden de tabulación de los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), tal vez haya que excluir algunos objetos de dicho orden. Para ello, se puede usar la propiedad [`FlyoutItem.IsTabStop`](xref:Xamarin.Forms.BaseShellItem.IsTabStop), que indica si un objeto `FlyoutItem` se incluye en la navegación entre pestañas. Su valor predeterminado es `true`, y cuando su valor es `false`, la infraestructura de navegación entre pestañas omite el objeto `FlyoutItem`, independientemente de si se ha definido un objeto [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex).

## <a name="flyoutitem-selection"></a>Selección de FlyoutItem

Cuando se ejecuta por primera vez una aplicación de Shell que usa un control flotante, la propiedad [`Shell.CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) se establecerá en el primer objeto `FlyoutItem` del objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Sin embargo, la propiedad se puede establecer en otro objeto `FlyoutItem`, como se muestra en el ejemplo siguiente:

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

En este ejemplo se establece la propiedad [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) en el objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) denominado `aboutItem`, lo que hace que se seleccione y se muestre. En este ejemplo, se usa una conversión implícita para encapsular el objeto `ShellContent` en un objeto [`Tab`](xref:Xamarin.Forms.Tab), que se encapsula en un objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem).

El código C# equivalente, dado un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) denominado `aboutItem`, es el siguiente:

```csharp
CurrentItem = aboutItem;
```

En este ejemplo, la propiedad [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) está establecida en la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Como alternativa, la propiedad `CurrentItem` se puede establecer en cualquier clase a través de la propiedad estática `Shell.Current`:

```csharp
Shell.Current.CurrentItem = aboutItem;
```

> [!NOTE]
> Una aplicación puede entrar en un estado en el que la selección de un elemento de control flotante no es una operación válida. En tales casos, se puede deshabilitar el objeto [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) estableciendo su propiedad `IsEnabled` en `false`. Esto impedirá que los usuarios puedan seleccionar el elemento de control flotante.

## <a name="flyoutitem-visibility"></a>Visibilidad de FlyoutItem

De forma predeterminada, los elementos de control flotante se ven en dicho control. Sin embargo, un elemento se puede ocultar en el control flotante con la propiedad `FlyoutItemIsVisible` y quitarse del control flotante con la propiedad [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible):

- `FlyoutItemIsVisible`, de tipo `bool`, indica si el elemento está oculto en el control flotante, pero todavía se puede acceder al método de navegación [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*). El valor predeterminado de esta propiedad es `true`.
- [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible), de tipo `bool`, indica si el elemento se debe quitar del árbol visual y, por tanto, no aparece en el control flotante. Su valor predeterminado es `true`.

En el ejemplo siguiente, se muestra cómo ocultar un elemento en el control flotante:

```xaml
<Shell ...>
    <FlyoutItem ...
                FlyoutItemIsVisible="False">
        ...
    </FlyoutItem>
</Shell>
```

> [!NOTE]
> También hay una propiedad adjunta `Shell.FlyoutItemIsVisible`, que se puede establecer en los objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem), [`MenuItem`](xref:Xamarin.Forms.MenuItem), [`Tab`](xref:Xamarin.Forms.Tab) y [`ShellContent`](xref:Xamarin.Forms.ShellContent).

## <a name="open-and-close-the-flyout-programmatically"></a>Apertura y cierre del control flotante mediante programación

Además, el control flotante se puede abrir y cerrar mediante programación si se establece la propiedad enlazable [`Shell.FlyoutIsPresented`](xref:Xamarin.Forms.Shell.FlyoutIsPresented) en un valor `boolean` que indica si el control flotante está abierto actualmente:

```xaml
<Shell ...
       FlyoutIsPresented="{Binding IsFlyoutOpen}">
</Shell>
```

También se puede realizar por medio de código:

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Clases de estilo de Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md)
- [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
