---
title: " Xamarin.Forms ToolbarItem" Description: "la clase ToolbarItem es un tipo especial de botón que se usa en la barra de navegación de una aplicación".
MS. Prod: Xamarin ms. AssetID: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1 ms. Technology: Xamarin-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 07/29/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-toolbaritem"></a>Xamarin.FormsToolbarItem

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) clase es un tipo especial de botón que se puede Agregar a la `Page` colección de un objeto `ToolbarItems` . Cada `ToolbarItem` objeto aparecerá como un botón en la barra de navegación de la aplicación. Una `ToolbarItem` instancia puede tener un icono y aparecer como un elemento de menú primario o secundario. La `ToolbarItem` clase hereda de [`MenuItem`](xref:Xamarin.Forms.MenuItem) .

Las siguientes capturas `ToolbarItem` de pantallas muestran objetos en la barra de navegación de iOS y Android:

![Captura de pantalla de demostración de ToolbarItem en Android e iOS](toolbaritem-images/toolbaritem-device-screenshot.png "Captura de pantalla de la demostración de ToolbarItem en Android e iOS")

La `ToolbarItem` clase define las siguientes propiedades:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)es un `ToolbarItemOrder` valor de enumeración que determina si la `ToolbarItem` instancia de se muestra en el menú principal o en el secundario.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)es un `integer` valor que determina el orden de presentación de los elementos de `Page` la colección de un objeto `ToolbarItems` .

La `ToolbarItem` clase hereda las siguientes propiedades utilizadas normalmente de la `MenuItem` clase:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)es un `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)es un `object` que especifica el parámetro que se debe pasar a `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)es un `ImageSource` valor que determina el icono de presentación de un `ToolbarItem` objeto.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)es un `string` que determina el texto que se va a mostrar en un `ToolbarItem` objeto.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, por lo que una `ToolbarItem` instancia de puede ser el destino de los enlaces de datos.

> [!NOTE]
> Una alternativa a la creación de una barra de herramientas a partir de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos es establecer la [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propiedad adjunta en una clase de diseño que contiene varias vistas. Para obtener más información, vea [Mostrar vistas en la barra de navegación](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Crear un ToolbarItem

`ToolbarItem`Se puede crear una instancia de un objeto en XAML. Las `Text` `IconImageSource` propiedades y se pueden establecer para determinar cómo se muestra el botón en la barra de navegación. En el ejemplo siguiente se muestra cómo crear una instancia de `ToolbarItem` con algunas propiedades comunes establecidas y agregarla a `ContentPage` una `ToolbarItems` colección de:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

En este ejemplo se generará un `ToolbarItem` objeto con texto, un icono y aparecerá en primer lugar en el área de la barra de navegación principal. `ToolbarItem`También se puede crear un en el código y agregarlo a la `ToolbarItems` colección:

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

El archivo representado por `string` , proporcionado como la `IconImageSource` propiedad, debe existir en cada proyecto de plataforma.

> [!NOTE]
> Los recursos de imagen se tratan de forma diferente en cada plataforma. `ImageSource`Puede proviene de orígenes que incluyen un archivo local o un recurso incrustado, un URI o un flujo. Para obtener más información sobre cómo establecer la `IconImageSource` propiedad y las imágenes en Xamarin.Forms , vea [imágenes en Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Definir el comportamiento del botón

La `ToolbarItem` clase hereda el `Clicked` evento de la `MenuItem` clase. Se puede adjuntar un controlador de eventos al `Clicked` evento para reaccionar a los grifos o clics en `ToolbarItem` instancias en XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

También se puede adjuntar un controlador de eventos en el código:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

En los ejemplos anteriores se hacía referencia a un `OnItemClicked` controlador de eventos. En el código siguiente se muestra una implementación de ejemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`los objetos también pueden utilizar `Command` las `CommandParameter` propiedades y para reaccionar a los datos proporcionados por el usuario sin controladores de eventos. Para obtener más información sobre la `ICommand` interfaz y el enlace de datos de MVVM, vea el [ Xamarin.Forms comportamiento del MVVM de MenuItem](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Habilitar o deshabilitar un ToolbarItem en tiempo de ejecución

Para habilitar la deshabilitación de un `ToolbarItem` en tiempo de ejecución, enlace su `Command` propiedad a una `ICommand` implementación de y asegúrese de que un `canExecute` delegado habilita y deshabilita `ICommand` según corresponda.

Para obtener más información, vea [habilitar o deshabilitar un MenuItem en tiempo de ejecución](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menús primarios y secundarios

La `ToolbarItemOrder` enumeración `Default` tiene `Primary` `Secondary` los valores, y.

Cuando la `Order` propiedad está establecida en `Primary` , el `ToolbarItem` objeto aparecerá en la barra de navegación principal en todas las plataformas. `ToolbarItem`los objetos se clasifican por orden de prioridad en el título de la página, que se truncará para dejar espacio para los elementos. Las siguientes capturas `ToolbarItem` de pantallas muestran objetos en el menú principal de iOS y Android:

![Captura de pantalla del menú principal de ToolbarItem Android y iOS "](toolbaritem-images/toolbaritem-primary-menu.png "Captura de pantalla del menú principal de ToolbarItem en Android e iOS")

Cuando la `Order` propiedad se establece en `Secondary` , el comportamiento varía entre plataformas. En UWP y Android, el `Secondary` menú elementos aparece como tres puntos que se pueden puntear o hacer clic en él para mostrar los elementos en una lista vertical. En iOS, el `Secondary` menú elementos aparece debajo de la barra de navegación como una lista horizontal. Las capturas de pantallas siguientes muestran un menú secundario en iOS y Android:

!["Captura de pantalla del menú secundario de ToolbarItem Android y iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Captura de pantalla del menú secundario de ToolbarItem en Android e iOS")

> [!WARNING]
> El comportamiento del icono en `ToolbarItem` los objetos que tienen su `Order` propiedad establecida en `Secondary` es incoherente entre las plataformas. Evite establecer la `IconImageSource` propiedad en los elementos que aparecen en el menú secundario.

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Imágenes enXamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.FormsObjetos](~/xamarin-forms/user-interface/menuitem.md)
