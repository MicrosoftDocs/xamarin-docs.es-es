---
title: Trabajar con vistas de colección de tvOS en Xamarin
description: En este documento se describe cómo trabajar con vistas de colección en una aplicación de tvOS compilada con Xamarin. Trata los diseños de la vista de colección, la creación de celdas y vistas complementarias, la respuesta a eventos de usuario, etc.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 2adc161742e7891fcce65cf2025a1b7c531c7a39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435249"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Trabajar con vistas de colección de tvOS en Xamarin

Las vistas de colección permiten mostrar un grupo de contenido mediante diseños arbitrarios. Mediante el uso de la compatibilidad integrada, permiten los diseños de cuadrícula de creación sencillas o lineales, a la vez que también admiten diseños personalizados.

[![Vista de colección de ejemplo](collection-views-images/collection01.png)](collection-views-images/collection01.png#lightbox)

La vista de colección mantiene una colección de elementos utilizando un delegado y un origen de datos para proporcionar la interacción del usuario y el contenido de la colección. Dado que la vista de colección se basa en un subsistema de diseño que es independiente de la propia vista, proporcionar un diseño diferente puede cambiar fácilmente la presentación de los datos de la vista de colección sobre la marcha.

<a name="About-Collection-Views"></a>

## <a name="about-collection-views"></a>Acerca de las vistas de colección

Como se indicó anteriormente, una vista de colección ( `UICollectionView` ) administra una colección ordenada de elementos y presenta esos elementos con diseños personalizables. Las vistas de colección funcionan de manera similar a las vistas de tabla ( `UITableView` ), salvo que pueden usar diseños para presentar elementos en más de una sola columna.

Cuando se usa una vista de colección en tvOS, la aplicación es responsable de proporcionar los datos asociados a la colección mediante un origen de datos ( `UICollectionViewDataSource` ). Opcionalmente, los datos de la vista de colección se pueden organizar y presentar en grupos diferentes (secciones).

La vista de colección presenta los elementos individuales en la pantalla mediante una celda ( `UICollectionViewCell` ) que proporciona la presentación de un fragmento de información determinado de la colección (como una imagen y su título).

Opcionalmente, se pueden agregar vistas adicionales a la presentación de la vista de colección para que actúen como encabezado y pie de página para las secciones y celdas. El diseño de la vista de colección es responsable de definir la posición de estas vistas junto con las celdas individuales.

La vista de colección puede responder a la interacción del usuario mediante un delegado ( `UICollectionViewDelegate` ). Este delegado también es responsable de determinar si una celda determinada puede obtener el foco, si se ha resaltado una celda o si se ha seleccionado una. En algunos casos, el delegado determina el tamaño de las celdas individuales.

<a name="Collection-View-Layouts"></a>

## <a name="collection-view-layouts"></a>Diseños de la vista de colección

Una característica clave de una vista de colección es su separación entre los datos que presenta y su diseño. Un diseño de vista de colección ( `UICollectionViewLayout` ) es responsable de proporcionar la organización y la ubicación de las celdas (y las vistas complementarias) con en la presentación en pantalla de la vista de colección.

La vista de colección crea las celdas individuales desde su origen de datos adjunto y, a continuación, se organizan y muestran en el diseño de vista de colección dado.

Normalmente, el diseño de la vista de colección se proporciona cuando se crea la vista de colección. Sin embargo, puede cambiar el diseño de la vista de colección en cualquier momento y la presentación en pantalla de los datos de la vista de colección se actualizará automáticamente con el nuevo diseño proporcionado.

El diseño de la vista de colección proporciona varios métodos que se pueden usar para animar la transición entre dos diseños diferentes (de forma predeterminada no se realiza ninguna animación). Además, los diseños de la vista de colección pueden trabajar con reconocedores de gestos para animar aún más la interacción del usuario, lo que provoca un cambio en el diseño.

<a name="Creating-Cells-and-Supplementary-Views"></a>

## <a name="creating-cells-and-supplementary-views"></a>Crear celdas y vistas complementarias

El origen de datos de una vista de colección no es solo responsable de proporcionar los datos que respaldan el elemento de la colección, sino también de las celdas que se usan para mostrar el contenido.

Dado que las vistas de colección se diseñaron para administrar grandes colecciones de elementos, las celdas individuales se pueden quitar de la cola y volver a usar para evitar las limitaciones de memoria que se sobrecargan. Hay dos métodos diferentes para quitar de la cola las vistas:

- `DequeueReusableCell` : Crea o devuelve una celda del tipo especificado (tal y como se especifica en el guión gráfico de la aplicación).
- `DequeueReusableSupplementaryView` : Crea o devuelve una vista complementaria del tipo especificado (tal y como se especifica en el guión gráfico de la aplicación).

Antes de llamar a cualquiera de estos métodos, debe registrar la clase, el guión gráfico o el `.xib` archivo usados para crear la vista de la celda con la vista de colección. Por ejemplo:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Donde `typeof(CityCollectionViewCell)` proporciona la clase que admite la vista y `CityViewDatasource.CardCellId` proporciona el identificador usado cuando se quita la cola de la celda (o vista).

Una vez que la celda se ha quitado de la cola, se configura con los datos del elemento que representa y se vuelve a la vista de recopilación para mostrarla.

<a name="About-Collection-View-Controllers"></a>

## <a name="about-collection-view-controllers"></a>Acerca de los controladores de vista de colección

Un controlador de vista de colección ( `UICollectionViewController` ) es un controlador de vista especializado ( `UIViewController` ) que proporciona el siguiente comportamiento:

- Es responsable de cargar la vista de colección desde su guión gráfico o `.xib` archivo y crear una instancia de la vista. Si se crea en el código, crea automáticamente una nueva vista de colección no configurada.
- Una vez cargada la vista de colección, el controlador intenta cargar su origen de datos y delegar desde el guión gráfico o el `.xib` archivo. Si no hay ninguno disponible, se establece como el origen de ambos.
- Garantiza que los datos se carguen antes de que la vista de colección se rellene la primera vez que se muestre y que se vuelva a cargar y borre la selección en cada pantalla subsiguiente.

Además, el controlador de vista de colección proporciona métodos reemplazables que se pueden usar para administrar el ciclo de vida de la vista de colección, como `AwakeFromNib` y `ViewWillDisplay` .

<a name="Collection-Views-and-Storyboards"></a>

## <a name="collection-views-and-storyboards"></a>Vistas de colección y guiones gráficos

La manera más sencilla de trabajar con una vista de colección en la aplicación Xamarin. tvOS es agregar una a su guion gráfico. Como ejemplo rápido, vamos a crear una aplicación de ejemplo que presenta una imagen, un título y un botón seleccionar. Si el usuario hace clic en el botón seleccionar, se mostrará una vista de colección que permite al usuario elegir una nueva imagen. Cuando se elige una imagen, la vista de colección se cierra y se mostrarán la nueva imagen y el título.

Vamos a hacer lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie una nueva **aplicación de tvOS de vista única** en Visual Studio para Mac.
1. En el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y ábralo en el diseñador de iOS.
1. Agregue una vista de imagen, una etiqueta y un botón a la vista existente y configúrela para que tengan un aspecto similar al siguiente: 

    [![Diseño de ejemplo](collection-views-images/collection02.png)](collection-views-images/collection02.png#lightbox)
1. Asigne un **nombre** a la vista de imagen y la etiqueta en la **pestaña widget** del **Explorador de propiedades**. Por ejemplo: 

    [![Establecimiento del nombre](collection-views-images/collection03.png)](collection-views-images/collection03.png#lightbox)
1. A continuación, arrastre un controlador de vista de colección al guion gráfico: 

    [![Un controlador de vista de colección](collection-views-images/collection04.png)](collection-views-images/collection04.png#lightbox)
1. Control: arrastre desde el botón hasta el controlador de **vista de colección y seleccione Enviar** en el menú emergente: 

    [![Seleccione la extracción en el menú emergente.](collection-views-images/collection05.png)](collection-views-images/collection05.png#lightbox)
1. Cuando se ejecute la aplicación, esto hará que se muestre la vista de colección siempre que el usuario haga clic en el botón.
1. Seleccione la vista de recopilación y escriba los valores siguientes en la **pestaña diseño** del **Explorador de propiedades**: 

    [![Explorador de propiedades](collection-views-images/collection06.png)](collection-views-images/collection06.png#lightbox)
1. Esto controla el tamaño de las celdas individuales y los bordes entre las celdas y el borde exterior de la vista de colección.
1. Seleccione el controlador de vista de colección y establezca su clase en `CityCollectionViewController` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionViewController](collection-views-images/collection07.png)](collection-views-images/collection07.png#lightbox)
1. Seleccione la vista de colección y establezca su clase en `CityCollectionView` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionView](collection-views-images/collection08.png)](collection-views-images/collection08.png#lightbox)
1. Seleccione la celda vista de colección y establezca su clase en `CityCollectionViewCell` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionViewCell](collection-views-images/collection09.png)](collection-views-images/collection09.png#lightbox)
1. En la **pestaña widget** , asegúrese de que el **diseño** sea `Flow` y que la **dirección de desplazamiento** sea `Vertical` para la vista de colección: 

    [![La pestaña widget](collection-views-images/collection10.png)](collection-views-images/collection10.png#lightbox)
1. Seleccione la celda vista de colección y establezca **Identity** su identidad `CityCell` en en la **pestaña widget**: 

    [![Establezca la identidad en CityCell](collection-views-images/collection11.png)](collection-views-images/collection11.png#lightbox)
1. Guarde los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie una nueva **aplicación de tvOS de vista única** en Visual Studio.
1. En el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y ábralo en el diseñador de iOS.
1. Agregue una vista de imagen, una etiqueta y un botón a la vista existente y configúrela para que tengan un aspecto similar al siguiente: 

    [![Configurar el diseño](collection-views-images/collection02vs.png)](collection-views-images/collection02vs.png#lightbox)
1. Asigne un **nombre** a la vista de imagen y la etiqueta en la **pestaña widget** del **Explorador de propiedades**. Por ejemplo: 

    [![Explorador de propiedades](collection-views-images/collection03vs.png)](collection-views-images/collection03vs.png#lightbox)
1. A continuación, arrastre un controlador de vista de colección al guion gráfico: 

    [![Un controlador de vista de colección](collection-views-images/collection04vs.png)](collection-views-images/collection04vs.png#lightbox)
1. Control: arrastre desde el botón hasta el controlador de **vista de colección y seleccione Enviar** en el menú emergente: 

    [![Seleccione la extracción en el menú emergente.](collection-views-images/collection05vs.png)](collection-views-images/collection05vs.png#lightbox)
1. Cuando se ejecute la aplicación, esto hará que se muestre la vista de colección siempre que el usuario haga clic en el botón.
1. Seleccione la vista de colección y, en la **pestaña diseño** del **Explorador de propiedades** , escriba el **ancho** en _361_ y el **alto** como _256_ 
1. Esto controla el tamaño de las celdas individuales y los bordes entre las celdas y el borde exterior de la vista de colección.
1. Seleccione el controlador de vista de colección y establezca su clase en `CityCollectionViewController` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionViewController](collection-views-images/collection07vs.png)](collection-views-images/collection07vs.png#lightbox)
1. Seleccione la vista de colección y establezca su clase en `CityCollectionView` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionView](collection-views-images/collection08vs.png)](collection-views-images/collection08vs.png#lightbox)
1. Seleccione la celda vista de colección y establezca su clase en `CityCollectionViewCell` en la **pestaña widget**: 

    [![Establezca la clase en CityCollectionViewCell](collection-views-images/collection09vs.png)](collection-views-images/collection09vs.png#lightbox)
1. En la **pestaña widget** , asegúrese de que el **diseño** sea `Flow` y que la **dirección de desplazamiento** sea `Vertical` para la vista de colección: 

    [![Pestaña del widget](collection-views-images/collection10vs.png)](collection-views-images/collection10vs.png#lightbox)
1. Seleccione la celda vista de colección y establezca **Identity** su identidad `CityCell` en en la **pestaña widget**: 

    [![Establezca la identidad en CityCell](collection-views-images/collection11vs.png)](collection-views-images/collection11vs.png#lightbox)
1. Guarde los cambios.

-----

Si hubiéramos elegido `Custom` para el **diseño**de la vista de colección, podríamos haber especificado un diseño personalizado. Apple proporciona una integrada `UICollectionViewFlowLayout` `UICollectionViewDelegateFlowLayout` que puede presentar fácilmente los datos en un diseño basado en cuadrícula (los usa el `flow` estilo de diseño). 

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View"></a>

## <a name="providing-data-for-the-collection-view"></a>Proporcionar datos para la vista de colección

Ahora que tenemos nuestra vista de colección (y el controlador de vista de colección) agregada a nuestro guion gráfico, es necesario proporcionar los datos de la colección. 

<a name="The-Data-Model"></a>

### <a name="the-data-model"></a>El modelo de datos

En primer lugar, vamos a crear un modelo para los datos que contienen el nombre de archivo de la imagen que se va a mostrar, el título y una marca para permitir la selección de la ciudad.

Cree una `CityInfo` clase y haga que tenga el aspecto siguiente:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>La celda de la vista de colección

Ahora es necesario definir cómo se presentarán los datos para cada celda. Edite el `CityCollectionViewCell.cs` archivo (creado automáticamente en el archivo de guion gráfico) y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Para nuestra aplicación tvOS, mostraremos una imagen y un título opcional. Si no se puede seleccionar la ciudad especificada, se atenúa la vista de imagen con el código siguiente:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Cuando el usuario pasa el foco a la celda que contiene la imagen, queremos usar el efecto de Parallax integrado en él estableciendo la siguiente propiedad:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Para obtener más información sobre la navegación y el foco, consulte nuestra documentación sobre [Cómo trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri de dispositivos remotos y Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="The-Collection-View-Data-Provider"></a>

### <a name="the-collection-view-data-provider"></a>Proveedor de datos de la vista de colección

Con nuestro modelo de datos creado y el diseño de celda definido, vamos a crear un origen de datos para nuestra vista de colección. El origen de datos no solo proporcionará los datos de respaldo, sino que también quitará la cola de las celdas para mostrar las celdas individuales en la pantalla.

Cree una `CityViewDatasource` clase y haga que tenga el aspecto siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Echemos un vistazo a esta clase en detalle. En primer lugar, se hereda de `UICollectionViewDataSource` y se proporciona un acceso directo al identificador de las celdas (que asignamos en el diseñador de iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

A continuación, se proporciona almacenamiento para los datos de la colección y se proporciona una clase para rellenar los datos:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

A continuación, se invalida el `NumberOfSections` método y se devuelve el número de secciones (grupos de elementos) que tiene nuestra vista de colección. En este caso, solo hay uno:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

A continuación, devolvemos el número de elementos de nuestra colección mediante el código siguiente:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Por último, se quitará de la cola una celda reutilizable cuando se solicite la vista de colección con el código siguiente:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Después de obtener una celda de vista de colección de nuestro `CityCollectionViewCell` tipo, la rellenamos con el elemento especificado.

<a name="Responding-to-User-Events"></a>

## <a name="responding-to-user-events"></a>Responder a eventos de usuario

Dado que queremos que el usuario pueda seleccionar un elemento de nuestra colección, es necesario proporcionar un delegado de vista de colección para controlar esta interacción. Y es necesario proporcionar una manera de permitir que nuestra vista de llamada sepa qué elemento ha seleccionado el usuario.

<a name="The-App-Delegate"></a>

### <a name="the-app-delegate"></a>Delegado de la aplicación

Necesitamos una manera de relacionar el elemento seleccionado actualmente de la vista de colección con la vista de llamada. Vamos a usar una propiedad personalizada en nuestro `AppDelegate` . Edite el `AppDelegate.cs` archivo y agregue el código siguiente:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Define la propiedad y establece la ciudad predeterminada que se mostrará inicialmente. Más adelante, usaremos esta propiedad para mostrar la selección del usuario y permitir que se cambie la selección.

<a name="The-Collection-View-Delegate"></a>

### <a name="the-collection-view-delegate"></a>Delegado de la vista de colección

A continuación, agregue una nueva `CityViewDelegate` clase al proyecto y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Echemos un vistazo más de cerca a esta clase. En primer lugar, se hereda de `UICollectionViewDelegateFlowLayout` . La razón por la que se hereda de esta clase y no `UICollectionViewDelegate` es que estamos usando el integrado `UICollectionViewFlowLayout` para presentar nuestros elementos y no un tipo de diseño personalizado.

A continuación, se devuelve el tamaño de los elementos individuales mediante este código:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

A continuación, decidimos si una celda determinada puede obtener el foco mediante el código siguiente: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

Comprobamos si una parte determinada de los datos de respaldo tiene su `CanSelect` marca establecida en `true` y devuelve ese valor. Para obtener más información sobre la navegación y el foco, consulte nuestra documentación sobre [Cómo trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri de dispositivos remotos y Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

Por último, se responde al usuario que selecciona un elemento con el código siguiente:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Aquí establecemos la `SelectedCity` propiedad de nuestro `AppDelegate` en el elemento que el usuario seleccionó y cerraremos el controlador de vista de colección, volviendo a la vista que nos llamó. Todavía no hemos definido la `ParentController` propiedad de nuestra vista de colección. lo haremos a continuación.

<a name="Configuring-the-Collection-View"></a>

## <a name="configuring-the-collection-view"></a>Configurar la vista de colección

Ahora es necesario editar la vista de colección y asignar el origen de datos y el delegado. Edite el `CityCollectionView.cs` archivo (creado para nosotros automáticamente desde nuestro guion gráfico) y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

En primer lugar, proporcionamos un acceso directo para acceder a `AppDelegate` : 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

A continuación, se proporciona un acceso directo al origen de datos de la vista de colección y una propiedad para tener acceso al controlador de vista de colección (usado por el delegado anterior para cerrar la colección cuando el usuario realiza una selección):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

A continuación, usamos el código siguiente para inicializar la vista de colección y asignar la clase de celda, el origen de datos y el delegado:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Por último, queremos que el título de la imagen solo esté visible cuando el usuario lo haya resaltado (enfocado). Lo hacemos con el código siguiente:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Establecemos la transparencia del elemento anterior que pierde el foco a cero (0) y la transparencia del elemento siguiente obtienen el foco al 100%. Esta transición también se anima.

## <a name="configuring-the-collection-view-controller"></a>Configurar el controlador de vista de colección

Ahora tenemos que realizar la configuración final en nuestra vista de colección y permitir que el controlador establezca la propiedad que hemos definido para que la vista de colección se pueda cerrar después de que el usuario realice una selección.

Edite el `CityCollectionViewController.cs` archivo (creado automáticamente desde nuestro guion gráfico) y haga que tenga un aspecto similar al siguiente:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Reunir todo 

Ahora que tenemos todas las piezas colocadas para rellenar y controlar nuestra vista de colección, es necesario realizar las modificaciones finales en nuestra vista principal para que todo esté unido.

Edite el `ViewController.cs` archivo (creado automáticamente desde nuestro guion gráfico) y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

El código siguiente muestra inicialmente el elemento seleccionado de la `SelectedCity` propiedad de `AppDelegate` y lo vuelve a mostrar cuando el usuario ha realizado una selección de la vista de colección:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app"></a>

## <a name="testing-the-app"></a>Probar la aplicación

Con todo en su lugar, Si compila y ejecuta la aplicación, la vista principal se muestra con la ciudad predeterminada:

[![La pantalla principal](collection-views-images/run01.png)](collection-views-images/run01.png#lightbox)

Si el usuario hace clic en el botón **seleccionar una vista** , se mostrará la vista de colección:

[![La vista de colección](collection-views-images/run02.png)](collection-views-images/run02.png#lightbox)

Cualquier ciudad que tenga su `CanSelect` propiedad establecida en se `false` mostrará atenuada y el usuario no podrá establecer el foco. Cuando el usuario resalta un elemento (conviértalo en el foco), se muestra el título y puede usar el efecto de Parallax para imponer la imagen en 3D.

Cuando el usuario hace clic en una imagen seleccionada, la vista de colección se cierra y la vista principal se vuelve a mostrar con la nueva imagen:

[![Una nueva imagen en la pantalla principal](collection-views-images/run03.png)](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items"></a>

## <a name="creating-custom-layout-and-reordering-items"></a>Creación de un diseño personalizado y reordenación de elementos

Una de las principales características del uso de una vista de colección es la capacidad de crear diseños personalizados. Dado que tvOS se hereda de iOS, el proceso para crear un diseño personalizado es el mismo. Consulte la documentación [Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md) para obtener más información.

Los agregados recientemente a las vistas de colección para iOS 9 era la capacidad de permitir fácilmente la reordenación de los elementos de la colección. Una vez más, como tvOS 9 es un subconjunto de iOS 9, se hace de la misma manera. Consulte el documento de cambios en la [vista de colección](~/ios/user-interface/controls/uicollectionview.md) para obtener más detalles.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con vistas de colección dentro de una aplicación Xamarin. tvOS. En primer lugar, se han descrito todos los elementos que componen la vista de colección. A continuación, se mostró cómo diseñar e implementar una vista de colección mediante un guión gráfico. Por último, se proporcionan vínculos a información sobre cómo crear diseños personalizados y Reordenar elementos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)