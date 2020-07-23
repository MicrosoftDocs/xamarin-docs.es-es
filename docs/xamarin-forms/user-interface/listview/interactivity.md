---
title: Interactividad de ListView
description: En este artículo se explica cómo agregar interactividad a un Xamarin.Forms control ListView implementando selecciones, acciones de contexto y extracción para actualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 287b116d9ba2cb84e4e196fff080b8212e4eca3b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938416"
---
# <a name="listview-interactivity"></a>Interactividad de ListView

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) clase admite la interacción del usuario con los datos que presenta.

## <a name="selection-and-taps"></a>Selección y pulsaciones

El [`ListView`](xref:Xamarin.Forms.ListView) modo de selección se controla estableciendo la [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propiedad en un valor de la [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) enumeración:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)indica que se puede seleccionar un solo elemento, con el elemento seleccionado resaltado. Este es el valor predeterminado.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)indica que no se pueden seleccionar elementos.

Cuando un usuario pulsa un elemento, se activan dos eventos:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)se desencadena cuando se selecciona un nuevo elemento.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)se desencadena cuando se puntea un elemento.

Si puntea dos veces en el mismo elemento, se activarán dos [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos, pero solo se desencadenará un [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento único.

> [!NOTE]
> La [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) clase, que contiene los argumentos de evento para el [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento, [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) tiene [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) las propiedades y, y una `ItemIndex` propiedad cuyo valor representa el índice en el [`ListView`](xref:Xamarin.Forms.ListView) del elemento punteado. Del mismo modo, la [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) clase, que contiene los argumentos de evento para el [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento, tiene una [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propiedad y una `SelectedItemIndex` propiedad cuyo valor representa el índice de la propiedad `ListView` del elemento seleccionado.

Cuando la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) , se pueden seleccionar los elementos de [`ListView`](xref:Xamarin.Forms.ListView) , se [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) activarán los eventos y, y la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado.

Cuando la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , los elementos de [`ListView`](xref:Xamarin.Forms.ListView) no se pueden seleccionar, el [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento no se desencadenará y la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propiedad permanecerá `null` . Sin embargo, los [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos se seguirán activando y el elemento punteado se resaltará brevemente durante la derivación.

Cuando se ha seleccionado un elemento y [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) se ha cambiado la propiedad de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en `null` y el evento se [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) desencadenará con un `null` elemento.

Las capturas de pantallas siguientes muestran un [`ListView`](xref:Xamarin.Forms.ListView) con el modo de selección predeterminado:

![ListView con la selección habilitada](interactivity-images/selection-default.png)

### <a name="disable-selection"></a>Deshabilitar selección

Para deshabilitar [`ListView`](xref:Xamarin.Forms.ListView) la selección, establezca la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propiedad en [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) :

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Acciones de contexto

A menudo, los usuarios quieren tomar medidas en un elemento de un `ListView` . Por ejemplo, considere una lista de correos electrónicos en la aplicación de correo. En iOS, puede deslizar rápidamente para eliminar un mensaje:

![ListView con acciones de contexto](interactivity-images/context-default.png)

Las acciones de contexto se pueden implementar en C# y XAML. A continuación encontrará guías específicas para ambos, pero primero echemos un vistazo a algunos detalles de implementación clave para ambos.

Las acciones de contexto se crean mediante `MenuItem` elementos. Los eventos TAP para los `MenuItems` objetos los genera el `MenuItem` propio, no el `ListView` . Esto es diferente de cómo se controlan los eventos TAP para las celdas, donde `ListView` provoca el evento en lugar de la celda. Dado `ListView` que el objeto está provocando el evento, su controlador de eventos proporciona información de clave, como qué elemento se ha seleccionado o punteado.

De forma predeterminada, `MenuItem` no tiene ninguna manera de saber a qué celda pertenece. La `CommandParameter` propiedad está disponible en `MenuItem` para almacenar objetos, como el objeto situado detrás de `MenuItem` `ViewCell` . La `CommandParameter` propiedad se puede establecer en XAML y C#.

### <a name="xaml"></a>XAML

`MenuItem`los elementos se pueden crear en una colección XAML. En el código XAML siguiente se muestra una celda personalizada con dos acciones de contexto implementadas:

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

En el archivo de código subyacente, asegúrese de que `Clicked` se implementan los métodos:

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
> `NavigationPageRenderer`Para Android tiene un método reemplazable `UpdateMenuItemIcon` que se puede usar para cargar iconos de un personalizado `Drawable` . Esta invalidación permite usar imágenes SVG como iconos en `MenuItem` instancias en Android.

### <a name="code"></a>Código

Las acciones de contexto se pueden implementar en cualquier `Cell` subclase (siempre y cuando no se usen como encabezado de grupo) creando `MenuItem` instancias y agregándolas a la `ContextActions` colección para la celda. Tiene las siguientes propiedades que se pueden configurar para la acción de contexto:

- **Texto** &ndash; de cadena que aparece en el elemento de menú.
- **Clic en** &ndash; evento cuando se hace clic en el elemento.
- **IsDestructive** &ndash; (opcional) cuando es true, el elemento se representa de forma diferente en iOS.

Se pueden agregar varias acciones de contexto a una celda, pero solo una debe tener `IsDestructive` establecido en `true` . En el código siguiente se muestra cómo se agregarán acciones de contexto a un `ViewCell` :

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

Los usuarios deben esperar que la extracción de una lista de datos se actualizará en la lista. El [`ListView`](xref:Xamarin.Forms.ListView) control es compatible con este. Para habilitar la funcionalidad de extracción a actualización, establezca [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) en `true` :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

El código de C# equivalente es el siguiente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Aparece un control de número durante la actualización, que es negro de forma predeterminada. Sin embargo, el color del control de número se puede cambiar en iOS y Android estableciendo la `RefreshControlColor` propiedad en [`Color`](xref:Xamarin.Forms.Color) :

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

![Incorporación de cambios de ListView a la actualización en curso](interactivity-images/refresh-start.png)

Las siguientes capturas de pantallas muestran la extracción a la actualización después de que el usuario haya lanzado la extracción, con el control de número que se muestra mientras [`ListView`](xref:Xamarin.Forms.ListView) se está actualizando:

![Incorporación de cambios de ListView a actualización completada](interactivity-images/refresh-in-progress.png)

[`ListView`](xref:Xamarin.Forms.ListView)activa el [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento para iniciar la actualización y la [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propiedad se establecerá en `true` . El código necesario para actualizar el contenido del `ListView` debería ejecutarse a continuación por el controlador de eventos para el `Refreshing` evento, o por el método ejecutado por [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) . Una vez que `ListView` se actualiza, la `IsRefreshing` propiedad debe establecerse en `false` , o [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) se debe llamar al método para indicar que la actualización se ha completado.

> [!NOTE]
> Al definir un [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) , `CanExecute` se puede especificar el método del comando para habilitar o deshabilitar el comando.

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`ListView`](xref:Xamarin.Forms.ListView)define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. En el ejemplo de XAML siguiente se muestra un `ListView` que establece un controlador de eventos para el `Scrolled` evento:

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

En este ejemplo de código, el `OnListViewScrolled` controlador de eventos se ejecuta cuando se `Scrolled` desencadena el evento:

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

El `OnListViewScrolled` controlador de eventos genera los valores del `ScrolledEventArgs` objeto que acompaña al evento.

## <a name="related-links"></a>Vínculos relacionados

- [Interactividad de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
