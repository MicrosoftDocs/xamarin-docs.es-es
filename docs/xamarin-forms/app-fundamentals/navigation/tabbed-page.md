---
title: TabbedPage de Xamarin.Forms
description: TabbedPage de Xamarin.Forms consta de una lista de pestañas y un área de detalles mayor. Cada pestaña carga contenido en el área de detalles. En este artículo se muestra cómo usar una instancia de TabbedPage para navegar por una colección de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910709"
---
# <a name="xamarinforms-tabbedpage"></a>TabbedPage de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms consiste en una lista de pestañas y un área más grande de detalles. Cada pestaña carga contenido en el área de detalles. En las capturas de pantalla siguientes se muestra un elemento `TabbedPage` en iOS y Android:

[![Captura de pantalla de un elemento TabbedPage que contiene tres pestañas, en iOS y Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tres pestañas")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tres pestañas")

En iOS, la lista de pestañas aparece en la parte inferior de la pantalla y el área de detalles está arriba. Cada pestaña está formada por un título y un icono, que debe ser un archivo PNG con un canal alfa. En orientación vertical, los iconos de la barra de pestañas aparecen encima de los títulos de pestañas. En orientación horizontal, los iconos y los títulos aparecen unos al lado de otros. Además, se puede mostrar una barra de pestañas normal o compacta, dependiendo del dispositivo y la orientación. Si hay más de cinco pestañas, aparece una pestaña **Más** que puede usarse para acceder a las demás pestañas. Para información sobre los requisitos de los iconos, consulte [Tamaño de los iconos de la barra de pestañas](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) en developer.apple.com.

> [!TIP]
> El elemento `TabbedRenderer` para iOS tiene un método reemplazable `GetIcon` que se puede usar para cargar iconos de pestaña desde un origen especificado. Esta invalidación permite usar imágenes SVG como iconos en un elemento `TabbedPage`. Además, se pueden proporcionar versiones seleccionadas y sin seleccionar de un icono.

En Android, la lista de pestañas aparece en la parte superior de la pantalla y el área de detalles está debajo. Cada pestaña está formada por un título y un icono, que debe ser un archivo PNG con un canal alfa. Sin embargo, se pueden mover las pestañas a la parte inferior de la pantalla con una plataforma específica. Si hay más de cinco pestañas, y la lista de pestañas está en la parte inferior de la pantalla, aparece una pestaña *Más* que puede usarse para acceder a las demás pestañas. Para información sobre los requisitos de los iconos, consulte [Pestañas](https://material.io/components/tabs/#) en material.io y [Compatibilidad con distintas densidades de píxeles](https://developer.android.com/training/multiscreen/screendensities) en developer.android.com. Para información sobre cómo mover las pestañas a la parte inferior de la pantalla, consulte [Establecimiento de la posición y el color de la barra de herramientas de TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> El elemento `TabbedPageRenderer` para Android AppCompat tiene un método reemplazable `GetIconDrawable` que se puede usar para cargar iconos de pestaña desde un elemento `Drawable` personalizado. Esta invalidación permite usar imágenes SVG como iconos en un elemento `TabbedPage` y funciona con barras de pestañas superiores e inferiores. También se puede usar el método reemplazable `SetTabIcon` para cargar iconos de pestaña desde un elemento `Drawable` personalizado para barras de pestañas superiores.

En la Plataforma universal de Windows (UWP), la lista de pestañas aparece en la parte superior de la pantalla y el área de detalles se muestra debajo. Cada pestaña está formada por un título. Sin embargo, se pueden agregar iconos a cada pestaña con una plataforma específica. Para más información, consulte [Iconos de TabbedPage en Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Creación de TabbedPage

Para crear una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se pueden usar dos métodos:

- Rellene el elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) con una colección de objetos [`Page`](xref:Xamarin.Forms.Page) secundarios, por ejemplo, objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Para más información, consulte [Relleno de un elemento TabbedPage con una colección de páginas](#populate-a-tabbedpage-with-a-page-collection).
- Asigne una colección a la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) y asigne un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para devolver páginas para los objetos de la colección. Para más información, consulte [Relleno de un elemento TabbedPage con una plantilla](#populate-a-tabbedpage-with-a-template).

Con ambos métodos, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) muestra cada página cuando el usuario selecciona cada pestaña.

> [!IMPORTANT]
> Se recomienda que una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se rellene únicamente con instancias de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Esto ayuda a garantizar una experiencia de usuario coherente en todas las plataformas.

Además, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) define las propiedades siguientes:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de fondo de la barra de pestañas.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color del texto de la barra de pestañas.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de la pestaña cuando está seleccionada.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de la pestaña cuando no está seleccionada.

Todas estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que se les pueden aplicar estilos y que las propiedades pueden ser los destinos de los enlaces de datos.

> [!WARNING]
> En un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), cada objeto [`Page`](xref:Xamarin.Forms.Page) se crea cuando se construye `TabbedPage`. Esto puede dar lugar a una experiencia de usuario deficiente, especialmente si `TabbedPage` es la página raíz de la aplicación. Sin embargo, el shell de Xamarin.Forms permite que las páginas a las que se accede mediante una barra de pestañas se creen a petición, en respuesta a la navegación. Para obtener más información, vea [Shell de Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Relleno de un elemento TabbedPage con una colección de páginas

Un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se puede rellenar con una colección de objetos [`Page`](xref:Xamarin.Forms.Page) secundarios, por ejemplo, objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Para ello, se agregan los objetos `Page` a la colección [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*). Esto se puede lograr en XAML de la siguiente manera:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> La propiedad [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) de la clase [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), de la que se deriva [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), es la propiedad `ContentProperty` de `MultiPage<T>`. Por lo tanto, en XAML no es necesario asignar explícitamente los objetos [`Page`](xref:Xamarin.Forms.Page) a la propiedad `Children`.

El código de C# equivalente es el siguiente:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

En este ejemplo, el elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se rellena con dos objetos [`Page`](xref:Xamarin.Forms.ContentPage). El primer elemento secundario es un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) y el segundo es un objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contiene un objeto `ContentPage`.

En las capturas de pantalla siguientes se muestra un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) de un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

[![Captura de pantalla de un elemento TabbedPage que contiene tres pestañas, en iOS y Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tres pestañas")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tres pestañas")

Al seleccionar otra pestaña se muestra el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que representa la pestaña:

[![Captura de pantalla de un elemento TabbedPage que contiene pestañas, en iOS y Android](tabbed-page-images/tabbedpage-week.png "TabbedPage con pestañas")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage con pestañas")

En la pestaña **Programar**, el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) está encapsulado en un objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!WARNING]
> Aunque se puede colocar un objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) en un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), no se recomienda colocar un elemento `TabbedPage` en un objeto `NavigationPage`. Esto se debe a que, en iOS, un elemento `UITabBarController` siempre actúa como contenedor de `UINavigationController`. Para obtener más información, vea [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces combinadas del controlador de vistas) en la biblioteca para desarrolladores de iOS.

## <a name="navigate-within-a-tab"></a>Navegación en una pestaña

La navegación se puede realizar dentro de una pestaña, siempre y cuando el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) esté encapsulado en un objeto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para ello, se invoca el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) en la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) del objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La página a la que se navega se especifica como argumento para el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*). En este ejemplo, la página `UpcomingAppointmentsPage` se inserta en la pila de navegación, donde se convierte en la página activa:

[![Captura de pantalla de navegación en una pestaña, en iOS y Android](tabbed-page-images/tabbedpage-upcoming.png "Navegación de TabbedPage en una pestaña")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navegación de TabbedPage en una pestaña")

Para obtener más información sobre la navegación mediante la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vea [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Relleno de un elemento TabbedPage con una plantilla

Un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se puede rellenar con páginas mediante la asignación de una colección de datos a la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) y la asignación de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) que crea una plantilla de los datos como objetos [`Page`](xref:Xamarin.Forms.Page). Esto se puede lograr en XAML de la siguiente manera:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

El código de C# equivalente es el siguiente:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

En este ejemplo, cada pestaña consta de un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que usa objetos [`Image`](xref:Xamarin.Forms.Image) y [`Label`](xref:Xamarin.Forms.Label) para mostrar los datos de la pestaña:

[![Captura de pantalla de un elemento TabbedPage con plantilla, en iOS y Android](tabbed-page-images/tabbedpage-template.png "TabbedPage con plantilla")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage con plantilla")

Al seleccionar otra pestaña se muestra el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que representa la pestaña.

## <a name="related-links"></a>Vínculos relacionados

- [TabbedPageWithNavigationPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas de Xamarin.Forms](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPage API](xref:Xamarin.Forms.TabbedPage)
