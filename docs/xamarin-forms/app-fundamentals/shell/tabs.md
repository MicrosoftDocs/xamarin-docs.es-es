---
title: Diseño de Xamarin.Forms Shell
description: Después de un control flotante, el siguiente nivel de navegación en una aplicación de Shell es la barra de pestañas de la parte inferior. Como alternativa, el modelo de navegación para una aplicación puede comenzar con pestañas en la parte inferior y no usar un control flotante. En ambos casos, cuando una pestaña inferior contiene más de una página, las páginas son navegables mediante las pestañas principales.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5df5560966def16741f6eabdacb3312944063a1f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372618"
---
# <a name="xamarinforms-shell-tabs"></a>Pestañas de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Cuando el modelo de navegación para una aplicación incluye un control flotante, el siguiente nivel de navegación de la aplicación es la barra de pestañas de la parte inferior. Además, cuando se cierra el control flotante, la barra de pestañas inferior se puede considerar el nivel superior de navegación.

Como alternativa, el modelo de navegación para una aplicación puede comenzar con pestañas en la parte inferior y no usar un control flotante. En este escenario, el elemento secundario del objeto `Shell` debe ser un objeto `TabBar`, que representa la barra de pestañas de la parte inferior.

> [!NOTE]
> El tipo `TabBar` deshabilita el control flotante.

Cada objeto `FlyoutItem` o `TabBar` puede contener uno o varios objetos `Tab`, donde cada objeto `Tab` representa una pestaña en la barra de pestañas inferior. Cada objeto `Tab` puede contener uno o varios objetos `ShellContent`, y cada objeto `ShellContent` mostrará un único objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos `ContentPage` serán navegables mediante las pestañas superiores.

Dentro de cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), se puede navegar a objetos `ContentPage` adicionales. Para obtener más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

## <a name="single-page-application"></a>Aplicación de página única

La aplicación más sencilla de Shell es una aplicación de página única, que se puede crear mediante la adición de un único objeto `Tab` a un objeto `TabBar`. Dentro del objeto `Tab`, un objeto `ShellContent` se debe establecer en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Este ejemplo de código tiene como resultado la siguiente aplicación de página única:

[![Captura de pantalla de una aplicación de página única de Shell en iOS y Android](tabs-images/single-page-app.png "Aplicación de una sola página de Shell")](tabs-images/single-page-app-large.png#lightbox "Aplicación de una sola página de Shell")

> [!NOTE]
> Si es necesario, la barra de navegación se puede ocultar mediante el establecimiento de la propiedad adjunta `Shell.NavBarIsVisible` en `false` en el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Shell tiene operadores de conversión implícita que permiten simplificar la jerarquía visual de Shell, sin introducir vistas adicionales en el árbol visual. Esto es posible porque un objeto `Shell` con subclases solo puede contener objetos `FlyoutItem` o un objeto `TabBar`, que solo pueden contener objetos `Tab`, que solo pueden contener objetos `ShellContent`. Estos operadores de conversión implícita pueden usarse para quitar los objetos `TabBar`, `Tab` y `ShellContent` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

Esta conversión implícita encapsula automáticamente el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) en un objeto `ShellContent`, que se encapsula en un objeto `Tab`, que se encapsula en un objeto `FlyoutItem`. Un control flotante no es necesario en una aplicación de página única y, por lo tanto, la propiedad `Shell.FlyoutBehavior` está establecida en `Disabled`.

> [!IMPORTANT]
> En una aplicación de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que es un elemento secundario de un objeto `ShellContent` se crea durante el inicio de la aplicación. Agregar objetos `ShellContent` adicionales mediante esta estrategia dará lugar a la creación de páginas adicionales durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también es capaz de crear páginas a petición, en respuesta a la navegación. Para más información, consulte [Carga eficiente de páginas](tabs.md#efficient-page-loading).

## <a name="bottom-tabs"></a>Pestañas inferiores

Los objetos `Tab` se representan como pestañas inferiores, siempre y cuando haya varios objetos `Tab` en un único objeto `TabBar`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Los títulos e iconos de pestaña se establecen en cada objeto `Tab`, y se muestran en las pestañas inferiores:

[![Captura de pantalla de una aplicación de dos páginas de Shell con pestañas inferiores, en iOS y Android](tabs-images/two-page-app-bottom-tabs.png "Aplicación de dos páginas de Shell con pestañas inferiores")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Aplicación de dos páginas de Shell con pestañas inferiores")

Si hay más de cinco pestañas, aparece una pestaña **Más** que puede usarse para acceder a las demás pestañas:

[![Captura de pantalla de una aplicación de Shell con una pestaña Más, en iOS y Android](tabs-images/more-tabs.png "Aplicación de Shell con pestaña Más")](tabs-images/more-tabs-large.png#lightbox "Aplicación de Shell con pestañas Más")

Como alternativa, se pueden usar operadores de conversión implícita de Shell para quitar los objetos `ShellContent` y `Tab` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

Esta conversión implícita encapsula automáticamente cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) en un objeto `ShellContent`, ambos de los cuales se encapsulan en un objeto `Tab`.

> [!IMPORTANT]
> En una aplicación de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que es un elemento secundario de un objeto `ShellContent` se crea durante el inicio de la aplicación. Agregar objetos `ShellContent` adicionales mediante esta estrategia dará lugar a la creación de páginas adicionales durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también es capaz de crear páginas a petición, en respuesta a la navegación. Para más información, consulte [Carga eficiente de páginas](tabs.md#efficient-page-loading).

### <a name="tab-class"></a>Clase Tab

La clase `Tab` incluye las siguientes propiedades que controlan la apariencia y el comportamiento de las pestañas:

- `CurrentItem`, de tipo `ShellContent`, el elemento seleccionado.
- `FlyoutDisplayOptions`, de tipo `FlyoutDisplayOptions`, define cómo se muestran el elemento y sus elementos secundarios en el control flotante. El valor predeterminado es `AsSingleItem`.
- `FlyoutIcon`, de tipo `ImageSource`, define el icono que se mostrará en el control flotante.
- `Icon`, de tipo `ImageSource`, define el icono que se mostrará en partes del cromo que no son la ventana flotante.
- `IsChecked`, de tipo `boolean`, define si el elemento está actualmente resaltado en la ventana flotante.
- `IsEnabled`, de tipo `boolean`, define si el elemento es seleccionable en el cromo.
- `IsTabStop`, de tipo `bool`, indica si se incluye un objeto `Tab` en la navegación entre pestañas. Su valor predeterminado es `true`, y cuando su valor es `false`, la infraestructura de navegación entre pestañas omite el objeto `Tab`, independientemente de si se ha definido un objeto `TabIndex`.
- `Items`, de tipo `IList<ShellContent>`, define todo el contenido dentro de un objeto `Tab`.
- `TabIndex`, de tipo `int`, indica el orden en que los objetos `Tab` reciben el foco cuando el usuario navega por los elementos presionando la tecla de tabulación. El valor predeterminado de la propiedad es 0.
- `Title`, de tipo `string`, el título que se mostrará en la pestaña en la interfaz de usuario.

## <a name="shell-content"></a>Contenido de Shell

El elemento secundario de cada objeto `Tab` es un objeto `ShellContent` cuya propiedad `Content` está establecida en [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Dentro de cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), se puede navegar a objetos `ContentPage` adicionales. Para obtener más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> El [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto `ShellContent` se hereda del objeto `Tab` primario.

### <a name="shellcontent-class"></a>Clase ShellContent

La clase `ShellContent` incluye las siguientes propiedades que controlan la apariencia del contenido de las pestañas y su comportamiento:

- `Content`, de tipo `object`, el contenido de `ShellContent`.
- `ContentTemplate`, de tipo `DataTemplate`, la plantilla usada para aumentar dinámicamente el contenido de `ShellContent`.
- `FlyoutIcon`, de tipo `ImageSource`, define el icono que se mostrará en el control flotante.
- `Icon`, de tipo `ImageSource`, define el icono que se mostrará en partes del cromo que no son la ventana flotante.
- `IsChecked`, de tipo `boolean`, define si el elemento está actualmente resaltado en la ventana flotante.
- `IsEnabled`, de tipo `boolean`, define si el elemento es seleccionable en el cromo.
- `IsVisible`, de tipo `bool`, indica si `ShellContent` está oculto en todas las estructuras de la interfaz de usuario. Su valor predeterminado es `true`.
- `MenuItems`, de tipo `MenuItemCollection`, los elementos de menú para mostrar en el control flotante cuando `ShellContent` es la página presentada.
- `Title`, de tipo `string`, el título que se mostrará en la interfaz de usuario.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

## <a name="bottom-and-top-tabs"></a>Pestañas inferiores y superiores

Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, se agrega una barra de pestañas principales a la pestaña inferior, mediante las cuales se puede navegar por los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

El resultado es el diseño que se muestra en las capturas de pantalla siguientes:

[![Captura de pantalla de una aplicación de dos páginas de Shell con pestañas inferior y superior, en iOS y Android](tabs-images/two-page-app-top-tabs.png "Aplicación de dos páginas de Shell con pestañas superior e inferior")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Aplicación de dos páginas de Shell con pestañas superior e inferior")

Como alternativa, se pueden usar operadores de conversión implícita de Shell para quitar los objetos `ShellContent` y el segundo objeto `Tab` del ejemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

Esta conversión implícita encapsula automáticamente `MonkeysPage` en un objeto `ShellContent`, el cual se encapsula en un objeto `Tab`. Además, `CatsPage` y `DogsPage` se encapsulan implícitamente en objetos `ShellContent`.

## <a name="efficient-page-loading"></a>Carga eficiente de páginas

En una aplicación de Shell, cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) de un objeto `ShellContent` se crea durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también permite que las páginas se creen a petición, en respuesta a la navegación. Para ello, se puede usar la extensión de marcado `DataTemplate` para convertir cada objeto `ContentPage` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) y, luego, establecer el resultado como el valor de la propiedad `ShellContent.ContentTemplate`:

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

Este XAML crea y muestra `CatsPage`, porque es el primer elemento del contenido declarado en el objeto `Shell` en subclase. Se puede navegar por las páginas `DogsPage` y `MonkeysPage` mediante pestañas inferiores, y estas páginas solo se crean cuando el usuario se desplaza hasta ellas. La ventaja de este enfoque es que se evita la experiencia de inicio deficiente, ya que las páginas se crean a petición en respuesta a la navegación, y no cuando se inicia la aplicación.

## <a name="tab-appearance"></a>Apariencia de las pestañas

La clase `Shell` define las siguientes propiedades adjuntas que controlan la apariencia de las pestañas:

- `TabBarBackgroundColor`, de tipo `Color`, que define el color de fondo de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `BackgroundColor`.
- `TabBarDisabledColor`, de tipo `Color`, que define el color deshabilitado de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `DisabledColor`.
- `TabBarForegroundColor`, de tipo `Color`, que define el color de primer plano de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `ForegroundColor`.
- `TabBarTitleColor`, de tipo `Color`, que define el color de título de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `TitleColor`.
- `TabBarUnselectedColor`, de tipo `Color`, que define el color no seleccionado de la barra de pestañas. Si la propiedad no está establecida, se usa el valor de la propiedad `UnselectedColor`.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos, y se les puede aplicar estilos.

En el ejemplo siguiente se muestra un estilo XAML que establece diferentes propiedades de color de las pestañas:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

Además, también se puede aplicar estilo a las pestañas mediante Hojas de estilos CSS. Para obtener más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegación de ShellXamarin.Forms](navigation.md)
- [Propiedades específicas de Xamarin.Forms CSS Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)