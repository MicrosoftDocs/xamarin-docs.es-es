---
title: "Xamarin.Forms: Página de carrusel" description: "CarouselPage de Xamarin.Forms es una página que los usuarios pueden deslizar de lado a lado para navegar por páginas de contenido, a modo de galería. En este artículo se muestra cómo usar CarouselPage para navegar por una colección de páginas."
ms.prod: xamarin ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 12/01/2017 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinforms-carousel-page"></a>Página de carrusel de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_CarouselPage de Xamarin.Forms es una página que los usuarios pueden deslizar de lado a lado para navegar por páginas de contenido, a modo de galería. En este artículo se muestra cómo usar CarouselPage para navegar por una colección de páginas._

> [!IMPORTANT]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) se ha reemplazado por [`CarouselView`](xref:Xamarin.Forms.CarouselView), que proporciona un diseño desplazable en el que los usuarios pueden deslizarse para desplazarse por una colección de elementos. Para obtener más información sobre `CarouselView`, vea [CarouselView de Xamarin.Forms](~/xamarin-forms/user-interface/carouselview/index.md).

En las capturas de pantalla siguientes se muestra un elemento [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) en cada plataforma:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

El diseño de un elemento [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) es idéntico en todas las plataformas. Para navegar por las páginas, hay que deslizar de derecha a izquierda para avanzar en la colección y de izquierda a derecha para retroceder en la colección. Las capturas de pantalla siguientes muestran la primera página de una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Al deslizar de derecha a izquierda se pasa a la segunda página, como se muestra en las capturas de pantalla siguientes:

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Al volver a deslizar de derecha a izquierda se pasa a la tercera página, mientras que al deslizar de izquierda a derecha se vuelve a la página anterior.

> [!NOTE]
> La instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) no admite la virtualización de la interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si `CarouselPage` contiene demasiados elementos secundarios.

Si hay una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) insertada en la página [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), la propiedad [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) debe establecerse en `false` para evitar conflictos entre `CarouselPage` y `MasterDetailPage`.

Para obtener más información sobre la instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), vea el [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

## <a name="create-a-carouselpage"></a>Creación de CarouselPage

Para crear una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) se pueden usar dos métodos:

- [Rellenar](#populate-a-carouselpage-with-a-page-collection) la instancia de `CarouselPage` con una colección de instancias secundarias de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Asignar](#populate-a-carouselpage-with-a-template) una colección a la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) y asignar un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para devolver instancias de [`ContentPage`](xref:Xamarin.Forms.ContentPage) para objetos de la colección.

Con ambos métodos, `CarouselPage` muestra a su vez cada página, con una interacción de deslizamiento que pasa a la página siguiente que se va a mostrar.

> [!NOTE]
> Una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) solo se puede rellenar con instancias de [`ContentPage`](xref:Xamarin.Forms.ContentPage), o derivados de `ContentPage`.

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Relleno de CarouselPage con una colección de páginas

El ejemplo de código XAML siguiente muestra una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) con tres instancias de [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

En el ejemplo de código siguiente se muestra la interfaz de usuario equivalente en C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) muestra simplemente un elemento [`Label`](xref:Xamarin.Forms.Label) para un color determinado y un elemento [`BoxView`](xref:Xamarin.Forms.BoxView) de ese color.

### <a name="populate-a-carouselpage-with-a-template"></a>Relleno de CarouselPage con una plantilla

El ejemplo de código XAML siguiente muestra una instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construida mediante la asignación de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) para devolver páginas para objetos de la colección:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage) se rellena con datos al establecer la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) en el constructor para el archivo de código subyacente:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

En el ejemplo de código siguiente se muestra la instancia de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) equivalente creada en C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

Cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) muestra simplemente un elemento [`Label`](xref:Xamarin.Forms.Label) para un color determinado y un elemento [`BoxView`](xref:Xamarin.Forms.BoxView) de ese color.

## <a name="related-links"></a>Vínculos relacionados

- [Páginas de Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
