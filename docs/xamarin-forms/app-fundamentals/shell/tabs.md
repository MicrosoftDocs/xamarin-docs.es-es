---
title: Pestañas de Xamarin.Forms Shell
description: Después de un control flotante, el siguiente nivel de navegación en una aplicación de Shell es la barra de pestañas de la parte inferior. Como alternativa, el modelo de navegación para una aplicación puede comenzar con pestañas en la parte inferior y no usar un control flotante. En ambos casos, cuando una pestaña inferior contiene más de una página, las páginas son navegables mediante las pestañas principales.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4dcc1e30a89fc5a1e804ffa07272dd2b28b37c04
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101758355"
---
# <a name="xamarinforms-shell-tabs"></a>Pestañas de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La experiencia de navegación proporcionada por Xamarin.Forms Shell se basa en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante o una barra de pestañas inferior, según los requisitos de navegación de la aplicación. Cuando la experiencia de navegación de una aplicación comienza con las pestañas en la parte inferior, el elemento secundario del objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases debe ser un objeto [`TabBar`](xref:Xamarin.Forms.TabBar), que representa la barra de pestañas inferior.

Cada objeto [`TabBar`](xref:Xamarin.Forms.TabBar) puede contener uno o varios objetos [`Tab`](xref:Xamarin.Forms.Tab), donde cada objeto `Tab` representa una pestaña en la barra de pestañas inferior. Cada objeto `Tab` puede contener uno o varios objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent), donde cada objeto `ShellContent` muestra un único objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos `ContentPage` serán navegables mediante las pestañas superiores. En una pestaña, se puede navegar por objetos `ContentPage` adicionales que se conocen como páginas de detalles.

> [!IMPORTANT]
> El tipo [`TabBar`](xref:Xamarin.Forms.TabBar) deshabilita el control flotante.

## <a name="single-page"></a>Página única

Se puede crear una aplicación de Shell de una sola página agregando un objeto [`Tab`](xref:Xamarin.Forms.Tab) a un objeto [`TabBar`](xref:Xamarin.Forms.TabBar). Dentro del objeto `Tab`, un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) se debe establecer en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
    </TabBar>
</Shell>
```

Este ejemplo de código tiene como resultado la siguiente aplicación de página única:

[![Captura de pantalla de una aplicación de página única de Shell en iOS y Android](tabs-images/single-page-app.png)](tabs-images/single-page-app-large.png#lightbox)

Shell tiene operadores de conversión implícita que permiten simplificar la jerarquía visual de Shell, sin introducir vistas adicionales en el árbol visual. Esto es posible porque un objeto `Shell` con subclases solo puede contener objetos [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o un objeto [`TabBar`](xref:Xamarin.Forms.TabBar), que solo pueden contener objetos [`Tab`](xref:Xamarin.Forms.Tab), que solo pueden contener objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent). Estos operadores de conversión implícita pueden usarse para quitar los objetos `Tab` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Tab>
        <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
    </Tab>
</Shell>
```

Esta conversión implícita encapsula automáticamente el objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`Tab`](xref:Xamarin.Forms.Tab), que se encapsula en un objeto [`TabBar`](xref:Xamarin.Forms.TabBar).

> [!IMPORTANT]
> En una aplicación de Shell, las páginas se crean a petición en respuesta a la navegación. Esto se logra mediante el uso de la extensión de marcado [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) para establecer la propiedad [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

## <a name="bottom-tabs"></a>Pestañas inferiores

Los objetos [`Tab`](xref:Xamarin.Forms.Tab) se representan como pestañas en la parte inferior, siempre y cuando haya varios objetos `Tab` en un único objeto [`TabBar`](xref:Xamarin.Forms.TabBar):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab Title="Cats"
            Icon="cat.png">
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
       <Tab Title="Dogs"
            Icon="dog.png">
           <ShellContent ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
    </TabBar>
</Shell>
```

La propiedad [`Title`](xref:Xamarin.Forms.BaseShellItem.Title), de tipo `string`, define el título de la pestaña. La propiedad [`Icon`](xref:Xamarin.Forms.BaseShellItem.Icon), de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), define el icono de la pestaña:

[![Captura de pantalla de una aplicación de dos páginas de Shell con pestañas en la parte inferior, en iOS y Android](tabs-images/two-page-app-bottom-tabs.png)](tabs-images/two-page-app-bottom-tabs-large.png#lightbox)

Si hay más de cinco pestañas en un objeto [`TabBar`](xref:Xamarin.Forms.TabBar), aparece una pestaña **Más** que permite acceder a las demás pestañas:

[![Captura de pantalla de una aplicación de Shell con una pestaña Más, en iOS y Android](tabs-images/more-tabs.png)](tabs-images/more-tabs-large.png#lightbox)

Además, se pueden usar los operadores de conversión implícita de Shell para quitar los objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent) y [`Tab`](xref:Xamarin.Forms.Tab) del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <ShellContent Title="Cats"
                     Icon="cat.png"
                     ContentTemplate="{DataTemplate views:CatsPage}" />
       <ShellContent Title="Dogs"
                     Icon="dog.png"
                     ContentTemplate="{DataTemplate views:DogsPage}" />
    </TabBar>
</Shell>
```

Esta conversión implícita encapsula automáticamente cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`Tab`](xref:Xamarin.Forms.Tab).

> [!IMPORTANT]
> En una aplicación de Shell, las páginas se crean a petición en respuesta a la navegación. Esto se logra mediante el uso de la extensión de marcado [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) para establecer la propiedad [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

## <a name="bottom-and-top-tabs"></a>Pestañas inferiores y superiores

Cuando hay más de un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`Tab`](xref:Xamarin.Forms.Tab), se agrega una barra de pestañas superior a la pestaña inferior, mediante las cuales se puede navegar por los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab Title="Domestic"
            Icon="paw.png">
           <ShellContent Title="Cats"
                         ContentTemplate="{DataTemplate views:CatsPage}" />
           <ShellContent Title="Dogs"
                         ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
       <Tab Title="Monkeys"
            Icon="monkey.png">
           <ShellContent ContentTemplate="{DataTemplate views:MonkeysPage}" />
       </Tab>
    </TabBar>
</Shell>
```

El resultado es el diseño que se muestra en las capturas de pantalla siguientes:

[![Captura de pantalla de una aplicación de dos páginas de Shell con pestañas inferior y superior, en iOS y Android](tabs-images/two-page-app-top-tabs.png "Aplicación de dos páginas de Shell con pestañas superior e inferior")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Aplicación de dos páginas de Shell con pestañas superior e inferior")

Además, se pueden usar los operadores de conversión implícita de Shell para quitar el segundo objeto [`Tab`](xref:Xamarin.Forms.Tab) del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
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
    </TabBar>
</Shell>
```

Esta conversión implícita encapsula automáticamente cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`Tab`](xref:Xamarin.Forms.Tab).

## <a name="tab-appearance"></a>Apariencia de las pestañas

La clase [`Shell`](xref:Xamarin.Forms.Shell) define las siguientes propiedades adjuntas que controlan la apariencia de las pestañas:

- [`TabBarBackgroundColor`](xref:Xamarin.Forms.Shell.TabBarBackgroundColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color de fondo de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `BackgroundColor`.
- [`TabBarDisabledColor`](xref:Xamarin.Forms.Shell.TabBarDisabledColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color deshabilitado de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `DisabledColor`.
- [`TabBarForegroundColor`](xref:Xamarin.Forms.Shell.TabBarForegroundColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color de primer plano de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `ForegroundColor`.
- [`TabBarTitleColor`](xref:Xamarin.Forms.Shell.TabBarTitleColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color de título de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `TitleColor`.
- [`TabBarUnselectedColor`](xref:Xamarin.Forms.Shell.TabBarUnselectedColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color no seleccionado de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `UnselectedColor`.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos, y se les puede aplicar estilos.

En el ejemplo siguiente se muestra un estilo XAML que establece diferentes propiedades de color de la barra de pestañas:

```xaml
<Style TargetType="TabBar">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="CornflowerBlue" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="Black" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="AntiqueWhite" />
</Style>
```

Además, también se puede aplicar estilo a las pestañas mediante Hojas de estilos CSS. Para obtener más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="tab-selection"></a>Selección de pestañas

Cuando se ejecuta por primera vez una aplicación de Shell que usa una barra de pestañas, la propiedad [`Shell.CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) se establecerá en el primer objeto [ `Tab`](xref:Xamarin.Forms.Tab) del objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Sin embargo, la propiedad se puede establecer en otro objeto `Tab`, como se muestra en el ejemplo siguiente:

```xaml
<Shell ...
       CurrentItem="{x:Reference dogsItem}">
    <TabBar>
        <ShellContent Title="Cats"
                      Icon="cat.png"
                      ContentTemplate="{DataTemplate views:CatsPage}" />
        <ShellContent x:Name="dogsItem"
                      Title="Dogs"
                      Icon="dog.png"
                      ContentTemplate="{DataTemplate views:DogsPage}" />
    </TabBar>
</Shell>
```

En este ejemplo se establece la propiedad [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) en el objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) denominado `dogsItem`, lo que hace que se seleccione y se muestre. En este ejemplo, se usa una conversión implícita para encapsular cada objeto `ShellContent` en un objeto [`Tab`](xref:Xamarin.Forms.Tab).

El código C# equivalente, dado un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) denominado `dogsItem`, es el siguiente:

```csharp
CurrentItem = dogsItem;
```

En este ejemplo, la propiedad [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) está establecida en la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Como alternativa, la propiedad `CurrentItem` se puede establecer en cualquier clase a través de la propiedad estática `Shell.Current`:

```csharp
Shell.Current.CurrentItem = dogsItem;
```

## <a name="tabbar-and-tab-visibility"></a>Visibilidad de la barra de pestañas y las pestañas

La barra de pestañas y las pestañas están visibles de forma predeterminada en las aplicaciones de Shell. Sin embargo, la barra de pestañas se puede ocultar estableciendo la propiedad [`Shell.TabBarIsVisible`](xref:Xamarin.Forms.Shell.TabBarIsVisibleProperty) adjunta en `false`.

Aunque esta propiedad se puede establecer en un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases, normalmente se establece en todos los objetos [`ShellContent`](xref:Xamarin.Forms.ShellContent) o [`ContentPage`](xref:Xamarin.Forms.ContentPage) donde se quiera que la barra de pestañas no esté visible:

```xaml
<TabBar>
   <Tab Title="Domestic"
        Icon="paw.png">
       <ShellContent Title="Cats"
                     ContentTemplate="{DataTemplate views:CatsPage}" />
       <ShellContent Shell.TabBarIsVisible="false"
                     Title="Dogs"
                     ContentTemplate="{DataTemplate views:DogsPage}" />
   </Tab>
   <Tab Title="Monkeys"
        Icon="monkey.png">
       <ShellContent ContentTemplate="{DataTemplate views:MonkeysPage}" />
   </Tab>
</TabBar>
```

En este ejemplo, la barra de pestañas se oculta cuando se selecciona la pestaña **perros** superior.

Además, se pueden ocultar los objetos [`Tab`](xref:Xamarin.Forms.Tab) estableciendo la propiedad enlazable [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible) en `false`:

```xaml
<TabBar>
    <ShellContent Title="Cats"
                  Icon="cat.png"
                  ContentTemplate="{DataTemplate views:CatsPage}" />
    <ShellContent Title="Dogs"
                  Icon="dog.png"
                  ContentTemplate="{DataTemplate views:DogsPage}"
                  IsVisible="False" />
    <ShellContent Title="Monkeys"
                  Icon="monkey.png"
                  ContentTemplate="{DataTemplate views:MonkeysPage}" />
</TabBar>
```

En este ejemplo, la segunda pestaña está oculta.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegación en Xamarin.Forms Shell](navigation.md)
- [Propiedades específicas de Xamarin.Forms CSS Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
