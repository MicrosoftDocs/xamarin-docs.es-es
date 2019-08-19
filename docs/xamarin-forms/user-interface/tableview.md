---
title: Xamarin.Forms TableView
description: En este artículo se explica cómo usar la clase Xamarin.Forms TableView para presentar los menús de desplazamiento, la configuración y los formularios de entrada en las aplicaciones.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: fc3837cd0d69d49b9939b04da667010aac919fe2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657121"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)es una vista para mostrar listas desplazables de datos u opciones en las que hay filas que no comparten la misma plantilla. A diferencia de [ListView](~/xamarin-forms/user-interface/listview/index.md), `TableView` no tiene el concepto de un `ItemsSource`, por lo que los elementos se deben agregar manualmente como elementos secundarios.

![](tableview-images/tableview-all-sml.png "Ejemplo de TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Casos de uso

[`TableView`](xref:Xamarin.Forms.TableView)resulta útil cuando:

- presentar una lista de configuración,
- recopilación de datos en un formulario, o
- mostrando datos que se presentan diferente de la fila a fila (por ejemplo, números, porcentajes e imágenes).

[`TableView`](xref:Xamarin.Forms.TableView)controla el desplazamiento y la colocación de las filas en secciones atractivas, una necesidad común de los escenarios anteriores. El `TableView` utiliza control de la plataforma subyacente de vista equivalente cuando esté disponible para crear una apariencia nativa para cada plataforma.

<a name="TableView_Structure" />

## <a name="structure"></a>Estructura

Los elementos de [`TableView`](xref:Xamarin.Forms.TableView) se organizan en secciones. En la raíz de, `TableView` es, [`TableRoot`](xref:Xamarin.Forms.TableRoot)que es el elemento primario de una o [`TableSection`](xref:Xamarin.Forms.TableSection) más instancias de. Cada [`TableSection`](xref:Xamarin.Forms.TableSection) una consta de un encabezado y una o [`ViewCell`](xref:Xamarin.Forms.ViewCell) varias instancias:

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
            <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

El código de C# equivalente es:

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>Apariencia

[`TableView`](xref:Xamarin.Forms.TableView)expone la [`Intent`](xref:Xamarin.Forms.TableView.Intent) propiedad, que se puede establecer en cualquiera de los [`TableIntent`](xref:Xamarin.Forms.TableIntent) miembros de enumeración:

- `Data`: sirve para mostrar entradas de datos. Tenga en cuenta que [ListView](~/xamarin-forms/user-interface/listview/index.md) puede ser una mejor opción para el desplazamiento de listas de datos.
- `Form`: se utiliza cuando el TableView actúa como un formulario.
- `Menu`: se usa para presentar un menú de selecciones.
- `Settings`: se usa para mostrar una lista de opciones de configuración.

El [`TableIntent`](xref:Xamarin.Forms.TableIntent) valor que elija puede afectar al [`TableView`](xref:Xamarin.Forms.TableView) modo en que aparece en cada plataforma. Incluso si hay no borrar las diferencias, es una práctica recomendada para seleccionar el `TableIntent` que más acerque cómo piensa usar la tabla.

Además, el color del texto que se muestra para cada [`TableSection`](xref:Xamarin.Forms.TableSection) puede cambiarse estableciendo la `TextColor` propiedad en [`Color`](xref:Xamarin.Forms.Color).

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Celdas integradas

Xamarin.Forms incluye celdas integradas para recopilar y mostrar información. Aunque [`ListView`](xref:Xamarin.Forms.ListView) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) y [`TableView`](xref:Xamarin.Forms.TableView) pueden utilizar las mismas celdas, y [`EntryCell`](xref:Xamarin.Forms.EntryCell) son las más relevantes para un `TableView` escenario.

Consulte [aspecto de la celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obtener una descripción detallada de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) y [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) es el control que se utilizará para presentar y capturar un activado/desactivado o `true` / `false` estado. Define las siguientes propiedades:

- `Text`: texto que se va a mostrar junto al modificador.
- `On`: indica si el modificador se muestra como activado o desactivado.
- `OnColor`: el [`Color`](xref:Xamarin.Forms.Color) del modificador cuando se encuentra en la posición de encendido.

Todas estas propiedades son enlazables.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)también expone el `OnChanged` evento, lo que le permite responder a los cambios en el estado de la celda.

![](tableview-images/switch-cell.png "Ejemplo de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) es útil cuando se necesita mostrar los datos de texto que el usuario puede editar. Define las siguientes propiedades:

- `Keyboard`: Teclado que se va a mostrar durante la edición. Hay opciones para cosas como valores numéricos, correo electrónico, números de teléfono, etcetera. [Consulte la documentación de API](xref:Xamarin.Forms.Keyboard).
- `Label`: El texto de la etiqueta que se va a mostrar a la izquierda del campo de entrada de texto.
- `LabelColor`: El color del texto de la etiqueta.
- `Placeholder`: Texto que se va a mostrar en el campo de entrada cuando es null o está vacío. Este texto desaparece cuando comienza la entrada de texto.
- `Text`: El texto del campo de entrada.
- `HorizontalTextAlignment`: Alineación horizontal del texto. Puede ser, izquierda o derecha centrado. [Consulte la documentación de API](xref:Xamarin.Forms.TextAlignment).

[`EntryCell`](xref:Xamarin.Forms.EntryCell)también expone el `Completed` evento, que se desencadena cuando el usuario presiona el botón ' DONE ' en el teclado mientras edita el texto.

![](tableview-images/entry-cell.png "Ejemplo de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Celdas personalizadas

Cuando las celdas integradas no son suficientes, celdas personalizadas pueden utilizarse para presentar y capturar datos de la manera que tenga sentido para su aplicación. Por ejemplo, es posible que desee presentar un control deslizante para permitir que un usuario puede elegir la opacidad de una imagen.

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la misma clase base que todos los de la celda integrada tipos de uso.

Este es un ejemplo de una celda personalizada:

![](tableview-images/custom-cell.png "Ejemplo de la celda personalizada")

[`TableView`](xref:Xamarin.Forms.TableView) En el ejemplo siguiente se muestra el código XAML que se usa para crear en las capturas de pantallas anteriores:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
      <TableView Intent="Settings">
          <TableRoot>
              <TableSection Title="Getting Started">
                  <ViewCell>
                      <StackLayout Orientation="Horizontal">
                          <Image Source="bulb.png" />
                          <Label Text="left"
                                 TextColor="#f35e20" />
                          <Label Text="right"
                                 HorizontalOptions="EndAndExpand"
                                 TextColor="#503026" />
                      </StackLayout>
                  </ViewCell>
              </TableSection>
          </TableRoot>
      </TableView>
</ContentPage>
```

El código de C# equivalente es:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

[`TableView`](xref:Xamarin.Forms.TableView) El elemento raíz en [`TableRoot`](xref:Xamarin.Forms.TableRoot)es y hay [`TableSection`](xref:Xamarin.Forms.TableSection) inmediatamente debajo de `TableRoot`. Se define directamente `TableSection`en, y [`StackLayout`](xref:Xamarin.Forms.StackLayout) se usa para administrar el diseño de la celda personalizada, aunque aquí se podría usar cualquier diseño. [`ViewCell`](xref:Xamarin.Forms.ViewCell)

> [!NOTE]
> A diferencia [`ListView`](xref:Xamarin.Forms.ListView)de [`TableView`](xref:Xamarin.Forms.TableView) , no requiere que `ItemTemplate`las celdas personalizadas (o ninguna) estén definidas en.

## <a name="row-height"></a>Alto de fila

El [ `TableView` ](xref:Xamarin.Forms.TableView) clase tiene dos propiedades que pueden usarse para cambiar el alto de fila de celdas:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) : establece el alto de cada fila a una `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) : las filas tienen diferentes alturas si establece en `true`. Tenga en cuenta que al establecer esta propiedad en `true`, automáticamente se calcula y aplicada por Xamarin.Forms alto de las filas.

Cuando el alto del contenido en una celda de un [ `TableView` ](xref:Xamarin.Forms.TableView) se cambia la fila se actualiza implícitamente alto en la plataforma Universal de Windows (UWP) y Android. Sin embargo, en iOS debe obligársele a actualizar mediante el establecimiento la [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) propiedad `true` y mediante una llamada a la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

El siguiente ejemplo XAML se muestra un [ `TableView` ](xref:Xamarin.Forms.TableView) que contiene un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Cuando el [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) se pulsa, el `OnViewCellTapped` se ejecuta el controlador de eventos:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

El `OnViewCellTapped` controlador de eventos muestra u oculta el segundo [ `Label` ](xref:Xamarin.Forms.Label) en el [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)y actualiza explícitamente el tamaño de la celda mediante una llamada a la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

Las capturas de pantalla siguientes muestran la celda antes de que se puntea en:

![](tableview-images/cell-beforeresize.png "ViewCell antes de que se va a cambiar de tamaño")

Las capturas de pantalla siguientes muestran la celda después de que se puntea en:

![](tableview-images/cell-afterresize.png "ViewCell después de que se va a cambiar de tamaño")

> [!IMPORTANT]
> Si esta característica está sobreutilizada, hay grandes posibilidades de degradación del rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [TableView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
