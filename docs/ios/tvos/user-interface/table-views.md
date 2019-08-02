---
title: Trabajar con vistas de tabla tvOS en Xamarin
description: En este artículo se describe el diseño y el trabajo con vistas de tabla y controladores de vista de tabla dentro de una aplicación Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3df0d8f686ec521a55948a9eb4632d77e5c3691f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652321"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Trabajar con vistas de tabla tvOS en Xamarin

_En este artículo se describe el diseño y el trabajo con vistas de tabla y controladores de vista de tabla dentro de una aplicación Xamarin. tvOS._

En tvOS, se presenta una vista de tabla como una sola columna de filas de desplazamiento que se puede organizar opcionalmente en grupos o secciones. Las vistas de tabla deben usarse cuando sea necesario mostrar una gran cantidad de datos de forma eficaz al usuario, de una forma clara de entender.

Las vistas de tabla se muestran normalmente en un lado de una [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado mostrados en el lado opuesto:

[![](table-views-images/intro01.png "Vista de tabla de ejemplo")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>Acerca de las vistas de tabla

Un `UITableView` muestra una sola columna de filas desplazables como una lista jerárquica de información que se puede organizar opcionalmente en grupos o secciones: 

[![](table-views-images/table01.png "Un elemento seleccionado")](table-views-images/table01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con tablas:

- **Tenga en cuenta el ancho** : intente conseguir el equilibrio correcto en el ancho de la tabla. Si la tabla es demasiado ancha, puede ser difícil de analizar desde una distancia y puede quitarse del área de contenido disponible. Si la tabla es demasiado estrecha, puede hacer que la información se trunque o se ajuste, de nuevo esto puede ser difícil para el usuario leer de la habitación.
- **Mostrar el contenido de la tabla rápidamente** : para listas grandes de datos, cargue el contenido de forma diferida y empiece a mostrar información en cuanto se presente la tabla al usuario. Si la tabla tarda mucho en cargarse, el usuario podría perder interés en la aplicación o pensar que está bloqueada.
- **Informar a los usuarios de cargas de contenido largas** : Si el tiempo de carga de una tabla larga es inevitable, presente una [barra de progreso o un indicador de actividad](~/ios/tvos/user-interface/progress-indicators.md) para que sepan que la aplicación no se ha bloqueado.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Tipos de celda de vista de tabla

`UITableViewCell` Se utiliza para representar las filas de datos individuales en la vista de tabla. Apple ha definido varios tipos de celdas de tabla predeterminadas:

- **Default** : este tipo presenta una imagen de opción en el lado izquierdo de la celda y el título alineado a la izquierda a la derecha. 
- **Subtítulo** : este tipo presenta un título alineado a la izquierda en la primera línea y un subtítulo alineado a la izquierda más pequeño en la línea siguiente.
- **Valor 1** : este tipo presenta un título alineado a la izquierda con un subtítulo más claro que está alineado a la derecha en la misma línea.
- **Valor 2** : este tipo presenta un título alineado a la derecha con un subtítulo más claro que está alineado a la izquierda en la misma línea.

Todos los tipos de celda de vista de tabla predeterminados también admiten elementos gráficos como indicadores de divulgación o marcas de verificación. 

Además, puede definir un tipo de celda de vista de tabla **personalizada** y presentar una _celda de prototipo_, que puede crear en el diseñador de la interfaz o mediante código.

Apple tiene las siguientes sugerencias para trabajar con las celdas de la vista de tabla:

- **Evitar** el recorte de texto: Mantenga las líneas de texto cortas para que no se trunquen. Las palabras o frases truncadas son difíciles de analizar a través de la habitación.
- **Tenga en cuenta el estado de la fila enfocada** : dado que una fila se vuelve más grande, con esquinas redondeadas en el foco, debe probar la apariencia de la celda en todos los Estados. Es posible que las imágenes o el texto se recorten o tengan un aspecto incorrecto en el estado Focused.
- **Usar tablas modificables** si se mueven de forma moderada o se eliminan filas de la tabla, se tarda más tiempo en TvOS que iOS. Debe decidir detenidamente si esta característica agregará o distraerá de la aplicación tvOS.
- **Crear tipos de celda personalizados cuando sea necesario** : mientras que los tipos de celda de la vista de tabla integrada son excelentes para muchas situaciones, considere la posibilidad de crear tipos de celda personalizados para información no estándar a fin de proporcionar un mayor control y presentar mejor la información al usuario.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Trabajar con vistas de tabla

La manera más sencilla de trabajar con vistas de tabla en una aplicación de Xamarin. tvOS es crear y modificar su apariencia en el diseñador de la interfaz.

Para comenzar, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. En Visual Studio para Mac, inicie un nuevo proyecto de aplicación de tvOS y seleccione**aplicación de vista única** de **tvOS** >  > y haga clic en el botón **siguiente** : 

    [![](table-views-images/table02.png "Seleccionar una aplicación de vista única")](table-views-images/table02.png#lightbox)
1. Escriba un **nombre** para la aplicación y haga clic en **siguiente**: 

    [![](table-views-images/table03.png "Escriba un nombre para la aplicación")](table-views-images/table03.png#lightbox)
1. Ajuste el **nombre del proyecto** y el nombre de la **solución** , o acepte los valores predeterminados y haga clic en el botón **crear** para crear la nueva solución: 

    [![](table-views-images/table04.png "El nombre del proyecto y el nombre de la solución")](table-views-images/table04.png#lightbox)
1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo para abrirlo en el diseñador de iOS: 

    [![](table-views-images/table05.png "El archivo main. Storyboard")](table-views-images/table05.png#lightbox)
1. Seleccione y elimine el **controlador de vista predeterminado**: 

    [![](table-views-images/table06.png "Seleccionar y eliminar el controlador de vista predeterminado")](table-views-images/table06.png#lightbox)
1. Seleccione un **controlador de vista en dos paneles** en el **cuadro de herramientas** y arrástrelo hasta el superficie de diseño.
1. De forma predeterminada, obtendrá una [vista en dos paneles](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un controlador de vista de **tabla** en el lado izquierdo y un **controlador de vista** en el lado derecho. Este es el uso sugerido de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08.png "Agregar una vista en dos paneles")](table-views-images/table08.png#lightbox)
1. Tendrá que seleccionar todas las partes de la vista de tabla y asignarle un **nombre de clase** personalizado en la pestaña **Widget** del **Explorador de propiedades** para que pueda tener acceso a ella C# más adelante en el código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09.png "Asignar un nombre de clase")](table-views-images/table09.png#lightbox)
1. Asegúrese de crear una clase personalizada para el **controlador de vista de tabla**, la vista de **tabla** y las **celdas de prototipo**. Visual Studio para Mac agregará las clases personalizadas al árbol del proyecto a medida que se crean: 

    [![](table-views-images/table10.png "Clases personalizadas en el árbol del proyecto")](table-views-images/table10.png#lightbox)
1. A continuación, seleccione la vista de tabla en el Superficie de diseño y ajuste sus propiedades según sea necesario. Como el número de **celdas de prototipo** y el **estilo** (simple o agrupado): 

    [![](table-views-images/table11.png "La pestaña widget")](table-views-images/table11.png#lightbox)
1. Para cada **celda prototipo**, selecciónela y asigne un **identificador** único en la pestaña **Widget** del **Explorador de propiedades**. Este paso es _muy importante_ , ya que necesitará este identificador más adelante al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12.png "La pestaña widget")](table-views-images/table12.png#lightbox)
1. También puede seleccionar la presentación de la celda como uno de los [tipos de celda de vista de tabla](#table-view-cell-types) predeterminados a través de la lista desplegable **estilo** o establecerlo en **personalizado** y usar el superficie de diseño para diseñar la celda arrastrando otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13.png "Diseño de celda")](table-views-images/table13.png#lightbox)
1. Asigne un **nombre** único a cada elemento de la interfaz de usuario en el diseño de la celda prototipo en la pestaña **Widget** del **Explorador de propiedades** para C# que pueda acceder a ellos más adelante en el código: 

    [![](table-views-images/table14.png "Asignación de un nombre")](table-views-images/table14.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo de la vista de tabla.
1. A continuación, asigne clases personalizadas al resto del diseño de la interfaz de usuario, diseñe la vista de detalles y asigne **nombres** únicos a cada elemento de la interfaz de usuario en la vista C# de detalles para que pueda tener acceso a ellos también en. Por ejemplo: 

    [![](table-views-images/table15.png "Diseño de la interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guion gráfico.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. En Visual Studio, inicie un nuevo proyecto de aplicación de tvOS y seleccione **tvOS** > **aplicación de vista única** y escriba un nombre para la aplicación. Haga clic en el botón **Aceptar** para crear una nueva solución: 

    [![](table-views-images/table02-vs.png "Seleccionar una aplicación de vista única")](table-views-images/table02-vs.png#lightbox)
1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo para abrirlo en el diseñador de iOS: 

    [![](table-views-images/table05-vs.png "El archivo main. Storyboard")](table-views-images/table05-vs.png#lightbox)
1. Seleccione y elimine el **controlador de vista predeterminado**: 

    [![](table-views-images/table06-vs.png "Seleccionar y eliminar el controlador de vista predeterminado")](table-views-images/table06-vs.png#lightbox)
1. Seleccione un **controlador de vista en dos paneles** en el **cuadro de herramientas** y arrástrelo hasta el superficie de diseño: 

    [![](table-views-images/table07-vs.png "Un controlador de vista en dos paneles")](table-views-images/table07-vs.png#lightbox)
1. De forma predeterminada, obtendrá una [vista en dos paneles](~/ios/tvos/user-interface/split-views.md) con un **controlador de vista de navegación** y un controlador de vista de **tabla** en el lado izquierdo y un **controlador de vista** en el lado derecho. Este es el uso sugerido de Apple de una vista de tabla en tvOS: 

    [![](table-views-images/table08-vs.png "Diseño de la interfaz de usuario")](table-views-images/table08-vs.png#lightbox)
1. Tendrá que seleccionar todas las partes de la vista de tabla y asignarle un **nombre de clase** personalizado en la pestaña **Widget** del **Explorador de propiedades** para que pueda tener acceso a ella C# más adelante en el código. Por ejemplo, el **controlador de vista de tabla**: 

    [![](table-views-images/table09-vs.png "La pestaña widget")](table-views-images/table09-vs.png#lightbox)
1. Asegúrese de crear una clase personalizada para el **controlador de vista de tabla**, la vista de **tabla** y las **celdas de prototipo**. Visual Studio para Mac agregará las clases personalizadas al árbol del proyecto a medida que se crean: 

    [![](table-views-images/table10-vs.png "Clases personalizadas en el árbol del proyecto")](table-views-images/table10-vs.png#lightbox)
1. A continuación, seleccione la vista de tabla en el Superficie de diseño y ajuste sus propiedades según sea necesario. Como el número de **celdas de prototipo** y el **estilo** (simple o agrupado): 

    [![](table-views-images/table11-vs.png "La pestaña widget")](table-views-images/table11-vs.png#lightbox)
1. Para cada **celda prototipo**, selecciónela y asigne un **identificador** único en la pestaña **Widget** del **Explorador de propiedades**. Este paso es _muy importante_ , ya que necesitará este identificador más adelante al rellenar la tabla. Por ejemplo `AttrCell`: 

    [![](table-views-images/table12-vs.png "Asignar un identificador")](table-views-images/table12-vs.png#lightbox)
1. También puede seleccionar la presentación de la celda como uno de los [tipos de celda de vista de tabla](#table-view-cell-types) predeterminados a través de la lista desplegable **estilo** o establecerlo en **personalizado** y usar el superficie de diseño para diseñar la celda arrastrando otros widgets de interfaz de usuario desde el **cuadro de herramientas**: 

    [![](table-views-images/table13-vs.png "La lista desplegable de estilo")](table-views-images/table13-vs.png#lightbox)
1. Asigne un **nombre** único a cada elemento de la interfaz de usuario en el diseño de la celda prototipo en la pestaña **Widget** del **Explorador de propiedades** para C# que pueda acceder a ellos más adelante en el código: 

    [![](table-views-images/table14-vs.png "La pestaña widget")](table-views-images/table14-vs.png#lightbox)
1. Repita el paso anterior para todas las celdas de prototipo de la vista de tabla.
1. A continuación, asigne clases personalizadas al resto del diseño de la interfaz de usuario, diseñe la vista de detalles y asigne **nombres** únicos a cada elemento de la interfaz de usuario en la vista C# de detalles para que pueda tener acceso a ellos también en. Por ejemplo: 

    [![](table-views-images/table15.png "Diseño de la interfaz de usuario")](table-views-images/table15.png#lightbox)
1. Guarde los cambios en el guion gráfico.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Diseñar un modelo de datos

Para trabajar con la información que muestra la vista de tabla y facilitar la presentación de información detallada (a medida que el usuario selecciona o resalta las filas en la vista de tabla), cree una clase o clases personalizadas que actúen como modelo de datos para la información presentada .

Tome el ejemplo de una aplicación de reserva de viajes que contiene una lista de **ciudades**, cada una de las cuales contiene una lista única de **atracciones** que el usuario puede seleccionar. El usuario podrá marcar una atracción como *favorita*, seleccionar para obtener *indicaciones* sobre una atracción y *reservar un vuelo* para una ciudad determinada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para crear el modelo de datos para una **atracción**, haga clic con el botón derecho en el nombre del proyecto en el **Panel de solución** y seleccione **Agregar** > **nuevo archivo..** .. Escriba `AttractionInformation` como **nombre** y haga clic en el botón **nuevo** : 

[![](table-views-images/data01.png "Escriba AttractionInformation para el nombre")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para crear el modelo de datos para una **atracción**, haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Agregar** > **nuevo elemento..** .. Seleccione **clase** y escriba `AttractionInformation` como **nombre** y haga clic en el botón **Agregar** : 

[![](table-views-images/data01-vs.png "Seleccione clase y escriba AttractionInformation para el nombre.")](table-views-images/data01-vs.png#lightbox)

-----

Edite `AttractionInformation.cs` el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Esta clase proporciona las propiedades para almacenar la información sobre un **atracción**determinado.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A continuación, haga clic con el botón derecho en el nombre del proyecto en el **Panel de solución** de nuevo y seleccione **Agregar** > **nuevo archivo..** .. Escriba `CityInformation` como **nombre** y haga clic en el botón **nuevo** : 

[![](table-views-images/data02.png "Escriba CityInformation para el nombre")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A continuación, haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** de nuevo y seleccione **Agregar** > **nuevo elemento..** .. Escriba `CityInformation` como **nombre** y haga clic en el botón **Agregar** : 

[![](table-views-images/data02-vs.png "Escriba CityInformation para el nombre")](table-views-images/data02-vs.png#lightbox)

-----

Edite `CityInformation.cs` el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Esta clase contiene toda la información sobre una **ciudad**de destino, una colección de **atracciones** para esa ciudad y proporciona dos métodos auxiliares (`AddAttraction`) para que sea más fácil agregar atracciones a la ciudad.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>Origen de datos de la vista de tabla

Cada vista de tabla requiere un origen de`UITableViewDataSource`datos () para proporcionar los datos de la tabla y generar las filas necesarias según sea necesario en la vista de tabla.

En el ejemplo anterior, haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones**, seleccione **Agregar** > **nuevo** `AttractionTableDatasource` archivo... y llámelo y haga clic en el botón **nuevo** para crearlo. Después, edite `AttractionTableDatasource.cs` el archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Echemos un vistazo a algunas secciones de la clase en detalle.

En primer lugar, se ha definido una constante para que contenga el identificador único de la celda del prototipo (es el mismo identificador asignado en el diseñador de la interfaz anterior), se ha agregado un acceso directo al controlador de vista de tabla y se ha creado un almacenamiento para los datos:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

A continuación, guardamos el controlador de vista de tabla y, a continuación, compilamos y rellenamos el origen de datos (con los modelos de datos definidos anteriormente) cuando se crea la clase:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Por ejemplo, el `PopulateCities` método simplemente crea objetos de modelo de datos en la memoria, sin embargo, se pueden leer fácilmente desde una base de datos o un servicio Web en una aplicación real:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

El `NumberOfSections` método devuelve el número de secciones de la tabla:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

En el caso de las vistas de tabla de estilo **simple** , siempre devuelve 1.

El `RowsInSection` método devuelve el número de filas de la sección actual:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

De nuevo, para las vistas de tabla **sin formato** , devuelva el número total de elementos del origen de datos.

El `TitleForHeader` método devuelve el título de la sección dada:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Para un tipo de vista de tabla **simple** , deje el título`""`en blanco ().

Por último, cuando se solicite en la vista de tabla, cree y rellene una `GetCell` celda de prototipo mediante el método: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Para obtener más información sobre cómo trabajar `UITableViewDatasource`con, consulte la documentación de [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) de Apple.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Delegado de la vista de tabla

Cada vista de tabla requiere un delegado`UITableViewDelegate`() para responder a la interacción del usuario u otros eventos del sistema en la tabla.

En el ejemplo anterior, haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones**, seleccione **Agregar** > **nuevo** `AttractionTableDelegate` archivo... y llámelo y haga clic en el botón **nuevo** para crearlo. Después, edite `AttractionTableDelegate.cs` el archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

Echemos un vistazo a varias secciones de esta clase en detalles.

En primer lugar, se crea un acceso directo al controlador de vista de tabla cuando se crea la clase:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

A continuación, cuando se selecciona una fila (el usuario hace clic en la superficie táctil del control remoto de Apple), queremos marcar la **atracción** representada por la fila seleccionada como favorita:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

A continuación, cuando el usuario resalta una fila (proporcionándole el foco mediante la superficie táctil remota de Apple) queremos presentar los detalles de la **atracción** representada por esa fila en la sección de detalles de nuestro controlador de vista en dos paneles:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

Se `CanFocusRow` llama al método para cada fila que está a punto de obtener el foco en la vista de tabla. Devuelve `true` si la fila puede obtener el foco; de `false`lo contrario, devuelve. En el caso de este ejemplo, se ha creado un evento `AttractionHighlighted` personalizado que se generará en cada fila a medida que recibe el foco.

Para obtener más información sobre cómo trabajar `UITableViewDelegate`con, consulte la documentación de [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) de Apple.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>Celda de vista de tabla

Para cada celda de prototipo que haya agregado a la vista de tabla en el diseñador de la interfaz, también ha creado una instancia personalizada de la`UITableViewCell`celda de la vista de tabla () para que pueda rellenar la nueva celda (fila) mientras se crea.

En la aplicación de ejemplo, haga doble clic `AttractionTableCell.cs` en el archivo para abrirlo para su edición y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Esta clase proporciona el almacenamiento para el objeto de modelo de`AttractionInformation` datos de atracción (tal y como se definió anteriormente) que se muestra en la fila determinada:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

El `UpdateUI` método rellena los widgets de interfaz de usuario (que se agregaron al prototipo de la celda en el diseñador de interfaces) según sea necesario:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Para obtener más información sobre cómo trabajar `UITableViewCell`con, consulte la documentación de [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) de Apple.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Controlador de vista de tabla

Un controlador de vista de`UITableViewController`tabla () administra una vista de tabla que se ha agregado a un guion gráfico a través del diseñador de la interfaz.

En la aplicación de ejemplo, haga doble clic `AttractionTableViewController.cs` en el archivo para abrirlo para su edición y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Echemos un vistazo más de cerca a esta clase. En primer lugar, hemos creado accesos directos para facilitar el acceso a la y `DataSource` `TableDelegate`la de la vista de tabla. Los usaremos más adelante para comunicarse entre la vista de tabla en el lado izquierdo de la vista en dos paneles y la vista de detalles de la derecha.

Por último, cuando la vista de tabla se carga en la memoria, se crean `AttractionTableDatasource` instancias `AttractionTableDelegate` de y (creadas anteriormente) y se adjuntan a la vista de tabla.

Para obtener más información sobre cómo trabajar `UITableViewController`con, consulte la documentación de [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) de Apple.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Extraer todo el conjunto

Como se indicó al principio de este documento, las vistas de tabla se muestran normalmente en un lado de una [vista dividida](~/ios/tvos/user-interface/split-views.md) como navegación, con los detalles del elemento seleccionado que se muestran en el lado opuesto. Por ejemplo: 

[![](table-views-images/intro01.png "Ejecución de la aplicación de ejemplo")](table-views-images/intro01.png#lightbox)

Dado que se trata de un patrón estándar en tvOS, echemos un vistazo a los pasos finales para reunir todo y hacer que los lados izquierdo y derecho de la vista en dos paneles interactúen entre sí.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>Vista de detalle

En el ejemplo de la aplicación de viajes anterior, se define una clase`AttractionViewController`personalizada () para el controlador de vista estándar presentado en el lado derecho de la vista de división como la vista de detalle:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

Aquí se ha proporcionado la **atracción** (`AttractionInformation`) que se muestra como una propiedad y se creó `UpdateUI` un método que rellena los widgets de interfaz de usuario agregados a la vista en el diseñador de la interfaz.

También hemos definido un acceso directo al controlador de vista en dos paneles`SplitView`() que se va a usar para comunicar los cambios de vuelta a`AcctractionTableView`la vista de tabla ().

Por último, las acciones personalizadas (eventos) se agregaron `UIButton` a las tres instancias creadas en el diseñador de la interfaz, que permiten al usuario marcar una atracción como _favorita_, obtener _indicaciones_ sobre una atracción y _reservar un vuelo_ para un determinado ciudad.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Controlador de vista de navegación

Dado que el controlador de vista de tabla está anidado en un controlador de vista de navegación en el lado izquierdo de la vista en dos paneles, el controlador de`MasterNavigationController`vista de navegación tenía asignada una clase personalizada () en el diseñador de interfaces y se define de la siguiente manera:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

De nuevo, esta clase simplemente define algunos métodos abreviados para facilitar la comunicación entre los dos lados del controlador de vista de división:

* `SplitView`: Es un vínculo al controlador de vista en dos`MainSpiltViewController`paneles () al que pertenece el controlador de vista de navegación.
* `TableController`: Obtiene el controlador de vista de`AttractionTableViewController`tabla () que se presenta como vista superior en el controlador de vista de navegación.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Controlador de vista en dos paneles

Dado que el controlador de vista en dos paneles es la base de nuestra aplicación, creamos`MasterSplitViewController`una clase personalizada () para ella en el diseñador de la interfaz y la definimos de la siguiente manera:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

En primer lugar, creamos accesos directos al lado de **detalles** de la`AttractionViewController`vista en dos paneles () y`MasterNavigationController`al lado **maestro** (). De nuevo, esto facilita la comunicación entre los dos lados más adelante.

Después, cuando la vista en dos paneles se carga en la memoria, adjuntamos el controlador de vista de división a ambos lados de la vista dividida y respondemos al usuario que resalta una atracción en la vista de tabla (`AttractionHighlighted`) al mostrar la nueva atracción en el lado de **detalles** de Vista dividida.

Consulte la aplicación de ejemplo [tvTables](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvtable) para obtener una implementación completa de las vistas de tabla dentro de una vista dividida.

## <a name="table-views-in-detail"></a>Vistas de tabla en detalle

Dado que tvOS se basa en iOS, las vistas de tabla y los controladores de vista de tabla están diseñados y comportados de manera similar. Para obtener información más detallada sobre cómo trabajar con la vista de tabla en una aplicación de Xamarin, consulte la documentación sobre [Cómo trabajar con tablas y celdas](~/ios/user-interface/controls/tables/index.md) de iOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con vistas de tabla dentro de una aplicación Xamarin. tvOS. Y ha presentado un ejemplo de cómo trabajar con una vista de tabla dentro de una vista dividida, que es el uso típico de una vista de tabla en una aplicación de tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
