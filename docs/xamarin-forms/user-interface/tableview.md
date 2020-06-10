---
title: " Xamarin.Forms TableView" Description: "en este artículo se explica cómo usar la Xamarin.Forms clase TableView para presentar menús desplazables, configuraciones y formularios de entrada en aplicaciones".
MS. Prod: Xamarin ms. AssetID: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/25/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-tableview"></a>Xamarin.FormsTableView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)es una vista para mostrar listas desplazables de datos u opciones en las que hay filas que no comparten la misma plantilla. A diferencia de [ListView](~/xamarin-forms/user-interface/listview/index.md), no `TableView` tiene el concepto de `ItemsSource` , por lo que los elementos se deben agregar manualmente como elementos secundarios.

![Ejemplo de TableView](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>Casos de uso

[`TableView`](xref:Xamarin.Forms.TableView)resulta útil cuando:

- presentar una lista de opciones de configuración
- recopilación de datos en un formulario o
- Mostrar los datos que se presentan de manera diferente de fila a fila (por ejemplo, números, porcentajes e imágenes).

[`TableView`](xref:Xamarin.Forms.TableView)controla el desplazamiento y la colocación de las filas en secciones atractivas, una necesidad común de los escenarios anteriores. El `TableView` control usa la vista equivalente subyacente de cada plataforma cuando está disponible, lo que crea una apariencia nativa para cada plataforma.

## <a name="structure"></a>Estructura

Los elementos de [`TableView`](xref:Xamarin.Forms.TableView) se organizan en secciones. En la raíz de `TableView` [`TableRoot`](xref:Xamarin.Forms.TableRoot) , es, que es el elemento primario de una o más instancias de [`TableSection`](xref:Xamarin.Forms.TableSection) . Cada [`TableSection`](xref:Xamarin.Forms.TableSection) una consta de un encabezado y una o varias [`ViewCell`](xref:Xamarin.Forms.ViewCell) instancias:

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

El código de C# equivalente es el siguiente:

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

## <a name="appearance"></a>Aspecto

[`TableView`](xref:Xamarin.Forms.TableView)expone la [`Intent`](xref:Xamarin.Forms.TableView.Intent) propiedad, que se puede establecer en cualquiera de los [`TableIntent`](xref:Xamarin.Forms.TableIntent) miembros de enumeración:

- `Data`: sirve para mostrar entradas de datos. Tenga en cuenta que [ListView](~/xamarin-forms/user-interface/listview/index.md) puede ser una opción mejor para desplazar listas de datos.
- `Form`: se utiliza cuando el TableView actúa como un formulario.
- `Menu`: se usa para presentar un menú de selecciones.
- `Settings`: se usa para mostrar una lista de opciones de configuración.

El [`TableIntent`](xref:Xamarin.Forms.TableIntent) valor que elija puede afectar al modo [`TableView`](xref:Xamarin.Forms.TableView) en que aparece en cada plataforma. Incluso si no hay diferencias claras, se recomienda seleccionar el `TableIntent` que más se aproxime a la forma de usar la tabla.

Además, el color del texto que se muestra para cada [`TableSection`](xref:Xamarin.Forms.TableSection) puede cambiarse estableciendo la `TextColor` propiedad en [`Color`](xref:Xamarin.Forms.Color) .

## <a name="built-in-cells"></a>Celdas integradas

Xamarin.Formsincluye las celdas integradas para recopilar y Mostrar información. Aunque [`ListView`](xref:Xamarin.Forms.ListView) y [`TableView`](xref:Xamarin.Forms.TableView) pueden utilizar las mismas celdas, [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) y [`EntryCell`](xref:Xamarin.Forms.EntryCell) son las más relevantes para un `TableView` escenario.

Vea la apariencia de una [celda de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) para obtener una descripción detallada de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) y [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell).

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)es el control que se va a utilizar para presentar y capturar un estado ON/OFF o `true` / `false` . Define las siguientes propiedades:

- `Text`: texto que se va a mostrar junto al modificador.
- `On`: indica si el modificador se muestra como activado o desactivado.
- `OnColor`: el [`Color`](xref:Xamarin.Forms.Color) del modificador cuando se encuentra en la posición de encendido.

Todas estas propiedades son enlazables.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)también expone el `OnChanged` evento, lo que le permite responder a los cambios en el estado de la celda.

![Ejemplo de SwitchCell](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell)resulta útil cuando es necesario Mostrar datos de texto que el usuario puede editar. Define las siguientes propiedades:

- `Keyboard`: Teclado que se va a mostrar durante la edición. Hay opciones para elementos como valores numéricos, correo electrónico, números de teléfono, etc. [consulte los documentos de la API](xref:Xamarin.Forms.Keyboard).
- `Label`: El texto de la etiqueta que se va a mostrar a la izquierda del campo de entrada de texto.
- `LabelColor`: El color del texto de la etiqueta.
- `Placeholder`: Texto que se va a mostrar en el campo de entrada cuando es null o está vacío. Este texto desaparece cuando comienza la entrada de texto.
- `Text`: El texto del campo de entrada.
- `HorizontalTextAlignment`: Alineación horizontal del texto. Los valores son Center, Left o Right aligned. [Vea los documentos de la API](xref:Xamarin.Forms.TextAlignment).
- `VerticalTextAlignment`: Alineación vertical del texto. Los valores son `Start` , `Center` o `End` .

[`EntryCell`](xref:Xamarin.Forms.EntryCell)también expone el `Completed` evento, que se desencadena cuando el usuario presiona el botón ' DONE ' en el teclado mientras edita el texto.

![Ejemplo de EntryCell](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>Celdas personalizadas

Cuando las celdas integradas no son suficientes, se pueden usar celdas personalizadas para presentar y capturar datos de la forma que tenga sentido para la aplicación. Por ejemplo, puede que desee presentar un control deslizante para permitir que un usuario elija la opacidad de una imagen.

Todas las celdas personalizadas se deben derivar de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la misma clase base que usan todos los tipos de celdas integrados.

Este es un ejemplo de una celda personalizada:

![Ejemplo de celda personalizada](tableview-images/custom-cell.png)

En el ejemplo siguiente se muestra el código XAML que se usa para crear [`TableView`](xref:Xamarin.Forms.TableView) en las capturas de pantallas anteriores:

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

El código de C# equivalente es el siguiente:

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

El elemento raíz en [`TableView`](xref:Xamarin.Forms.TableView) es [`TableRoot`](xref:Xamarin.Forms.TableRoot) y hay [`TableSection`](xref:Xamarin.Forms.TableSection) inmediatamente debajo de `TableRoot` . [`ViewCell`](xref:Xamarin.Forms.ViewCell)Se define directamente en `TableSection` , y [`StackLayout`](xref:Xamarin.Forms.StackLayout) se usa para administrar el diseño de la celda personalizada, aunque aquí se podría usar cualquier diseño.

> [!NOTE]
> A diferencia de [`ListView`](xref:Xamarin.Forms.ListView) , no [`TableView`](xref:Xamarin.Forms.TableView) requiere que las celdas personalizadas (o ninguna) estén definidas en `ItemTemplate` .

## <a name="row-height"></a>Alto de fila

La [`TableView`](xref:Xamarin.Forms.TableView) clase tiene dos propiedades que se pueden usar para cambiar el alto de las filas de las celdas:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight): establece el alto de cada fila en un `int` .
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows): las filas tienen altos variables si están establecidas en `true` . Tenga en cuenta que al establecer esta propiedad en, el alto de las `true` filas se calculará y aplicará automáticamente Xamarin.Forms .

Cuando cambia el alto del contenido de una celda de un [`TableView`](xref:Xamarin.Forms.TableView) , el alto de las filas se actualiza implícitamente en Android y en el plataforma universal de Windows (UWP). Sin embargo, en iOS se debe forzar la actualización estableciendo la [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) propiedad en `true` y llamando al [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

En el ejemplo de XAML siguiente se muestra un [`TableView`](xref:Xamarin.Forms.TableView) que contiene un [`ViewCell`](xref:Xamarin.Forms.ViewCell) :

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

Cuando [`ViewCell`](xref:Xamarin.Forms.ViewCell) se puntea en, `OnViewCellTapped` se ejecuta el controlador de eventos:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

El `OnViewCellTapped` controlador de eventos muestra u oculta el segundo [`Label`](xref:Xamarin.Forms.Label) en y [`ViewCell`](xref:Xamarin.Forms.ViewCell) actualiza explícitamente el tamaño de la celda llamando al [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método.

Las capturas de pantallas siguientes muestran la celda antes de que se Puntee sobre:

![ViewCell antes de cambiar de tamaño](tableview-images/cell-beforeresize.png)

Las siguientes capturas de pantallas muestran la celda después de la derivación:

![ViewCell después de cambiar el tamaño](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> Si esta característica está sobreutilizada, existe una gran probabilidad de degradación del rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [TableView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
