---
title: Interactividad de ListView
description: En este artículo se explica cómo agregar interactividad a un control ListView de Xamarin. Forms implementando selecciones, acciones de contexto y extracción para actualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696866"
---
# <a name="listview-interactivity"></a>Interactividad de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La clase de [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin. Forms admite la interacción del usuario con los datos que presenta.

## <a name="selection-and-taps"></a>Selección y pulsaciones

El modo de selección de [`ListView`](xref:Xamarin.Forms.ListView) se controla estableciendo la propiedad [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) en un valor de la enumeración [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica que se puede seleccionar un solo elemento, con el elemento seleccionado resaltado. Este es el valor predeterminado.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica que no se pueden seleccionar elementos.

Cuando un usuario pulsa un elemento, se activan dos eventos:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se activa cuando se selecciona un nuevo elemento.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se desencadena cuando se puntea un elemento.

Si puntea dos veces en el mismo elemento, se activarán dos [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos, pero solo se activará un evento de [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) .

> [!NOTE]
> La clase [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) , que contiene los argumentos de evento para el evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , tiene [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) y [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) propiedades, y una propiedad `ItemIndex` cuyo valor representa el índice en el [0](xref:Xamarin.Forms.ListView) del elemento punteado. Del mismo modo, la clase [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) , que contiene los argumentos de evento para el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) , tiene una propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) y una propiedad `SelectedItemIndex` cuyo valor representa el índice en el `ListView` del elemento seleccionado.

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) está establecida en [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single), se pueden seleccionar los elementos de la [`ListView`](xref:Xamarin.Forms.ListView) , se activarán los eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) y [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) y la propiedad [1](xref:Xamarin.Forms.ListView.SelectedItem) se establecerá en el valor del elemento seleccionado. movs.

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) está establecida en [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), no se pueden seleccionar los elementos de la [`ListView`](xref:Xamarin.Forms.ListView) , no se desencadenará el evento de [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) y la propiedad [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) permanecerá 0. Sin embargo, se seguirán activando [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos y el elemento punteado se resaltará brevemente durante la derivación.

Cuando se ha seleccionado un elemento y se cambia la propiedad [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), la propiedad [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) se establecerá en `null` y el evento [0](xref:Xamarin.Forms.ListView.ItemSelected) se desencadenará con un elemento 1.

Las capturas de pantallas siguientes muestran un [`ListView`](xref:Xamarin.Forms.ListView) con el modo de selección predeterminada:

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Deshabilitar selección

Para deshabilitar [`ListView`](xref:Xamarin.Forms.ListView) selección, establezca la propiedad [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) en [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Acciones de contexto

A menudo, los usuarios quieren tomar medidas en un elemento de un `ListView`. Por ejemplo, considere una lista de correos electrónicos en la aplicación de correo. En iOS, puede deslizar rápidamente para eliminar un mensaje:

![](interactivity-images/context-default.png "ListView with Context Actions")

Las acciones de contexto se pueden C# implementar en y en XAML. A continuación encontrará guías específicas para ambos, pero primero echemos un vistazo a algunos detalles de implementación clave para ambos.

Las acciones de contexto se crean mediante elementos `MenuItem`. Los eventos TAP para los objetos `MenuItems` los genera el `MenuItem` mismo, no los `ListView`. Esto es diferente de cómo se controlan los eventos TAP para las celdas, donde el `ListView` genera el evento en lugar de la celda. Dado que el `ListView` está provocando el evento, su controlador de eventos proporciona información de clave, como qué elemento se ha seleccionado o punteado.

De forma predeterminada, un `MenuItem` no tiene ninguna manera de saber a qué celda pertenece. La propiedad `CommandParameter` está disponible en `MenuItem` para almacenar objetos, como el objeto situado detrás del `ViewCell` del `MenuItem`. La propiedad `CommandParameter` se puede establecer en XAML y C#.

### <a name="xaml"></a>XAML

`MenuItem` elementos se pueden crear en una colección XAML. En el código XAML siguiente se muestra una celda personalizada con dos acciones de contexto implementadas:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

En el archivo de código subyacente, asegúrese de que se implementan los métodos de `Clicked`:

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> El `NavigationPageRenderer` para Android tiene un método de `UpdateMenuItemIcon` reemplazable que se puede usar para cargar iconos desde un `Drawable` personalizado. Esta invalidación permite usar imágenes SVG como iconos en instancias de `MenuItem` en Android.

### <a name="code"></a>Código

Las acciones de contexto se pueden implementar en cualquier subclase de `Cell` (siempre y cuando no se use como encabezado de grupo) creando instancias de `MenuItem` y agregándolas a la colección de `ContextActions` para la celda. Tiene las siguientes propiedades que se pueden configurar para la acción de contexto:

- **Texto** &ndash; la cadena que aparece en el elemento de menú.
- **Haga clic en** &ndash; el evento cuando se haga clic en el elemento.
- **IsDestructive** &ndash; (opcional) si es true, el elemento se representa de forma diferente en iOS.

Se pueden agregar varias acciones de contexto a una celda, pero solo una debe tener `IsDestructive` establecida en `true`. En el código siguiente se muestra cómo se agregarán acciones de contexto a un `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>Extraer para actualizar

Los usuarios deben esperar que la extracción de una lista de datos se actualizará en la lista. El control [`ListView`](xref:Xamarin.Forms.ListView) es compatible con esta plataforma. Para habilitar la funcionalidad de extracción para actualizar, establezca [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) en `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

El código de C# equivalente es el siguiente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Aparece un control de número durante la actualización, que es negro de forma predeterminada. Sin embargo, el color del control de número se puede cambiar en iOS y Android estableciendo la propiedad `RefreshControlColor` en un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

El código de C# equivalente es el siguiente:

```csharp
listView.RefreshControlColor = Color.Red;
```

Las siguientes capturas de pantallas muestran la incorporación de cambios a la actualización cuando el usuario extrae:

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

Las siguientes capturas de pantallas muestran la extracción a la actualización después de que el usuario haya lanzado la extracción, con el control de número que se muestra mientras se actualiza el [`ListView`](xref:Xamarin.Forms.ListView) :

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView) activa el evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) para iniciar la actualización y la propiedad [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) se establecerá en `true`. Cualquier código necesario para actualizar el contenido de la `ListView` debe ser ejecutado por el controlador de eventos para el evento `Refreshing`, o por el método ejecutado por el [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand). Una vez actualizado el `ListView`, la propiedad `IsRefreshing` se debe establecer en `false`, o se debe llamar al método [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) para indicar que la actualización se ha completado.

> [!NOTE]
> Al definir un [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand), se puede especificar el método de `CanExecute` del comando para habilitar o deshabilitar el comando.

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`ListView`](xref:Xamarin.Forms.ListView) define un evento de `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. En el ejemplo de XAML siguiente se muestra un `ListView` que establece un controlador de eventos para el evento `Scrolled`:

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

El código de C# equivalente es el siguiente:

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

En este ejemplo de código, se ejecuta el controlador de eventos `OnListViewScrolled` cuando se activa el evento `Scrolled`:

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

El controlador de eventos `OnListViewScrolled` devuelve los valores del objeto `ScrolledEventArgs` que acompaña al evento.

## <a name="related-links"></a>Vínculos relacionados

- [Interactividad de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
