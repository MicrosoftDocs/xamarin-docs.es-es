---
title: Navegación jerárquica
description: En este artículo se muestra cómo utilizar la clase NavigationPage para realizar la navegación en una pila de páginas en la que el último en entrar es el primero en salir (LIFO).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0d6a18ec55b49da2b668ccc183646d5e4dde5849
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937233"
---
# <a name="hierarchical-navigation"></a>Navegación jerárquica

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_La clase NavigationPage proporciona una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas hacia adelante y hacia atrás, como quiera. La clase implementa la navegación como una pila de objetos de página en la que el último en entrar es el primero en salir (LIFO). En este artículo se muestra cómo utilizar la clase NavigationPage para realizar la navegación en una pila de páginas._

Para pasar de una página a otra, una aplicación insertará una página nueva en la pila de navegación, donde se convertirá en la página activa, tal como se muestra en el diagrama siguiente.

![Inserción de una página en la pila de navegación](hierarchical-images/pushing.png)

Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convertirá en la página activa, tal como se muestra en el diagrama siguiente:

![Sacar una página de la pila de navegación](hierarchical-images/popping.png)

La propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) expone los métodos de navegación en cualquiera de los tipos derivados de [`Page`](xref:Xamarin.Forms.Page). Estos métodos proporcionan la capacidad para insertar páginas en la pila de navegación, sacar páginas de la pila de navegación y manipular la pila.

## <a name="performing-navigation"></a>Realizar la navegación

En la navegación jerárquica, se usa la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para navegar por una pila de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). En las siguientes capturas de pantalla, se muestran los componentes principales de la `NavigationPage` en cada plataforma:

![Componentes de NavigationPage](hierarchical-images/navigationpage-components.png)

El diseño de una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) depende de la plataforma:

- En iOS, en la parte superior de la página, hay una barra de navegación que muestra un título y presenta un botón *Atrás* que vuelve a la página anterior.
- En Android, en la parte superior de la página, hay una barra de navegación que muestra un título, un icono y un botón *Atrás* que vuelve a la página anterior. El icono se define en el atributo `[Activity]` que decora la clase `MainActivity` en el proyecto específico de la plataforma Android.
- En la Plataforma universal de Windows, en la parte superior de la página, hay una barra de navegación que muestra un título.

En todas las plataformas, el valor de la propiedad [`Page.Title`](xref:Xamarin.Forms.Page.Title) se mostrará como el título de la página. Además, la propiedad `IconColor` se puede establecer en un elemento [`Color`](xref:Xamarin.Forms.Color) que se aplica al icono de la barra de navegación.

> [!NOTE]
> Se recomienda que una `NavigationPage` debe rellenarse únicamente con instancias de `ContentPage`.

### <a name="creating-the-root-page"></a>Creación de la página raíz

La primera página que se agrega a una pila de navegación se denomina página *raíz* de la aplicación. En el ejemplo de código siguiente se muestra cómo se consigue:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Esto hace que la instancia [`ContentPage`](xref:Xamarin.Forms.ContentPage) de `Page1Xaml` se inserte en la pila de navegación, donde se convertirá en la página activa y en la página raíz de la aplicación. Esto se muestra en las capturas de pantalla siguientes:

![Página raíz de la pila de navegación](hierarchical-images/mainpage.png)

> [!NOTE]
> La propiedad [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) de una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) permite acceder a la primera página de la pila de navegación.

### <a name="pushing-pages-to-the-navigation-stack"></a>Inserción de páginas en la pila de navegación

Para navegar a `Page2Xaml`, es necesario invocar el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) en la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Esto hace que la instancia de `Page2Xaml` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguientes:

![Página insertada en la pila de navegación](hierarchical-images/secondpage.png)

Al invocar el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*), ocurre lo siguiente:

- Se invoca la invalidación de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) de la página que llama a `PushAsync`.
- Se invoca la invalidación de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la página a la que se ha navegado.
- La tarea `PushAsync` finaliza.

Sin embargo, el orden exacto en el que se producen estos eventos depende de la plataforma. Para obtener más información, consulte el [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Las llamadas a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) y las invalidaciones de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) no se pueden tratar como indicaciones garantizadas de navegación de páginas. Por ejemplo, en iOS, la invalidación de `OnDisappearing` se llama en la página activa cuando la aplicación finaliza.

### <a name="popping-pages-from-the-navigation-stack"></a>Sacar páginas de la pila de navegación

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla.

Para volver mediante programación a la página original, la instancia `Page2Xaml` debe invocar el método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Esto hace que la instancia de `Page2Xaml` se quite de la pila de navegación y que la nueva página de nivel superior se convierta en la página activa. Al invocar el método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), ocurre lo siguiente:

- Se invoca la invalidación de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) de la página que llama a `PopAsync`.
- Se invoca la invalidación de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la página a la que se ha regresado.
- La tarea `PopAsync` vuelve.

Sin embargo, el orden exacto en el que se producen estos eventos depende de la plataforma. Para obtener más información, consulte el [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

Al igual que los métodos [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) y [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de cada página también proporciona un método [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync), que se muestra en el ejemplo de código siguiente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Este método saca todas las páginas de la pila de navegación, excepto la [`Page`](xref:Xamarin.Forms.Page) raíz, de manera que la página raíz de la aplicación se convierte en la página activa.

### <a name="animating-page-transitions"></a>Animación de transiciones de página

La propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de cada página también proporciona métodos de inserción y extracción invalidados que incluyen un parámetro `boolean` que controla si se debe mostrar una animación de página durante la navegación, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

Al establecer el parámetro `boolean` en `false`, la animación de transición de página se deshabilita, mientras que al establecer el parámetro en `true`, la animación de transición de página se habilita, siempre que la plataforma subyacente lo admita. Sin embargo, los métodos de inserción y extracción que carecen de este parámetro habilitan la animación de manera predeterminada.

## <a name="passing-data-when-navigating"></a>Pasar datos al navegar

A veces es necesario que una página pase datos a otra durante la navegación. Dos técnicas para llevar a cabo esto son pasar datos a través de un constructor de página y establecer el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página nueva en los datos. A continuación, se explicarán de uno en uno.

### <a name="passing-data-through-a-page-constructor"></a>Pasar datos a través de un constructor de página

La técnica más sencilla para pasar datos a otra página durante la navegación es hacerlo a través de un parámetro de constructor de página, que se muestra en el ejemplo de código siguiente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Este código crea una instancia de `MainPage` y pasa la fecha y la hora actuales en formato ISO8601, que se encapsula en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

La instancia de `MainPage` recibe los datos a través de un parámetro de constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

A continuación, se establece la propiedad [`Label.Text`](xref:Xamarin.Forms.Label.Text) para que los datos se muestren en la página, como se muestra en las capturas de pantalla siguientes:

![Datos pasados a través de un constructor de página](hierarchical-images/passing-data-constructor.png)

### <a name="passing-data-through-a-bindingcontext"></a>Pasar datos a través de un objeto BindingContext

Un enfoque alternativo para pasar datos a otra página durante la navegación es establecer el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página nueva en los datos, como se muestra en el siguiente ejemplo de código:

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Este código establece el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la instancia de `SecondPage` en la instancia de `Contact` y después navega a la `SecondPage`.

Después, la `SecondPage` utiliza el enlace de datos para mostrar los datos de la instancia de `Contact`, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En el ejemplo de código siguiente se muestra cómo se puede realizar el enlace de datos en C#:

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

A continuación, una serie de controles [`Label`](xref:Xamarin.Forms.Label) muestran los datos en la página, como se muestra en las capturas de pantalla siguientes:

![Datos pasados a través de un objeto BindingContext](hierarchical-images/passing-data-bindingcontext.png)

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

## <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

La propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) expone una propiedad [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) desde la que se pueden obtener las páginas de la pila de navegación. Mientras que Xamarin.Forms mantiene el acceso a la pila de navegación, la propiedad `Navigation` proporciona los métodos [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) y [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) para manipular la pila mediante la inserción o la eliminación de páginas.

El método [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) inserta una página especificada en la pila de navegación antes de una página existente especificada, como se muestra en el diagrama siguiente:

![Inserción de una página en la pila de navegación](hierarchical-images/insert-page-before.png)

El método [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) quita la página especificada de la pila de navegación, como se muestra en el diagrama siguiente:

![Quitar una página de la pila de navegación](hierarchical-images/remove-page.png)

Estos métodos permiten una experiencia de navegación personalizada, como sustituir una página de inicio de sesión por una página nueva, después de iniciar sesión correctamente. El ejemplo de código siguiente muestra esta situación:

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

Siempre que las credenciales del usuario sean correctas, la instancia de `MainPage` se inserta en la pila de navegación antes de la página actual. A continuación, el método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) quita la página actual de la pila de navegación y la instancia de `MainPage` se convierte en la página activa.

## <a name="displaying-views-in-the-navigation-bar"></a>Mostrar vistas en la barra de navegación

Cualquier [`View`](xref:Xamarin.Forms.View) de Xamarin.Forms se puede mostrar en la barra de navegación de una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para ello, establezca la propiedad adjunta [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) en una `View`. Esta propiedad adjunta se puede establecer en cualquier [`Page`](xref:Xamarin.Forms.Page) y, cuando la `Page` se inserte en una `NavigationPage`, la `NavigationPage` respetará el valor de la propiedad.

En el ejemplo siguiente, tomado del [Ejemplo de vista de título](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview), se muestra cómo establecer la propiedad adjunta [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) de XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

El código equivalente en C# es el siguiente:

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

Esto da como resultado un [`Slider`](xref:Xamarin.Forms.Slider) que se muestra en la barra de navegación en la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

[![Control deslizante TitleView](hierarchical-images/titleview-small.png "Control deslizante TitleView")](hierarchical-images/titleview-large.png#lightbox "Control deslizante TitleView")

> [!IMPORTANT]
> Muchas vistas no aparecen en la barra de navegación, salvo que el tamaño de la vista se especifique con las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest). Como alternativa, la vista puede encapsularse en un [`StackLayout`](xref:Xamarin.Forms.StackLayout) con las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) establecidas en los valores adecuados.

Tenga en cuenta que, dado que la clase [`Layout`](xref:Xamarin.Forms.Layout) deriva de la clase [`View`](xref:Xamarin.Forms.View), la propiedad adjunta [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) se puede establecer para mostrar una clase de diseño que contiene varias vistas. En iOS y la Plataforma universal de Windows (UWP), la altura de la barra de navegación no se puede cambiar y, por tanto, se recortará si la vista que se muestra en la barra de navegación es mayor que el tamaño predeterminado de la barra de navegación. Sin embargo, en Android, la altura de la barra de navegación se puede cambiar estableciendo la propiedad enlazable [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) en un `double` que represente la altura nueva. Para obtener más información, consulte [Establecer la altura de la barra de navegación en una NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

Como alternativa, para sugerir una barra de exploración extendida, se puede colocar una parte del contenido en la barra de navegación y otra en una vista en la parte superior del contenido de la página que coincida con el color de la barra de navegación. Además, en iOS, la línea y la sombra del separador que se encuentran en la parte inferior de la barra de navegación se pueden quitar estableciendo la propiedad enlazable [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) en `true`. Para obtener más información, consulte [Ocultar el separador de la barra de navegación en una NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> Las propiedades [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) y [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) pueden definir valores que ocupan espacio en la barra de navegación. Mientras que el tamaño de la barra de navegación varía según la plataforma y el tamaño de pantalla, al establecer todas estas propiedades se producirán conflictos a causa del espacio limitado disponible. En lugar de intentar utilizar una combinación de estas propiedades, es posible que, si solo establece la propiedad `TitleView`, pueda elaborar mejor el diseño de la barra de navegación deseado.

### <a name="limitations"></a>Limitaciones

Al mostrar una [`View`](xref:Xamarin.Forms.View) en la barra de navegación de una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), hay una serie de limitaciones que debe conocer:

- En iOS, las vistas colocadas en la barra de navegación de una `NavigationPage` se muestran en una posición diferente según si están habilitados los títulos de gran tamaño. Para obtener más información sobre cómo habilitar títulos grandes, consulte [Mostrar títulos grandes](~/xamarin-forms/platform/ios/page-large-title.md).
- En Android, colocar las vistas en la barra de navegación de una `NavigationPage` solo puede realizarse en las aplicaciones que utilizan la compatibilidad de aplicaciones.
- No se recomienda colocar vistas grandes y complejas, como [`ListView`](xref:Xamarin.Forms.ListView) y [`TableView`](xref:Xamarin.Forms.TableView), en la barra de navegación de una `NavigationPage`.

## <a name="related-links"></a>Vínculos relacionados

- [Navegación de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Jerárquica (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Vídeo sobre cómo crear un flujo de pantalla de inicio de sesión en Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
