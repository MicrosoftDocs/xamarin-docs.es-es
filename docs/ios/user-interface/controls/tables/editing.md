---
title: Editar tablas con Xamarin. iOS
description: En este documento se describe cómo editar tablas en Xamarin. iOS. Se describe el deslizamiento hacia la eliminación, el modo de edición y la inserción de filas.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: c022c1af78a5a3800cd61096c3f142c1ed0235e7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930941"
---
# <a name="editing-tables-with-xamarinios"></a>Editar tablas con Xamarin. iOS

Las características de edición de tablas se habilitan mediante la invalidación de métodos en una `UITableViewSource` subclase. El comportamiento de edición más sencillo es el gesto de deslizar hasta eliminar que se puede implementar con una única invalidación de método.
Se puede realizar una edición más compleja (incluidas las filas en movimiento) con la tabla en modo de edición.

## <a name="swipe-to-delete"></a>Deslizar rápidamente a eliminar

La característica de deslizar rápidamente para eliminar es un gesto natural en iOS que los usuarios esperan. 

 [![Ejemplo de deslizar rápidamente a eliminar](editing-images/image10.png)](editing-images/image10.png#lightbox)

Hay tres invalidaciones de método que afectan al gesto de deslizar rápidamente para mostrar un botón de **eliminación** en una celda:

- **CommitEditingStyle** : el origen de la tabla detecta si este método se invalida y habilita automáticamente el gesto de deslizar hacia la eliminación. La implementación del método debe llamar a `DeleteRows` en `UITableView` para hacer desaparecer las celdas y quitar también los datos subyacentes del modelo (por ejemplo, una matriz, un diccionario o una base de datos). 
- **CanEditRow** : si se reemplaza CommitEditingStyle, se supone que todas las filas son editables. Si este método se implementa y devuelve false (para algunas filas específicas o para todas las filas), el gesto de deslizar a eliminar no estará disponible en esa celda. 
- **TitleForDeleteConfirmation** : especifica opcionalmente el texto del botón **eliminar** . Si este método no está implementado, el texto del botón será "Delete". 

Estos métodos se implementan en la `TableSource` clase siguiente:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

En este ejemplo, se ha `UITableViewSource` actualizado para usar un `List<TableItem>` elemento (en lugar de una matriz de cadenas) como origen de datos porque admite la adición y eliminación de elementos de la colección.

## <a name="edit-mode"></a>Modo de edición

Cuando una tabla está en modo de edición, el usuario ve un widget "detener" rojo en cada fila, lo que revela un botón eliminar cuando se toca. En la tabla también se muestra un icono de "identificador" para indicar que la fila se puede arrastrar para cambiar el orden.
En el ejemplo **TableEditMode** se implementan estas características como se muestra.

 [![En el ejemplo TableEditMode se implementan estas características como se muestra](editing-images/image11.png)](editing-images/image11.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla:

- **CanEditRow** : indica si se puede editar cada fila. Devuelve false para evitar la operación de deslizar y eliminar el dedo en el modo de edición. 
- **CanMoveRow** : devuelve true para habilitar el movimiento ' handle ' o false para evitar el movimiento. 
- **EditingStyleForRow** : cuando la tabla está en modo de edición, el valor devuelto de este método determina si la celda muestra el icono de eliminación de color rojo o el icono de agregar verde. Devuelve `UITableViewCellEditingStyle.None` si la fila no debe ser editable. 
- **MoveRow** : se le llama cuando se mueve una fila, de modo que la estructura de datos subyacente se puede modificar para que coincida con los datos que se muestran en la tabla. 

La implementación de los tres primeros métodos es relativamente sencilla, a menos que desee utilizar `indexPath` para cambiar el comportamiento de filas específicas, codificando simplemente los valores devueltos para toda la tabla.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

La `MoveRow` implementación es un poco más complicada porque necesita modificar la estructura de datos subyacente para que coincida con el nuevo pedido. Dado que los datos se implementan como `List` el código siguiente, elimina el elemento de datos en su ubicación anterior y lo inserta en la nueva ubicación. Si los datos se almacenaron en una tabla de base de datos SQLite con una columna "Order" (por ejemplo,), este método tendría que realizar algunas operaciones SQL para reordenar los números de la columna.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Por último, para obtener la tabla en el modo de edición, el botón **Editar** debe llamar a `SetEditing` este

```csharp
table.SetEditing (true, true);
```

y cuando el usuario haya terminado de editar, el botón **listo** debe desactivar el modo de edición:

```csharp
table.SetEditing (false, true);
```

## <a name="row-insertion-editing-style"></a>Estilo de edición de inserción de filas

La inserción de filas desde dentro de la tabla es una interfaz de usuario no común: el ejemplo principal de las aplicaciones estándar de iOS es la pantalla **Editar contacto** . En esta captura de pantalla se muestra cómo funciona la funcionalidad de inserción de filas: en el modo de edición hay una fila adicional en la que, cuando se hace clic en ella, se insertan filas adicionales en los datos. Una vez completada la edición, se quita la fila temporal **(agregar nueva)** .

 [![Una vez completada la edición, se quita la nueva fila temporal agregar.](editing-images/image12.png)](editing-images/image12.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla. Estos métodos se han implementado como se indica a continuación en el código de ejemplo:

- **EditingStyleForRow** : devuelve `UITableViewCellEditingStyle.Delete` para las filas que contienen datos y devuelve `UITableViewCellEditingStyle.Insert` para la última fila (que se agregará específicamente para comportarse como un botón de inserción). 
- **CustomizeMoveTarget** : mientras el usuario mueve una celda, el valor devuelto de este método opcional puede invalidar su elección de ubicación. Esto significa que puede evitar que "quite" la celda en determinadas posiciones, como este ejemplo, que impide que se mueva una fila después de la fila **(agregar nuevo)** . 
- **CanMoveRow** : devuelve true para habilitar el movimiento ' handle ' o false para evitar el movimiento. En el ejemplo, la última fila tiene el "identificador" de movimiento oculto porque está pensado para el servidor como un botón Insertar. 

También agregamos dos métodos personalizados para agregar la fila ' INSERT ' y, a continuación, volver a quitarla cuando ya no sea necesaria. Se llaman desde los botones **Editar** y **listo** :

- **WillBeginTableEditing** : cuando se toca el botón **Editar** , llama `SetEditing` a para poner la tabla en modo de edición. Esto desencadena el método WillBeginTableEditing en el que se muestra la fila **(agregar nuevo)** al final de la tabla para que actúe como un "botón Insertar". 
- **DidFinishTableEditing** : cuando se toca el botón listo, `SetEditing` se vuelve a llamar para desactivar el modo de edición. En el código de ejemplo se quita la fila **(agregar nuevo)** de la tabla cuando ya no se necesita la edición. 

Estas invalidaciones de método se implementan en el archivo de ejemplo **TableEditModeAdd/Code/TableSource. CS**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Estos dos métodos personalizados se utilizan para agregar y quitar la fila **(agregar nuevo)** cuando el modo de edición de la tabla está habilitado o deshabilitado:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Por último, este código crea instancias de los botones **Editar** y **listo** , con expresiones lambda que habilitan o deshabilitan el modo de edición cuando se tocan:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Este patrón de interfaz de usuario de inserción de filas no se utiliza muy a menudo, pero también puede utilizar los `UITableView.BeginUpdates` `EndUpdates` métodos y para animar la inserción o eliminación de las celdas de cualquier tabla. La regla para usar esos métodos es que la diferencia en el valor devuelto por `RowsInSection` entre las `BeginUpdates` `EndUpdates` llamadas y debe coincidir con el número neto de celdas agregadas o eliminadas con los `InsertRows` `DeleteRows` métodos y. Si el origen de información subyacente no se cambia para que coincida con las inserciones o eliminaciones en la vista de tabla, se producirá un error.

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
