---
title: Listas de origen en Xamarin. Mac
description: En este artículo se explica cómo trabajar con listas de origen en una aplicación de Xamarin. Mac. Describe cómo crear y mantener listas de origen en Xcode y Interface Builder e interactuar con ellas en C# el código.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 33c94e933a2e69ca6896ef2adba44c4681be9741
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030025"
---
# <a name="source-lists-in-xamarinmac"></a>Listas de origen en Xamarin. Mac

_En este artículo se explica cómo trabajar con listas de origen en una aplicación de Xamarin. Mac. Describe cómo crear y mantener listas de origen en Xcode y Interface Builder e interactuar con ellas en C# el código._

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a las mismas listas de origen que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las listas de origen (o, opcionalmente, C# crearlas directamente en el código).

Una lista de origen es un tipo especial de vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes.

[![](source-list-images/source05.png "An example source list")](source-list-images/source05.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con listas de origen en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exponer C# clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , donde se explican los comandos`Register`y`Export`que se usan para conectar las C# clases a Objective-C. objetos y elementos de la interfaz de usuario.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introducción a las listas de origen

Como se indicó anteriormente, una lista de origen es un tipo especial de vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes. Una lista de origen es un tipo de tabla que permite al usuario expandir o contraer filas de datos jerárquicos. A diferencia de una vista de tabla, los elementos de una lista de origen no están en una lista plana, sino que se organizan en una jerarquía, como los archivos y las carpetas de una unidad de disco duro. Si un elemento de una lista de origen contiene otros elementos, el usuario puede expandirlo o contraerlo.

La lista de origen es una vista de esquema con estilo especial (`NSOutlineView`), que es una subclase de la vista de tabla (`NSTableView`) y, como tal, hereda gran parte de su comportamiento de la clase primaria. Como resultado, muchas operaciones admitidas por una vista de esquema también se admiten en una lista de origen. Una aplicación de Xamarin. Mac tiene el control de estas características y puede configurar los parámetros de la lista de origen (ya sea en código o Interface Builder) para permitir o impedir determinadas operaciones.

Una lista de origen no almacena sus propios datos, sino que se basa en un origen de datos (`NSOutlineViewDataSource`) para proporcionar las filas y las columnas requeridas, según sea necesario.

El comportamiento de una lista de origen se puede personalizar proporcionando una subclase del delegado de la vista esquema (`NSOutlineViewDelegate`) para admitir el tipo de esquema para seleccionar la funcionalidad, la selección y edición de elementos, el seguimiento personalizado y las vistas personalizadas para elementos individuales.

Puesto que una lista de origen comparte gran parte del comportamiento y la funcionalidad de una vista de tabla y una vista de esquema, es posible que desee consultar la documentación sobre [vistas de tabla](~/mac/user-interface/table-view.md) y [esquemas](~/mac/user-interface/outline-view.md) antes de continuar con este artículo.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Trabajar con listas de origen

Una lista de origen es un tipo especial de vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes. A diferencia de las vistas de esquema, antes de definir nuestra lista de origen en Interface Builder, vamos a crear las clases de respaldo en Xamarin. Mac.

En primer lugar, vamos a crear una nueva clase de `SourceListItem` para almacenar los datos de la lista de origen. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListItem` para el **nombre** y haga clic en el botón **nuevo** :

[![](source-list-images/source01.png "Adding an empty class")](source-list-images/source01.png#lightbox)

Haga que el archivo de `SourceListItem.cs` tenga el aspecto siguiente: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListDataSource` para el **nombre** y haga clic en el botón **nuevo** . Haga que el archivo de `SourceListDataSource.cs` tenga el aspecto siguiente:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Esto proporcionará los datos de la lista de origen.

En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListDelegate` para el **nombre** y haga clic en el botón **nuevo** . Haga que el archivo de `SourceListDelegate.cs` tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

Esto proporcionará el comportamiento de nuestra lista de origen.

Por último, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **nuevo archivo..** . Seleccione **General** > **clase vacía**, escriba `SourceListView` para el **nombre** y haga clic en el botón **nuevo** . Haga que el archivo de `SourceListView.cs` tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Esto crea una subclase personalizada y reutilizable de `NSOutlineView` (`SourceListView`) que se pueden usar para controlar la lista de origen en cualquier aplicación de Xamarin. Mac que se realice.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Crear y mantener listas de origen en Xcode

Ahora, vamos a diseñar nuestra lista de origen en Interface Builder. Haga doble clic en el archivo de `Main.storyboard` para abrirlo y editarlo en Interface Builder y arrastre una vista en dos paneles desde el **Inspector de biblioteca**, agréguelo al controlador de vista y establézcalo para que cambie de tamaño con la vista del **Editor de restricciones**:

[![](source-list-images/source00.png "Editing constraints")](source-list-images/source00.png#lightbox)

A continuación, arrastre una lista de origen desde el **Inspector de biblioteca**, agréguela al lado izquierdo de la vista en dos paneles y establézcalo para que cambie de tamaño con la vista del **Editor de restricciones**:

[![](source-list-images/source02.png "Editing constraints")](source-list-images/source02.png#lightbox)

A continuación, cambie a la **vista identidad**, seleccione la lista origen y cambie su **clase** a `SourceListView`:

[![](source-list-images/source03.png "Setting the class name")](source-list-images/source03.png#lightbox)

Por último, cree una **salida** para nuestra lista de origen denominada `SourceList` en el archivo de `ViewController.h`:

[![](source-list-images/source04.png "Configuring an Outlet")](source-list-images/source04.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Rellenar la lista de origen

Vamos a editar el archivo de `RotationWindow.cs` en Visual Studio para Mac y hacer que `AwakeFromNib` método sea similar al siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

Es necesario llamar al método `Initialize ()` en la **salida** de la lista de origen _antes_ de que se le agreguen elementos. Para cada grupo de elementos, se crea un elemento primario y, a continuación, se agregan los subelementos a ese elemento de grupo. Cada grupo se agrega a continuación a la colección de la lista de origen `SourceList.AddItem (...)`. Las dos últimas líneas cargan los datos de la lista de origen y expanden todos los grupos:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Por último, edite el archivo de `AppDelegate.cs` y haga que el método `DidFinishLaunching` tenga el aspecto siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Si ejecutamos nuestra aplicación, se mostrará lo siguiente:

[![](source-list-images/source05.png "An example app run")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con listas de origen en una aplicación de Xamarin. Mac. Vimos cómo crear y mantener listas de origen en la Interface Builder de Xcode y cómo trabajar con listas de origen en C# el código.

## <a name="related-links"></a>Vínculos relacionados

- [MacOutlines (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a las vistas de esquema](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
