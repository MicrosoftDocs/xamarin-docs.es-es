---
title: Cambiar el alto de fila automáticamente en Xamarin. iOS
description: En este documento se describe cómo agregar a las filas de la vista de tabla de aplicaciones de Xamarin. iOS cuyo alto varía en función del contenido. Describe el diseño de las celdas en el diseñador de iOS y la habilitación del cambio de tamaño automático.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4129370ecb465340a893e0a7f16703a08cc1db72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021932"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Cambiar el alto de fila automáticamente en Xamarin. iOS

A partir de iOS 8, Apple ha agregado la capacidad de crear una vista de tabla (`UITableView`) que puede aumentar y reducir automáticamente el alto de una fila determinada en función del tamaño de su contenido mediante el diseño automático, las clases y las restricciones de tamaño.

iOS 11 ha agregado la posibilidad de que las filas se expandan automáticamente. Ahora se puede cambiar automáticamente el tamaño de los encabezados, pies de página y celdas según su contenido. Sin embargo, si la tabla se crea en el diseñador de iOS, Interface Builder o si tiene un alto de filas fijo, debe habilitar manualmente las celdas de autoajuste de tamaño, como se describe en esta guía.

## <a name="cell-layout-in-the-ios-designer"></a>Diseño de celda en el diseñador de iOS

Abra el guion gráfico de la vista de tabla para la que desea que se cambie el tamaño automático de la fila en el diseñador de iOS, seleccione el *prototipo* de la celda y diseñe el diseño de la celda. Por ejemplo:

[![](autosizing-row-height-images/table01.png "The Cell's Prototype design")](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento del prototipo, agregue restricciones para mantener los elementos en la posición correcta a medida que se cambie el tamaño de la vista de tabla para rotación o tamaños de pantalla diferentes del dispositivo iOS. Por ejemplo, al anclar el `Title` en la parte superior, izquierda y derecha de la *vista de contenido*de la celda:

[![](autosizing-row-height-images/table02.png "Pinning the Title to the top, left and right of the Cells Content View")](autosizing-row-height-images/table02.png#lightbox)

En el caso de la tabla de ejemplo, el pequeño `Label` (bajo el `Title`) es el campo que se puede reducir y aumentar para aumentar o disminuir el alto de la fila. Para lograr este efecto, agregue las siguientes restricciones para anclar los elementos izquierdo, derecho, superior e inferior de la etiqueta:

[![](autosizing-row-height-images/table03.png "These constraints to pin the left, right, top and bottom of the label")](autosizing-row-height-images/table03.png#lightbox)

Ahora que hemos restringido totalmente los elementos de la celda, es necesario aclarar qué elemento debe ajustarse. Para ello, establezca el **contenido hugging prioridad** y la **prioridad de resistencia de compresión de contenido** según sea necesario en la sección de **diseño** del panel de propiedades:

[![](autosizing-row-height-images/table03a.png "The Layout section of the Properties Pad")](autosizing-row-height-images/table03a.png#lightbox)

Establezca el elemento que desea expandir para que tenga un valor de prioridad hugging **inferior** y un valor de prioridad de resistencia de compresión **inferior** .

A continuación, es necesario seleccionar el prototipo de celda y asignarle un **identificador**único:

[![](autosizing-row-height-images/table04.png "Giving the Cell Prototype a unique Identifier")](autosizing-row-height-images/table04.png#lightbox)

En el caso de nuestro ejemplo, `GrowCell`. Usaremos este valor más adelante al rellenar la tabla.

> [!IMPORTANT]
> Si la tabla contiene más de un tipo de celda (**prototipo**), debe asegurarse de que cada tipo tiene su propio `Identifier` único para que funcione el cambio de tamaño de fila automático.

Para cada elemento de nuestro prototipo de celda, asigne un **nombre** para exponerlo al C# código. Por ejemplo:

[![](autosizing-row-height-images/table05.png "Assign a Name to expose it to C# code")](autosizing-row-height-images/table05.png#lightbox)

A continuación, agregue una clase personalizada para el `UITableViewController`, el `UITableView` y el `UITableCell` (prototipo). Por ejemplo: 

[![](autosizing-row-height-images/table06.png "Adding a custom class for the UITableViewController, the UITableView and the UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Por último, para asegurarse de que todo el contenido esperado se muestra en nuestra etiqueta, establezca la propiedad **Lines** en `0`:

[![](autosizing-row-height-images/table06.png "The Lines property set to 0")](autosizing-row-height-images/table06a.png#lightbox)

Con la interfaz de usuario definida, vamos a agregar el código para habilitar el cambio de tamaño automático de filas.

## <a name="enabling-auto-resizing-height"></a>Habilitar el alto de cambio de tamaño automático

En el origen de los orígenes (`UITableViewDatasource`) o el origen (`UITableViewSource`) de la vista de tabla, cuando se quita la cola de una celda, es necesario usar el `Identifier` que se definió en el diseñador. Por ejemplo:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

De forma predeterminada, se establecerá la vista de tabla para cambiar el tamaño de las filas de manera automática. Para asegurarse de esto, la propiedad `RowHeight` debe establecerse en `UITableView.AutomaticDimension`. También es necesario establecer la propiedad `EstimatedRowHeight` en nuestro `UITableViewController`. Por ejemplo:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

No es necesario que esta estimación sea exacta, solo una estimación aproximada del alto promedio de cada fila en la vista de tabla.

Con este código en su lugar, cuando se ejecute la aplicación, cada fila se reducirá y aumentará en función del alto de la última etiqueta del prototipo de celda. Por ejemplo:

[![](autosizing-row-height-images/table07.png "A sample table run")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [GrowRowTable (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
