---
title: Páginas de Xamarin.Forms Shell
description: La clase Shell define las propiedades adjuntas que se pueden usar para configurar la apariencia de las páginas en las aplicaciones de Xamarin.Forms Shell. Esto incluye establecer los colores de la página, deshabilitar la barra de navegación y la barra de pestañas y mostrar las vistas en la barra de navegación.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c008ffb6d58a86301f41fdaa54b23eb2d9869618
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101760248"
---
# <a name="xamarinforms-shell-pages"></a>Páginas de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) representa el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) correspondiente a cada [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o [`Tab`](xref:Xamarin.Forms.Tab). Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos `ContentPage` serán navegables mediante las pestañas superiores. En una página, se puede navegar por objetos `ContentPage` adicionales que se conocen como páginas de detalles.

La clase [`Shell`](xref:Xamarin.Forms.Shell) define las propiedades adjuntas que se pueden usar para configurar la apariencia de las páginas en las aplicaciones de Xamarin.Forms Shell. Esto incluye configurar los colores y el modo de presentación de la página, deshabilitar la barra de navegación y la barra de pestañas y mostrar las vistas en la barra de navegación.

## <a name="display-pages"></a>Presentación de páginas

En las aplicaciones de Xamarin.Forms Shell, las páginas se crean a petición en respuesta a la navegación. Esto se logra mediante el uso de la extensión de marcado [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) para establecer la propiedad [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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
       <ShellContent Title="Monkeys"
                     Icon="monkey.png"
                     ContentTemplate="{DataTemplate views:MonkeysPage}" />
    </TabBar>
</Shell>
```

En este ejemplo, se usan los operadores de conversión implícita de Shell para quitar los objetos [`Tab`](xref:Xamarin.Forms.Tab) de la jerarquía visual. Sin embargo, cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) se representa en una pestaña:

[![Captura de pantalla de una aplicación de Shell de tres páginas, en iOS y Android](pages-images/three-pages.png)](pages-images/three-pages-large.png#lightbox)

> [!NOTE]
> El elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) se hereda del objeto [`Tab`](xref:Xamarin.Forms.Tab) primario.

Dentro de cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), se puede navegar a objetos `ContentPage` adicionales. Para obtener más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

## <a name="load-pages-at-application-startup"></a>Carga de páginas al iniciar la aplicación

En una aplicación de Shell, cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) se crea normalmente a petición en respuesta a la navegación. Sin embargo, también es posible crear objetos `ContentPage` al iniciar la aplicación.

> [!WARNING]
> Los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) que se crean al iniciar la aplicación pueden dar lugar a una mala experiencia de inicio.

Los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) se pueden crear al iniciar la aplicación estableciendo las propiedades [`ShellContent.Content`](xref:Xamarin.Forms.ShellContent.Content) en objetos `ContentPage`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
     <ShellContent Title="Cats"
                   Icon="cat.png">
         <views:CatsPage />
     </ShellContent>
     <ShellContent Title="Dogs"
                   Icon="dog.png">
         <views:DogsPage />
     </ShellContent>
     <ShellContent Title="Monkeys"
                   Icon="monkey.png">
         <views:MonkeysPage />
     </ShellContent>
    </TabBar>
</Shell>
```

En este ejemplo, los objetos `CatsPage`, `DogsPage` y `MonkeysPage` se crean al iniciar la aplicación y no a petición en respuesta a la navegación.

> [!NOTE]
> La propiedad [`Content`](xref:Xamarin.Forms.ShellContent.Content) es la propiedad de contenido de la clase [`ShellContent`](xref:Xamarin.Forms.ShellContent) y, por tanto, no hay que establecerla explícitamente.

## <a name="set-page-colors"></a>Establecimiento de los colores de la página

La clase [`Shell`](xref:Xamarin.Forms.Shell) define las siguientes propiedades adjuntas que se pueden usar para establecer los colores de la página en una aplicación de Shell:

- [`BackgroundColor`](xref:Xamarin.Forms.Shell.BackgroundColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color de fondo en el cromo de Shell. El color no se rellena detrás del contenido de Shell.
- [`DisabledColor`](xref:Xamarin.Forms.Shell.DisabledColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color para sombrear el texto y los iconos que están deshabilitados.
- [`ForegroundColor`](xref:Xamarin.Forms.Shell.ForegroundColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color para sombrear el texto y los iconos.
- [`TitleColor`](xref:Xamarin.Forms.Shell.TitleColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color usado para el título de la página actual.
- [`UnselectedColor`](xref:Xamarin.Forms.Shell.UnselectedColorProperty), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color usado para el texto y los iconos no seleccionados en el cromo de Shell.

Todas estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos y se les pueden aplicar estilos mediante XAML. Además, estas propiedades se pueden establecer mediante Hojas de estilo CSS. Para obtener más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

> [!NOTE]
> También hay propiedades que permiten la definición de los colores de las pestañas. Para obtener más información, vea [Apariencia de las pestañas](tabs.md#tab-appearance).

El XAML siguiente muestra el establecimiento de las propiedades de color en una clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases:

```xaml
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

Para obtener más información sobre los estilos XAML, vea [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Configuración del modo de presentación de la página

De forma predeterminada, se produce una animación de navegación pequeña cuando se navega a una página con el método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*). Sin embargo, este comportamiento se puede cambiar si se configura la propiedad adjunta [`Shell.PresentationMode`](xref:Xamarin.Forms.Shell.PresentationModeProperty) de una clase [`ContentPage`](xref:Xamarin.Forms.ContentPage) en uno de los miembros de la enumeración [`PresentationMode`](xref:Xamarin.Forms.PresentationMode):

- `NotAnimated` indica que la página se mostrará sin una animación de navegación.
- `Animated` indica que la página se mostrará con una animación de navegación. Se trata del valor predeterminado de la propiedad adjunta `Shell.PresentationMode`.
- `Modal` indica que la página se mostrará como una página modal.
- `ModalAnimated` indica que la página se mostrará como una página modal, con una animación de navegación.
- `ModalNotAnimated` indica que la página se mostrará como una página modal, sin una animación de navegación.

> [!IMPORTANT]
> El tipo `PresentationMode` es una enumeración de marcas. Esto significa que se puede aplicar una combinación de miembros de enumeración en el código. Sin embargo, para facilitar su uso en XAML, el miembro `ModalAnimated` es una combinación de los miembros `Animated` y `Modal`, y el miembro `ModalNotAnimated` es una combinación de los miembros `NotAnimated` y `Modal`. Para obtener más información sobre las enumeraciones de marcas, vea [Tipos de enumeración como marcas de bits](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

En el siguiente ejemplo de XAML se establece la propiedad adjunta [`Shell.PresentationMode`](xref:Xamarin.Forms.Shell.PresentationModeProperty) en una clase [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

En este ejemplo, la clase [`ContentPage`](xref:Xamarin.Forms.ContentPage) se establece para mostrarse como una página modal, cuando se navega a la página con el método [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*).

## <a name="enable-navigation-bar-shadow"></a>Habilitación de sombra en la barra de navegación

La propiedad adjunta [`Shell.NavBarHasShadow`](xref:Xamarin.Forms.Shell.NavBarHasShadowProperty), de tipo `bool`, controla si la barra de navegación tiene una sombra. De forma predeterminada, el valor de la propiedad es `false` en iOS y `true` en Android.

Aunque esta propiedad se puede establecer en un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases, también se puede establecer en todas las páginas donde se quiera que se habilite la sombra de la barra de navegación. Por ejemplo, el siguiente XAML muestra la habilitación de la sombra de la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Esto hace que se habilite la sombra de la barra de navegación.

## <a name="disable-the-navigation-bar"></a>Deshabilitación de la barra de navegación

La propiedad adjunta [`Shell.NavBarIsVisible`](xref:Xamarin.Forms.Shell.NavBarIsVisibleProperty), de tipo `bool`, controla si la barra de navegación está visible cuando se presenta una página. De forma predeterminada, el valor de la propiedad es `true`.

Aunque esta propiedad se puede establecer en un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases, normalmente se establece en todas las páginas donde se quiera que la barra de navegación no esté visible. Por ejemplo, el siguiente XAML muestra la deshabilitación de la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Como resultado, la barra de navegación deja de ser visible cuando se presenta la página:

![Página de captura de la página de Shell con una barra de navegación invisible, en iOS y Android](pages-images/navigationbar-invisible.png)

## <a name="display-views-in-the-navigation-bar"></a>Visualización de las vistas en la barra de navegación

La propiedad adjunta [`Shell.TitleView`](xref:Xamarin.Forms.Shell.TitleViewProperty), de tipo [`View`](xref:Xamarin.Forms.View), permite que cualquier objeto `View` se muestre en la barra de navegación.

Aunque esta propiedad se puede establecer en un objeto [`Shell`](xref:Xamarin.Forms.Shell) con subclases, también se puede establecer en todas las páginas donde se quiera que se muestre una vista en la barra de navegación. Por ejemplo, el siguiente XAML muestra la visualización de [`Image`](xref:Xamarin.Forms.Image) en la barra de navegación de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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

![Página de captura de la página de Shell con una vista de título, en iOS y Android](pages-images/titleview.png "Página de Shell con una vista de título")

> [!IMPORTANT]
> Si se ha configurado la barra de navegación para que no sea visible, con la propiedad adjunta [`NavBarIsVisible`](xref:Xamarin.Forms.Shell.NavBarIsVisibleProperty), la vista de título no se mostrará.

Muchas vistas no aparecen en la barra de navegación, salvo que el tamaño de la vista se especifique con las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest), o la ubicación de la vista se especifique con las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Dado que la clase [`Layout`](xref:Xamarin.Forms.Layout) se deriva de la clase [`View`](xref:Xamarin.Forms.View), se puede establecer la propiedad adjunta [`TitleView`](xref:Xamarin.Forms.Shell.TitleViewProperty) para mostrar una clase de diseño que contiene varias vistas. De igual manera, dado que la clase [`ContentView`](xref:Xamarin.Forms.ContentView) se deriva en última instancia de la clase [`View`](xref:Xamarin.Forms.View), la propiedad adjunta `TitleView` se puede establecer para mostrar un `ContentView` que contenga una sola vista.

## <a name="page-visibility"></a>Visibilidad de la página

Shell respeta la visibilidad de la página, establecida con la propiedad [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible). Por lo tanto, cuando la propiedad `IsVisible` de una página se establece en `false`, no será visible en la aplicación Shell y no será posible navegar hasta ella.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegación en Xamarin.Forms Shell](navigation.md)
- [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Propiedades específicas de Xamarin.Forms CSS Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
