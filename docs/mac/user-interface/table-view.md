---
title: Vistas de tabla en Xamarin. Mac
description: En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. Describe la creación de vistas de tabla en Xcode y Interface Builder e interactuar con ellas en el código.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d768be516b67ed23bdb851d87286a856a7269de4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935556"
---
# <a name="table-views-in-xamarinmac"></a>Vistas de tabla en Xamarin. Mac

_En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. Describe la creación de vistas de tabla en Xcode y Interface Builder e interactuar con ellas en el código._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a las mismas vistas de tabla que un desarrollador que trabaja en *Objective-C* y *Xcode* . Dado que Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las vistas de tabla (o, opcionalmente, crearlas directamente en código de C#).

Una vista de tabla muestra los datos en formato tabular que contiene una o más columnas de información en varias filas. En función del tipo de vista de tabla que se va a crear, el usuario puede ordenar por columna, reorganizar columnas, Agregar columnas, quitar columnas o modificar los datos contenidos en la tabla.

[![Tabla de ejemplo](table-view-images/intro01.png)](table-view-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con las vistas de tabla en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la sección [exponer clases y métodos de C# a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican `Register` los `Export` comandos y que se usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction_to_Table_Views"></a>

## <a name="introduction-to-table-views"></a>Introducción a las vistas de tabla

Una vista de tabla muestra los datos en formato tabular que contiene una o más columnas de información en varias filas. Las vistas de tabla se muestran dentro de las vistas de desplazamiento ( `NSScrollView` ) y a partir de macOS 10,7, puede usar cualquier `NSView` celda en lugar de celdas ( `NSCell` ) para mostrar las filas y las columnas. Dicho esto, sin embargo, todavía puede usarse `NSCell` , por lo general, `NSTableCellView` y crear las filas y columnas personalizadas.

Una vista de tabla no almacena sus propios datos, sino que se basa en un origen de datos ( `NSTableViewDataSource` ) para proporcionar las filas y columnas necesarias, según sea necesario.

El comportamiento de una vista de tabla se puede personalizar proporcionando una subclase del delegado de la vista de tabla ( `NSTableViewDelegate` ) para admitir la administración de columnas de tabla, el tipo para seleccionar la funcionalidad, la selección de filas y la edición, el seguimiento personalizado y las vistas personalizadas para columnas y filas individuales.

Al crear vistas de tabla, Apple sugiere lo siguiente:

- Permite al usuario ordenar la tabla haciendo clic en los encabezados de columna.
- Cree encabezados de columna que sean nombres o frases cortas que describan los datos que se muestran en esa columna.

Para obtener más información, consulte la sección [vistas de contenido](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) de las directrices de la interfaz humana de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode"></a>

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Crear y mantener vistas de tabla en Xcode

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un `.storyboard` archivo que se incluye automáticamente en el proyecto. Para editar el diseño de Windows, en el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![Seleccionar el guión gráfico principal](table-view-images/edit01.png)](table-view-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![Edición de la interfaz de usuario en Xcode](table-view-images/edit02.png)](table-view-images/edit02.png#lightbox)

Escriba `table` en el cuadro **de búsqueda del inspector** de la biblioteca para que sea más fácil encontrar los controles de la vista de tabla:

[![Seleccionar una vista de tabla de la biblioteca](table-view-images/edit03.png)](table-view-images/edit03.png#lightbox)

Arrastre una vista de tabla hasta el controlador de vista en el editor de la **interfaz**, haga que rellene el área de contenido del controlador de vista y establézcalo en donde se reduzca y crezca con la ventana del **Editor de restricciones**:

[![Modificar restricciones](table-view-images/edit04.png)](table-view-images/edit04.png#lightbox)

Seleccione la vista de tabla en la jerarquía de la **interfaz** y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/edit05.png)](table-view-images/edit05.png#lightbox)

- **Modo de contenido** : permite usar las vistas ( `NSView` ) o las celdas ( `NSCell` ) para mostrar los datos de las filas y columnas. A partir de macOS 10,7, debe utilizar las vistas.
- **Floating Group Rows** : Si `true` es, la vista de tabla dibujará celdas agrupadas como si fueran flotantes.
- **Columnas** : define el número de columnas que se muestran.
- **Encabezados** : si es `true` , las columnas tendrán encabezados.
- **Reordenación** : Si `true` es, el usuario podrá arrastrar de nuevo el orden de las columnas de la tabla.
- **Cambiar** el tamaño: Si `true` es, el usuario podrá arrastrar encabezados de columna para cambiar el tamaño de las columnas.
- **Tamaño de columna** : controla el modo en que la tabla ajustará automáticamente el tamaño de las columnas.
- **Resaltar** : controla el tipo de resaltado que usa la tabla cuando se selecciona una celda.
- **Filas alternativas** : si es `true` , alguna otra fila tendrá un color de fondo diferente.
- **Cuadrícula horizontal** : selecciona el tipo de borde dibujado entre las celdas horizontalmente.
- **Cuadrícula vertical** : selecciona el tipo de borde que se dibuja entre las celdas verticalmente.
- **Color** de la cuadrícula: establece el color del borde de la celda.
- **Background** : establece el color de fondo de la celda.
- **Selección** : permite controlar el modo en que el usuario puede seleccionar las celdas de la tabla como:
  - **Múltiple** : si es `true` , el usuario puede seleccionar varias filas y columnas.
  - **Columna** : si es `true` , el usuario puede seleccionar columnas.
  - **Escriba Select** -if `true` , el usuario puede escribir un carácter para seleccionar una fila.
  - **Empty** : Si `true` no se requiere que el usuario seleccione una fila o una columna, la tabla no permite ninguna selección.
- **Autosave** : nombre en el que se guarda automáticamente el formato de las tablas.
- **Información de columna** : Si `true` es, el orden y el ancho de las columnas se guardarán automáticamente.
- **Saltos de línea** : Seleccione cómo controla la celda los saltos de línea.
- **Trunca la última línea visible** : si es `true` , la celda se truncará en los datos no caben dentro de sus límites.

> [!IMPORTANT]
> A menos que esté manteniendo una aplicación de Xamarin. Mac heredada, las `NSView` vistas de tabla basadas en se deben usar en `NSCell` vistas de tabla basadas en. `NSCell`se considera heredada y puede que no se admita en el futuro.

Seleccione una columna de la tabla en la jerarquía de la **interfaz** y las siguientes propiedades estarán disponibles en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/edit06.png)](table-view-images/edit06.png#lightbox)

- **Título** : establece el título de la columna.
- **Alineación** : establece la alineación del texto dentro de las celdas.
- **Fuente de título** : selecciona la fuente del texto de encabezado de la celda.
- **Criterio de ordenación** : es la clave utilizada para ordenar los datos de la columna. Déjelo en blanco si el usuario no puede ordenar esta columna.
- **Selector** : es la **acción** que se usa para realizar la ordenación. Déjelo en blanco si el usuario no puede ordenar esta columna.
- **Order** : es el criterio de ordenación de los datos de las columnas.
- **Cambio de tamaño** : selecciona el tipo de cambio de tamaño de la columna.
- **Editable** : si es `true` , el usuario puede editar las celdas de una tabla basada en celdas.
- **Hidden** : Si `true` es, la columna está oculta.

También puede cambiar el tamaño de la columna arrastrando su identificador (verticalmente centrado en el lado derecho de la columna) hacia la izquierda o la derecha.

Vamos a seleccionar cada columna en la vista de tabla y asignaremos a la primera columna un **título** `Product` y la segunda `Details` .

Seleccione una vista de celda de tabla ( `NSTableViewCell` ) en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/edit07.png)](table-view-images/edit07.png#lightbox)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de vista de tabla (de forma predeterminada, es `NSTextField` ) en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el inspector de **atributos**:

[![Inspector de atributo](table-view-images/edit08.png)](table-view-images/edit08.png#lightbox)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, se usa un campo de texto estándar para mostrar los datos de una celda de una columna.

Seleccione una vista de celda de tabla ( `NSTableFieldCell` ) en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/edit09.png)](table-view-images/edit09.png#lightbox)

La configuración más importante aquí es:

- **Diseño** : Seleccione cómo se colocan las celdas de esta columna.
- **Usa el modo de una sola línea** : Si `true` es, la celda está limitada a una sola línea.
- **Ancho del primer diseño en tiempo de ejecución** : Si `true` es, la celda preferirá el ancho establecido para ella (ya sea de forma manual o automática) cuando se muestre la primera vez que se ejecute la aplicación.
- **Acción** : controla cuándo se envía la **acción** de edición para la celda.
- **Comportamiento** : define si una celda es seleccionable o editable.
- **Texto enriquecido** : si es `true` , la celda puede mostrar texto con formato y con estilo.
- **Deshacer** : si es `true` , la celda asume la responsabilidad del comportamiento de deshacer.

Seleccione la vista de celda de tabla ( `NSTableFieldCell` ) en la parte inferior de una columna de tabla de la jerarquía de la **interfaz**:

[![Seleccionar la vista de celda de tabla](table-view-images/edit10.png)](table-view-images/edit10.png#lightbox)

Esto le permite editar la vista de celda de tabla utilizada como _patrón_ base para todas las celdas creadas para la columna especificada.

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>Agregar acciones y salidas

Al igual que cualquier otro control de la interfaz de usuario de coco, es necesario exponer nuestra vista de tabla y sus columnas y celdas al código de C# mediante **acciones** y **salidas** (según la funcionalidad necesaria).

El proceso es el mismo para cualquier elemento de vista de tabla que desee exponer:

1. Cambie al **Editor de asistentes** y asegúrese de que el `ViewController.h` archivo está seleccionado: 

    [![Editor de asistentes](table-view-images/edit11.png)](table-view-images/edit11.png#lightbox)
2. Seleccione la vista de tabla de la jerarquía de la **interfaz**, haga clic en el control y arrástrela hasta el `ViewController.h` archivo.
3. Cree una **salida** para la vista de tabla denominada `ProductTable` : 

    [![Configuración de una salida](table-view-images/edit13.png)](table-view-images/edit13.png#lightbox)
4. Cree **salidas** para las columnas de las tablas, también denominadas `ProductColumn` y `DetailsColumn` : 

    [![Configuración de una salida](table-view-images/edit14.png)](table-view-images/edit14.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación, escribiremos el código para mostrar algunos datos de la tabla cuando se ejecute la aplicación.

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-table-view"></a>Rellenar la vista de tabla

Con nuestra vista de tabla diseñada en Interface Builder y expuesta a través de una **salida**, es necesario crear el código de C# para rellenarlo.

En primer lugar, vamos a crear una nueva `Product` clase para almacenar la información de las filas individuales. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `Product` para el **nombre** y haga clic en el botón **nuevo** :

[![Crear una clase vacía](table-view-images/populate01.png)](table-view-images/populate01.png#lightbox)

Haga que el `Product.cs` archivo tenga el aspecto siguiente:

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

A continuación, es necesario crear una subclase de `NSTableDataSource` para proporcionar los datos de la tabla tal y como se solicitan. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `ProductTableDataSource` para el **nombre** y haga clic en el botón **nuevo** .

Edite el `ProductTableDataSource.cs` archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

Esta clase tiene almacenamiento para los elementos de la vista de tabla e invalida el `GetRowCount` para devolver el número de filas de la tabla.

Por último, es necesario crear una subclase de `NSTableDelegate` para proporcionar el comportamiento de la tabla. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `ProductTableDelegate` para el **nombre** y haga clic en el botón **nuevo** .

Edite el `ProductTableDelegate.cs` archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

Cuando se crea una instancia de `ProductTableDelegate` , también se pasa una instancia de `ProductTableDataSource` que proporciona los datos de la tabla. El `GetViewForItem` método es responsable de devolver una vista (datos) para mostrar la celda para una columna y una fila. Si es posible, se volverá a usar una vista existente para mostrar la celda, en caso de que no se deba crear una nueva vista.

Para rellenar la tabla, vamos a editar el `ViewController.cs` archivo y hacer que el método tenga el `AwakeFromNib` aspecto siguiente:

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Si ejecutamos la aplicación, se muestra lo siguiente:

[![Ejecución de una aplicación de ejemplo](table-view-images/populate02.png)](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir que el usuario ordene los datos de la tabla haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la `Product` columna, escriba `Title` para la **clave de ordenación**, `compare:` para el **selector** y seleccione `Ascending` para el **pedido**:

[![Establecer el criterio de ordenación](table-view-images/sort01.png)](table-view-images/sort01.png#lightbox)

Seleccione la `Details` columna, escriba `Description` para la **clave de ordenación**, `compare:` para el **selector** y seleccione `Ascending` para el **pedido**:

[![Establecer el criterio de ordenación](table-view-images/sort02.png)](table-view-images/sort02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductTableDataSource.cs` archivo y agregar los siguientes métodos:

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

El `Sort` método nos permite ordenar los datos en el origen de datos basándose en un `Product` campo de clase determinado en orden ascendente o descendente. Se llamará al método invalidado `SortDescriptorsChanged` cada vez que se haga clic en un encabezado de columna. Se le pasará el valor de **clave** que se establece en Interface Builder y el criterio de ordenación de esa columna.

Si ejecutamos la aplicación y hacemos clic en los encabezados de columna, las filas se ordenarán por esa columna:

[![Ejecución de una aplicación de ejemplo](table-view-images/sort03.png)](table-view-images/sort03.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>Selección de filas

Si desea permitir que el usuario seleccione una sola fila, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y desactive la casilla **múltiple** en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/select01.png)](table-view-images/select01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el `ProductTableDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de tabla. Vuelva a `false` `ShouldSelectRow` para cualquier fila que no desee que el usuario pueda seleccionar o `false` para cada fila si no desea que el usuario pueda seleccionar las filas que desee.

La vista de tabla ( `NSTableView` ) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectRow(nint)`-Anula la selección de la fila especificada en la tabla.
- `SelectRow(nint,bool)`: Selecciona la fila especificada. Pase `false` el segundo parámetro para seleccionar solo una fila cada vez.
- `SelectedRow`: Devuelve la fila actual seleccionada en la tabla.
- `IsRowSelected(nint)`-Devuelve `true` si se selecciona la fila especificada.

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>Selección de varias filas

Si desea permitir que el usuario seleccione varias filas, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **múltiple** en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/select02.png)](table-view-images/select02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el `ProductTableDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de tabla. Vuelva a `false` `ShouldSelectRow` para cualquier fila que no desee que el usuario pueda seleccionar o `false` para cada fila si no desea que el usuario pueda seleccionar las filas que desee.

La vista de tabla ( `NSTableView` ) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectAll(NSObject)`-Anula la selección de todas las filas de la tabla. Use `this` para el primer parámetro que se va a enviar en el objeto que realiza la selección. 
- `DeselectRow(nint)`-Anula la selección de la fila especificada en la tabla.
- `SelectAll(NSobject)`: Selecciona todas las filas de la tabla. Use `this` para el primer parámetro que se va a enviar en el objeto que realiza la selección.
- `SelectRow(nint,bool)`: Selecciona la fila especificada. Pass `false` para el segundo parámetro borra la selección y selecciona una sola fila, Pass `true` para extender la selección e incluir esta fila.
- `SelectRows(NSIndexSet,bool)`: Selecciona el conjunto de filas especificado. Pass `false` para el segundo parámetro borre la selección y seleccione solo estas filas, Pass `true` para extender la selección e incluir estas filas.
- `SelectedRow`: Devuelve la fila actual seleccionada en la tabla.
- `SelectedRows`: Devuelve un objeto `NSIndexSet` que contiene los índices de las filas seleccionadas.
- `SelectedRowCount`: Devuelve el número de filas seleccionadas.
- `IsRowSelected(nint)`-Devuelve `true` si se selecciona la fila especificada.

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>Tipo para seleccionar fila

Si desea permitir que el usuario escriba un carácter con la vista de tabla seleccionada y seleccione la primera fila que contiene ese carácter, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **tipo Select** en el **Inspector de atributos**:

[![Establecer el tipo de selección](table-view-images/type01.png)](table-view-images/type01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductTableDelegate.cs` archivo y agregaremos el método siguiente:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

El `GetNextTypeSelectMatch` método toma el determinado `searchString` y devuelve la fila de la primera `Product` que tiene esa cadena en `Title` .

Si ejecutamos la aplicación y escribemos un carácter, se selecciona una fila:

[![Ejecución de una aplicación de ejemplo](table-view-images/type02.png)](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>Reordenación de columnas

Si desea permitir que el usuario arrastre columnas de reordenación en la vista de tabla, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **reordenación** en el **Inspector de atributos**:

[![Inspector de atributo](table-view-images/reorder01.png)](table-view-images/reorder01.png#lightbox)

Si asignamos un valor a la propiedad **autosave** y comprobamos el campo de **información de columna** , los cambios que se realicen en el diseño de la tabla se guardarán automáticamente y se restaurarán la próxima vez que se ejecute la aplicación.

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductTableDelegate.cs` archivo y agregaremos el método siguiente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

El `ShouldReorder` método debe devolver `true` para cualquier columna que desee permitir que el arrastre se reordene a, de lo `newColumnIndex` contrario, devuelva `false` ;

Si ejecutamos la aplicación, podemos arrastrar encabezados de columna para reordenar las columnas:

[![Ejemplo de las columnas reordenadas](table-view-images/reorder02.png)](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>Editar celdas

Si desea permitir que el usuario edite los valores de una celda determinada, edite el `ProductTableDelegate.cs` archivo y cambie el `GetViewForItem` método de la siguiente manera:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Ahora, Si ejecutamos la aplicación, el usuario puede editar las celdas en la vista de tabla:

[![Ejemplo de edición de una celda](table-view-images/editing01.png)](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views"></a>

## <a name="using-images-in-table-views"></a>Usar imágenes en las vistas de tabla

Para incluir una imagen como parte de la celda en una `NSTableView` , deberá cambiar la forma en que el método de la vista de tabla devuelve los datos `NSTableViewDelegate's` `GetViewForItem` para usar en `NSTableCellView` lugar de la típica `NSTextField` . Por ejemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Para obtener más información, consulte la sección [uso de imágenes con vistas de tabla](~/mac/app-fundamentals/image.md) de nuestra documentación sobre [Cómo trabajar con imágenes](~/mac/app-fundamentals/image.md) .

<a name="Adding-a-Delete-Button-to-a-Row"></a>

## <a name="adding-a-delete-button-to-a-row"></a>Agregar un botón eliminar a una fila

En función de los requisitos de la aplicación, puede haber ocasiones en las que necesite proporcionar un botón de acción para cada fila de la tabla. Como ejemplo, vamos a expandir el ejemplo de vista de tabla creado anteriormente para incluir un botón **eliminar** en cada fila.

En primer lugar, edite `Main.storyboard` en la Interface Builder de Xcode, seleccione la vista de tabla y aumente el número de columnas a tres (3). A continuación, cambie el **título** de la nueva columna a `Action` :

[![Editar el nombre de columna](table-view-images/delete01.png)](table-view-images/delete01.png#lightbox)

Guarde los cambios en el guion gráfico y vuelva a Visual Studio para Mac para sincronizar los cambios.

Después, edite el `ViewController.cs` archivo y agregue el siguiente método público:

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

En el mismo archivo, modifique la creación del nuevo delegado de vista de tabla dentro del método de la `ViewDidLoad` siguiente manera:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Ahora, edite el `ProductTableDelegate.cs` archivo para incluir una conexión privada con el controlador de vista y para tomar el controlador como parámetro al crear una nueva instancia del delegado:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

A continuación, agregue el siguiente método privado nuevo a la clase:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

Esto toma todas las configuraciones de la vista de texto que se realizaron previamente en el `GetViewForItem` método y las coloca en una única ubicación a la que se puede llamar (puesto que la última columna de la tabla no incluye una vista de texto pero un botón).

Por último, edite el `GetViewForItem` método y haga que tenga un aspecto similar al siguiente:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

Echemos un vistazo a varias secciones de este código con más detalle. En primer lugar, si se crea una acción nueva, `NSTableViewCell` se toma la acción basándose en el nombre de la columna. En las dos primeras columnas (**producto** y **detalles**), `ConfigureTextField` se llama al nuevo método.

En la columna **acción** , `NSButton` se crea un nuevo y se agrega a la celda como una vista secundaria:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

La propiedad del botón `Tag` se usa para contener el número de la fila que se está procesando actualmente. Este número se usará más adelante cuando el usuario solicite que se elimine una fila en el evento del botón `Activated` :

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

Al principio del controlador de eventos, obtenemos el botón y el producto que se encuentra en la fila de la tabla dada. A continuación, se presenta una alerta al usuario para confirmar la eliminación de la fila. Si el usuario decide eliminar la fila, se quitará la fila especificada del origen de datos y se recargará la tabla:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Por último, si se está reutilizando la celda de la vista de tabla en lugar de crearse una nueva, el código siguiente la configura en función de la columna que se está procesando:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

En la columna **acción** , todas las vistas secundarias se examinan hasta que `NSButton` se encuentra la `Tag` propiedad, se actualiza para que apunte a la fila actual.

Una vez realizados estos cambios, cuando se ejecute la aplicación cada fila tendrá un botón **eliminar** :

[![Vista de tabla con botones de eliminación](table-view-images/delete02.png)](table-view-images/delete02.png#lightbox)

Cuando el usuario hace clic en un botón **eliminar** , se mostrará una alerta en la que se le pedirá que elimine la fila determinada:

[![Una alerta de eliminación de fila](table-view-images/delete03.png)](table-view-images/delete03.png#lightbox)

Si el usuario elige eliminar, se quitará la fila y se volverá a dibujar la tabla:

[![La tabla después de eliminar la fila](table-view-images/delete04.png)](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views"></a>

## <a name="data-binding-table-views"></a>Vistas de tabla de enlace de datos

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

La codificación de valores clave (KVC) es un mecanismo para tener acceso indirectamente a las propiedades de un objeto, mediante claves (cadenas con formato especial) para identificar propiedades en lugar de tener acceso a ellas a través de variables de instancia o métodos de descriptor de acceso ( `get/set` ). Mediante la implementación de los descriptores de acceso compatibles con el código clave-valor en la aplicación de Xamarin. Mac, obtiene acceso a otras características de macOS como la observación de clave-valor (KVO), el enlace de datos, los enlaces de cacao y la creación de scripts.

Para obtener más información, vea la sección enlace de datos de la [vista de tabla](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) de nuestra documentación sobre el enlace de [datos y el código de valor de clave](~/mac/app-fundamentals/databinding.md) .

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con las vistas de tabla en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de las vistas de tabla, cómo crear y mantener vistas de tabla en la Interface Builder de Xcode y cómo trabajar con vistas de tabla en código de C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
