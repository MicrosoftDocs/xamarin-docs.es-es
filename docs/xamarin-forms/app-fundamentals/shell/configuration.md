---
title: Configuración de la página Xamarin.Forms Shell
description: La clase Shell define las propiedades adjuntas que se pueden usar para configurar la apariencia de las páginas en las aplicaciones de Xamarin.Forms Shell. Esto incluye establecer los colores de la página, deshabilitar la barra de navegación y la barra de pestañas y mostrar las vistas en la barra de navegación.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2019
ms.openlocfilehash: e207949d607219393ffeb51fce818ddfb68ae344
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489913"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configuración de la página Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La clase `Shell` define las propiedades adjuntas que se pueden usar para configurar la apariencia de las páginas en las aplicaciones de Xamarin.Forms Shell. Esto incluye establecer los colores de la página, deshabilitar la barra de navegación y la barra de pestañas y mostrar las vistas en la barra de navegación.

## <a name="set-page-colors"></a>Establecimiento de los colores de la página

La clase `Shell` define las siguientes propiedades adjuntas que pueden usarse para establecer los colores de la página en una aplicación de Shell:

- `BackgroundColor`, de tipo `Color`, que define el color de fondo en el cromo de Shell. El color no se rellena detrás del contenido de Shell.
- `DisabledColor`, de tipo `Color`, que define el color para sombrear el texto y los iconos que están deshabilitados.
- `ForegroundColor`, de tipo `Color`, que define el color para sombrear el texto y los iconos.
- `TitleColor`, de tipo `Color`, que define el color usado para el título de la página actual.
- `UnselectedColor`, de tipo `Color`, que define el color usado para el texto y los iconos no seleccionados en el cromo de Shell.

Todas estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos y se les puede aplicar estilos mediante XAML. Además, estas propiedades se pueden establecer mediante Hojas de estilo CSS. Para más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

> [!NOTE]
> También hay propiedades que permiten la definición de los colores de las pestañas. Para obtener más información, vea [Apariencia de las pestañas](tabs.md#tab-appearance).

El XAML siguiente muestra el establecimiento de las propiedades de color en una clase `Shell` con subclases:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

En este ejemplo, los valores de color se aplicarán a todas las páginas de la aplicación de Shell, a menos que se invaliden en el nivel de página.

Dado que las propiedades de color son las propiedades adjuntas, también pueden establecerse en páginas individuales, para establecer los colores de esa página:

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

Como alternativa, se pueden establecer las propiedades de color con un estilo XAML:

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

Para obtener más información sobre los estilos XAML, vea [Styling Xamarin.Forms Apps using XAML Styles](~/xamarin-forms/user-interface/styles/xaml/index.md) (Aplicación de estilo a aplicaciones Xamarin.Forms mediante XAML).

## <a name="enable-navigation-bar-shadow"></a>Habilitación de sombra en la barra de navegación

La clase `Shell` define la propiedad adjunta `NavBarHasShadow`, de tipo `bool`, que controla si la barra de navegación tiene una sombra. De forma predeterminada, el valor de la propiedad es `false`.

Aunque esta propiedad se puede establecer en un objeto `Shell` con subclases, también se puede establecer en cualquier página que desee habilitar la sombra de la barra de navegación. Por ejemplo, el siguiente XAML muestra la habilitación de la sombra de la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Esto hace que se habilite la sombra de la barra de navegación.

## <a name="disable-the-navigation-bar"></a>Deshabilitación de la barra de navegación

La clase `Shell` define la propiedad adjunta `NavBarIsVisible`, de tipo `bool`, que define si la barra de navegación debe estar visible cuando se presenta una página. De forma predeterminada, el valor de la propiedad es `true`.

Aunque esta propiedad se puede establecer en un objeto `Shell` con subclases, normalmente se establece en todas las páginas en las que se desea que no esté visible la barra de navegación. Por ejemplo, el siguiente XAML muestra la deshabilitación de la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Como resultado, la barra de navegación deja de ser visible cuando se presenta la página:

![Página de captura de la página de Shell con una barra de navegación invisible, en iOS y Android](configuration-images/navigationbar-invisible.png "Página de Shell con barra de navegación invisible")

## <a name="disable-the-tab-bar"></a>Deshabilitación de la barra de pestañas

La clase `Shell` define la propiedad adjunta `TabBarIsVisible`, de tipo `bool`, que define si la barra de pestañas debe estar visible cuando se presenta una página. De forma predeterminada, el valor de la propiedad es `true`.

Aunque esta propiedad se puede establecer en un objeto `Shell` con subclases, normalmente se establece en todas las páginas en las que se desea que no esté visible la barra de pestañas. Por ejemplo, el siguiente XAML muestra la deshabilitación de la barra de pestañas de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Como resultado, la barra de pestañas deja de ser visible cuando se presenta la página:

![Página de captura de la página de Shell con una barra de pestañas invisible, en iOS y Android](configuration-images/tabbar-invisible.png "Página de Shell con barra de pestañas invisible")

## <a name="display-views-in-the-navigation-bar"></a>Visualización de las vistas en la barra de navegación

La clase `Shell` define la propiedad adjunta `TitleView`, de tipo `View`, que permite que cualquier [`View`](xref:Xamarin.Forms.View) de Xamarin.Forms se muestre en la barra de navegación.

Aunque esta propiedad se puede establecer en un objeto `Shell` con subclases, también se puede establecer en cualquier página en la que se desea que se muestre una vista en la barra de navegación. Por ejemplo, el siguiente XAML muestra la visualización de [`Image`](xref:Xamarin.Forms.Image) en la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

Esto da como resultado una imagen que se muestra en la barra de navegación de la página:

![Página de captura de la página de Shell con una vista de título, en iOS y Android](configuration-images/titleview.png "Página de Shell con una vista de título")

> [!IMPORTANT]
> Si se ha configurado la barra de navegación para que no sea visible, con la propiedad adjunta `NavBarIsVisible`, la vista de título no se mostrará.

Muchas vistas no aparecen en la barra de navegación, salvo que el tamaño de la vista se especifique con las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest), o la ubicación de la vista se especifique con las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Dado que la clase [`Layout`](xref:Xamarin.Forms.Layout) se deriva de la clase [`View`](xref:Xamarin.Forms.View), la propiedad adjunta `TitleView` se puede establecer para mostrar una clase de diseño que contiene varias vistas. De igual manera, dado que la clase [`ContentView`](xref:Xamarin.Forms.ContentView) se deriva en última instancia de la clase [`View`](xref:Xamarin.Forms.View), la propiedad adjunta `TitleView` se puede establecer para mostrar un `ContentView` que contenga una sola vista.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell specific properties](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties) (Propiedades específicas de Xamarin.Forms CSS Shell)
