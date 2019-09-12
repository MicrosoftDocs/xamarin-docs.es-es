---
title: TabbedPage de Xamarin.Forms
description: TabbedPage de Xamarin.Forms consta de una lista de pestañas y un área de detalles mayor. Cada pestaña carga contenido en el área de detalles. En este artículo se muestra cómo usar una instancia de TabbedPage para navegar por una colección de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f8f2fc489c10d404b5864ea13c438e3e5508eabe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771359"
---
# <a name="xamarinforms-tabbed-page"></a>TabbedPage de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

_TabbedPage de Xamarin.Forms consta de una lista de pestañas y un área de detalles mayor. Cada pestaña carga contenido en el área de detalles. En este artículo se muestra cómo usar una instancia de TabbedPage para navegar por una colección de páginas._

## <a name="overview"></a>Información general

En las capturas de pantalla siguientes se muestra un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) en cada plataforma:

![](tabbed-page-images/tab1.png "Ejemplo de TabbedPage")

Las capturas de pantalla siguientes se centran en el formato de pestaña en cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "Componentes de pestañas de TabbedPage")

El diseño de una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) y sus pestañas depende de la plataforma:

- En iOS, la lista de pestañas aparece en la parte inferior de la pantalla y el área de detalles está arriba. Cada pestaña además tiene una imagen de icono que debe tener el formato PNG 30 x 30 con transparencia para la resolución normal, 60 x 60 para alta resolución y 90 x 90 para resolución de iPhone 6 Plus. Si hay más de cinco pestañas, aparece una pestaña *Más* que puede usarse para acceder a las demás pestañas. Para obtener más información sobre cómo cargar imágenes en una aplicación de Xamarin.Forms, vea [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md). Para obtener más información sobre los requisitos de los iconos, vea [Barras de pestañas y controladores de la barra de pestañas de Xamarin.iOS](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Observe que el elemento `TabbedRenderer` para iOS tiene un método reemplazable `GetIcon` que se puede usar para cargar iconos de pestaña desde un origen especificado. Esta invalidación permite usar imágenes SVG como iconos en un elemento `TabbedPage`. Además, se pueden proporcionar versiones seleccionadas y sin seleccionar de un icono.

- En Android, la lista de pestañas aparece en la parte superior de la pantalla de forma predeterminada y el área de detalles está debajo. Pero se puede mover la lista de pestañas a la parte inferior de la pantalla con una plataforma específica. Para obtener más información, vea [Establecer ubicación de la barra de herramientas TabbedPage y Color](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

  > [!NOTE]
  > Tenga en cuenta que al usar AppCompat en Android, cada pestaña también muestra un icono. Además, el elemento `TabbedPageRenderer` para Android AppCompat tiene un método reemplazable `GetIconDrawable` que se puede usar para cargar iconos de pestaña desde un elemento `Drawable` personalizado. Esta invalidación permite usar imágenes SVG como iconos en un elemento `TabbedPage` y funciona con barras de pestañas superiores e inferiores. También se puede usar el método reemplazable `SetTabIcon` para cargar iconos de pestaña desde un elemento `Drawable` personalizado para barras de pestañas superiores.

- En los factores de forma de tableta de Windows, las pestañas no siempre están visibles y los usuarios tienen que deslizar hacia abajo (o hacer clic con el botón derecho, si tienen un mouse asociado) para ver las pestañas en un elemento `TabbedPage` (como se muestra a continuación).

    ![](tabbed-page-images/windows-tabs.png "Pestañas de TabbedPage en Windows")

## <a name="creating-a-tabbedpage"></a>Creación de TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) define las siguientes propiedades:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de fondo de la barra de pestañas.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color del texto en la barra de pestañas.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de la pestaña cuando está seleccionada.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color de la pestaña cuando no está seleccionada.

Todas estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que se les pueden aplicar estilos y que las propiedades pueden ser los destinos de los enlaces de datos.

Para crear una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se pueden usar dos métodos:

- [Rellenar](#Populating_a_TabbedPage_with_a_Page_Collection) la instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) con una colección de objetos secundarios [`Page`](xref:Xamarin.Forms.Page), como una colección de instancias de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Asignar](#Populating_a_TabbedPage_with_a_Template) una colección a la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) y asignar un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para devolver páginas de objetos de la colección.

Con ambos métodos, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) muestra cada página cuando el usuario selecciona cada pestaña.

> [!NOTE]
> Se recomienda que una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se rellene únicamente con instancias de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y [`ContentPage`](xref:Xamarin.Forms.ContentPage). Esto ayuda a garantizar una experiencia de usuario coherente en todas las plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Rellenar TabbedPage con una colección de páginas

El ejemplo de código XAML siguiente muestra un elemento [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construido al rellenarse con una colección de objetos secundarios [`Page`](xref:Xamarin.Forms.Page):

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

En el ejemplo de código siguiente se muestra la instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente creada en C#:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se rellena con dos objetos secundarios [`Page`](xref:Xamarin.Forms.Page). El primer elemento secundario es una instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage) y la segunda pestaña es un elemento [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contiene una instancia de `ContentPage`.

> [!NOTE]
> La instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) no admite la virtualización de la interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si `TabbedPage` contiene demasiados elementos secundarios.

Las capturas de pantalla siguientes muestran el elemento `TodayPage` de la instancia [`ContentPage`](xref:Xamarin.Forms.ContentPage), que se muestra en la pestaña *Today*:

![](tabbed-page-images/today-page.png "ContentPage en TabbedPage")

Al seleccionar la pestaña *Schedule*, se muestra el elemento `SchedulePage` de la instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage), que se incluye en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y se muestra en la captura de pantalla siguiente:

![](tabbed-page-images/schedule-page.png "NavigationPage en TabbedPage")

Para obtener información sobre el diseño de un elemento [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vea [Realizar la navegación](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Aunque es aceptable colocar un elemento [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) en una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), no se recomienda colocar un elemento `TabbedPage` en `NavigationPage`. Esto se debe a que, en iOS, un elemento `UITabBarController` siempre actúa como contenedor de `UINavigationController`. Para obtener más información, vea [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfaces combinadas del controlador de vistas) en la biblioteca para desarrolladores de iOS.

#### <a name="navigation-inside-a-tab"></a>Navegación dentro de una pestaña

La navegación se puede realizar desde la segunda pestaña si se invoca al método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) en la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de la instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Esto hace que la instancia de `UpcomingAppointmentsPage` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguientes:

![](tabbed-page-images/navigationpage.png "Navegación dentro de una pestaña")

Para obtener más información sobre la navegación mediante la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vea [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Rellenar TabbedPage con una plantilla

El ejemplo de código XAML siguiente muestra una instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) construida mediante la asignación de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para devolver páginas para objetos de la colección:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
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

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se rellena con datos al establecer la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) en el constructor para el archivo de código subyacente:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

En el ejemplo de código siguiente se muestra la instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente creada en C#:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
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

Cada pestaña muestra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) que usa una serie de instancias de [`StackLayout`](xref:Xamarin.Forms.StackLayout) y [`Label`](xref:Xamarin.Forms.Label) para mostrar datos para la pestaña. Las capturas de pantalla siguientes muestran el contenido de la pestaña *Tamarin*:

![](tabbed-page-images/tab3.png "Relleno de TabbedPage con una plantilla")

Al seleccionar otra pestaña, se muestra el contenido de esa pestaña.

> [!NOTE]
> La instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) no admite la virtualización de la interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si `TabbedPage` contiene demasiados elementos secundarios.

Para obtener más información sobre la instancia de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), vea el [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar una instancia de TabbedPage para navegar por una colección de páginas. [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms consta de una lista de pestañas y un área de detalles mayor. Cada pestaña carga contenido en el área de detalles.

## <a name="related-links"></a>Vínculos relacionados

- [Páginas de Xamarin.Forms](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
