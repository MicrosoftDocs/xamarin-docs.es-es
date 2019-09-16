---
title: Interactividad de ListView
description: En este artículo se explica cómo agregar interactividad a un ListView Xamarin.Forms mediante la implementación de las selecciones, acciones de contexto y Deslizar para actualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: e2d51f42339b1ff2a99f2a00bb5a9e662fb01d87
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998075"
---
# <a name="listview-interactivity"></a>Interactividad de ListView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La clase de Xamarin [`ListView`](xref:Xamarin.Forms.ListView) . Forms admite la interacción del usuario con los datos que presenta.

## <a name="selection-and-taps"></a>Selección y pulsaciones

El [ `ListView` ](xref:Xamarin.Forms.ListView) modo de selección se controla estableciendo la [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad con un valor de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) enumeración:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica que se puede seleccionar un solo elemento con el elemento seleccionado se resalta. Este es el valor predeterminado.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica que no se pueden seleccionar elementos.

Cuando un usuario puntea un elemento, se activan dos eventos:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se desencadena cuando se selecciona un elemento nuevo.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se desencadena cuando se pulsa un elemento.

Pulsar dos veces el mismo elemento se desencadenarán dos [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) eventos, pero tendrá solo desencadena una sola [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos.

> [!NOTE]
> La [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) clase, que contiene los argumentos de evento para [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) el evento, [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) tiene [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) las propiedades y, `ItemIndex` y una propiedad cuyo valor representa el índice [`ListView`](xref:Xamarin.Forms.ListView) en el del elemento punteado. Del mismo modo [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) , la clase, que contiene los argumentos de [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento para el evento [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) , tiene una propiedad `SelectedItemIndex` y una propiedad cuyo valor representa el índice `ListView` de la propiedad del elemento seleccionado.

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), los elementos de la [ `ListView` ](xref:Xamarin.Forms.ListView) puede seleccionarse el [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) y [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) se desencadena eventos y el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado.

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), los elementos de la [ `ListView` ](xref:Xamarin.Forms.ListView) no puede seleccionarse el [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) no se desencadenará el evento y el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad permanecerá `null`. Sin embargo, [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) todavía se desencadena eventos y el elemento punteado aparecerán resaltado brevemente durante la derivación.

Cuando se selecciona un elemento y el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) se cambia la propiedad de [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en `null` y [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) se desencadenará el evento con un `null` elemento.

El siguientes capturas de pantalla se muestra un [ `ListView` ](xref:Xamarin.Forms.ListView) con el modo de selección predeterminado:

![](interactivity-images/selection-default.png "ListView con selección habilitada")

### <a name="disable-selection"></a>Deshabilitar selección

Para deshabilitar [ `ListView` ](xref:Xamarin.Forms.ListView) selección conjunto el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Acciones de contexto

A menudo, los usuarios deseen realizar acciones en un elemento en un `ListView`. Por ejemplo, considere la posibilidad de obtener una lista de mensajes de correo electrónico en la aplicación de correo. En iOS, puede deslizar para eliminar un mensaje::

![](interactivity-images/context-default.png "ListView con acciones de contexto")

Acciones de contexto se pueden implementar en C# y XAML. A continuación encontrará guías específicas para ambos, pero primero vamos a Eche un vistazo a algunos detalles de implementación clave para ambos.

Las acciones de contexto se `MenuItem` crean mediante elementos. Los eventos TAP `MenuItems` para los objetos los genera `MenuItem` el propio, no `ListView`el. Esto es diferente de cómo se controlan los eventos TAP para las celdas `ListView` , donde provoca el evento en lugar de la celda. Dado que `ListView` el objeto está provocando el evento, su controlador de eventos proporciona información de clave, como qué elemento se ha seleccionado o punteado.

De forma predeterminada, `MenuItem` no tiene ninguna manera de saber a qué celda pertenece. La `CommandParameter` propiedad está disponible en `MenuItem` para almacenar objetos, como `ViewCell`el objeto situado detrás `MenuItem`de. La `CommandParameter` propiedad se puede establecer en XAML y C#.

### <a name="xaml"></a>XAML

`MenuItem`los elementos se pueden crear en una colección XAML. El XAML siguiente muestra una celda personalizada con dos acciones de contexto implementadas:

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

En el archivo de código subyacente, asegúrese de que el `Clicked` se implementan los métodos:

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
> El `NavigationPageRenderer` para Android tiene una reemplazable `UpdateMenuItemIcon` método que se puede usar para cargar los iconos de un personalizado `Drawable`. Esta invalidación permite usar las imágenes SVG como iconos en `MenuItem` instancias en Android.

### <a name="code"></a>Código

Las acciones de contexto se pueden implementar `Cell` en cualquier subclase (siempre y cuando no se usen como encabezado de grupo) creando `MenuItem` instancias y agregándolas a la `ContextActions` colección para la celda. Tiene las siguientes propiedades se pueden configurar para la acción de contexto:

- **Texto** &ndash; la cadena que aparece en el elemento de menú.
- **Hacer clic en** &ndash; el evento cuando se hace clic en el elemento.
- **IsDestructive** &ndash; (opcional) cuando es true, el elemento se representa de forma diferente en iOS.

Se pueden agregar varias acciones de contexto a una celda, pero solo uno debe tener `IsDestructive` establecido en `true`. El código siguiente muestra cómo las acciones de contexto se agregarían a un `ViewCell`:

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

Los usuarios han llegado a esperar que extraer en una lista de datos se actualizará esa lista. El [`ListView`](xref:Xamarin.Forms.ListView) control es compatible con este. Para habilitar la funcionalidad de extracción a actualización, establezca [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) en `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

El código de C# equivalente es:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Aparece un control de número durante la actualización, que es negro de forma predeterminada. Sin embargo, el color del control de número se puede cambiar en iOS y `RefreshControlColor` Android estableciendo la [`Color`](xref:Xamarin.Forms.Color)propiedad en:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

El código de C# equivalente es:

```csharp
listView.RefreshControlColor = Color.Red;
```

Las siguientes capturas de pantallas muestran la incorporación de cambios a la actualización cuando el usuario extrae:

![](interactivity-images/refresh-start.png "Incorporación de cambios de ListView a actualizar en curso")

Las siguientes capturas de pantallas muestran la extracción a la actualización después de que el usuario haya lanzado la extracción, con el [`ListView`](xref:Xamarin.Forms.ListView) control de número que se muestra mientras se está actualizando:

![](interactivity-images/refresh-in-progress.png "Incorporación de cambios de ListView a actualización completada")

[`ListView`](xref:Xamarin.Forms.ListView)activa el [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento para iniciar la actualización y la [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propiedad se establecerá en `true`. El código necesario para actualizar el contenido del `ListView` debería ejecutarse a continuación por el controlador de eventos para el `Refreshing` evento, o por [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)el método ejecutado por. Una vez que `IsRefreshing` `false` [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) se actualiza, la propiedad debe establecerse en, o se debe llamar al método para indicar que la actualización se ha completado. `ListView`

> [!NOTE]
> Al definir un [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand), se `CanExecute` puede especificar el método del comando para habilitar o deshabilitar el comando.

## <a name="related-links"></a>Vínculos relacionados

- [Interactividad de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
