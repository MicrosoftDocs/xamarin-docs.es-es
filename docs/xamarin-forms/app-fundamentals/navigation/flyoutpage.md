---
title: FlyoutPage de Xamarin.Forms
description: 'FlyoutPage de Xamarin.Forms es una página que administra dos páginas relacionadas de información: una página de control flotante que presenta elementos y una página de detalles en la que se muestran detalles sobre los elementos de la página de control flotante. En este artículo se explica cómo usar una instancia de FlyoutPage y cómo navegar entre sus páginas de información.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a2947ef2c326c57bc646cd980109ed9a1b97347
ms.sourcegitcommit: 9ab5a1e346e20f54e8b7aa655fd3d117b43978cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223716"
---
# <a name="xamarinforms-flyoutpage"></a>FlyoutPage de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)

Normalmente, una página de control flotante presenta una lista de elementos, como se muestra en las siguientes capturas de pantalla:

[![Componentes de FlyoutPage](flyoutpage-images/flyoutpage-components.png)](flyoutpage-images/flyoutpage-components-large.png#lightbox "Componentes de FlyoutPage")

La ubicación de la lista de elementos es idéntica en cada plataforma; al seleccionar uno de los elementos, se le lleva a la página de detalles correspondiente. Además, la página de control flotante también incluye una barra de navegación que contiene un botón que se puede usar para ir a la página de detalles activa:

- En iOS, la barra de navegación se encuentra en la parte superior de la página y tiene un botón que lleva a la página de detalles. Además, se puede ir a la página de detalles activa si se desliza el control flotante hacia la izquierda.
- En Android, la barra de navegación se encuentra en la parte superior de la página y presenta un título, un icono y un botón que lleva a la página de detalles. El icono se define en el atributo `[Activity]` que decora la clase `MainActivity` en el proyecto específico de la plataforma Android. Además, se puede ir a la página de detalles activa si se desliza la página de control flotante hacia la izquierda, si se pulsa en el extremo derecho de la pantalla en la página de detalles y si se pulsa el botón *Atrás* situado en la parte inferior de la pantalla.
- En Plataforma universal de Windows (UWP), la barra de navegación se encuentra en la parte superior de la página y tiene un botón que lleva a la página de detalles.

Una página de detalles presenta datos correspondientes al elemento seleccionado en la página de control flotante; los componentes principales de la página de detalles se muestran en las capturas de pantalla siguientes:

![Componentes de página de detalles](flyoutpage-images/detailpage-components.png)

La página de detalles contiene una barra de navegación cuyo contenido depende de la plataforma:

- En iOS, la barra de navegación se encuentra en la parte superior de la página, muestra un título y tiene un botón que devuelve a la página de control flotante, siempre que la instancia de la página de detalles esté incluida en la instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Además, se puede volver a la página de control flotante si se desliza la página de detalles hacia la derecha.
- En Android, hay una barra de navegación en la parte superior de la página que muestra un título, un icono y un botón que devuelve a la página de control flotante. El icono se define en el atributo `[Activity]` que decora la clase `MainActivity` en el proyecto específico de la plataforma Android.
- En UWP, la barra de navegación se encuentra en la parte superior de la página, muestra un título y tiene un botón que devuelve a la página de control flotante.

## <a name="navigation-behavior"></a>Comportamiento de navegación

El comportamiento de la experiencia de navegación entre la página de control flotante y la de detalles depende de la plataforma:

- En iOS, la página de detalles *se desliza* hacia la derecha cuando la página de control flotante se desliza desde la izquierda, y la parte izquierda de la página de detalles sigue siendo visible.
- En Android, la página de control flotante y la de detalles se *superponen*.
- En UWP, la página de control flotante se desplaza desde el lateral izquierdo de la página de detalles, siempre que la propiedad [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) esté establecida en `Popover`.

En el modo horizontal se observa un comportamiento similar, salvo que la página de control flotante en iOS y Android tiene un ancho similar al de la página de control flotante en modo vertical, así que se ve más superficie de la página de detalles.

Para obtener información sobre cómo controlar el comportamiento de navegación, vea [Control del comportamiento de diseño de la página de detalles](#control-the-detail-page-layout-behavior).

## <a name="create-a-flyoutpage"></a>Creación de una instancia de FlyoutPage

Una instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) contiene propiedades [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) y [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) que son de tipo [`Page`](xref:Xamarin.Forms.Page) y se usan para obtener y establecer la página de control flotante y la de detalles, respectivamente.

> [!IMPORTANT]
> Una instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) está diseñada para ser una página raíz. Su empleo como página secundaria en otros tipos de páginas podría dar lugar a un comportamiento inesperado e incoherente. Además, se recomienda que la página de control flotante de una instancia de `FlyoutPage` siempre sea una instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage) y que la página de detalles solo se rellene con instancias de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) y `ContentPage`. Esto ayuda a garantizar una experiencia de usuario coherente en todas las plataformas.

El ejemplo de código XAML siguiente muestra una instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) que establece las propiedades [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) y [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail):

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:FlyoutPageNavigation;assembly=FlyoutPageNavigation"
            x:Class="FlyoutPageNavigation.MainPage">
    <FlyoutPage.Flyout>
        <local:FlyoutMenuPage x:Name="flyoutPage" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

En el ejemplo de código siguiente se muestra la instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) equivalente creada en C#:

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        flyoutPage = new FlyoutMenuPageCS();
        Flyout = flyoutPage;
        Detail = new NavigationPage(new ContactsPageCS());
        ...
    }
    ...
}    
```

La propiedad [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) está establecida en una instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage). La propiedad [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) está establecida en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que contiene una instancia de `ContentPage`.

### <a name="create-the-flyout-page"></a>Creación de la página de control flotante

El ejemplo de código XAML siguiente muestra la declaración del objeto `FlyoutMenuPage`, al que se hace referencia mediante la propiedad [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:FlyoutPageNavigation"
             x:Class="FlyoutPageNavigation.FlyoutMenuPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:FlyoutPageItem}">
                    <local:FlyoutPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:FlyoutPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:FlyoutPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
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

La página consta de un elemento [`ListView`](xref:Xamarin.Forms.ListView) que se rellena con datos de XAML al establecer su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) en una matriz de objetos de `FlyoutPageItem`. Cada `FlyoutPageItem` define propiedades `Title`, `IconSource` y `TargetType`.

Se asigna un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) para mostrar cada `FlyoutPageItem`. `DataTemplate` contiene un elemento [`ViewCell`](xref:Xamarin.Forms.ViewCell) que consta de un elemento [`Image`](xref:Xamarin.Forms.Image) y un [`Label`](xref:Xamarin.Forms.Label). [`Image`](xref:Xamarin.Forms.Image) muestra el valor de la propiedad `IconSource` y [`Label`](xref:Xamarin.Forms.Label) muestra el valor de la propiedad `Title`, para cada `FlyoutPageItem`.

La página tiene establecidas sus propiedades [`Title`](xref:Xamarin.Forms.Page.Title) y [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource). El icono aparece en la página de detalles, siempre que esta tenga una barra de título. Esta debe habilitarse en iOS al incluir la instancia de la página de detalles en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!NOTE]
> La página [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) debe tener su propiedad [`Title`](xref:Xamarin.Forms.Page.Title) establecida, o se produce una excepción.

En el ejemplo de código siguiente se muestra la página equivalente creada en C#:

```csharp
public class FlyoutMenuPageCS : ContentPage
{
    ListView listView;
    public ListView ListView { get { return listView; } }

    public FlyoutMenuPageCS()
    {
        var flyoutPageItems = new List<FlyoutPageItem>();
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Contacts",
            IconSource = "contacts.png",
            TargetType = typeof(ContactsPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "TodoList",
            IconSource = "todo.png",
            TargetType = typeof(TodoListPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Reminders",
            IconSource = "reminders.png",
            TargetType = typeof(ReminderPageCS)
        });

        listView = new ListView
        {
            ItemsSource = flyoutPageItems,
            ItemTemplate = new DataTemplate(() =>
            {
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
        Padding = new Thickness(0, 40, 0, 0);
        Content = new StackLayout
        {
            Children = { listView }
        };
    }
}
```

En las capturas de pantalla siguientes se muestra la página de control flotante en cada plataforma:

![Ejemplo de página de control flotante](flyoutpage-images/flyoutpage.png)

### <a name="create-and-display-the-detail-page"></a>Creación y visualización de la página de detalles

La instancia de `FlyoutMenuPage` contiene una propiedad `ListView` que expone su instancia de [`ListView`](xref:Xamarin.Forms.ListView) para que el elemento `MainPage` de la instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) pueda registrar un controlador de eventos para controlar el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Esto permite a la instancia de `MainPage` establecer la propiedad [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) en la página que representa al elemento seleccionado `ListView`. En el ejemplo de código siguiente se muestra el controlador de eventos:

```csharp
public partial class MainPage : FlyoutPage
{
    public MainPage()
    {
        ...
        flyoutPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as FlyoutPageItem;
        if (item != null)
        {
            Detail = new NavigationPage((Page)Activator.CreateInstance(item.TargetType));
            flyoutPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

El método `OnItemSelected` realiza las siguientes acciones:

- Recupera el elemento [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de la instancia de [`ListView`](xref:Xamarin.Forms.ListView) y, siempre que no sea `null`, establece la página de detalles en una nueva instancia del tipo de página almacenado en la propiedad `TargetType` de `FlyoutPageItem`. El tipo de página se incluye en una instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para asegurarse de que el icono al que se hace referencia mediante la propiedad [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) en `FlyoutMenuPage` se muestre en la página de detalles en iOS.
- El elemento seleccionado en [`ListView`](xref:Xamarin.Forms.ListView) se estable en `null` para asegurarse de que ninguno de los elementos `ListView` se seleccione la próxima vez que el elemento `FlyoutMenuPage` esté presente.
- La página de detalles se presenta al usuario al establecer la propiedad [`FlyoutPage.IsPresented`](xref:Xamarin.Forms.FlyoutPage.IsPresented) en `false`. Esta propiedad controla si se presenta la página de control flotante o de detalles. Se debe establecer en `true` para mostrar la página de control flotante y en `false` para mostrar la página de detalles.

Las capturas de pantalla siguientes muestran la página de detalles `ContactPage`, que se presenta después de haberse seleccionado en la página de control flotante:

![Ejemplo de página de detalles](flyoutpage-images/detailpage.png)

### <a name="control-the-detail-page-layout-behavior"></a>Control del comportamiento de diseño de la página de detalles

La forma en que [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) administre la página de control flotante y la de detalles depende de si la aplicación se ejecuta en un teléfono o tableta, de la orientación del dispositivo y del valor de la propiedad [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior). Esta propiedad determina cómo se muestra la página de detalles. Sus posibles valores son:

- `Default`: las páginas se muestran con el valor predeterminado de la plataforma.
- `Popover`: la página de detalles cubre total o parcialmente la página de control flotante.
- `Split`: la página de control flotante se muestra a la izquierda y la página de detalles a la derecha.
- `SplitOnLandscape`: se usa una pantalla dividida cuando el dispositivo está en orientación horizontal.
- `SplitOnPortrait`: se usa una pantalla dividida cuando el dispositivo está en orientación vertical.

En el siguiente ejemplo de código XAML se muestra cómo establecer la propiedad [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) en una instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage):

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            x:Class="FlyoutPageNavigation.MainPage"
            FlyoutLayoutBehavior="Popover">
  ...
</FlyoutPage>
```

En el ejemplo de código siguiente se muestra la instancia de [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) equivalente creada en C#:

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        ...
        FlyoutLayoutBehavior = FlyoutLayoutBehavior.Popover;
    }
}
```

> [!IMPORTANT]
> El valor de la propiedad [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) solo afecta a las aplicaciones que se ejecutan en el escritorio o en tabletas. Las aplicaciones que se ejecutan en teléfonos siempre tienen el comportamiento `Popover`.

## <a name="related-links"></a>Vínculos relacionados

- [Páginas de Xamarin.Forms](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [FlyoutPage (ejemplo)](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)
- [API FlyoutPage](xref:Xamarin.Forms.FlyoutPage)
