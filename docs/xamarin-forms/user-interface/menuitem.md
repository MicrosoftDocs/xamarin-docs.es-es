---
title: Xamarin. Forms MenuItem
description: La clase MenuItem se usa para crear elementos de menú para menús como menús contextuales de elementos de ListView y menús flotante de aplicación de Shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: b4690feb6444405d090a0b2bafd6c8615b2ffa8b
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2020
ms.locfileid: "77567071"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. Forms MenuItem

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La clase de [`MenuItem`](xref:Xamarin.Forms.MenuItem) de Xamarin. Forms define los elementos de menú para los menús, como los menús contextuales de elementos `ListView` y los menús contextuales de la aplicación Shell.

Las capturas de pantallas siguientes muestran `MenuItem` objetos en un menú contextual de `ListView` en iOS y Android:

[!["MenuItem en iOS y Android"](menuitem-images/menuitem-demo-cropped.png "MenuItem en iOS y Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItem en la imagen completa de iOS y Android")

La clase `MenuItem` define las siguientes propiedades:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) es una `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) es una `object` que especifica el parámetro que se debe pasar a la `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) es un valor `ImageSource` que define el icono de presentación.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) es un valor `bool` que indica si el `MenuItem` quita su elemento de interfaz de usuario asociado de la lista.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled) es un valor `bool` que indica si este objeto responde a la entrada del usuario.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) es un valor `string` que especifica el texto que se va a mostrar.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , por lo que la instancia de `MenuItem` puede ser el destino de los enlaces de datos.

## <a name="create-a-menuitem"></a>Crear un MenuItem

`MenuItem` objetos se pueden utilizar dentro de un menú contextual en los elementos de un objeto `ListView`. El patrón más común es crear `MenuItem` objetos dentro de una instancia de `ViewCell`, que se usa como el objeto `DataTemplate` para el `ItemTemplate``ListView`s. Cuando se rellena el objeto de `ListView`, creará cada elemento mediante el `DataTemplate`, exponiendo las opciones de `MenuItem` cuando se active el menú contextual para un elemento.

En el ejemplo siguiente se muestra la creación de instancias de `MenuItem` en el contexto de un objeto `ListView`:

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

También se puede crear un `MenuItem` en el código:

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

La clase `MenuItem` expone un evento `Clicked`. Se puede adjuntar un controlador de eventos a este evento para reaccionar a los grifos o clics en la instancia de `MenuItem` en XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

También se puede adjuntar un controlador de eventos en el código:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

En los ejemplos anteriores se hacía referencia a un controlador de eventos `OnItemClicked`. En el código siguiente se muestra una implementación de ejemplo:

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

La clase `MenuItem` admite el patrón Model-View-ViewModel (MVVM) a través de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos y la interfaz `ICommand`. En el siguiente código XAML se muestran `MenuItem` instancias enlazadas a comandos definidos en un ViewModel:

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

En el ejemplo anterior, dos objetos `MenuItem` se definen con sus `Command` y `CommandParameter` propiedades enlazadas a los comandos del ViewModel. El ViewModel contiene los comandos a los que se hace referencia en el código XAML:

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

La aplicación de ejemplo incluye una clase `DataService` utilizada para obtener una lista de elementos para rellenar los objetos `ListView`. Se crea una instancia de ViewModel, con elementos de la clase `DataService`, y se establece como `BindingContext` en el código subyacente:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Iconos de MenuItem

> [!WARNING]
> `MenuItem` objetos solo muestran iconos en Android. En otras plataformas, solo se mostrará el texto especificado por la propiedad `Text`.

 Los iconos se especifican mediante la propiedad `IconImageSource`. Si se especifica un icono, no se mostrará el texto especificado por la propiedad `Text`. En la captura de pantalla siguiente se muestra un `MenuItem` con un icono en Android:

!["Captura de pantalla del icono de MenuItem en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del icono de MenuItem en Android")

Para obtener más información sobre el uso de imágenes en Xamarin. Forms, consulte [imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>Habilitar o deshabilitar un MenuItem en tiempo de ejecución

Para habilitar la deshabilitación de un `MenuItem` en tiempo de ejecución, enlace su propiedad `Command` a una implementación de `ICommand` y asegúrese de que un delegado `canExecute` habilite y deshabilite la `ICommand` según corresponda.

> [!IMPORTANT]
> No enlace la propiedad `IsEnabled` a otra propiedad al utilizar la propiedad `Command` para habilitar o deshabilitar la `MenuItem`.

En el ejemplo siguiente se muestra un `MenuItem` cuya propiedad `Command` se enlaza a una `ICommand` denominada `MyCommand`:

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

La implementación de `ICommand` requiere un delegado de `canExecute` que devuelva el valor de una propiedad `bool` para habilitar y deshabilitar el `MenuItem`:

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

En este ejemplo, el `MenuItem` está deshabilitado hasta que se establece la propiedad `IsMenuItemEnabled`. Cuando esto ocurre, se llama al método `Command.ChangeCanExecute` que hace que se vuelva a evaluar el delegado de `canExecute` para `MyCommand`.

## <a name="cross-platform-context-menu-behavior"></a>Comportamiento del menú contextual entre plataformas

Se tiene acceso a los menús contextuales y se muestran de forma diferente en cada plataforma.

En Android, el menú contextual se activa en un elemento de lista. El menú contextual reemplaza el área de título y de la barra de navegación y `MenuItem` opciones se muestran como botones horizontales.

!["Captura de pantalla del menú contextual en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del menú contextual en Android")

En iOS, el menú contextual se activa presionando el dedo en un elemento de lista. El menú contextual se muestra en el elemento de lista y `MenuItems` se muestran como botones horizontales.

!["Captura de pantalla del menú contextual en iOS"](menuitem-images/menuitem-ios-contextmenu.png "Captura de pantalla del menú contextual en iOS")

En UWP, el menú contextual se activa haciendo clic con el botón derecho en un elemento de la lista. El menú contextual se muestra cerca del cursor como una lista vertical.

!["Captura de pantalla del menú contextual en UWP"](menuitem-images/menuitem-uwp.png "Captura de pantalla del menú contextual en UWP")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
