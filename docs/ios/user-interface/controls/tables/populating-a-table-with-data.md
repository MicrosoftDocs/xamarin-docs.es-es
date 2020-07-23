---
title: Rellenar una tabla con datos en Xamarin. iOS
description: En este documento se describe cómo rellenar una tabla con datos en una aplicación de Xamarin. iOS. Describe UITableViewSource, la reutilización de celdas, la adición de un índice, y encabezados y pies de página.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 48aeaf8e3036c9b4e1ed548208b7daa822a00913
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933424"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Rellenar una tabla con datos en Xamarin. iOS

Para agregar filas a `UITableView` , debe implementar una `UITableViewSource` subclase e invalidar los métodos a los que llama la vista de tabla para rellenarse.

En esta guía se trata:

- Subclase de un UITableViewSource
- Reutilización de celdas
- Agregar un índice
- Agregar encabezados y pies de página

<a name="Subclassing_UITableViewSource"></a>

## <a name="subclassing-uitableviewsource"></a>Subclase de UITableViewSource

Una `UITableViewSource` subclase se asigna a cada `UITableView` . La vista de tabla consulta la clase de origen para determinar cómo se representa a sí misma (por ejemplo, cuántas filas son necesarias y el alto de cada fila si son diferentes de los predeterminados). Lo más importante es que el origen suministre cada vista de celda rellenada con datos.

Solo se requieren dos métodos obligatorios para que se muestren los datos de una tabla:

- **RowsInSection** : devuelve un [`nint`](~/cross-platform/macios/nativetypes.md) recuento del número total de filas de datos que debe mostrar la tabla.
- **GetCell** : devuelve un `UITableViewCell` rellenado con datos para el índice de fila correspondiente que se pasa al método.

El archivo de ejemplo BasicTable **TableSource.CS** tiene la implementación más sencilla posible de `UITableViewSource` . Puede ver en el fragmento de código siguiente que acepta una matriz de cadenas para mostrar en la tabla y devuelve un estilo de celda predeterminado que contiene cada cadena:

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //if there are no cells to reuse, create a new one
            if (cell == null)
            { 
                cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); 
            }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

`UITableViewSource`Puede utilizar cualquier estructura de datos, desde una matriz de cadenas simple (como se muestra en este ejemplo) hasta una lista <> u otra colección. La implementación de `UITableViewSource` métodos aísla la tabla de la estructura de datos subyacente.

Para usar esta subclase, cree una matriz de cadenas para construir el origen y luego asígnelo a una instancia de `UITableView` :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

La tabla resultante tiene el siguiente aspecto:

 [![Tabla de ejemplo en ejecución](populating-a-table-with-data-images/image3.png)](populating-a-table-with-data-images/image3.png#lightbox)

La mayoría de las tablas permiten al usuario tocar una fila para seleccionarla y realizar alguna otra acción (como reproducir una canción, llamar a un contacto o Mostrar otra pantalla). Para ello, hay algunas cosas que debemos hacer. En primer lugar, vamos a crear un AlertController para mostrar un mensaje cuando el usuario haga clic en una fila agregando lo siguiente al `RowSelected` método:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

A continuación, cree una instancia de nuestro controlador de vista:

```csharp
HomeScreen owner;
```

Agregue un constructor a la clase UITableViewSource que toma un controlador de vista como parámetro y lo guarda en un campo:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

Modifique el método ViewDidLoad donde se crea la clase UITableViewSource para pasar la `this` Referencia:

```csharp
table.Source = new TableSource(tableItems, this);
```

Por último, vuelva al `RowSelected` método, llame a `PresentViewController` en el campo almacenado en caché:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

Ahora el usuario puede tocar una fila y aparecerá una alerta:

 [![La alerta de fila seleccionada](populating-a-table-with-data-images/image4.png)](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>Reutilización de celdas

En este ejemplo, solo hay seis elementos, por lo que no se requiere ninguna reutilización de celdas. Sin embargo, al mostrar cientos o miles de filas, sería un desperdicio de memoria para crear cientos o miles de `UITableViewCell` objetos cuando solo caben unos pocos en la pantalla a la vez.

Para evitar esta situación, cuando una celda desaparece de la pantalla, su vista se coloca en una cola para su reutilización. A medida que el usuario se desplaza, la tabla llama `GetCell` a para solicitar nuevas vistas para mostrar: para reutilizar una celda existente (que no se está mostrando actualmente), simplemente llame al `DequeueReusableCell` método. Si una celda está disponible para su reutilización, se devolverá; de lo contrario, se devolverá un valor NULL y el código deberá crear una nueva instancia de celda.

Este fragmento de código del ejemplo muestra el patrón:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`Crea de forma eficaz colas independientes para diferentes tipos de celda. En este ejemplo, todas las celdas tienen el mismo aspecto, de modo que solo se usa un identificador codificado de forma rígida. Si hay diferentes tipos de celda, cada uno debe tener una cadena de identificador diferente, tanto cuando se crea una instancia como cuando se solicitan desde la cola de reutilizar.

### <a name="cell-reuse-in-ios-6"></a>Reutilización de celdas en iOS 6 +

iOS 6 agregó un patrón de reutilización de celdas similar al de una introducción con las vistas de colección. Aunque el patrón de reutilización existente mostrado anteriormente todavía se admite por compatibilidad con versiones anteriores, este nuevo patrón es preferible, ya que elimina la necesidad de la comprobación de NULL en la celda.

Con el nuevo patrón, una aplicación registra la clase de celda o Xib que se va a usar mediante una llamada a `RegisterClassForCellReuse` o `RegisterNibForCellReuse` en el constructor del controlador. A continuación, al quitar de la cola la celda en el `GetCell` método, basta con llamar a `DequeueReusableCell` pasar el identificador registrado para la clase de celda o Xib y la ruta de acceso del índice.

Por ejemplo, el código siguiente registra una clase de celda personalizada en un UITableViewController:

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

Una vez registrada la clase celda, la celda se puede quitar de la cola en el `GetCell` método de `UITableViewSource` sin necesidad de la comprobación nula adicional, como se muestra a continuación:

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Tenga en cuenta que al usar el nuevo patrón de reutilización con una clase de celda personalizada, debe implementar el constructor que toma un `IntPtr` , como se muestra en el siguiente fragmento de código; de lo contrario, Objective-C no podrá construir una instancia de la clase Cell:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Puede ver ejemplos de los temas descritos anteriormente en el ejemplo **BasicTable** vinculado a este artículo.

<a name="Adding_an_Index"></a>

## <a name="adding-an-index"></a>Agregar un índice

Un índice ayuda al usuario a desplazarse por las listas largas, normalmente ordenadas alfabéticamente, aunque puede indizar según los criterios que desee. En el ejemplo **BasicTableIndex** se carga una lista de elementos mucho más larga de un archivo para mostrar el índice. Cada elemento del índice corresponde a una ' sección ' de la tabla.

 [![La presentación del índice](populating-a-table-with-data-images/image5.png)](populating-a-table-with-data-images/image5.png#lightbox)

Para admitir ' secciones ', es necesario agrupar los datos subyacentes de la tabla, por lo que el ejemplo BasicTableIndex crea un a `Dictionary<>` partir de la matriz de cadenas usando la primera letra de cada elemento como clave del diccionario:

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

`UITableViewSource`A continuación, la subclase necesita que se agreguen o modifiquen los métodos siguientes para usar `Dictionary<>` :

- **NumberOfSections** : este método es opcional; de forma predeterminada, la tabla presupone una sección. Al mostrar un índice, este método debe devolver el número de elementos del índice (por ejemplo, 26 si el índice contiene todas las letras del alfabeto inglés).
- **RowsInSection** : devuelve el número de filas de una sección determinada.
- **SectionIndexTitles** : devuelve la matriz de cadenas que se utilizará para mostrar el índice. El código de ejemplo devuelve una matriz de letras.

Los métodos actualizados en el archivo de ejemplo **BasicTableIndex/TableSource. CS** tienen el siguiente aspecto:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Los índices generalmente se usan solo con el estilo de tabla simple.

<a name="Adding_Headers_and_Footers"></a>

## <a name="adding-headers-and-footers"></a>Agregar encabezados y pies de página

Los encabezados y pies de página se pueden usar para agrupar las filas de una tabla visualmente. La estructura de datos necesaria es muy similar a agregar un índice: un `Dictionary<>` funciona realmente bien. En lugar de usar el alfabeto para agrupar las celdas, en este ejemplo se agruparán las verduras por tipo botánico.
El resultado tendrá un aspecto similar al siguiente:

 [![Encabezados y pies de página de ejemplo](populating-a-table-with-data-images/image6.png)](populating-a-table-with-data-images/image6.png#lightbox)

Para mostrar encabezados y pies de página, la `UITableViewSource` subclase requiere estos métodos adicionales:

- **TitleForHeader** : devuelve el texto que se va a usar como encabezado.
- **TitleForFooter** : devuelve el texto que se va a usar como pie de página.

Los métodos actualizados en el archivo de ejemplo **BasicTableHeaderFooter/Code/TableSource. CS** tienen el siguiente aspecto:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

Puede personalizar aún más la apariencia del encabezado y del pie de página con un objeto de vista, mediante las `GetViewForHeader` `GetViewForFooter` invalidaciones del método y en `UITableViewSource` .

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
