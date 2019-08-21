---
title: Xamarin. Forms MenuItem
description: La clase MenuItem se usa para crear elementos de menú para menús como menús contextuales de elementos de ListView y menús flotante de aplicación de Shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 396853e2e61904689069b7abd157c73d1da59fed
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658092"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. Forms MenuItem

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)

La clase de Xamarin [`MenuItem`](xref:Xamarin.Forms.MenuItem) . Forms define los elementos de menú `ListView` para menús como menús contextuales de elementos y menús flotante de aplicación de Shell.

Las siguientes capturas `MenuItem` de pantallas muestran `ListView` objetos en un menú contextual de iOS y Android:

[ !["MenuItem en iOS y Android"](menuitem-images/menuitem-demo-cropped.png "MenuItem en iOS y Android") ] (menuitem-images/menuitem-demo-full.png#lightbox "MenuItem en iOS y Androidfull image")

La `MenuItem` clase define las siguientes propiedades:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)es un `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)es un `object` que especifica el parámetro que se debe pasar `Command`a.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)es un `ImageSource` valor que define el icono de presentación.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)es un `bool` valor que indica `MenuItem` si quita su elemento de interfaz de usuario asociado de la lista.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)es un `bool` valor que determina si este objeto responde a los datos proporcionados por el usuario.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)es un `string` valor que especifica el texto para mostrar.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, por `MenuItem` lo que la instancia puede ser el destino de los enlaces de datos.

## <a name="create-a-menuitem"></a>Crear un MenuItem

`MenuItem`los objetos se pueden usar dentro de un menú contextual en los elementos de un `ListView` objeto. El patrón `MenuItem` más común es crear objetos dentro de una `ViewCell` instancia de, que se utiliza como el `DataTemplate` objeto para `ListView`los `ItemTemplate`s. Cuando el `ListView` objeto se rellena `DataTemplate`, creará cada elemento con, exponiendo las `MenuItem` opciones cuando el menú contextual se active para un elemento.

En el ejemplo siguiente `MenuItem` se muestra la creación de instancias en `ListView` el contexto de un objeto:

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

También `MenuItem` se puede crear en el código:

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>Definir el comportamiento de MenuItem con eventos

La clase `MenuItem` expone un evento `Clicked`. Se puede adjuntar un controlador de eventos a este evento para reaccionar a los `MenuItem` grifos o clics en la instancia en XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

También se puede adjuntar un controlador de eventos en el código:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

En los ejemplos anteriores se `OnItemClicked` hacía referencia a un controlador de eventos. En el código siguiente se muestra una implementación de ejemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>Definir el comportamiento de MenuItem con MVVM

La `MenuItem` clase admite el patrón Model-View-ViewModel (MVVM) a [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) través de los `ICommand` objetos y la interfaz. En el siguiente código `MenuItem` XAML se muestran las instancias enlazadas a comandos definidos en un ViewModel:

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

En el ejemplo anterior, se `MenuItem` definen dos objetos con sus `Command` propiedades `CommandParameter` y enlazadas a comandos en el ViewModel. El ViewModel contiene los comandos a los que se hace referencia en el código XAML:

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

La aplicación de ejemplo incluye `DataService` una clase que se usa para obtener una lista de elementos `ListView` para rellenar los objetos. Se crea una instancia de ViewModel, con elementos de `DataService` la clase, y se establece `BindingContext` como en el código subyacente:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Iconos de MenuItem

> [!WARNING]
> `MenuItem`los objetos solo muestran iconos en Android. En otras plataformas, solo se mostrará el texto especificado `Text` por la propiedad.

 Los iconos se especifican `IconImageSource` mediante la propiedad. Si se especifica un icono, no se mostrará el texto `Text` especificado por la propiedad. En la captura de pantalla `MenuItem` siguiente se muestra un con un icono en Android:

!["Captura de pantalla del icono de MenuItem en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del icono de MenuItem en Android")

Para obtener más información sobre el uso de imágenes en Xamarin. Forms, consulte [imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="cross-platform-context-menu-behavior"></a>Comportamiento del menú contextual entre plataformas

Se tiene acceso a los menús contextuales y se muestran de forma diferente en cada plataforma.

En Android, el menú contextual se activa en un elemento de lista. El menú contextual reemplaza el área de título y la `MenuItem` barra de navegación, y las opciones se muestran como botones horizontales.

!["Captura de pantalla del menú contextual en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del menú contextual en Android")

En iOS, el menú contextual se activa presionando el dedo en un elemento de lista. El menú contextual se muestra en el elemento de `MenuItems` lista y se muestra como botones horizontales.

!["Captura de pantalla del menú contextual en iOS"](menuitem-images/menuitem-ios-contextmenu.png "Captura de pantalla del menú contextual en iOS")

En UWP, el menú contextual se activa haciendo clic con el botón derecho en un elemento de la lista. El menú contextual se muestra cerca del cursor como una lista vertical.

!["Captura de pantalla del menú contextual en UWP"](menuitem-images/menuitem-uwp.png "Captura de pantalla del menú contextual en UWP")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de MenuItem](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)
* [Imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
