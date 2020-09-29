---
title: Esquematizar vistas en Xamarin. Mac
description: En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin. Mac. Describe cómo crear y mantener vistas de esquema en Xcode y Interface Builder y cómo trabajar con ellas mediante programación.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c5120fead9084b50912fb4ea1e8cfa100f4b3bbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436322"
---
# <a name="outline-views-in-xamarinmac"></a>Esquematizar vistas en Xamarin. Mac

_En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin. Mac. Describe cómo crear y mantener vistas de esquema en Xcode y Interface Builder y cómo trabajar con ellas mediante programación._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a las mismas vistas de esquema que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las vistas de esquema (o, opcionalmente, crearlas directamente en código de C#).

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer filas de datos jerárquicos. Al igual que una vista de tabla, una vista de esquema muestra los datos de un conjunto de elementos relacionados, con filas que representan elementos individuales y columnas que representan los atributos de esos elementos. A diferencia de una vista de tabla, los elementos de una vista de esquema no están en una lista plana, sino que se organizan en una jerarquía, como los archivos y las carpetas de una unidad de disco duro.

[![Ejecución de una aplicación de ejemplo](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con las vistas de esquema en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la sección [exponer clases y métodos de C# a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican `Register` los `Export` comandos y que se usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction_to_Outline_Views"></a>

## <a name="introduction-to-outline-views"></a>Introducción a las vistas de esquema

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer filas de datos jerárquicos. Al igual que una vista de tabla, una vista de esquema muestra los datos de un conjunto de elementos relacionados, con filas que representan elementos individuales y columnas que representan los atributos de esos elementos. A diferencia de una vista de tabla, los elementos de una vista de esquema no están en una lista plana, sino que se organizan en una jerarquía, como los archivos y las carpetas de una unidad de disco duro.

Si un elemento de una vista de esquema contiene otros elementos, el usuario puede expandirlo o contraerlo. Un elemento expansible muestra un triángulo de divulgación, que apunta a la derecha cuando el elemento está contraído y apunta hacia abajo cuando se expande el elemento. Al hacer clic en el triángulo de divulgación, el elemento se expande o se contrae.

La vista de esquema ( `NSOutlineView` ) es una subclase de la vista de tabla ( `NSTableView` ) y, como tal, hereda gran parte de su comportamiento de la clase primaria. Como resultado, muchas de las operaciones admitidas por una vista de tabla, como la selección de filas o columnas, la reposición de las columnas arrastrando los encabezados de columna, etc., también se admiten en una vista de esquema. Una aplicación de Xamarin. Mac tiene el control de estas características y puede configurar los parámetros de la vista de esquema (ya sea en código o Interface Builder) para permitir o no determinadas operaciones.

Una vista de esquema no almacena sus propios datos, sino que se basa en un origen de datos ( `NSOutlineViewDataSource` ) para proporcionar las filas y las columnas requeridas, según sea necesario.

El comportamiento de una vista de esquema se puede personalizar proporcionando una subclase del delegado de la vista de esquema ( `NSOutlineViewDelegate` ) para admitir la administración de columnas de esquema, el tipo para seleccionar la funcionalidad, la selección de filas y la edición, el seguimiento personalizado y las vistas personalizadas para columnas y filas individuales.

Dado que una vista de esquema comparte gran parte del comportamiento y la funcionalidad de la vista de tabla, es posible que desee consultar la documentación de las [vistas de tabla](~/mac/user-interface/table-view.md) antes de continuar con este artículo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode"></a>

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Crear y mantener vistas de esquema en Xcode

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un `.storyboard` archivo que se incluye automáticamente en el proyecto. Para editar el diseño de Windows, en el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![Seleccionar el guión gráfico principal](outline-view-images/edit01.png)](outline-view-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![Edición de la interfaz de usuario en Xcode](outline-view-images/edit02.png)](outline-view-images/edit02.png#lightbox)

Escriba `outline` en el cuadro **de búsqueda del inspector** de la biblioteca para que sea más fácil encontrar los controles de la vista de esquema:

[![Seleccionar una vista de esquema de la biblioteca](outline-view-images/edit03.png)](outline-view-images/edit03.png#lightbox)

Arrastre una vista de esquema en el controlador de vista en el editor de la **interfaz**, haga que rellene el área de contenido del controlador de vista y establézcalo en donde se reduzca y crezca con la ventana del **Editor de restricciones**:

[![Editar las restricciones](outline-view-images/edit04.png)](outline-view-images/edit04.png#lightbox)

Seleccione la vista esquema en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/edit05.png)](outline-view-images/edit05.png#lightbox)

- **Columna de esquema** : columna de la tabla en la que se muestran los datos jerárquicos.
- **Columna de esquema autoguardado** : Si `true` es, la columna de esquema se guardará y restaurará automáticamente entre las ejecuciones de la aplicación.
- **Indentación** : la cantidad para aplicar sangría a las columnas bajo un elemento expandido.
- La **sangría sigue a las celdas** : si es, se aplicará `true` sangría a la marca de sangría junto con las celdas.
- **Autoguardar elementos expandidos** : Si `true` es, el estado expandido o contraído de los elementos se guardará automáticamente y se restaurará entre las ejecuciones de la aplicación.
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
> A menos que esté manteniendo una aplicación de Xamarin. Mac heredada, las `NSView` vistas de esquema basadas en se deben usar en `NSCell` vistas de tabla basadas en. `NSCell` se considera heredada y puede que no se admita en el futuro.

Seleccione una columna de la tabla en la jerarquía de la **interfaz** y las siguientes propiedades estarán disponibles en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/edit06.png)](outline-view-images/edit06.png#lightbox)

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

[![Inspector de atributo](outline-view-images/edit07.png)](outline-view-images/edit07.png#lightbox)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de vista de tabla (de forma predeterminada, es `NSTextField` ) en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el inspector de **atributos**:

[![Inspector de atributo](outline-view-images/edit08.png)](outline-view-images/edit08.png#lightbox)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, se usa un campo de texto estándar para mostrar los datos de una celda de una columna.

Seleccione una vista de celda de tabla ( `NSTableFieldCell` ) en la **jerarquía** de la interfaz y las siguientes propiedades están disponibles en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/edit09.png)](outline-view-images/edit09.png#lightbox)

La configuración más importante aquí es:

- **Diseño** : Seleccione cómo se colocan las celdas de esta columna.
- **Usa el modo de una sola línea** : Si `true` es, la celda está limitada a una sola línea.
- **Ancho del primer diseño en tiempo de ejecución** : Si `true` es, la celda preferirá el ancho establecido para ella (ya sea de forma manual o automática) cuando se muestre la primera vez que se ejecute la aplicación.
- **Acción** : controla cuándo se envía la **acción** de edición para la celda.
- **Comportamiento** : define si una celda es seleccionable o editable.
- **Texto enriquecido** : si es `true` , la celda puede mostrar texto con formato y con estilo.
- **Deshacer** : si es `true` , la celda asume la responsabilidad del comportamiento de deshacer.

Seleccione la vista de celda de tabla ( `NSTableFieldCell` ) en la parte inferior de una columna de tabla de la jerarquía de la **interfaz**:

[![Seleccionar la vista de celda de tabla](outline-view-images/edit11.png)](outline-view-images/edit10.png#lightbox)

Esto le permite editar la vista de celda de tabla utilizada como _patrón_ base para todas las celdas creadas para la columna especificada.

<a name="Adding_Actions_and_Outlets"></a>

### <a name="adding-actions-and-outlets"></a>Agregar acciones y salidas

Al igual que cualquier otro control de la interfaz de usuario de coco, necesitamos exponer nuestra vista de esquema y sus columnas y celdas al código de C# mediante **acciones** y **salidas** (según la funcionalidad necesaria).

El proceso es el mismo para cualquier elemento de la vista de esquema que desee exponer:

1. Cambie al **Editor de asistentes** y asegúrese de que el `ViewController.h` archivo está seleccionado:

    [![Seleccionar el archivo. h correcto](outline-view-images/edit11.png)](outline-view-images/edit11.png#lightbox)
2. Seleccione la vista de esquema en la jerarquía de la **interfaz**, haga clic en el control y arrástrela hasta el `ViewController.h` archivo.
3. Cree una **salida** para la vista de esquema denominada `ProductOutline` :

    [![Configuración de una salida](outline-view-images/edit13.png)](outline-view-images/edit13.png#lightbox)
4. Cree **salidas** para las columnas de las tablas, también denominadas `ProductColumn` y `DetailsColumn` :

    [![Configuración de una salida](outline-view-images/edit14.png)](outline-view-images/edit14.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación, escribiremos el código para mostrar algunos datos del esquema cuando se ejecute la aplicación.

<a name="Populating_the_Table_View"></a>

## <a name="populating-the-outline-view"></a>Rellenar la vista de esquema

Con nuestra vista de esquema diseñada en Interface Builder y expuesta a través de una **salida**, es necesario crear el código de C# para rellenarlo.

En primer lugar, vamos a crear una nueva `Product` clase para almacenar la información de las filas y grupos de subproductos individuales. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `Product` para el **nombre** y haga clic en el botón **nuevo** :

[![Crear una clase vacía](outline-view-images/populate01.png)](outline-view-images/populate01.png#lightbox)

Haga que el `Product.cs` archivo tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

A continuación, es necesario crear una subclase de `NSOutlineDataSource` para proporcionar los datos para el esquema que se solicitan. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `ProductOutlineDataSource` para el **nombre** y haga clic en el botón **nuevo** .

Edite el `ProductTableDataSource.cs` archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

Esta clase tiene almacenamiento para los elementos de la vista de esquema e invalida el `GetChildrenCount` para devolver el número de filas de la tabla. `GetChild`Devuelve un elemento primario o secundario específico (tal y como lo solicita la vista de esquema) y `ItemExpandable` define el elemento especificado como primario o secundario.

Por último, es necesario crear una subclase de `NSOutlineDelegate` para proporcionar el comportamiento de nuestro esquema. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar**  >  **nuevo archivo..** . Seleccione **General**  >  **clase vacía**general, escriba `ProductOutlineDelegate` para el **nombre** y haga clic en el botón **nuevo** .

Edite el `ProductOutlineDelegate.cs` archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Cuando se crea una instancia de `ProductOutlineDelegate` , también se pasa una instancia de `ProductOutlineDataSource` que proporciona los datos para el esquema. El `GetView` método es responsable de devolver una vista (datos) para mostrar la celda para una columna y una fila. Si es posible, se volverá a usar una vista existente para mostrar la celda, en caso de que no se deba crear una nueva vista.

Para rellenar el esquema, vamos a editar el `MainWindow.cs` archivo y hacer que el método tenga el `AwakeFromNib` aspecto siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Si ejecutamos la aplicación, se muestra lo siguiente:

[![La vista contraída](outline-view-images/populate02.png)](outline-view-images/populate02.png#lightbox)

Si expandimos un nodo en la vista de esquema, tendrá un aspecto similar al siguiente:

[![Vista expandida](outline-view-images/populate03.png)](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column"></a>

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir que el usuario ordene los datos en el esquema haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la `Product` columna, escriba `Title` para la **clave de ordenación**, `compare:` para el **selector** y seleccione `Ascending` para el **pedido**:

[![Establecer el orden de las claves de ordenación](outline-view-images/sort01.png)](outline-view-images/sort01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductOutlineDataSource.cs` archivo y agregar los siguientes métodos:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

El `Sort` método nos permite ordenar los datos en el origen de datos basándose en un `Product` campo de clase determinado en orden ascendente o descendente. Se llamará al método invalidado `SortDescriptorsChanged` cada vez que se haga clic en un encabezado de columna. Se le pasará el valor de **clave** que se establece en Interface Builder y el criterio de ordenación de esa columna.

Si ejecutamos la aplicación y hacemos clic en los encabezados de columna, las filas se ordenarán por esa columna:

[![Ejemplo de salida ordenada](outline-view-images/sort02.png)](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection"></a>

## <a name="row-selection"></a>Selección de filas

Si desea permitir que el usuario seleccione una sola fila, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista esquema en la **jerarquía** de la interfaz y desactive la casilla **múltiple** en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/select01.png)](outline-view-images/select01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de esquema. Vuelva a para `false` `ShouldSelectItem` cualquier elemento que no desea que el usuario pueda seleccionar o para todos los elementos `false` si no desea que el usuario pueda seleccionar ningún elemento.

<a name="Multiple_Row_Selection"></a>

## <a name="multiple-row-selection"></a>Selección de varias filas

Si desea permitir que el usuario seleccione varias filas, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista esquema en la **jerarquía** de la interfaz y active la casilla **múltiple** en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/select02.png)](outline-view-images/select02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Después, edite el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto permitirá al usuario seleccionar cualquier fila única en la vista de esquema. Vuelva a para `false` `ShouldSelectRow` cualquier elemento que no desea que el usuario pueda seleccionar o para todos los elementos `false` si no desea que el usuario pueda seleccionar ningún elemento.

<a name="Type_to_Select_Row"></a>

## <a name="type-to-select-row"></a>Tipo para seleccionar fila

Si desea permitir que el usuario escriba un carácter con la vista de esquema seleccionada y seleccionar la primera fila que contiene ese carácter, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista esquema en la **jerarquía** de la interfaz y active la casilla **tipo Select** en el **Inspector de atributos**:

[![Editar el tipo de fila](outline-view-images/type01.png)](outline-view-images/type01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` archivo y agregaremos el método siguiente:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

El `GetNextTypeSelectMatch` método toma el determinado `searchString` y devuelve el elemento de la primera `Product` que tiene esa cadena en `Title` .

<a name="Reordering_Columns"></a>

## <a name="reordering-columns"></a>Reordenación de columnas

Si desea permitir que el usuario arrastre columnas de reordenación en la vista de esquema, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder. Seleccione la vista esquema en la **jerarquía** de la interfaz y active la casilla **reordenación** en el **Inspector de atributos**:

[![Inspector de atributo](outline-view-images/reorder01.png)](outline-view-images/reorder01.png#lightbox)

Si asignamos un valor a la propiedad **autosave** y comprobamos el campo de **información de columna** , los cambios que se realicen en el diseño de la tabla se guardarán automáticamente y se restaurarán la próxima vez que se ejecute la aplicación.

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` archivo y agregaremos el método siguiente:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

El `ShouldReorder` método debe devolver `true` para cualquier columna que desee permitir que el arrastre se reordene a, de lo `newColumnIndex` contrario, devuelva `false` ;

Si ejecutamos la aplicación, podemos arrastrar encabezados de columna para reordenar las columnas:

[![Ejemplo de reordenación de columnas](outline-view-images/reorder02.png)](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells"></a>

## <a name="editing-cells"></a>Editar celdas

Si desea permitir que el usuario edite los valores de una celda determinada, edite el `ProductOutlineDelegate.cs` archivo y cambie el `GetViewForItem` método de la siguiente manera:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

Ahora, Si ejecutamos la aplicación, el usuario puede editar las celdas en la vista de tabla:

[![Ejemplo de edición de celdas](outline-view-images/editing01.png)](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views"></a>

## <a name="using-images-in-outline-views"></a>Usar imágenes en las vistas de esquema

Para incluir una imagen como parte de la celda en una `NSOutlineView` , deberá cambiar la forma en que el método de la vista esquema devuelve los datos `NSTableViewDelegate's` `GetView` para usar en `NSTableCellView` lugar de la típica `NSTextField` . Por ejemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Para obtener más información, consulte la sección [uso de imágenes con las vistas de esquema](~/mac/app-fundamentals/image.md) de nuestra documentación sobre [Cómo trabajar con imágenes](~/mac/app-fundamentals/image.md) .

<a name="Data_Binding_Outline_Views"></a>

## <a name="data-binding-outline-views"></a>Enlace de datos, vistas de esquema

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

La codificación de valores clave (KVC) es un mecanismo para tener acceso indirectamente a las propiedades de un objeto, mediante claves (cadenas con formato especial) para identificar propiedades en lugar de tener acceso a ellas a través de variables de instancia o métodos de descriptor de acceso ( `get/set` ). Mediante la implementación de los descriptores de acceso compatibles con el código clave-valor en la aplicación de Xamarin. Mac, obtiene acceso a otras características de macOS como la observación de clave-valor (KVO), el enlace de datos, los enlaces de cacao y la creación de scripts.

Para obtener más información, vea la sección sobre el enlace de datos de la [vista de esquema](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) de nuestra documentación sobre el enlace de [datos y el código de valor de clave](~/mac/app-fundamentals/databinding.md) .

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha tomado una visión detallada del trabajo con las vistas de esquema en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de las vistas de esquema, cómo crear y mantener las vistas de esquema en la Interface Builder de Xcode y cómo trabajar con las vistas de esquema en el código de C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacOutlines (ejemplo)](/samples/xamarin/mac-samples/macoutlines)
- [MacImages (ejemplo)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a las vistas de esquema](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)