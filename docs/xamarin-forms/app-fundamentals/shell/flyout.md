---
title: Control flotante de Xamarin.Forms Shell
description: El control flotante es el menú raíz de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lado de la pantalla. El control flotante consta de un encabezado opcional, elementos de control flotante y elementos de menú opcionales.
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bad3a19de5a8feae2ca2fd02c1a454ac379e9f42
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115163"
---
# <a name="no-locxamarinforms-shell-flyout"></a>Control flotante de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

El control flotante es el menú raíz de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lado de la pantalla. El control flotante consta de un encabezado opcional, elementos flotantes, elementos de menú opcionales y un pie de página opcional:

![Captura de pantalla de un control flotante anotado de Shell](flyout-images/flyout-annotated.png "Control flotante anotado")

Si es necesario, el color de fondo del control flotante se puede establecer [`Color`](xref:Xamarin.Forms.Color) mediante la propiedad enlazable `Shell.FlyoutBackgroundColor`. Esta propiedad también se puede establecer con una hoja de estilo CSS. Para obtener más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="flyout-icon"></a>Icono de control flotante

De forma predeterminada, las aplicaciones de Shell tienen un icono de tres barras que, cuando se presiona, abre el control flotante. Este icono se puede cambiar si se establece la propiedad enlazable `Shell.FlyoutIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), en un icono adecuado:

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>Comportamiento del control flotante

Al control flotante se tiene acceso mediante el icono de tres barras o al deslizar el dedo desde el lado de la pantalla. Sin embargo, este comportamiento se puede cambiar si se establece la propiedad adjunta `Shell.FlyoutBehavior` en uno de los miembros de la enumeración `FlyoutBehavior`:

- `Disabled`: indica que el usuario no puede abrir el control flotante.
- `Flyout`: indica que el usuario puede abrir y cerrar el control flotante. Este es el valor predeterminado de la propiedad `FlyoutBehavior`.
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

Además, el control flotante se puede abrir y cerrar mediante programación si se establece la propiedad enlazable `Shell.FlyoutIsPresented` en un valor `boolean` que indica si el control flotante es visible actualmente:

```csharp
Shell.Current.FlyoutIsPresented = false;
```

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

## <a name="flyout-header"></a>Encabezado de control flotante

El encabezado de control flotante es el contenido que aparece opcionalmente en la parte superior del control flotante y su apariencia se define mediante un elemento `object` que se puede establecer mediante el valor de la propiedad `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
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

![Captura de pantalla del encabezado de control flotante](flyout-images/flyout-header.png "Encabezado de control flotante")

Como alternativa, la apariencia del encabezado de control flotante se puede definir mediante el establecimiento de la propiedad `Shell.FlyoutHeaderTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
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
```

De forma predeterminada, el encabezado de control flotante se corregirá en el control flotante mientras el contenido que va a continuación se desplazará si hay suficientes elementos. Sin embargo, este comportamiento se puede cambiar si se establece la propiedad enlazable `Shell.FlyoutHeaderBehavior` en uno de los miembros de la enumeración `FlyoutHeaderBehavior`:

- `Default`: indica que se usará el comportamiento predeterminado para la plataforma. Este es el valor predeterminado de la propiedad `FlyoutHeaderBehavior`.
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

El pie de página de control flotante es el contenido que aparece opcionalmente en la parte inferior del control flotante y su apariencia se define mediante un elemento `object` que se puede establecer mediante el valor de la propiedad `Shell.FlyoutFooter`:

```xaml
<Shell.FlyoutFooter>
    <controls:FlyoutFooter />
</Shell.FlyoutFooter>
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
```

El pie de página de control flotante se fija en la parte inferior del control flotante y puede tener cualquier altura. Además, el pie de página nunca oculta ningún elemento de menú.

## <a name="flyout-background-image"></a>Imagen de fondo del control flotante

El control flotante puede tener una imagen de fondo opcional, que aparece debajo del encabezado del control flotante y detrás de los elementos del control flotante y los elementos del menú. La imagen de fondo se puede especificar si se establece la propiedad enlazable `FlyoutBackgroundImage`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), en un archivo, un recurso incrustado, un URI o un flujo.

La relación de aspecto de la imagen de fondo se puede configurar si se establece la propiedad enlazable `FlyoutBackgroundImageAspect`, de tipo [`Aspect`](xref:Xamarin.Forms.Aspect), en uno de los miembros de enumeración de `Aspect`:

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): recorta la imagen para que rellene el área de visualización y conserve la relación de aspecto.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): aplica el formato letterbox a la imagen si es necesario para que la imagen quepa en el área de visualización, con un espacio en blanco agregado a la parte superior o inferior o a los laterales, en función de si la imagen es ancha o alta.
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): ajusta la imagen para rellenar completa y exactamente el área de visualización. Esto puede producir que la imagen se distorsione.

De forma predeterminada, la propiedad `FlyoutBackgroundImageAspect` se establecerá en `AspectFit`.

En el ejemplo siguiente se muestra cómo configurar estas propiedades:

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

Esto da como resultado una imagen de fondo en el control flotante:

![Captura de pantalla de la imagen de fondo de un control flotante](flyout-images/flyout-backgroundimage.png "Imagen de fondo del control flotante")

## <a name="flyout-items"></a>Elementos del control flotante

Cuando el modelo de navegación de una aplicación incluye un control flotante, el objeto `Shell` con subclases debe contener uno o varios objetos `FlyoutItem`, donde cada objeto `FlyoutItem` representa un elemento del control flotante. Cada objeto `FlyoutItem` debe ser un elemento secundario del objeto `Shell`.

En el ejemplo siguiente se crea un control flotante que contiene un encabezado y dos elementos:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

En este ejemplo, solo se puede acceder a cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) mediante elementos de control flotante:

[![Captura de pantalla de una aplicación de dos páginas de Shell con elementos de control flotante, en iOS y Android](flyout-images/two-page-app-flyout.png "Aplicación de dos páginas de Shell con elementos de control flotante")](flyout-images/two-page-app-flyout-large.png#lightbox "Aplicación de dos páginas de Shell con elementos de control flotante")

> [!NOTE]
> Cuando no existe un encabezado de control flotante, aparecen elementos de control flotante en la parte superior del control flotante. En caso contrario, aparecen debajo del encabezado de control flotante.

Shell tiene operadores de conversión implícita que permiten simplificar la jerarquía visual de Shell, sin introducir vistas adicionales en el árbol visual. Esto es posible porque un objeto `Shell` con subclases solo puede contener objetos `FlyoutItem` o un objeto `TabBar`, que solo pueden contener objetos `Tab`, que solo pueden contener objetos `ShellContent`. Estos operadores de conversión implícita pueden usarse para quitar los objetos `FlyoutItem`, `Tab` y `ShellContent` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

Esta conversión implícita encapsula automáticamente cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) en objetos `ShellContent`, que se encapsulan en objetos `Tab`, que se encapsulan en objetos `FlyoutItem`.

> [!IMPORTANT]
> En una aplicación de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que es un elemento secundario de un objeto `ShellContent` se crea durante el inicio de la aplicación. Agregar objetos `ShellContent` adicionales mediante esta estrategia dará lugar a la creación de páginas adicionales durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también es capaz de crear páginas a petición, en respuesta a la navegación. Para más información, consulte [Carga eficiente de páginas](tabs.md#efficient-page-loading) en la guía [Pestañas de Xamarin.Forms Shell](tabs.md).

### <a name="flyoutitem-class"></a>Clase FlyoutItem

La clase `FlyoutItem` incluye las siguientes propiedades que controlan la apariencia y comportamiento del elemento del control flotante:

- `FlyoutDisplayOptions`, de tipo `FlyoutDisplayOptions`, define cómo se muestran el elemento y sus elementos secundarios en el control flotante. El valor predeterminado es `AsSingleItem`.
- `CurrentItem`, de tipo `Tab`, el elemento seleccionado.
- `Items`, de tipo `IList<Tab>`, define todas las pestañas dentro de `FlyoutItem`.
- `FlyoutIcon`, de tipo `ImageSource`, el icono que se usará para el elemento. Si esta propiedad no está establecida, se volverá a usar el valor de la propiedad `Icon`.
- `Icon`, de tipo `ImageSource`, define el icono que se mostrará en las partes del cromo que no son el control flotante.
- `IsChecked`, de tipo `boolean`, define si el elemento está actualmente resaltado en la ventana flotante.
- `IsEnabled`, de tipo `boolean`, define si el elemento es seleccionable en el cromo.
- `IsTabStop`, de tipo `bool`, indica si se incluye un objeto `FlyoutItem` en la navegación entre pestañas. Su valor predeterminado es `true`, y cuando su valor es `false`, la infraestructura de navegación entre pestañas omite el objeto `FlyoutItem`, independientemente de si se ha definido un objeto `TabIndex`.
- `IsVisible`, de tipo `bool`, indica si `FlyoutItem` está oculto en el menú de control flotante. Su valor predeterminado es `true`.
- `TabIndex`, de tipo `int`, indica el orden en que los objetos `FlyoutItem` reciben el foco cuando el usuario navega por los elementos presionando la tecla de tabulación. El valor predeterminado de la propiedad es 0.
- `Title`, de tipo `string`, el título que se mostrará en la interfaz de usuario.
- `Route`, de tipo `string`, la cadena usada para abordar el elemento.

Todas estas propiedades, excepto la propiedad `Route`, están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

> [!NOTE]
> Todos los objetos `FlyoutItem` del objeto Shell en subclase se agregan a la colección `Shell.Items`, que define la lista de elementos que se mostrarán en el control flotante.

Además, la clase `FlyoutItem` expone los siguientes métodos reemplazables:

- `OnTabIndexPropertyChanged`, que se llama cada vez que cambia la propiedad `TabIndex`.
- `OnTabStopPropertyChanged`, que se llama cada vez que cambia la propiedad `IsTabStop`.
- `TabIndexDefaultValueCreator`, devuelve un elemento `int`, y se llama para establecer el valor predeterminado de la propiedad `TabIndex`.
- `TabStopDefaultValueCreator`, devuelve un elemento `bool`, y se llama para establecer el valor predeterminado de la propiedad `TabStop`.

## <a name="flyout-backdrop"></a>Fondo del control flotante

El fondo del control flotante, que es la apariencia de la superposición del control flotante, se puede especificar estableciendo la propiedad adjunta `Shell.FlyoutBackdrop` en `Brush`:

```xaml
<Shell ...
       FlyoutBackdrop="Silver">
    ...
</Shell>
```

En este ejemplo, el fondo del control flotante se pinta con `SolidColorBrush` plata.

> [!IMPORTANT]
> La propiedad adjunta `FlyoutBackdrop` se puede establecer en cualquier elemento del shell, pero solo se aplicará cuando se establezca en objetos `Shell`, `FlyoutItem` o `TabBar`.

En el ejemplo siguiente se muestra cómo establecer el fondo del control flotante en `FlyoutItem` en `LinearGradientBrush`:

```xaml
<Shell ...>
    <FlyoutItem ...>
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
    </FlyoutItem>
    ...
</Shell>
```

Para más información acerca de los pinceles, vea [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md).

## <a name="flyout-vertical-scroll"></a>Desplazamiento vertical de control flotante

De forma predeterminada, un control flotante se puede desplazar verticalmente cuando los elementos del control flotante no caben en este. Este comportamiento se puede cambiar si se establece la propiedad enlazable `Shell.FlyoutVerticalScrollMode` en uno de los miembros de la enumeración `ScrollMode`:

- `Disabled`: indica que se deshabilitará el desplazamiento vertical.
- `Enabled` indica que se habilitará el desplazamiento vertical.
- `Auto`: indica que se habilitará el desplazamiento vertical si los elementos de control flotante no caben en el control flotante. Este es el valor predeterminado de la propiedad `Shell.FlyoutVerticalScrollMode`.

En el siguiente ejemplo se muestra cómo deshabilitar el desplazamiento vertical:

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>Opciones de presentación de control flotante

La enumeración `FlyoutDisplayOptions` define los miembros siguientes:

- `AsSingleItem`, indica que el elemento será visible como un único elemento.
- `AsMultipleItems`, indica que el elemento y sus elementos secundarios serán visibles en el control flotante como un grupo de elementos.

Al establecer la propiedad `FlyoutItem.FlyoutDisplayOptions` en `AsMultipleItems`, se creará un elemento de control flotante para cada objeto `Tab` dentro de un objeto `FlyoutItem`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
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

En este ejemplo, se crean elementos de control flotante para el objeto `Tab`, que es secundario del objeto `FlyoutItem`, y el objeto `ShellContent`, que son elementos secundarios del objeto `FlyoutItem`. Esto ocurre porque cada objeto `ShellContent` que es secundario del objeto `FlyoutItem` se encapsula automáticamente en un objeto `Tab`. Además, se crea un elemento de control flotante para el objeto `ShellContent` final, que se encapsula en un objeto `Tab` y, luego, en un objeto `FlyoutItem`.

El resultado son los siguientes elementos de control flotante:

[![Captura de pantalla de un control flotante que contiene objetos FlyoutItem, en iOS y Android](flyout-images/flyout-reduced.png "Control flotante de Shell con objetos FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Control flotante de Shell con objetos FlyoutItem")

## <a name="define-flyoutitem-appearance"></a>Definición de la apariencia de FlyoutItem

Se puede personalizar la apariencia de cada `FlyoutItem` mediante el establecimiento de la propiedad adjunta `Shell.ItemTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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

En este ejemplo se muestra el título de cada objeto `FlyoutItem` en cursiva:

[![Captura de pantalla de objetos FlyoutItem con plantilla, en iOS y Android](flyout-images/flyoutitem-templated.png "Objetos FlyoutItem con plantilla de Shell")](flyout-images/flyoutitem-templated-large.png#lightbox "Objetos FlyoutItem con plantilla de Shell")

Como `Shell.ItemTemplate` es una propiedad adjunta, se pueden asociar otras plantillas a objetos `FlyoutItem` específicos.

> [!NOTE]
> Shell proporciona las propiedades `Title` y `FlyoutIcon` para el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `ItemTemplate`.

Shell incluye también tres clases de estilo, que se aplican automáticamente a objetos `FlyoutItem`. Para más información, vea [Clases de estilo FlyoutItem y MenuItem](#flyoutitem-and-menuitem-style-classes).

### <a name="default-template-for-flyoutitems"></a>Plantilla predeterminada para objetos FlyoutItem

Aquí se muestra la plantilla[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) predeterminada usada con cada `FlyoutItem`:

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
> Esta misma plantilla también se puede usar con los objetos `MenuItem`.

## <a name="set-flyoutitem-visibility"></a>Establecimiento de la visibilidad de FlyoutItem

De forma predeterminada, los elementos de control flotante se ven en dicho control. Sin embargo, se puede ocultar un elemento del control flotante estableciendo la propiedad adjunta `Shell.FlyoutItemIsVisible`, que tiene un valor predeterminado de `true`, en `false`:

```xaml
<Shell ...>
    <FlyoutItem ...
                Shell.FlyoutItemIsVisible="False">
        ...
    </FlyoutItem>
</Shell>
```

> [!NOTE]
> La propiedad adjunta `Shell.FlyoutItemIsVisible` se puede establecer en los objetos `FlyoutItem`, `MenuItem`, `Tab` y `ShellContent`.

## <a name="set-flyoutitem-tab-order"></a>Establecimiento del orden de pestañas de FlyoutItem

De forma predeterminada, el orden de tabulación de los objetos `FlyoutItem` es el mismo orden en que se enumeran en XAML o se agregan mediante programación a una colección secundaria. Este es el orden en que se navegará por los objetos `FlyoutItem` con un teclado y, con frecuencia, este orden predeterminado es el mejor orden posible.

Se puede cambiar el orden de tabulación predeterminado mediante el establecimiento de la propiedad `FlyoutItem.TabIndex`, que indica el orden en que los objetos `FlyoutItem` reciben el foco cuando el usuario navega por los elementos presionando la tecla de tabulación. El valor predeterminado de la propiedad es 0, y se puede establecer en cualquier valor `int`.

Las reglas siguientes aplican cuando se usa el orden de tabulación predeterminado, o cuando se establece la propiedad `TabIndex`:

- Los objetos `FlyoutItem` con un objeto `TabIndex` igual a 0 se agregan al orden de tabulación según su orden de declaración en XAML o colecciones secundarias.
- Los objetos `FlyoutItem` con un valor de `TabIndex` mayor que 0 se agregan al orden de tabulación en función de su valor `TabIndex`.
- Los objetos `FlyoutItem` con un valor de `TabIndex` menor que 0 se agregan al orden de tabulación y aparecen antes que cualquier valor 0.
- Los conflictos relacionados con `TabIndex` se resuelven por orden de declaración.

Tras definirse un orden de tabulación, al presionar la tecla TAB se recorrerá cíclicamente el foco a través de objetos `FlyoutItem` en orden de `TabIndex` ascendente, con una encapsulación alrededor del principio una vez alcanzado el control final.

Además de establecer el orden de tabulación de los objetos `FlyoutItem`, puede ser necesario excluir algunos objetos de dicho orden. Para ello, se puede usar la propiedad `FlyoutItem.IsTabStop`, que indica si un objeto `FlyoutItem` se incluye en la navegación entre pestañas. Su valor predeterminado es `true`, y cuando su valor es `false`, la infraestructura de navegación entre pestañas omite el objeto `FlyoutItem`, independientemente de si se ha definido un objeto `TabIndex`.

## <a name="set-the-current-flyoutitem"></a>Establecimiento del objeto FlyoutItem actual

La clase `Shell` tiene una propiedad enlazable llamada `CurrentItem`, de tipo `FlyoutItem`, que representa actualmente el objeto `FlyoutItem` seleccionado. La primera vez que se ejecuta una aplicación de Shell, esta propiedad se establecerá en el primer objeto `FlyoutItem` del objeto `Shell` en subclase. Sin embargo, la propiedad se puede establecer en otro objeto `FlyoutItem`, como se muestra en el ejemplo siguiente:

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

Este código establece el objeto `ShellContent` llamado `aboutItem` como la propiedad `CurrentItem`, que hace que se muestre. En este ejemplo, se usa una conversión implícita para encapsular el objeto `ShellContent` en un objeto `Tab`, que se encapsula en un objeto `FlyoutItem`.

El código C# equivalente, dado un objeto `ShellContent` denominado `aboutItem`, es el siguiente:

```csharp
CurrentItem = aboutItem;
```

En este ejemplo, la propiedad `CurrentItem` está establecida en la clase `Shell` de la que se crean subclases. Como alternativa, la propiedad `CurrentItem` se puede establecer en cualquier clase a través de la propiedad estática `Shell.Current`:

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="replace-flyout-content"></a>Reemplazo del contenido de un control flotante

Los elementos de un control flotante, que representan su contenido, pueden reemplazarse opcionalmente por contenido propio estableciendo la propiedad enlazable `Shell.FlyoutContent` en un valor `object`:

```xaml
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
```

En este ejemplo, el contenido del control flotante se reemplaza por un valor [`CollectionView`](xref:Xamarin.Forms.CollectionView) que muestra el título de cada elemento en la colección `FlyoutItems`.

> [!NOTE]
> La propiedad `FlyoutItems`, en la clase `Shell`, es una colección de solo lectura de elementos de control flotante.

Como alternativa, el contenido del control flotante se puede definir estableciendo la propiedad `Shell.FlyoutContentTemplate` en un valor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
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
```

> [!IMPORTANT]
> Opcionalmente, se puede mostrar un encabezado de control flotante encima del contenido del control. También se puede mostrar un pie de página de control flotante debajo del contenido del control. Si el contenido del control flotante es desplazable, el shell intentará respetar el comportamiento de desplazamiento del encabezado de control flotante.

## <a name="menu-items"></a>Elementos de menú

Los elementos del menú se pueden agregar opcionalmente a la ventana flotante, y cada elemento de menú se representa mediante un objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem). La posición de los objetos `MenuItem` en el control flotante depende de su orden de declaración en la jerarquía visual del Shell. Por lo tanto, cualquier objeto `MenuItem` declarado antes de los objetos `FlyoutItem` aparecerá en la parte superior del control flotante, y cualquier objeto `MenuItem` declarado después de los objetos `FlyoutItem` aparecerá en la parte inferior del control flotante.

> [!NOTE]
> La clase `MenuItem` tiene un evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) y una propiedad [`Command`](xref:Xamarin.Forms.MenuItem.Command). Por lo tanto, los objetos `MenuItem` permiten escenarios que ejecutan una acción en respuesta al elemento `MenuItem` que se pulsa. Estos escenarios incluyen realizar la navegación y abrir un explorador web en una página web específica.

Se pueden agregar objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem) al control flotante, tal como se muestra en el ejemplo siguiente:

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

Este código agrega dos objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem) al control flotante, debajo de todos los elementos del control flotante:

[![Captura de pantalla de un control flotante que contiene objetos MenuItem, en iOS y Android](flyout-images/flyout.png "Control flotante de Shell con objetos MenuItem")](flyout-images/flyout-large.png#lightbox "Control flotante de Shell con objetos MenuItem")

El primer objeto [`MenuItem`](xref:Xamarin.Forms.MenuItem) ejecuta un elemento `ICommand` llamado `RandomPageCommand`, que lleva a una página aleatoria de la aplicación. El segundo objeto `MenuItem` ejecuta un elemento `ICommand` llamado `HelpCommand`, que abre la dirección URL especificada por la propiedad `CommandParameter` en un explorador web.

> [!NOTE]
> El elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada `MenuItem` se hereda del objeto `Shell` en subclase.

## <a name="define-menuitem-appearance"></a>Definición de la apariencia de MenuItem

Se puede personalizar la apariencia de cada `MenuItem` mediante el establecimiento de la propiedad adjunta `Shell.MenuItemTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

Este ejemplo adjunta el nivel de Shell `MenuItemTemplate` a cada objeto `MenuItem`, mostrando el título de cada objeto `MenuItem` en cursiva:

[![Captura de pantalla de objetos MenuItem con plantilla, en iOS y Android](flyout-images/menuitem-templated.png "Objetos MenuItem con plantilla de Shell")](flyout-images/menuitem-templated-large.png#lightbox "Objetos MenuItem con plantilla de Shell")

> [!NOTE]
> Shell proporciona las propiedades [`Text`](xref:Xamarin.Forms.MenuItem.Text) y [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) para el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del objeto `MenuItemTemplate`. También puede usar `Title` en lugar de `Text` y `Icon` en lugar de `IconImageSource`, lo que le permitirá volver a usar la misma plantilla para los elementos de menú y los de control flotante

Dado que `Shell.MenuItemTemplate` es una propiedad adjunta, se pueden asociar diferentes plantillas a objetos `MenuItem` específicos:

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```

Este ejemplo adjunta el `MenuItemTemplate` de nivel de Shell al primer objeto `MenuItem` y adjunta el `MenuItemTemplate` alineado al segundo `MenuItem`.

> [!NOTE]
> La plantilla predeterminada para objetos `FlyoutItem` también se puede usar con objetos `MenuItem`. Para más información, vea [Plantilla predeterminada para objetos FlyoutItem](#default-template-for-flyoutitems).

## <a name="flyoutitem-and-menuitem-style-classes"></a>Clases de estilo de FlyoutItem y MenuItem

Shell incluye tres clases de estilo, que se aplican automáticamente a los objetos `FlyoutItem` y `MenuItem`. Los nombres de esas clases de estilo son:

- `FlyoutItemLabelStyle`
- `FlyoutItemImageStyle`
- `FlyoutItemLayoutStyle`

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

Estos estilos se aplicarán automáticamente a objetos `FlyoutItem` y `MenuItem`, sin tener que establecer sus propiedades [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) en los nombres de clase de estilo.

Además, se pueden definir y aplicar clases de estilo personalizadas a objetos `FlyoutItem` y `MenuItem`. Para obtener más información sobre las clases de estilo, vea [Clases de estilo de Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Clases de estilo de Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md)
