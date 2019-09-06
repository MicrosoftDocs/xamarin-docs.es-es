---
title: Vistas de colección en Xamarin. Mac
description: En este artículo se describe cómo trabajar con vistas de colección en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de colección en Xcode y Interface Builder y cómo trabajar con ellas mediante programación.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/24/2017
ms.openlocfilehash: a3673f017a5dd50e5cc3ae44790bf359c2871440
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279624"
---
# <a name="collection-views-in-xamarinmac"></a>Vistas de colección en Xamarin. Mac

_En este artículo se describe cómo trabajar con vistas de colección en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de colección en Xcode y Interface Builder y cómo trabajar con ellas mediante programación._

Cuando se trabaja C# con y .net en una aplicación de Xamarin. Mac, el desarrollador tiene acceso a los mismos controles de vista de colección de AppKit que un desarrollador que trabaja en *Objective-C* y *Xcode* . Dado que Xamarin. Mac se integra directamente con Xcode, el desarrollador usa _Interface Builder_ de Xcode para crear y mantener vistas de colección.

Un `NSCollectionView` muestra una cuadrícula de subvistas organizadas `NSCollectionViewLayout`mediante. Cada subvista de la cuadrícula se representa mediante un `NSCollectionViewItem` que administra la carga del contenido de la vista desde un `.xib` archivo.

[![Ejecución de una aplicación de ejemplo](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

En este artículo se describen los conceptos básicos sobre cómo trabajar con vistas de colección en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que se usan. en este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) también, en él se explican `Export` los `Register` comandos y que se usan para C# conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Acerca de las vistas de colección

El objetivo principal de una vista de colección`NSCollectionView`() es organizar visualmente un grupo de objetos de forma organizada mediante un diseño de vista de colección`NSCollectionViewLayout`(), con cada objeto individual`NSCollectionViewItem`() obteniendo su propia vista en la colección más grande. Las vistas de colección funcionan mediante el enlace de datos y las técnicas de codificación de valor de clave y, como tal, debe leer la documentación sobre el [enlace de datos y la codificación de valores de clave](~/mac/app-fundamentals/databinding.md) antes de continuar con este artículo.

La vista de colección no tiene ningún elemento de vista de colección estándar integrado (como un esquema o una vista de tabla), por lo que el desarrollador es responsable de diseñar e implementar una _vista de prototipo_ mediante otros controles AppKit, como campos de imagen, campos de texto, etiquetas, otros. Esta vista de prototipo se usará para mostrar y trabajar con cada elemento administrado por la vista de colección y se almacena en un `.xib` archivo.

Dado que el desarrollador es responsable de la apariencia de un elemento de vista de colección, la vista de colección no tiene compatibilidad integrada para resaltar un elemento seleccionado en la cuadrícula. En este artículo se tratará la implementación de esta característica.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definir el modelo de datos

Antes de enlazar datos a una vista de colección en Interface Builder, se debe definir una clase compatible con la codificación de clave-valor (KVC)/Key-Value observando (KVO) en la aplicación de Xamarin. Mac para que actúe como _modelo de datos_ para el enlace. El modelo de datos proporciona todos los datos que se mostrarán en la colección y recibirá cualquier modificación de los datos que el usuario realice en la interfaz de usuario mientras se ejecuta la aplicación.

Tome el ejemplo de una aplicación que administra un grupo de empleados; la clase siguiente podría usarse para definir el modelo de datos:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

El `PersonModel` modelo de datos se usará en el resto de este artículo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Trabajar con una vista de colección

El enlace de datos con una vista de colección es muy similar al enlace con una vista `NSCollectionViewDataSource` de tabla, como se usa para proporcionar datos para la colección. Dado que la vista de colección no tiene un formato de presentación preestablecido, se requiere más trabajo para proporcionar comentarios de interacción del usuario y realizar un seguimiento de la selección del usuario.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Crear el prototipo de celda

Dado que la vista de colección no incluye un prototipo de celda predeterminado, el desarrollador deberá agregar uno o varios `.xib` archivos a la aplicación de Xamarin. Mac para definir el diseño y el contenido de las celdas individuales.

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Agregar** > **nuevo archivo..** .
2. Seleccione >  `EmployeeItem` **controlador de vista**de Mac y asígnele un nombre (por ejemplo, en este ejemplo) y haga clic en el botón **nuevo** para crear: 

    ![Agregar un nuevo controlador de vista](collection-view-images/proto01.png)

    Esto agregará un `EmployeeItem.cs`archivo `EmployeeItemController.cs` , `EmployeeItemController.xib` y a la solución del proyecto.
3. Haga doble clic en `EmployeeItemController.xib` el archivo para abrirlo y editarlo en la Interface Builder de Xcode.
4. Agregue y dos`NSLabel` controles a la vista y colóquelos de la siguiente manera: `NSBox` `NSImageView`

    ![Diseñar el diseño del prototipo de celda](collection-view-images/proto02.png)
5. Abra el **Editor del asistente** y cree una **salida** para `NSBox` que se pueda usar para indicar el estado de selección de una celda:

    ![Exponer el NSBox en una salida](collection-view-images/proto03.png)
6. Vuelva al **Editor estándar** y seleccione la vista imagen.
7. En el **Inspector de enlace**, seleccione **enlazar al** > **propietario del archivo** y escriba una ruta de `self.Person.Icon`acceso de clave de **modelo** de:

    ![Enlazar el icono](collection-view-images/proto04.png)
8. Seleccione la primera etiqueta y, en **el inspector de enlace**, seleccione **enlazar al** > **propietario del archivo** y escriba una ruta `self.Person.Name`de acceso de clave de **modelo** de:

    ![Enlazar el nombre](collection-view-images/proto05.png)
9. Seleccione la segunda etiqueta y, en **el inspector de enlace**, seleccione **enlazar al** > **propietario del archivo** y escriba una ruta `self.Person.Occupation`de acceso de clave de **modelo** de:

    ![Enlace de la profesión](collection-view-images/proto06.png)
10. Guarde los cambios en el `.xib` archivo y vuelva a Visual Studio para sincronizar los cambios.

Edite `EmployeeItemController.cs` el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

Al examinar este código en detalle, la clase hereda de `NSCollectionViewItem` para que pueda actuar como prototipo de una celda de vista de colección. La `Person` propiedad expone la clase que se usó para enlazar datos a la vista de imagen y a las etiquetas en Xcode. Se trata de una instancia del `PersonModel` creado anteriormente.

La `BackgroundColor` propiedad es un acceso directo `NSBox` al del `FillColor` control que se utilizará para mostrar el estado de selección de una celda. Al invalidar la `Selected` propiedad `NSCollectionViewItem`de, el código siguiente establece o borra este estado de selección:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Crear el origen de datos de la vista de colección

Un origen de datos de vista`NSCollectionViewDataSource`de colección () proporciona todos los datos de una vista de colección y crea y rellena una celda de vista de `.xib` colección (mediante el prototipo) según sea necesario para cada elemento de la colección.

Agregue una nueva clase al proyecto, llámela `CollectionViewDataSource` y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

Al examinar este código en detalle, la clase hereda de `NSCollectionViewDataSource` y expone una lista de `PersonModel` instancias a través de su `Data` propiedad.

Puesto que esta colección solo tiene una sección, el código invalida el `GetNumberOfSections` método y siempre devuelve. `1` Además, el `GetNumberofItems` método se invalida en, devuelve el número de elementos de la `Data` lista de propiedades.

Se `GetItem` llama al método siempre que se requiera una nueva celda y tenga el aspecto siguiente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

Se llama al `EmployeeItemController` `Person` método de la vista de colección para crear o devolver una instancia reutilizable de y su propiedad se establece en el elemento que se muestra en la celda solicitada. `MakeItem` 

El `EmployeeItemController` debe registrarse con el controlador de vista de colección de antemano mediante el código siguiente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

El **identificador** (`EmployeeCell`) usado en la `MakeItem` llamada _debe_ coincidir con el nombre del controlador de vista que se registró con la vista de colección. Este paso se explicará con más detalle a continuación.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Controlar la selección de elementos

Para controlar la selección y la anulación de la selección de los elementos de `NSCollectionViewDelegate` la colección, se necesitará un. Dado que este ejemplo utilizará el tipo de `NSCollectionViewFlowLayout` diseño integrado, se `NSCollectionViewDelegateFlowLayout` requerirá una versión específica de este delegado.

Agregue una nueva clase al proyecto, llámela `CollectionViewDelegate` y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

Los `ItemsSelected` métodos `ItemsDeselected` y se reemplazan y se usan para establecer o borrar `PersonSelected` la propiedad del controlador de vistas que está controlando la vista de colección cuando el usuario selecciona o anula la selección de un elemento. Esto se mostrará en detalle a continuación.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Crear la vista de colección en Interface Builder

Con todas las piezas de soporte técnico necesarias en su lugar, se puede editar el guión gráfico principal y agregarle una vista de colección.

Haga lo siguiente:

1. Haga doble clic en `Main.Storyboard` el archivo en el **Explorador de soluciones** para abrirlo para su edición en el Interface Builder de Xcode.
2. Arrastre una vista de colección a la vista principal y cambie su tamaño para rellenar la vista:

    ![Agregar una vista de colección al diseño](collection-view-images/collection01.png)
3. Con la vista de recopilación seleccionada, use el editor de restricciones para anclarlo a la vista cuando se cambie el tamaño:

    ![Agregar restricciones](collection-view-images/collection02.png)
4. Asegúrese de que la vista de recopilación está seleccionada en el **superficie de diseño** (y no en la vista de desplazamiento o el **recorte** con **borde** que la contiene), cambie al **Editor del asistente** y cree una **salida** para la vista de recopilación:

    ![Agregar restricciones](collection-view-images/collection03.png)
5. Guarde los cambios y vuelva a Visual Studio para realizar la sincronización.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Reunir todo

Ahora se han colocado todas las piezas auxiliares con una clase que actúe como`PersonModel`modelo de datos (), se ha agregado un `NSCollectionViewDataSource` para proporcionar los datos, se ha creado un `NSCollectionViewDelegateFlowLayout` para administrar la selección de elementos y `NSCollectionView` se ha agregado un al guion gráfico principal. y se exponen como`EmployeeCollection`una salida ().

El paso final consiste en editar el controlador de vista que contiene la vista de colección y reunir todas las piezas para rellenar la colección y controlar la selección de elementos.

Edite `ViewController.cs` el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

Al examinar este código en detalle, se define una `Datasource` propiedad para `CollectionViewDataSource` que contenga una instancia de que proporcionará los datos para la vista de colección. Se `PersonSelected` define una propiedad para que `PersonModel` contenga el elemento que representa el elemento actualmente seleccionado en la vista de colección. Esta propiedad también genera el `SelectionChanged` evento cuando cambia la selección.

La `ConfigureCollectionView` clase se usa para registrar el controlador de vista que actúa como el prototipo de celda con la vista de colección mediante la línea siguiente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Observe que el **identificador** (`EmployeeCell`) que se usa para registrar el prototipo coincide con el llamado en `GetItem` `CollectionViewDataSource` el método del definido anteriormente:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Además, el tipo del controlador de vistas **debe** coincidir exactamente con el `.xib` nombre del archivo que defineel prototipo. En el caso de este ejemplo, `EmployeeItemController` y `EmployeeItemController.xib`.

El diseño real de los elementos de la vista de colección se controla mediante una clase de diseño de vista de colección y se puede cambiar dinámicamente en tiempo de ejecución mediante la asignación `CollectionViewLayout` de una nueva instancia a la propiedad. Al cambiar esta propiedad, se actualiza la apariencia de la vista de colección sin animar el cambio.

Apple distribuye dos tipos de diseño integrados con la vista de colección que controlará los usos más habituales `NSCollectionViewFlowLayout` : `NSCollectionViewGridLayout`y. Si el desarrollador requirió un formato personalizado, como la colocación de los elementos en un círculo, puede crear una instancia personalizada de `NSCollectionViewLayout` e invalidar los métodos necesarios para lograr el efecto deseado.

En este ejemplo se usa el diseño de flujo predeterminado para crear una instancia `NSCollectionViewFlowLayout` de la clase y configurarla como se indica a continuación:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

La `ItemSize` propiedad define el tamaño de cada celda individual de la colección. La `SectionInset` propiedad define los insets del borde de la colección en las que se colocarán las celdas. `MinimumInteritemSpacing`define el espaciado mínimo entre los elementos `MinimumLineSpacing` y define el espaciado mínimo entre las líneas de la colección.

El diseño se asigna a la vista de colección y `CollectionViewDelegate` se adjunta una instancia de para administrar la selección de elementos:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

El `PopulateWithData` método crea una nueva instancia `CollectionViewDataSource`de, la rellena con datos, la adjunta a la vista de `ReloadData` colección y llama al método para mostrar los elementos:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

El `ViewDidLoad` método se invalida y llama a los `ConfigureCollectionView` métodos `PopulateWithData` y para mostrar la vista de colección final al usuario:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con las vistas de colección en una aplicación de Xamarin. Mac. En primer lugar, se examinó la exposición C# de una clase a Objective-C mediante el uso de código de clave-valor (KVC) y el valor de clave (KVO). A continuación, se mostró cómo usar una clase compatible con KVO y cómo enlazarla a las vistas de colección del Interface Builder de Xcode. Por último, se ha mostrado cómo interactuar con las vistas C# de colección en el código.

## <a name="related-links"></a>Vínculos relacionados

- [MacCollectionNew (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
