---
title: Páginas modales de Xamarin.Forms
description: Xamarin.Forms es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea. En este artículo se muestra cómo navegar a páginas modales.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ba5224c23eaed6b7c37312f8a765556d0263d9c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563034"
---
# <a name="no-locxamarinforms-modal-pages"></a>Páginas modales de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea. En este artículo se muestra cómo navegar a páginas modales._

En este artículo se tratan los siguientes temas:

- [Realizar la navegación](#performing-navigation): insertar páginas en la pila modal, sacar páginas de la pila modal, deshabilitar el botón Atrás y animar las transiciones de página.
- [Pasar datos al navegar](#passing-data-when-navigating): pasar datos a través de un constructor de página y de un `BindingContext`.

## <a name="overview"></a>Información general

Una página modal puede ser cualquiera de los tipos [Página](~/xamarin-forms/user-interface/controls/pages.md) compatibles con Xamarin.Forms. Para mostrar una página modal, la aplicación la insertará en la pila modal, donde se convertirá en la página activa, como se muestra en el siguiente diagrama:

![Inserción de una página en la pila modal](modal-images/pushing.png)

Para volver a la página anterior, la aplicación mostrará la página actual de la pila modal y la nueva página de nivel superior se convertirá en la página activa, tal como se muestra en el siguiente diagrama:

![Sacar una página de la pila modal](modal-images/popping.png)

## <a name="performing-navigation"></a>Realizar la navegación

Los métodos de navegación modal se exponen mediante la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) en cualquier tipo [`Page`](xref:Xamarin.Forms.Page) derivado. Estos métodos proporcionan la capacidad de [insertar páginas modales](#pushing-pages-to-the-modal-stack) en la pila modal, y [sacar páginas modales](#popping-pages-from-the-modal-stack) de la pila modal.

La propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) también expone una propiedad [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) desde la que se pueden obtener las páginas modales de la pila modal. Pero no existe el concepto de realizar una manipulación de pila modal o extraer la página raíz de la navegación modal. Esto se debe a que estas operaciones no se admiten de forma universal en las plataformas subyacentes.

> [!NOTE]
> No es necesaria una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para realizar la navegación de páginas modal.

### <a name="pushing-pages-to-the-modal-stack"></a>Inserción de páginas en la pila modal

Para navegar a `ModalPage`, es necesario invocar el método [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) en la propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Esto hace que la instancia de `ModalPage` se inserte en la pila modal, donde se convertirá en la página activa, siempre que se haya seleccionado un elemento en la [`ListView`](xref:Xamarin.Forms.ListView) en la instancia de `MainPage`. La instancia de `ModalPage` se muestra en la siguiente captura de pantalla:

![Ejemplo de página modal](modal-images/modalpage.png)

Al invocar [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*), ocurre lo siguiente:

- La página que llama a `PushModalAsync` invoca su invalidación [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing), siempre que la plataforma subyacente no sea Android.
- Se invoca la invalidación de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la página a la que se ha navegado.
- La tarea `PushAsync` finaliza.

Con todo, el orden exacto en el que se producen estos eventos depende de la plataforma. Para obtener más información, consulte el [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Las llamadas a [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) y las invalidaciones de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) no se pueden tratar como indicaciones garantizadas de navegación de páginas. Por ejemplo, en iOS, la invalidación de `OnDisappearing` se llama en la página activa cuando la aplicación finaliza.

### <a name="popping-pages-from-the-modal-stack"></a>Sacar páginas de la pila modal

La página activa se puede extraer de la pila modal. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla.

Para volver mediante programación a la página original, la instancia `ModalPage` debe invocar el método [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Esto hace que la instancia de `ModalPage` se quite de la pila modal y que la nueva página de nivel superior se convierta en la página activa. Al invocar [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), ocurre lo siguiente:

- Se invoca la invalidación de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) de la página que llama a `PopModalAsync`.
- Se invoca la invalidación de [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la página a la que se vuelve, siempre que la plataforma subyacente no sea Android.
- Se devuelve la tarea `PopModalAsync`.

Con todo, el orden exacto en el que se producen estos eventos depende de la plataforma. Para obtener más información, consulte el [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

### <a name="disabling-the-back-button"></a>Deshabilitación del botón Atrás

En Android, el usuario siempre puede volver a la página anterior presionando el botón *Atrás* estándar en el dispositivo. Si la página modal requiere que el usuario complete una tarea independiente antes de salir de la página, la aplicación debe deshabilitar el botón *Atrás*. Esto puede realizarse invalidando el método [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) en la página modal. Para obtener más información, consulte el [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro sobre Xamarin.Forms de Charles Petzold.

### <a name="animating-page-transitions"></a>Animación de transiciones de página

La propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de cada página también proporciona métodos de inserción y extracción invalidados que incluyen un parámetro `boolean` que controla si se debe mostrar una animación de página durante la navegación, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

Al establecer el parámetro `boolean` en `false`, la animación de transición de página se deshabilita, mientras que al establecer el parámetro en `true`, la animación de transición de página se habilita, siempre que la plataforma subyacente lo admita. Sin embargo, los métodos de inserción y extracción que carecen de este parámetro habilitan la animación de manera predeterminada.

## <a name="passing-data-when-navigating"></a>Pasar datos al navegar

A veces es necesario que una página pase datos a otra durante la navegación. Dos técnicas para llevar a cabo esto son pasar datos a través de un constructor de página y establecer el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página nueva en los datos. En las siguientes secciones se explicarán de uno en uno.

### <a name="passing-data-through-a-page-constructor"></a>Pasar datos a través de un constructor de página

La técnica más sencilla para pasar datos a otra página durante la navegación es hacerlo a través de un parámetro de constructor de página, que se muestra en el siguiente ejemplo de código:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Este código crea una instancia de `MainPage` y pasa la fecha y la hora actuales en formato ISO8601.

La instancia de `MainPage` recibe los datos a través de un parámetro de constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Después, se establece la propiedad [`Label.Text`](xref:Xamarin.Forms.Label.Text) para que los datos se muestren en la página.

### <a name="passing-data-through-a-bindingcontext"></a>Pasar datos a través de un objeto BindingContext

Un enfoque alternativo para pasar datos a otra página durante la navegación es establecer el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página nueva en los datos, como se muestra en el siguiente ejemplo de código:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Este código establece el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la instancia de `DetailPage` en la instancia de `Contact` y después navega a la `DetailPage`.

Después, la `DetailPage` utiliza el enlace de datos para mostrar los datos de la instancia de `Contact`, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En el ejemplo de código siguiente se muestra cómo se puede realizar el enlace de datos en C#:

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

Después, una serie de controles [`Label`](xref:Xamarin.Forms.Label) muestran los datos en la página.

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Aspectos básicos del enlace de datos).

## <a name="summary"></a>Resumen

En este artículo se mostró cómo navegar a páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.

## <a name="related-links"></a>Vínculos relacionados

- [Page Navigation](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) (Navegación de páginas)
- [Modal (sample)](/samples/xamarin/xamarin-forms-samples/navigation-modal) (Modal [ejemplo])
- [PassingData (sample)](/samples/xamarin/xamarin-forms-samples/navigation-passingdata) (PassingData [ejemplo])