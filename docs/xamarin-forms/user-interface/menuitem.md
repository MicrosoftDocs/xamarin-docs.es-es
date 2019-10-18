---
title: Xamarin. Forms MenuItem
description: La clase MenuItem se usa para crear elementos de menú para menús como menús contextuales de elementos de ListView y menús flotante de aplicación de Shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 5bc36f03eac4ced7c19a0053dfea93dbe2ca4497
ms.sourcegitcommit: 850dd7a3ed10eb3f66692e765d3e31438cff0288
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531011"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. Forms MenuItem

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La clase de [`MenuItem`](xref:Xamarin.Forms.MenuItem) de Xamarin. Forms define los elementos de menú para los menús, como los menús contextuales de elementos `ListView` y los menús contextuales de la aplicación Shell.

Las capturas de pantallas siguientes muestran `MenuItem` objetos en un menú contextual de `ListView` en iOS y Android:

[!["MenuItem en iOS y Android"](menuitem-images/menuitem-demo-cropped.png "MenuItem en iOS y Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItem en iOS y Android full image")

La clase `MenuItem` define las siguientes propiedades:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) es una `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) es una `object` que especifica el parámetro que se debe pasar a la `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) es un valor `ImageSource` que define el icono de presentación.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) es un valor `bool` que indica si el `MenuItem` quita su elemento de interfaz de usuario asociado de la lista.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled) es un valor `bool` que determina si este objeto responde a los datos proporcionados por el usuario.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) es un valor `string` que especifica el texto que se va a mostrar.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , por lo que la instancia de `MenuItem` puede ser el destino de los enlaces de datos.

## <a name="create-a-menuitem"></a>Crear un MenuItem

`MenuItem` objetos se pueden utilizar dentro de un menú contextual en los elementos de un objeto `ListView`. El patrón más común consiste en crear `MenuItem` objetos dentro de una instancia de `ViewCell`, que se utiliza como el objeto `DataTemplate` para el `ItemTemplate` de `ListView`s. Cuando se rellena el objeto de `ListView`, creará cada elemento mediante el `DataTemplate`, exponiendo las opciones de `MenuItem` cuando se active el menú contextual para un elemento.

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
