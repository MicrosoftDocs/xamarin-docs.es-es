---
title: Página maestra y de detalles de Xamarin.Forms
description: 'MasterDetailPage de Xamarin.Forms es una página que administra dos páginas relacionadas de información: una página maestra que presenta elementos y una página de detalles que presenta detalles sobre los elementos de la página maestra. En este artículo se explica cómo usar una instancia de MasterDetailPage y cómo navegar entre sus páginas de información.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a65e9bb90f01bcb5e0b1182a21d998e2335da9a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934529"
---
# <a name="xamarinforms-master-detail-page"></a>Página maestra y de detalles de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_MasterDetailPage de Xamarin.Forms es una página que administra dos páginas relacionadas de información: una página maestra que presenta elementos y una página de detalles que presenta detalles sobre los elementos de la página maestra. En este artículo se explica cómo usar una instancia de MasterDetailPage y cómo navegar entre sus páginas de información._

## <a name="overview"></a>Información general

Normalmente, una página maestra presenta una lista de elementos, como se muestra en las siguientes capturas de pantalla:

[![Componentes de la página maestra](master-detail-page-images/masterpage-components.png)](master-detail-page-images/masterpage-components-large.png#lightbox "Componentes de la página maestra")

La ubicación de la lista de elementos es idéntica en cada plataforma; al seleccionar uno de los elementos, se le lleva a la página de detalles correspondiente. Además, la página maestra también incluye una barra de navegación que contiene un botón que se puede usar para ir a la página de detalles activa:

- En iOS, la barra de navegación se encuentra en la parte superior de la página y tiene un botón que lleva a la página de detalles. Además, se puede ir a la página de detalles activa si se desliza la página maestra hacia la izquierda.
- En Android, la barra de navegación se encuentra en la parte superior de la página y presenta un título, un icono y un botón que lleva a la página de detalles. El icono se define en el atributo `[Activity]` que decora la clase `MainActivity` en el proyecto específico de la plataforma Android. Además, se puede ir a la página de detalles activa si se desliza la página maestra hacia la izquierda, si se puntea en el extremo derecho de la pantalla en la página de detalles y si se pulsa el botón *Atrás* situado en la parte inferior de la pantalla.
- En Plataforma universal de Windows (UWP), la barra de navegación se encuentra en la parte superior de la página y tiene un botón que lleva a la página de detalles.

Una página de detalles presenta datos correspondientes al elemento seleccionado en la página maestra; los componentes principales de la página de detalles se muestran en las capturas de pantalla siguientes:

![Componentes de página de detalles](master-detail-page-images/detailpage-components.png)

La página de detalles contiene una barra de navegación cuyo contenido depende de la plataforma:

- En iOS, la barra de navegación se encuentra en la parte superior de la página, muestra un título y tiene un botón que devuelve a la página maestra, siempre que la instancia de la página de detalles esté incluida en la instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Además, se puede volver a la página maestra si se desliza la página de detalles hacia la derecha.
- En Android, hay una barra de navegación en la parte superior de la página que muestra un título, un icono y un botón que devuelve a la página maestra. El icono se define en el atributo `[Activity]` que decora la clase `MainActivity` en el proyecto específico de la plataforma Android.
- En UWP, la barra de navegación se encuentra en la parte superior de la página, muestra un título y tiene un botón que devuelve a la página maestra.

### <a name="navigation-behavior"></a>Comportamiento de navegación

El comportamiento de la experiencia de navegación entre las páginas maestra y de detalles depende de la plataforma:

- En iOS, la página de detalles *se desliza* hacia la derecha cuando la página maestra se desliza desde la izquierda, y la parte izquierda de la página de detalles sigue siendo visible.
- En Android, las páginas maestra y de detalles se *superponen*.
- En UWP, la página maestra se desplaza desde el lateral izquierdo de la página de detalles, siempre que la propiedad [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) esté establecida en `Popover`. Para obtener más información, consulte [Control del comportamiento de presentación de la página de detalles](#controlling-the-detail-page-display-behavior).

En el modo horizontal se observa un comportamiento similar, salvo que la página maestra en iOS y Android tiene un ancho similar al de la página maestra en modo vertical, así que se ve más superficie de la página de detalles.

Para obtener información sobre cómo controlar el comportamiento de navegación, vea [Control del comportamiento de presentación de la página de detalles](#controlling-the-detail-page-display-behavior).

## <a name="creating-a-masterdetailpage"></a>Creación de MasterDetailPage

Una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) contiene propiedades [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) y [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) que son de tipo [`Page`](xref:Xamarin.Forms.Page) y se usan para obtener y establecer las páginas maestra y de detalles, respectivamente.

> [!IMPORTANT]
> Una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) está diseñada para ser una página raíz. Su empleo como página secundaria en otros tipos de páginas podría dar lugar a un comportamiento inesperado e incoherente. Además, se recomienda que la página maestra de una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) siempre sea una instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage) y que la página de detalles solo se rellene con instancias de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y `ContentPage`. Esto ayuda a garantizar una experiencia de usuario coherente en todas las plataformas.

El ejemplo de código XAML siguiente muestra una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) que establece las propiedades [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) y [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail):

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

En el ejemplo de código siguiente se muestra la instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente creada en C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

La propiedad [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) está establecida en una instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage). La propiedad [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) está establecida en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contiene una instancia de `ContentPage`.

### <a name="creating-the-master-page"></a>Creación de la página maestra

El ejemplo de código XAML siguiente muestra la declaración del objeto `MasterPage`, al que se hace referencia mediante la propiedad [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

La página consta de un elemento [`ListView`](xref:Xamarin.Forms.ListView) que se rellena con datos de XAML al establecer su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) en una matriz de instancias de `MasterPageItem`. Cada `MasterPageItem` define propiedades `Title`, `IconSource` y `TargetType`.

Se asigna un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) para mostrar cada `MasterPageItem`. `DataTemplate` contiene un elemento [`ViewCell`](xref:Xamarin.Forms.ViewCell) que consta de un elemento [`Image`](xref:Xamarin.Forms.Image) y un [`Label`](xref:Xamarin.Forms.Label). [`Image`](xref:Xamarin.Forms.Image) muestra el valor de la propiedad `IconSource` y [`Label`](xref:Xamarin.Forms.Label) muestra el valor de la propiedad `Title`, para cada `MasterPageItem`.

La página tiene establecidas sus propiedades [`Title`](xref:Xamarin.Forms.Page.Title) y [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource). El icono aparece en la página de detalles, siempre que esta tenga una barra de título. Esta debe habilitarse en iOS al incluir la instancia de la página de detalles en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!NOTE]
> La página [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) debe tener su propiedad [`Title`](xref:Xamarin.Forms.Page.Title) establecida, o se produce una excepción.

En el ejemplo de código siguiente se muestra la página equivalente creada en C#:

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

En las capturas de pantalla siguientes se muestra la página maestra en cada plataforma:

![Ejemplo de página maestra](master-detail-page-images/masterpage.png)

### <a name="creating-and-displaying-the-detail-page"></a>Creación y presentación de la página de detalles

La instancia de `MasterPage` contiene una propiedad `ListView` que expone su instancia de [`ListView`](xref:Xamarin.Forms.ListView) para que el elemento `MainPage` de la instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) pueda registrar un controlador de eventos para controlar el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Esto permite a la instancia de `MainPage` establecer la propiedad [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) en la página que representa al elemento seleccionado `ListView`. En el ejemplo de código siguiente se muestra el controlador de eventos:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

El método `OnItemSelected` realiza las siguientes acciones:

- Recupera el elemento [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de la instancia de [`ListView`](xref:Xamarin.Forms.ListView) y, siempre que no sea `null`, establece la página de detalles en una nueva instancia del tipo de página almacenado en la propiedad `TargetType` de `MasterPageItem`. El tipo de página se incluye en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para asegurarse de que el icono al que se hace referencia mediante la propiedad [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) en `MasterPage` se muestre en la página de detalles en iOS.
- El elemento seleccionado en [`ListView`](xref:Xamarin.Forms.ListView) se estable en `null` para asegurarse de que ninguno de los elementos `ListView` se seleccione la próxima vez que el elemento `MasterPage` esté presente.
- La página de detalles se presenta al usuario al establecer la propiedad [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) en `false`. Esta propiedad controla si se presenta la página maestra o de detalles. Se debe establecer en `true` para mostrar la página maestra y en `false` para mostrar la página de detalles.

Las capturas de pantalla siguientes muestran la página de detalles `ContactPage`, que se presenta después de haberse seleccionado en la página maestra:

![Ejemplo de página de detalles](master-detail-page-images/detailpage.png)

### <a name="controlling-the-detail-page-display-behavior"></a>Control del comportamiento de presentación de la página de detalles

La forma en que [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) administre las páginas maestra y de detalles depende de si la aplicación se ejecuta en un teléfono o tableta, de la orientación del dispositivo y del valor de la propiedad [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior). Esta propiedad determina cómo se muestra la página de detalles. Sus posibles valores son:

- **Default**: las páginas se muestran con el valor predeterminado de la plataforma.
- **Popover**: la página de detalles cubre, o cubre parcialmente, la página maestra.
- **Split**: la página maestra se muestra a la izquierda y la página de detalles a la derecha.
- **SplitOnLandscape**: se usa una pantalla dividida cuando el dispositivo está en orientación horizontal.
- **SplitOnPortrait**: se usa una pantalla dividida cuando el dispositivo está en orientación vertical.

En el siguiente ejemplo de código XAML se muestra cómo establecer la propiedad [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) en una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

En el ejemplo de código siguiente se muestra la instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente creada en C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

Pero el valor de la propiedad [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) solo afecta a las aplicaciones que se ejecutan en el escritorio o en tabletas. Las aplicaciones que se ejecutan en teléfonos siempre tienen el comportamiento *Popover*.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar una instancia de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) y cómo navegar entre sus páginas de información. `MasterDetailPage` de Xamarin.Forms es una página que administra dos páginas de información relacionada: una página maestra que presenta elementos y una página de detalles que muestra detalles sobre elementos de la página maestra.

## <a name="related-links"></a>Vínculos relacionados

- [Páginas de Xamarin.Forms](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
