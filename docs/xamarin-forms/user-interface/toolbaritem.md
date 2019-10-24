---
title: De Xamarin. Forms ToolbarItem
description: La clase ToolbarItem es un tipo especial de botón que se usa en la barra de navegación de una aplicación.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: 0812347e85b0ccb6aa0bbb16649a89bb4d961c9b
ms.sourcegitcommit: a14edebf00f3e0f8944e59042ca7aa5c42173e30
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780356"
---
# <a name="xamarinforms-toolbaritem"></a>De Xamarin. Forms ToolbarItem

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La clase de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) de Xamarin. Forms es un tipo especial de botón que se puede Agregar a la colección de `ToolbarItems` de un objeto `Page`. Cada objeto `ToolbarItem` aparecerá como un botón en la barra de navegación de la aplicación. Una instancia de `ToolbarItem` puede tener un icono y aparecer como un elemento de menú primario o secundario. La clase `ToolbarItem` hereda de [`MenuItem`](xref:Xamarin.Forms.MenuItem).

Las siguientes capturas de pantallas muestran `ToolbarItem` objetos en la barra de navegación de iOS y Android:

![Captura de pantalla de demostración de ToolbarItem en Android e iOS](toolbaritem-images/toolbaritem-device-screenshot.png "Captura de pantalla de la demostración de ToolbarItem en Android e iOS")

La clase `ToolbarItem` define las siguientes propiedades:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) es un valor de enumeración `ToolbarItemOrder` que determina si la instancia de `ToolbarItem` se muestra en el menú principal o en el secundario.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) es un valor `integer` que determina el orden de presentación de los elementos de la colección `ToolbarItems` de un objeto `Page`.

La clase `ToolbarItem` hereda las siguientes propiedades utilizadas normalmente de la clase `MenuItem`:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) es una `ICommand` que permite enlazar acciones de usuario, como pulsaciones de dedo o clics, a comandos definidos en un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) es una `object` que especifica el parámetro que se debe pasar a la `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) es un valor `ImageSource` que determina el icono de presentación de un objeto `ToolbarItem`.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) es una `string` que determina el texto que se va a mostrar en un objeto `ToolbarItem`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , por lo que una instancia de `ToolbarItem` puede ser el destino de los enlaces de datos.

> [!NOTE]
> Una alternativa a la creación de una barra de herramientas a partir de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos es establecer el [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propiedad adjunta en una clase de diseño que contiene varias vistas. Para obtener más información, vea [Mostrar vistas en la barra de navegación](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Crear un ToolbarItem

Se puede crear una instancia de un objeto `ToolbarItem` en XAML. Las propiedades `Text` y `IconImageSource` se pueden establecer para determinar cómo se muestra el botón en la barra de navegación. En el ejemplo siguiente se muestra cómo crear una instancia de un `ToolbarItem` con algunas propiedades comunes establecidas y cómo agregarla a la colección `ToolbarItems` de un `ContentPage`:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Este ejemplo dará como resultado un objeto `ToolbarItem` que tiene texto, un icono y que aparece en primer lugar en el área de la barra de navegación principal. También se puede crear un `ToolbarItem` en código y agregarlo a la colección de `ToolbarItems`:

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

El archivo representado por el `string`, proporcionado como la propiedad `IconImageSource`, debe existir en cada proyecto de plataforma.

> [!NOTE]
> Los recursos de imagen se tratan de forma diferente en cada plataforma. Un `ImageSource` puede proviene de orígenes como un archivo local o un recurso incrustado, un URI o un flujo. Para obtener más información sobre cómo establecer la propiedad de `IconImageSource` y las imágenes de Xamarin. Forms, consulte [imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Definir el comportamiento del botón

La clase `ToolbarItem` hereda el evento `Clicked` de la clase `MenuItem`. Se puede adjuntar un controlador de eventos al evento `Clicked` para reaccionar a los grifos o clics en `ToolbarItem` instancias en XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

También se puede adjuntar un controlador de eventos en el código:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

En los ejemplos anteriores se hacía referencia a un controlador de eventos `OnItemClicked`. En el código siguiente se muestra una implementación de ejemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` objetos también pueden usar las propiedades `Command` y `CommandParameter` para reaccionar a los datos proporcionados por el usuario sin controladores de eventos. Para obtener más información sobre la interfaz de `ICommand` y el enlace de datos de MVVM, vea el [comportamiento de Xamarin. Forms MenuItem MVVM](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="primary-and-secondary-menus"></a>Menús primarios y secundarios

La enumeración `ToolbarItemOrder` tiene `Default`, `Primary` y `Secondary` valores.

Cuando la propiedad `Order` está establecida en `Primary`, el objeto de `ToolbarItem` aparecerá en la barra de navegación principal en todas las plataformas. `ToolbarItem` objetos se clasifican por orden de prioridad en el título de la página, lo que se truncará para dejar espacio para los elementos. Las capturas de pantallas siguientes muestran `ToolbarItem` objetos en el menú principal en iOS y Android:

![Captura de pantalla del menú principal de ToolbarItem Android y iOS "](toolbaritem-images/toolbaritem-primary-menu.png "Captura de pantalla del menú principal de ToolbarItem en Android e iOS")

Cuando la propiedad `Order` está establecida en `Secondary`, el comportamiento varía en todas las plataformas. En UWP y Android, el menú `Secondary` elementos aparece como tres puntos que se pueden puntear o en los que se puede hacer clic para mostrar los elementos en una lista vertical. En iOS, el menú `Secondary` elementos aparece debajo de la barra de navegación como una lista horizontal. Las capturas de pantallas siguientes muestran un menú secundario en iOS y Android:

!["Captura de pantalla del menú secundario de ToolbarItem Android y iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Captura de pantalla del menú secundario de ToolbarItem en Android e iOS")

> [!WARNING]
> Comportamiento del icono en `ToolbarItem` objetos que tienen su propiedad `Order` establecida en `Secondary` es incoherente entre las plataformas. Evite establecer la propiedad `IconImageSource` en los elementos que aparecen en el menú secundario.

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
