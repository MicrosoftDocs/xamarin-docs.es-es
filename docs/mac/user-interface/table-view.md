---
title: Vistas de tabla en Xamarin. Mac
description: En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. Describe la creación de vistas de tabla en Xcode y Interface Builder e interactuar con ellas en el código.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 36bed05c1e60004125406c3ed2df66fcfe2be10b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008326"
---
# <a name="table-views-in-xamarinmac"></a>Vistas de tabla en Xamarin. Mac

_En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. Describe la creación de vistas de tabla en Xcode y Interface Builder e interactuar con ellas en el código._

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a las mismas vistas de tabla que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las vistas de tabla (o, opcionalmente, C# crearlas directamente en el código).

Una vista de tabla muestra los datos en formato tabular que contiene una o más columnas de información en varias filas. En función del tipo de vista de tabla que se va a crear, el usuario puede ordenar por columna, reorganizar columnas, Agregar columnas, quitar columnas o modificar los datos contenidos en la tabla.

[![](table-view-images/intro01.png "An example table")](table-view-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con las vistas de tabla en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exponer C# clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , donde se explican los comandos`Register`y`Export`que se usan para conectar las C# clases a Objective-C. objetos y elementos de la interfaz de usuario.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introducción a las vistas de tabla

Una vista de tabla muestra los datos en formato tabular que contiene una o más columnas de información en varias filas. Las vistas de tabla se muestran dentro de las vistas de desplazamiento (`NSScrollView`) y a partir de macOS 10,7, puede usar cualquier `NSView` en lugar de celdas (`NSCell`) para mostrar filas y columnas. Dicho esto, todavía puede usar `NSCell` sin embargo, normalmente subclases `NSTableCellView` y crear filas y columnas personalizadas.

Una vista de tabla no almacena sus propios datos, sino que se basa en un origen de datos (`NSTableViewDataSource`) para proporcionar las filas y las columnas requeridas, según sea necesario.

El comportamiento de una vista de tabla se puede personalizar proporcionando una subclase del delegado de vista de tabla (`NSTableViewDelegate`) para admitir la administración de columnas de tabla, el tipo para seleccionar la funcionalidad, la selección de filas y la edición, el seguimiento personalizado y las vistas personalizadas para columnas y filas individuales.

Al crear vistas de tabla, Apple sugiere lo siguiente:

- Permite al usuario ordenar la tabla haciendo clic en los encabezados de columna.
- Cree encabezados de columna que sean nombres o frases cortas que describan los datos que se muestran en esa columna.

Para obtener más información, consulte la sección [vistas de contenido](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) de las directrices de la interfaz humana de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Crear y mantener vistas de tabla en Xcode

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un archivo `.storyboard` incluido automáticamente en el proyecto. Para editar el diseño de Windows, en el **Explorador de soluciones**, haga doble clic en el archivo de `Main.storyboard`:

[![](table-view-images/edit01.png "Selecting the main storyboard")](table-view-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![](table-view-images/edit02.png "Editing the UI in Xcode")](table-view-images/edit02.png#lightbox)

Escriba `table` en el cuadro **de búsqueda del inspector** de la biblioteca para que sea más fácil encontrar los controles de la vista de tabla:

[![](table-view-images/edit03.png "Selecting a Table View from the Library")](table-view-images/edit03.png#lightbox)

Arrastre una vista de tabla hasta el controlador de vista en el editor de la **interfaz**, haga que rellene el área de contenido del controlador de vista y establézcalo en donde se reduzca y crezca con la ventana del **Editor de restricciones**:

[![](table-view-images/edit04.png "Editing constraints")](table-view-images/edit04.png#lightbox)

Seleccione la vista de tabla en la jerarquía de la **interfaz** y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![](table-view-images/edit05.png "The Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **Modo de contenido** : permite usar vistas (`NSView`) o celdas (`NSCell`) para mostrar los datos en las filas y columnas. A partir de macOS 10,7, debe utilizar las vistas.
- **Floating Group Rows** : si `true`, la vista de tabla dibujará celdas agrupadas como si fueran flotantes.
- **Columnas** : define el número de columnas que se muestran.
- **Encabezados** : si `true`, las columnas tendrán encabezados.
- **Reordenación** : si `true`, el usuario podrá arrastrar de nuevo el orden de las columnas de la tabla.
- **Cambio de tamaño** : si `true`, el usuario podrá arrastrar encabezados de columna para cambiar el tamaño de las columnas.
- **Tamaño de columna** : controla el modo en que la tabla ajustará automáticamente el tamaño de las columnas.
- **Resaltar** : controla el tipo de resaltado que usa la tabla cuando se selecciona una celda.
- **Filas alternativas** : si `true`, otra fila tendrá un color de fondo diferente.
- **Cuadrícula horizontal** : selecciona el tipo de borde dibujado entre las celdas horizontalmente.
- **Cuadrícula vertical** : selecciona el tipo de borde que se dibuja entre las celdas verticalmente.
- **Color** de la cuadrícula: establece el color del borde de la celda.
- **Background** : establece el color de fondo de la celda.
- **Selección** : permite controlar el modo en que el usuario puede seleccionar las celdas de la tabla como:
  - **Multiple** : si `true`, el usuario puede seleccionar varias filas y columnas.
  - **Columna** : si `true`, el usuario puede seleccionar columnas.
  - **Escriba Select** : si `true`, el usuario puede escribir un carácter para seleccionar una fila.
  - **Vacío** : si `true`, no es necesario que el usuario seleccione una fila o columna; la tabla no permite ninguna selección.
- **Autosave** : nombre en el que se guarda automáticamente el formato de las tablas.
- **Información de columna** : si `true`, el orden y el ancho de las columnas se guardarán automáticamente.
- **Saltos de línea** : Seleccione cómo controla la celda los saltos de línea.
- **Trunca la última línea visible** : si `true`, la celda se truncará en los datos no caben dentro de sus límites.

> [!IMPORTANT]
> A menos que esté manteniendo una aplicación de Xamarin. Mac heredada, las vistas de tabla basadas en `NSView` deben usarse en vistas de tabla basadas en `NSCell`. `NSCell` se considera heredada y puede que no se admita en el futuro.

Seleccione una columna de la tabla en la jerarquía de la **interfaz** y las siguientes propiedades estarán disponibles en el **Inspector de atributos**:

[![](table-view-images/edit06.png "The Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **Título** : establece el título de la columna.
- **Alineación** : establece la alineación del texto dentro de las celdas.
- **Fuente de título** : selecciona la fuente del texto de encabezado de la celda.
- **Criterio de ordenación** : es la clave utilizada para ordenar los datos de la columna. Déjelo en blanco si el usuario no puede ordenar esta columna.
- **Selector** : es la **acción** que se usa para realizar la ordenación. Déjelo en blanco si el usuario no puede ordenar esta columna.
- **Order** : es el criterio de ordenación de los datos de las columnas.
- **Cambio de tamaño** : selecciona el tipo de cambio de tamaño de la columna.
- **Editable** : si `true`, el usuario puede editar las celdas de una tabla basada en celdas.
- **Hidden** : si `true`, la columna está oculta.

También puede cambiar el tamaño de la columna arrastrando su identificador (verticalmente centrado en el lado derecho de la columna) hacia la izquierda o la derecha.

Vamos a seleccionar cada columna en la vista de tabla y asignaremos a la primera columna un **título** de `Product` y la segunda `Details`.

Seleccione una vista de celda de tabla (`NSTableViewCell`) en la jerarquía de la **interfaz** y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![](table-view-images/edit07.png "The Attribute Inspector")](table-view-images/edit07.png#lightbox)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de vista de tabla (de forma predeterminada, se trata de una `NSTextField`) en la jerarquía de la **interfaz** y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![](table-view-images/edit08.png "The Attribute Inspector")](table-view-images/edit08.png#lightbox)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, se usa un campo de texto estándar para mostrar los datos de una celda de una columna.

Seleccione una vista de celda de tabla (`NSTableFieldCell`) en la jerarquía de la **interfaz** y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![](table-view-images/edit09.png "The Attribute Inspector")](table-view-images/edit09.png#lightbox)

La configuración más importante aquí es:

- **Diseño** : Seleccione cómo se colocan las celdas de esta columna.
- **Usa el modo de una sola línea** : si `true`, la celda se limita a una sola línea.
- **Ancho del primer diseño en tiempo de ejecución** : si `true`, la celda preferirá el ancho establecido para ella (ya sea de forma manual o automática) cuando se muestre la primera vez que se ejecute la aplicación.
- **Acción** : controla cuándo se envía la **acción** de edición para la celda.
- **Comportamiento** : define si una celda es seleccionable o editable.
- **Texto enriquecido** : si `true`, la celda puede mostrar texto con formato y con estilo.
- **Deshacer** : si `true`, la celda asume la responsabilidad del comportamiento de deshacer.

Seleccione la vista celda de tabla (`NSTableFieldCell`) en la parte inferior de una columna de la tabla en la jerarquía de la **interfaz**:

[![](table-view-images/edit10.png "Selecting the Table Cell View")](table-view-images/edit10.png#lightbox)

Esto le permite editar la vista de celda de tabla utilizada como _patrón_ base para todas las celdas creadas para la columna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Agregar acciones y salidas

Al igual que cualquier otro control de la interfaz de usuario de coco, es necesario exponer nuestra vista de tabla y C# sus columnas y celdas al código mediante **acciones** y **salidas** (según la funcionalidad requerida).

El proceso es el mismo para cualquier elemento de vista de tabla que desee exponer:

1. Cambie al **Editor de asistentes** y asegúrese de que está seleccionado el archivo de `ViewController.h`: 

    [![](table-view-images/edit11.png "The Assistant Editor")](table-view-images/edit11.png#lightbox)
2. Seleccione la vista de tabla de la jerarquía de la **interfaz**, haga clic en el control y arrástrela hasta el archivo de `ViewController.h`.
3. Cree una **salida** para la vista de tabla denominada `ProductTable`: 

    [![](table-view-images/edit13.png "Configuring an Outlet")](table-view-images/edit13.png#lightbox)
4. Cree **salidas** para las columnas de las tablas, también denominadas `ProductColumn` y `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configuring an Outlet")](table-view-images/edit14.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación, escribiremos el código para mostrar algunos datos de la tabla cuando se ejecute la aplicación.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Rellenar la vista de tabla

Con nuestra vista de tabla diseñada en Interface Builder y expuesta a través de una **salida**, es necesario crear C# el código para rellenarlo.

En primer lugar, vamos a crear una nueva clase de `Product` para almacenar la información de las filas individuales. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `Product` para el **nombre** y haga clic en el botón **nuevo** :

[![](table-view-images/populate01.png "Creating an empty class")](table-view-images/populate01.png#lightbox)

Haga que el archivo de `Product.cs` tenga el aspecto siguiente:

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

A continuación, es necesario crear una subclase de `NSTableDataSource` para proporcionar los datos de la tabla a medida que se solicitan. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductTableDataSource` para el **nombre** y haga clic en el botón **nuevo** .

Edite el archivo de `ProductTableDataSource.cs` y haga que tenga un aspecto similar al siguiente:

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

Por último, es necesario crear una subclase de `NSTableDelegate` para proporcionar el comportamiento de la tabla. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductTableDelegate` para el **nombre** y haga clic en el botón **nuevo** .

Edite el archivo de `ProductTableDelegate.cs` y haga que tenga un aspecto similar al siguiente:

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

Cuando se crea una instancia de la `ProductTableDelegate`, también se pasa una instancia del `ProductTableDataSource` que proporciona los datos de la tabla. El método `GetViewForItem` es responsable de devolver una vista (datos) para mostrar la celda para una columna y una fila. Si es posible, se volverá a usar una vista existente para mostrar la celda, en caso de que no se deba crear una nueva vista.

Para rellenar la tabla, vamos a editar el archivo de `ViewController.cs` y hacer que el método `AwakeFromNib` tenga el aspecto siguiente:

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

[![](table-view-images/populate02.png "A sample app run")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir que el usuario ordene los datos de la tabla haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el archivo `Main.storyboard` para abrirlo y editarlo en Interface Builder. Seleccione la columna `Product`, escriba `Title` para la **clave de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **pedido**:

[![](table-view-images/sort01.png "Setting the sort key")](table-view-images/sort01.png#lightbox)

Seleccione la columna `Details`, escriba `Description` para la **clave de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **pedido**:

[![](table-view-images/sort02.png "Setting the sort key")](table-view-images/sort02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el archivo de `ProductTableDataSource.cs` y agregar los siguientes métodos:

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

El método `Sort` nos permite ordenar los datos en el origen de datos basándose en un campo de clase `Product` determinado en orden ascendente o descendente. Se llamará al método `SortDescriptorsChanged` invalidado cada vez que se haga clic en un encabezado de columna. Se le pasará el valor de **clave** que se establece en Interface Builder y el criterio de ordenación de esa columna.

Si ejecutamos la aplicación y hacemos clic en los encabezados de columna, las filas se ordenarán por esa columna:

[![](table-view-images/sort03.png "An example app run")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selección de filas

Si desea permitir que el usuario seleccione una sola fila, haga doble clic en el archivo `Main.storyboard` para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y desactive la casilla **múltiple** en el **Inspector de atributos**:

[![](table-view-images/select01.png "The Attribute Inspector")](table-view-images/select01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el archivo de `ProductTableDelegate.cs` y agregue el siguiente método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de tabla. Devuelva `false` para la `ShouldSelectRow` de cualquier fila que no desee que el usuario pueda seleccionar o `false` para cada fila si no desea que el usuario pueda seleccionar ninguna fila.

La vista de tabla (`NSTableView`) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectRow(nint)`: anula la selección de la fila especificada en la tabla.
- `SelectRow(nint,bool)`: selecciona la fila especificada. Pase `false` para el segundo parámetro para seleccionar solo una fila cada vez.
- `SelectedRow`: devuelve la fila actual seleccionada en la tabla.
- `IsRowSelected(nint)`: devuelve `true` si se selecciona la fila especificada.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selección de varias filas

Si desea permitir que el usuario seleccione varias filas, haga doble clic en el archivo `Main.storyboard` para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **múltiple** en el **Inspector de atributos**:

[![](table-view-images/select02.png "The Attribute Inspector")](table-view-images/select02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el archivo de `ProductTableDelegate.cs` y agregue el siguiente método:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de tabla. Devuelva `false` para la `ShouldSelectRow` de cualquier fila que no desee que el usuario pueda seleccionar o `false` para cada fila si no desea que el usuario pueda seleccionar ninguna fila.

La vista de tabla (`NSTableView`) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectAll(NSObject)`: anula la selección de todas las filas de la tabla. Utilice `this` para el primer parámetro que se va a enviar en el objeto que realiza la selección. 
- `DeselectRow(nint)`: anula la selección de la fila especificada en la tabla.
- `SelectAll(NSobject)`: selecciona todas las filas de la tabla. Utilice `this` para el primer parámetro que se va a enviar en el objeto que realiza la selección.
- `SelectRow(nint,bool)`: selecciona la fila especificada. Pasar `false` para el segundo parámetro borrar la selección y seleccionar una sola fila, pasar `true` para extender la selección e incluir esta fila.
- `SelectRows(NSIndexSet,bool)`: selecciona el conjunto de filas especificado. Pass `false` para el segundo parámetro borre la selección y seleccione solo estas filas, pase `true` para ampliar la selección e incluir estas filas.
- `SelectedRow`: devuelve la fila actual seleccionada en la tabla.
- `SelectedRows`: devuelve un `NSIndexSet` que contiene los índices de las filas seleccionadas.
- `SelectedRowCount`: devuelve el número de filas seleccionadas.
- `IsRowSelected(nint)`: devuelve `true` si se selecciona la fila especificada.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para seleccionar fila

Si desea permitir que el usuario escriba un carácter con la vista de tabla seleccionada y seleccione la primera fila que contiene ese carácter, haga doble clic en el archivo de `Main.storyboard` para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **tipo Select** en el **Inspector de atributos**:

[![](table-view-images/type01.png "Setting the selection type")](table-view-images/type01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el archivo de `ProductTableDelegate.cs` y agregaremos el método siguiente:

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

El método `GetNextTypeSelectMatch` toma el `searchString` dado y devuelve la fila del primer `Product` que tiene esa cadena en su `Title`.

Si ejecutamos la aplicación y escribemos un carácter, se selecciona una fila:

[![](table-view-images/type02.png "A sample app run")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenación de columnas

Si desea permitir que el usuario arrastre columnas de reordenación en la vista de tabla, haga doble clic en el archivo de `Main.storyboard` para abrirlo y editarlo en Interface Builder. Seleccione la vista de tabla en la jerarquía de la **interfaz** y active la casilla **reordenación** en el **Inspector de atributos**:

[![](table-view-images/reorder01.png "The Attribute Inspector")](table-view-images/reorder01.png#lightbox)

Si asignamos un valor a la propiedad **autosave** y comprobamos el campo de **información de columna** , los cambios que se realicen en el diseño de la tabla se guardarán automáticamente y se restaurarán la próxima vez que se ejecute la aplicación.

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el archivo de `ProductTableDelegate.cs` y agregaremos el método siguiente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

El método `ShouldReorder` debe devolver `true` para cualquier columna que desee permitir que se vuelva a ordenar en el `newColumnIndex`; de lo contrario, devolverá `false`;

Si ejecutamos la aplicación, podemos arrastrar encabezados de columna para reordenar las columnas:

[![](table-view-images/reorder02.png "An example of the reordered columns")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Editar celdas

Si desea permitir que el usuario edite los valores de una celda determinada, edite el archivo de `ProductTableDelegate.cs` y cambie el método `GetViewForItem` de la siguiente manera:

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

[![](table-view-images/editing01.png "An example of editing a cell")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Usar imágenes en las vistas de tabla

Para incluir una imagen como parte de la celda en una `NSTableView`, deberá cambiar la forma en que el método `NSTableViewDelegate's` `GetViewForItem` de la vista de tabla devuelve los datos para usar un `NSTableCellView` en lugar de la `NSTextField` típica. Por ejemplo:

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

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Agregar un botón eliminar a una fila

En función de los requisitos de la aplicación, puede haber ocasiones en las que necesite proporcionar un botón de acción para cada fila de la tabla. Como ejemplo, vamos a expandir el ejemplo de vista de tabla creado anteriormente para incluir un botón **eliminar** en cada fila.

En primer lugar, edite el `Main.storyboard` en Interface Builder de Xcode, seleccione la vista de tabla y aumente el número de columnas a tres (3). A continuación, cambie el **título** de la nueva columna a `Action`:

[![](table-view-images/delete01.png "Editing the column name")](table-view-images/delete01.png#lightbox)

Guarde los cambios en el guion gráfico y vuelva a Visual Studio para Mac para sincronizar los cambios.

Después, edite el archivo de `ViewController.cs` y agregue el siguiente método público:

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

En el mismo archivo, modifique la creación del nuevo delegado de vista de tabla dentro del método `ViewDidLoad` como se indica a continuación:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Ahora, edite el archivo de `ProductTableDelegate.cs` para incluir una conexión privada con el controlador de vista y para tomar el controlador como parámetro al crear una nueva instancia del delegado:

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

Esto toma todas las configuraciones de la vista de texto que se realizaron anteriormente en el método `GetViewForItem` y las coloca en una única ubicación a la que se puede llamar (puesto que la última columna de la tabla no incluye una vista de texto pero un botón).

Por último, edite el método `GetViewForItem` y haga que tenga un aspecto similar al siguiente:

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

Echemos un vistazo a varias secciones de este código con más detalle. En primer lugar, si se está creando una nueva `NSTableViewCell` se realiza una acción basada en el nombre de la columna. En las dos primeras columnas (**Product** y **Details**), se llama al método New `ConfigureTextField`.

En la columna **acción** , se crea un nuevo `NSButton` y se agrega a la celda como una vista secundaria:

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

La propiedad `Tag` del botón se usa para contener el número de la fila que se está procesando actualmente. Este número se usará más adelante cuando el usuario solicite que se elimine una fila en el evento de `Activated` del botón:

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

En la columna **acción** , todas las vistas secundarias se examinan hasta que se encuentra el `NSButton`, es `Tag` propiedad se actualiza para que apunte a la fila actual.

Una vez realizados estos cambios, cuando se ejecute la aplicación cada fila tendrá un botón **eliminar** :

[![](table-view-images/delete02.png "The table view with deletion buttons")](table-view-images/delete02.png#lightbox)

Cuando el usuario hace clic en un botón **eliminar** , se mostrará una alerta en la que se le pedirá que elimine la fila determinada:

[![](table-view-images/delete03.png "A delete row alert")](table-view-images/delete03.png#lightbox)

Si el usuario elige eliminar, se quitará la fila y se volverá a dibujar la tabla:

[![](table-view-images/delete04.png "The table after the row is deleted")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Vistas de tabla de enlace de datos

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

La codificación de valores clave (KVC) es un mecanismo para tener acceso indirectamente a las propiedades de un objeto, mediante claves (cadenas con formato especial) para identificar propiedades en lugar de tener acceso a ellas a través de variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de los descriptores de acceso compatibles con el código clave-valor en la aplicación de Xamarin. Mac, obtiene acceso a otras características de macOS como la observación de clave-valor (KVO), el enlace de datos, los enlaces de cacao y la creación de scripts.

Para obtener más información, vea la sección enlace de datos de la [vista de tabla](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) de nuestra documentación sobre el enlace de [datos y el código de valor de clave](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con las vistas de tabla en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de las vistas de tabla, cómo crear y mantener vistas de tabla en la Interface Builder de Xcode y cómo trabajar con vistas de C# tabla en el código.

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
