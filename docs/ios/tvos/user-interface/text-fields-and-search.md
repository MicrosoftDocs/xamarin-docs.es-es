---
title: Trabajar con campos de texto y búsqueda de tvOS en Xamarin
description: En este documento se describe cómo trabajar con campos de texto y de búsqueda en una aplicación de tvOS compilada con Xamarin. Proporciona información general de alto nivel sobre los campos de texto y de búsqueda, y describe los teclados, la integración de guiones gráficos, los modelos de datos de búsqueda, etc.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 6dbd0b3e7d14307a3b9e7de552e2d59e0fbbcaa4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768561"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Trabajar con campos de texto y búsqueda de tvOS en Xamarin

Cuando sea necesario, la aplicación Xamarin. tvOS puede solicitar pequeños fragmentos de texto del usuario (como identificadores de usuario y contraseñas) mediante un campo de texto y el teclado en pantalla:

[![](text-fields-and-search-images/intro01.png "Ejemplo de campo de búsqueda")](text-fields-and-search-images/intro01.png#lightbox)

Opcionalmente, puede proporcionar la capacidad de búsqueda de palabras clave del contenido de la aplicación mediante un campo de búsqueda:

[![](text-fields-and-search-images/intro02.png "Resultados de la búsqueda de ejemplo")](text-fields-and-search-images/intro02.png#lightbox)

En este documento se explican los detalles del trabajo con campos de texto y de búsqueda en una aplicación Xamarin. tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Acerca de los campos de texto y de búsqueda

Como se indicó anteriormente, si es necesario, Xamarin. tvOS puede presentar uno o más campos de texto para recopilar pequeñas cantidades de texto del usuario mediante una pantalla (o un teclado Bluetooth opcional según la versión de tvOS que el usuario haya instalado).

Además, si la aplicación presenta grandes cantidades de contenido al usuario (como una música, películas o una colección de imágenes), es posible que desee incluir un campo de búsqueda que permita al usuario escribir una pequeña cantidad de texto para filtrar la lista de elementos disponibles.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campos de texto

En tvOS, un campo de texto se presenta como un cuadro de entrada de esquina redondeada de alto fijo que abrirá un teclado en pantalla cuando el usuario haga clic en él:

[![](text-fields-and-search-images/text01.png "Campos de texto en tvOS")](text-fields-and-search-images/text01.png#lightbox)

Cuando el usuario mueve el [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) a un campo de texto determinado, aumentará de tamaño y mostrará una sombra profunda. Tendrá que tener esto en cuenta al diseñar la interfaz de usuario, ya que los campos de texto pueden superponerse a otros elementos de la interfaz de usuario cuando estén enfocados.

Apple tiene las siguientes sugerencias para trabajar con campos de texto:

- **Use la entrada de texto con moderación** , debido a la naturaleza del teclado en pantalla, la introducción de secciones largas de texto o la rellenado de varios campos de texto es tediosa para el usuario. Una solución mejor es limitar la cantidad de entradas de texto mediante listas de selección o [botones](~/ios/tvos/user-interface/buttons.md).
- **Usar sugerencias para comunicar** el campo de texto de propósito puede mostrar el marcador de posición "hints" cuando está vacío. Si es aplicable, use sugerencias para describir el propósito del campo de texto en lugar de una etiqueta independiente.
- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado diferentes y creados por el usuario que puede especificar para el campo de texto. Por ejemplo, el teclado de dirección de correo electrónico puede facilitar la entrada permitiendo al usuario seleccionar en una lista de direcciones escritas recientemente.
- **Cuando sea necesario, use campos de texto seguro** : un campo de texto seguro presenta los caracteres especificados como puntos (en lugar de las letras reales). Use siempre un campo de texto seguro al recopilar información confidencial, como contraseñas.

<a name="Keyboards" />

## <a name="keyboards"></a>Teclados

Siempre que el usuario haga clic en un campo de texto de la interfaz de usuario, se mostrará un teclado en pantalla lineal. El usuario usa la superficie táctil [Siri remota](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para seleccionar Letras individuales del teclado y escribir la información solicitada:

[![](text-fields-and-search-images/keyboard01.png "Teclado remoto Siri")](text-fields-and-search-images/keyboard01.png#lightbox)

Si hay más de un campo de texto en la vista actual, se mostrará automáticamente un botón **siguiente** para llevar al usuario al siguiente campo de texto. Se mostrará un botón **listo** para el último campo de texto que finalizará la entrada de texto y devolverá el usuario a la pantalla anterior.

En cualquier momento, el usuario también puede presionar el botón de **menú** en la entrada de texto de Siri remoto para finalizar y volver a la pantalla anterior.

Apple tiene las siguientes sugerencias para trabajar con teclados en pantalla:

- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado diferentes y creados por el usuario que puede especificar para el campo de texto. Por ejemplo, el teclado de dirección de correo electrónico puede facilitar la entrada permitiendo al usuario seleccionar en una lista de direcciones escritas recientemente.
- **Cuando sea necesario, use las vistas de accesorios de teclado** : además de la información estándar que se muestra siempre, se pueden agregar a la pantalla vistas de accesorio opcionales (como imágenes o etiquetas) para aclarar el propósito de la entrada de texto o para ayudar el usuario que especifica la información necesaria.

Para obtener más información sobre cómo trabajar con el teclado en pantalla, consulte la guía de programación de [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [Administración del teclado](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [vistas personalizadas de datos de entrada y de](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) [texto a](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) la documentación de iOS.

<a name="Search" />

## <a name="search"></a>Buscar

Un campo de búsqueda presenta una pantalla especializada que proporciona un campo de texto y un teclado en pantalla que permite al usuario filtrar una colección de elementos que se muestran debajo del teclado:

[![](text-fields-and-search-images/search01.png "Resultados de la búsqueda de ejemplo")](text-fields-and-search-images/search01.png#lightbox)

A medida que el usuario escribe letras en el campo de búsqueda, los resultados siguientes reflejarán automáticamente los resultados de la búsqueda. En cualquier momento, el usuario puede cambiar el foco a los resultados y seleccionar uno de los elementos presentados.

Apple tiene las siguientes sugerencias para trabajar con campos de búsqueda:

- **Proporcionar búsquedas recientes** : dado que escribir texto con el Siri remoto puede ser tedioso y los usuarios tienden a repetir solicitudes de búsqueda, considere la posibilidad de agregar una sección de resultados de búsqueda recientes antes de los resultados actuales en el área del teclado.
- **Siempre que sea posible, limite el número de resultados** , ya que una lista grande de elementos puede ser difícil de analizar y navegar. considere la posibilidad de limitar el número de resultados devueltos.
- **Si es necesario, proporcione los filtros de resultados** de la búsqueda. Si el contenido proporcionado por la aplicación se presta a sí mismo, considere la posibilidad de agregar barras de ámbito para permitir que el usuario filtre aún más los resultados de búsqueda devueltos.

Para obtener más información, consulte referencia de la [clase UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)de Apple.

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Trabajar con campos de texto

La forma más fácil de trabajar con campos de texto en una aplicación Xamarin. tvOS es agregarlos al diseño de la interfaz de usuario mediante el diseñador de iOS.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo para abrirlo para su edición.
1. Arrastre uno o más **campos de texto** a la superficie de diseño en una vista:

    [![](text-fields-and-search-images/text02.png "Un campo de texto")](text-fields-and-search-images/text02.png#lightbox)
1. Seleccione los **campos de texto** y asigne a cada uno un **nombre** único en la pestaña **Widget** del **Panel de propiedades**:

    [![](text-fields-and-search-images/text03.png "Ficha widget del Panel de propiedades")](text-fields-and-search-images/text03.png#lightbox)
1. En la sección **campo de texto** , puede definir elementos como la sugerencia de marcador de **posición** y el **valor**predeterminado:

    [![](text-fields-and-search-images/text04.png "Sección campo de texto")](text-fields-and-search-images/text04.png#lightbox)
1. Desplácese hacia abajo para definir propiedades como la revisión **ortográfica**, el **capitalización** y el **tipo de teclado**predeterminado:

    [![](text-fields-and-search-images/text05.png "Revisión ortográfica, capitalización y el tipo de teclado predeterminado")](text-fields-and-search-images/text05.png#lightbox)
1. Guarde los cambios en el guion gráfico.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre uno o más **campos de texto** a la superficie de diseño en una vista:

    [![](text-fields-and-search-images/text02-vs.png "Un campo de texto")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Seleccione los **campos de texto** y asigne a cada uno un **nombre** único en la pestaña **Widget** del **Explorador de propiedades**:

    [![](text-fields-and-search-images/text03-vs.png "La pestaña widget")](text-fields-and-search-images/text03-vs.png#lightbox)
1. En la sección **campo de texto** , puede definir elementos como la sugerencia de marcador de **posición** y el **valor**predeterminado:

    [![](text-fields-and-search-images/text04-vs.png "Sección campo de texto")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Desplácese hacia abajo para definir propiedades como la revisión **ortográfica**, el **capitalización** y el **tipo de teclado**predeterminado:

    [![](text-fields-and-search-images/text05-vs.png "Revisión ortográfica, capitalización y el tipo de teclado predeterminado")](text-fields-and-search-images/text05-vs.png#lightbox)
1. Guarde los cambios en el guion gráfico.

-----

En el código, puede obtener o establecer el valor de un campo de texto mediante `Text` su propiedad:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

Opcionalmente, puede usar los `Started` eventos `Ended` de campo de texto y para responder a la entrada de texto a partir de y finalizar.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Trabajar con campos de búsqueda

La forma más fácil de trabajar con campos de búsqueda en una aplicación de Xamarin. tvOS es agregarlos al diseño de la interfaz de usuario mediante el diseñador de la interfaz.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo para abrirlo para su edición.
1. Arrastre un nuevo controlador de vista de colección al guion gráfico para mostrar los resultados de la búsqueda del usuario:

    [![](text-fields-and-search-images/search02.png "Un controlador de vista de colección")](text-fields-and-search-images/search02.png#lightbox)
1. En la **pestaña widget** del **Panel de propiedades**, use `SearchResultsViewController` para la **clase** y `SearchResults` para el identificador de **guión gráfico**:

    [![](text-fields-and-search-images/search03.png "La pestaña widget")](text-fields-and-search-images/search03.png#lightbox)
1. Seleccione el **prototipo de celda** en la superficie de diseño.
1. En la pestaña **Widget** del **Explorador de propiedades**, use `SearchResultCell` para la **clase** y `ImageCell` para el **identificador**:

    [![](text-fields-and-search-images/search04.png "La pestaña widget")](text-fields-and-search-images/search04.png#lightbox)
1. Diseñe el diseño del **prototipo de celda** y exponga cada elemento con un **nombre** único en la pestaña **Widget** del explorador de **propiedades**:

    [![](text-fields-and-search-images/search05.png "Diseño del prototipo de celda")](text-fields-and-search-images/search05.png#lightbox)
1. Guarde los cambios en el guion gráfico.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre un nuevo controlador de vista de colección al guion gráfico para mostrar los resultados de la búsqueda del usuario:

    [![](text-fields-and-search-images/seach02-vs.png "Un controlador de vista de colección")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. En la pestaña **Widget** del **Explorador de propiedades**, use `SearchResultsViewController` para la **clase** y `SearchResults` para el **identificador de guión gráfico**:

    [![](text-fields-and-search-images/search03-vs.png "La pestaña widget")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Seleccione el **prototipo de celda** en la superficie de diseño.
1. En la pestaña **Widget** del **Explorador de propiedades**, use `SearchResultCell` para la **clase** y `ImageCell` para el **identificador**:

    [![](text-fields-and-search-images/search04-vs.png "La pestaña widget")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Diseñe el diseño del **prototipo de celda** y exponga cada elemento con un **nombre** único en la pestaña **Widget** del explorador de **propiedades**:

    [![](text-fields-and-search-images/search05-vs.png "Diseño del prototipo de celda")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Guarde los cambios en el guion gráfico.

-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Proporcionar un modelo de datos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A continuación, deberá proporcionar una clase para que actúe como modelo de datos para los resultados que buscará el usuario. En el **Explorador de soluciones**, haga clic con el botón secundario en el nombre del proyecto y seleccione **Agregar** > **nuevo archivo..** . Clase vacíaGeneral > y proporcione un nombre:  > 

[![](text-fields-and-search-images/search06.png "Seleccione clase vacía y proporcione un nombre.")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A continuación, deberá proporcionar una clase para que actúe como modelo de datos para los resultados que buscará el usuario. En el **Explorador de soluciones**, haga clic con el botón secundario en el nombre del proyecto y seleccione **Agregar** > **nuevo elemento..** .AppleMisc > Class yproporcioneunnombre: >   > 

[![](text-fields-and-search-images/search06-vs.png "Seleccione la clase y proporcione un nombre.")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Por ejemplo, una aplicación que permite al usuario buscar una colección de imágenes por título y palabra clave podría ser similar a la siguiente:

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>La celda de la vista de colección

Con el modelo de datos en su lugar, edite la`SearchResultViewCell.cs` **celda del prototipo** () y haga que parezca ser la siguiente:

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

El `UpdateUI` método se usará para mostrar campos individuales de los elementos **PictureInformation** (la `PictureInfo` propiedad) en los elementos de la interfaz de usuario con nombre cada vez que se actualice la propiedad. Por ejemplo, la imagen y el título asociados a la imagen.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>El controlador de vista de colección

Después, edite el controlador de vista de la`SearchResultsViewController.cs`colección de resultados de la búsqueda () y haga que tenga un aspecto similar al siguiente:

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

En primer lugar `IUISearchResultsUpdating` , la interfaz se agrega a la clase para controlar el filtro del controlador de búsqueda que está actualizando el usuario:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

También se define una constante para especificar el identificador de la **celda del prototipo** (que coincide con el identificador definido en el diseñador de la interfaz anterior) que se usará más adelante cuando el controlador de la colección solicite una nueva celda:

```csharp
public const string CellID = "ImageCell";
```

El almacenamiento se crea para la lista completa de elementos que se están buscando, el término del filtro de búsqueda y la lista de elementos que coinciden con ese término:

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Cuando se `SearchFilter` cambia el, la lista de elementos coincidentes se actualiza y se recarga el contenido de la vista de recopilación. La `FindPictures` rutina es responsable de buscar los elementos que coinciden con el nuevo término de búsqueda:

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

El valor de `SearchFilter` se actualizará (con lo que se actualizará la vista de la colección de resultados) cuando el usuario cambie el filtro en el controlador de búsqueda:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

El `PopulatePictures` método rellena inicialmente la colección de elementos disponibles:

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

En este ejemplo, todos los datos de ejemplo se crean en memoria cuando se carga el controlador de vista de colección. En una aplicación real, estos datos probablemente se leerán desde una base de datos o un servicio Web y solo cuando sea necesario no sobreejecutar la memoria limitada de Apple TV.

Los `NumberOfSections` métodos `GetItemsCount` y proporcionan el número de elementos coincidentes:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

El `GetCell` método devuelve una nueva **celda de prototipo** (basada en `CellID` el definido anteriormente en el guión gráfico) para cada elemento de la vista de colección:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

Se `WillDisplayCell` llama al método antes de que se muestre la celda para que se pueda configurar:

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

El `DidUpdateFocus` método proporciona comentarios visuales al usuario a medida que resaltan los elementos en la vista de colección de resultados:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Por último, `ItemSelected` el método controla el usuario que selecciona un elemento (al hacer clic en la superficie táctil con el Siri remoto) en la vista de colección de resultados:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Si el campo de búsqueda se presentó como una vista de cuadro de diálogo modal (en la parte superior de la vista que `DismissViewController` lo llama), use el método para descartar la vista de búsqueda cuando el usuario selecciona un elemento. En este ejemplo, el campo de búsqueda se presenta como el contenido de una pestaña de vista de pestaña, por lo que no se está descartando aquí.

Para más información sobre las vistas de colección, consulte nuestra documentación sobre [Cómo trabajar con vistas de colección](~/ios/tvos/user-interface/collection-views.md) .

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presentar el campo de búsqueda

Hay dos formas principales en que se puede presentar al usuario un campo de búsqueda (y sus resultados de búsqueda y teclado en pantalla asociados) en tvOS:

- **Vista de cuadro de diálogo modal** : el campo de búsqueda se puede presentar en la vista y el controlador de vista actuales como una vista de cuadro de diálogo modal de pantalla completa. Esto se hace normalmente en respuesta al usuario que hace clic en un botón o en otro elemento de la interfaz de usuario. Se descarta el cuadro de diálogo cuando el usuario selecciona un elemento de los resultados de la búsqueda.
- **Ver contenido** : el campo de búsqueda es una parte directa de una vista determinada. Por ejemplo, como el contenido de una pestaña de búsqueda en un controlador de vista de pestañas.

En el ejemplo de una lista de imágenes en las que se pueden realizar búsquedas, el campo de búsqueda se presenta como ver contenido en la pestaña buscar y el controlador de vistas de la pestaña buscar es similar al siguiente:

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

En primer lugar, se define una constante que coincide con el **identificador de guión gráfico** que se asignó al controlador de vista de la colección de resultados de la búsqueda en el diseñador de la interfaz:

```csharp
public const string SearchResultsID = "SearchResults";
```

A continuación, `ShowSearchController` el método crea un nuevo controlador de colección de vistas de búsqueda y muestra que era necesario:

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

En el método anterior, una vez `SearchResultsViewController` que se ha creado una instancia de desde el guión `UISearchController` gráfico, se crea un nuevo para presentar el campo de búsqueda y el teclado en pantalla al usuario. La colección de resultados de la búsqueda (tal `SearchResultsViewController`y como se define en) se mostrará en este teclado.

A continuación, `SearchBar` se configura con información como la sugerencia de **marcador de posición** . Esto proporciona a los usuarios información sobre el tipo de búsqueda que se va a realizar en el formato.

A continuación, el campo de búsqueda se presenta al usuario de una de estas dos maneras:

- **Vista de cuadro de diálogo modal** : `PresentViewController` se llama al método para presentar la búsqueda sobre la vista existente, pantalla completa.
- **Ver contenido** : se `UISearchContainerViewController` crea un para que contenga el controlador de búsqueda. Se `UINavigationController` crea un que contiene el contenedor de búsqueda y, a continuación, se agrega el controlador de `AddChildViewController (navController)`navegación al controlador de vista `View.Add (navController.View)`y se presenta la vista.

Por último, y de nuevo en función del tipo de presentación `ViewDidLoad` , `ViewDidAppear` el método o llamará `ShowSearchController` al método para presentar la búsqueda al usuario:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

Cuando se ejecuta la aplicación y la pestaña buscar seleccionada por el usuario, se presentará al usuario la lista completa sin filtrar de elementos:

[![](text-fields-and-search-images/intro02.png "Resultados de búsqueda predeterminados")](text-fields-and-search-images/intro02.png#lightbox)

A medida que el usuario comienza a escribir un término de búsqueda, la lista de resultados se filtrará según ese término y se actualizará automáticamente:

[![](text-fields-and-search-images/intro03.png "Resultados de la búsqueda filtrados")](text-fields-and-search-images/intro03.png#lightbox)

En cualquier momento, el usuario puede cambiar el foco a un elemento en los resultados de la búsqueda y hacer clic en la superficie táctil del control remoto Siri para seleccionarlo.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con texto y los campos de búsqueda dentro de una aplicación Xamarin. tvOS. Se ha mostrado cómo crear texto y contenido de la colección de búsqueda en el diseñador de la interfaz y se ha mostrado de dos maneras diferentes en que se podría presentar un campo de búsqueda al usuario en tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
