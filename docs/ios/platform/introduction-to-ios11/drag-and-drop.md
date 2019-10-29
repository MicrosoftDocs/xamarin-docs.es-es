---
title: Arrastrar y colocar en Xamarin. iOS
description: En este documento se describe cómo implementar la función de arrastrar y colocar en aplicaciones de Xamarin. iOS con las API introducidas en iOS 11. En concreto, se explica cómo habilitar la función de arrastrar y colocar en UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: 928936815c89dd74d0ad3775f59ea210702c8857
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032172"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastrar y colocar en Xamarin. iOS

_Implementación de la función de arrastrar y colocar para iOS 11_

iOS 11 incluye compatibilidad con arrastrar y colocar para copiar datos entre aplicaciones en el iPad. Los usuarios pueden seleccionar y arrastrar todos los tipos de contenido de las aplicaciones colocadas en paralelo o arrastrando el icono de una aplicación, lo que desencadenará la aplicación para abrirla y permitir que se quiten los datos:

![Arrastre y coloque el ejemplo de la aplicación personalizada en la aplicación de Notes](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Arrastrar y colocar solo está disponible en la misma aplicación en iPhone.

Considere la posibilidad de admitir operaciones de arrastrar y colocar en cualquier lugar en el que pueda crearse o editarse contenido:

- Los controles de texto admiten arrastrar y colocar para todas las aplicaciones compiladas en iOS 11, sin ningún trabajo adicional.
- Las vistas de tabla y las vistas de colección incluyen mejoras en iOS 11 que simplifican la adición del comportamiento de arrastrar y colocar.
- Cualquier otra vista puede realizarse para admitir la función de arrastrar y colocar con personalización adicional.

Al agregar compatibilidad con arrastrar y colocar a las aplicaciones, puede proporcionar diferentes niveles de fidelidad del contenido; por ejemplo, puede proporcionar una versión de texto sin formato y de texto sin formato de los datos para que la aplicación receptora pueda elegir lo que mejor se adapte al destino de arrastre. También es posible personalizar la visualización de arrastre y también permitir arrastrar varios elementos a la vez.

## <a name="drag-and-drop-with-text-controls"></a>Arrastrar y colocar con controles de texto

`UITextView` y `UITextField` admiten automáticamente arrastrar texto seleccionado y colocar el contenido de texto en.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastrar y colocar con UITableView

`UITableView` tiene un control integrado para las interacciones de arrastrar y colocar con las filas de la tabla, requiriendo solo unos cuantos métodos para habilitar el comportamiento predeterminado.

Hay dos interfaces implicadas:

- `IUITableViewDragDelegate`: empaqueta información cuando se inicia una arrastre en la vista de tabla.
- `IUITableViewDropDelegate`: procesa información cuando se intenta y completa una operación de quitar.

En el [ejemplo DragAndDropTableView](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview) , estas dos interfaces se implementan en la clase `UITableViewController`, junto con el delegado y el origen de datos. Están asignadas en el método `ViewDidLoad`:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

A continuación se explica el código necesario mínimo para estas dos interfaces.

### <a name="table-view-drag-delegate"></a>Delegado de arrastre de vista de tabla

El único método _necesario_ para admitir arrastrar una fila desde una vista de tabla es `GetItemsForBeginningDragSession`. Si el usuario comienza a arrastrar una fila, se llamará a este método.

A continuación se muestra una implementación de. Recupera los datos asociados a la fila arrastrada, los codifica y configura una `NSItemProvider` que determina cómo controlarán las aplicaciones la parte "Drop" de la operación (por ejemplo, si pueden controlar el tipo de datos, `PlainText`, en el ejemplo):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Hay muchos métodos opcionales en el delegado de arrastre que se pueden implementar para personalizar el comportamiento de arrastre, como proporcionar varias representaciones de datos que se pueden aprovechar en las aplicaciones de destino (como texto con formato y texto sin formato, o un vector y versiones de mapa de bits de un dibujo). También puede proporcionar representaciones de datos personalizadas que se usarán al arrastrar y colocar dentro de la misma aplicación.

### <a name="table-view-drop-delegate"></a>Quitar delegado de vista de tabla

Se llama a los métodos del delegado Drop cuando se produce una operación de arrastre sobre una vista de tabla o se completa por encima de él. Los métodos necesarios determinan si se permite quitar los datos y qué acciones se realizan en caso de que se complete la eliminación:

- `CanHandleDropSession`: mientras un arrastre está en curso y, potencialmente, se coloca en la aplicación, este método determina si se permite quitar los datos que se arrastran.
- `DropSessionDidUpdate`: mientras la operación de arrastrar está en curso, se llama a este método para determinar qué acción está prevista. Se puede usar la información de la vista de tabla que se está arrastrando, la sesión de arrastre y la ruta de acceso del índice posible para determinar el comportamiento y los comentarios visuales proporcionados al usuario.
- `PerformDrop`: cuando el usuario completa la colocación (al levantar el dedo), este método extrae los datos que se arrastran y modifica la vista de tabla para agregar los datos en una nueva fila (o filas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica si la vista de tabla puede aceptar los datos que se arrastran. En este fragmento de código, `CanLoadObjects` se utiliza para confirmar que esta vista de tabla puede aceptar datos de cadena.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

Se llama repetidamente al método `DropSessionDidUpdate` mientras la operación de arrastrar está en curso, para proporcionar indicaciones visuales al usuario.

En el código siguiente, `HasActiveDrag` se utiliza para determinar si la operación se originó en la vista de tabla actual. Si es así, solo se permite que se muevan las filas individuales.
Si el arrastre procede de otro origen, se indicará una operación de copia:

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

La operación Drop puede ser una de `Cancel`, `Move`o `Copy`.

La intención de colocar puede ser insertar una nueva fila o agregar o anexar datos a una fila existente.

#### <a name="performdrop"></a>PerformDrop

Se llama al método `PerformDrop` cuando el usuario completa la operación y modifica la vista de tabla y el origen de datos para reflejar los datos colocados.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

Se puede agregar código adicional para cargar objetos de datos grandes de forma asincrónica.

### <a name="testing-drag-and-drop"></a>Pruebas de arrastrar y colocar

Debe usar un iPad para probar el [ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview).
Abra el ejemplo junto a otra aplicación (como notas) y arrastre las filas y el texto entre ellas:

![captura de pantalla de la operación de arrastrar en curso](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>Vínculos relacionados

- [Arrastrar y colocar instrucciones de la interfaz humana (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Arrastrar y colocar el ejemplo de vista de tabla](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [Arrastrar y colocar el ejemplo de vista de colección](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [Introducción a la función de arrastrar y colocar (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastrar y colocar con la vista de colección y tabla (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
