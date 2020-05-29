---
title: Xamarin.FormsObjetos
description: ''
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de8c6bff2c9dc72821692708f5852cd874c31ede
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139209"
---
# <a name="xamarinforms-menuitem"></a>Xamarin.FormsObjetos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) clase define los elementos de menú para los menús como `ListView` menús contextuales de elementos y menús flotante de aplicación de Shell.

Las siguientes capturas `MenuItem` de pantallas muestran objetos en un `ListView` menú contextual de iOS y Android:

[!["MenuItem en iOS y Android"](menuitem-images/menuitem-demo-cropped.png "MenuItem en iOS y Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItem en la imagen completa de iOS y Android")

La `MenuItem` clase define las siguientes propiedades:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)es un `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)es un `object` que especifica el parámetro que se debe pasar a `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)es un `ImageSource` valor que define el icono de presentación.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)es un `bool` valor que indica si `MenuItem` quita su elemento de interfaz de usuario asociado de la lista.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)es un `bool` valor que indica si este objeto responde a la entrada del usuario.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)es un `string` valor que especifica el texto para mostrar.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, por lo que la `MenuItem` instancia puede ser el destino de los enlaces de datos.

## <a name="create-a-menuitem"></a>Crear un MenuItem

`MenuItem`los objetos se pueden usar dentro de un menú contextual en `ListView` los elementos de un objeto. El patrón más común es crear `MenuItem` objetos dentro de una `ViewCell` instancia de, que se utiliza como el `DataTemplate` objeto para los `ListView` s `ItemTemplate` . Cuando el `ListView` objeto se rellena, creará cada elemento con `DataTemplate` , exponiendo las `MenuItem` opciones cuando el menú contextual se active para un elemento.

En el ejemplo siguiente se muestra la `MenuItem` creación de instancias en el contexto de un `ListView` objeto:

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

`MenuItem`También se puede crear en el código:

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

La clase `MenuItem` expone un evento `Clicked`. Se puede adjuntar un controlador de eventos a este evento para reaccionar a los grifos o clics en la `MenuItem` instancia en XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

También se puede adjuntar un controlador de eventos en el código:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

En los ejemplos anteriores se hacía referencia a un `OnItemClicked` controlador de eventos. En el código siguiente se muestra una implementación de ejemplo:

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

La `MenuItem` clase admite el patrón Model-View-ViewModel (MVVM) a través de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) los objetos y la `ICommand` interfaz. En el siguiente código XAML `MenuItem` se muestran las instancias enlazadas a comandos definidos en un ViewModel:

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

En el ejemplo anterior, `MenuItem` se definen dos objetos con sus `Command` `CommandParameter` propiedades y enlazadas a comandos en el ViewModel. El ViewModel contiene los comandos a los que se hace referencia en el código XAML:

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

La aplicación de ejemplo incluye una `DataService` clase que se usa para obtener una lista de elementos para rellenar los `ListView` objetos. Se crea una instancia de ViewModel, con elementos de la `DataService` clase, y se establece como `BindingContext` en el código subyacente:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Iconos de MenuItem

> [!WARNING]
> `MenuItem`los objetos solo muestran iconos en Android. En otras plataformas, solo se mostrará el texto especificado por la `Text` propiedad.

 Los iconos se especifican mediante la `IconImageSource` propiedad. Si se especifica un icono, no se mostrará el texto especificado por la `Text` propiedad. En la captura de pantalla siguiente se muestra un `MenuItem` con un icono en Android:

!["Captura de pantalla del icono de MenuItem en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del icono de MenuItem en Android")

Para obtener más información sobre el uso de imágenes en Xamarin.Forms , vea [imágenes en Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>Habilitar o deshabilitar un MenuItem en tiempo de ejecución

Para habilitar la deshabilitación de un `MenuItem` en tiempo de ejecución, enlace su `Command` propiedad a una `ICommand` implementación de y asegúrese de que un `canExecute` delegado habilita y deshabilita `ICommand` según corresponda.

> [!IMPORTANT]
> No enlace la `IsEnabled` propiedad a otra propiedad cuando utilice la `Command` propiedad para habilitar o deshabilitar `MenuItem` .

En el ejemplo siguiente se muestra una `MenuItem` cuya `Command` propiedad se enlaza a una `ICommand` denominada `MyCommand` :

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

La `ICommand` implementación requiere un `canExecute` delegado que devuelve el valor de una `bool` propiedad para habilitar y deshabilitar `MenuItem` :

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

En este ejemplo, el `MenuItem` está deshabilitado hasta que `IsMenuItemEnabled` se establece la propiedad. Cuando esto ocurre, `Command.ChangeCanExecute` se llama al método, que hace que se `canExecute` vuelva a evaluar el delegado de `MyCommand` .

## <a name="cross-platform-context-menu-behavior"></a>Comportamiento del menú contextual entre plataformas

Se tiene acceso a los menús contextuales y se muestran de forma diferente en cada plataforma.

En Android, el menú contextual se activa en un elemento de lista. El menú contextual reemplaza el área de título y la barra de navegación, y `MenuItem` las opciones se muestran como botones horizontales.

!["Captura de pantalla del menú contextual en Android"](menuitem-images/menuitem-android-icon.png "Captura de pantalla del menú contextual en Android")

En iOS, el menú contextual se activa presionando el dedo en un elemento de lista. El menú contextual se muestra en el elemento de lista y `MenuItems` se muestra como botones horizontales.

!["Captura de pantalla del menú contextual en iOS"](menuitem-images/menuitem-ios-contextmenu.png "Captura de pantalla del menú contextual en iOS")

En UWP, el menú contextual se activa haciendo clic con el botón derecho en un elemento de la lista. El menú contextual se muestra cerca del cursor como una lista vertical.

!["Captura de pantalla del menú contextual en UWP"](menuitem-images/menuitem-uwp.png "Captura de pantalla del menú contextual en UWP")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Imágenes enXamarin.Forms](~/xamarin-forms/user-interface/images.md)
