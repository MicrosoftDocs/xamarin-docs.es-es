---
title: Vistas de colección en Xamarin. iOS
description: Las vistas de colección permiten mostrar el contenido mediante diseños arbitrarios. Permiten crear diseños similares a cuadrículas de forma sencilla, a la vez que también admiten diseños personalizados.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 871020ff1f69edc20b582806a584dfd51982d336
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528609"
---
# <a name="collection-views-in-xamarinios"></a>Vistas de colección en Xamarin. iOS

_Las vistas de colección permiten mostrar el contenido mediante diseños arbitrarios. Permiten crear diseños similares a cuadrículas de forma sencilla, a la vez que también admiten diseños personalizados._

Las vistas de colección, disponibles `UICollectionView` en la clase, son un nuevo concepto en iOS 6 que presenta la presentación de varios elementos en la pantalla mediante diseños. Los patrones para proporcionar datos a un `UICollectionView` para crear elementos e interactuar con esos elementos siguen la misma delegación y los mismos patrones de origen de datos que se usan habitualmente en el desarrollo de iOS.

Sin embargo, las `UICollectionView` vistas de colección funcionan con un subsistema de diseño que es independiente del propio. Por lo tanto, simplemente proporcionar un diseño diferente puede cambiar fácilmente la presentación de una vista de colección.

iOS proporciona una clase de diseño `UICollectionViewFlowLayout` denominada que permite crear diseños basados en líneas, como una cuadrícula, sin trabajo adicional. Además, también se pueden crear diseños personalizados que permitan cualquier presentación que pueda imaginar.

## <a name="uicollectionview-basics"></a>Aspectos básicos de UICollectionView

La `UICollectionView` clase se compone de tres elementos diferentes:

- **Celdas** : vistas controladas por datos para cada elemento
- **Vistas complementarias** : vistas controladas por datos asociadas a una sección.
- **Vistas de decoración** : vistas no controladas por datos creadas por un diseño

## <a name="cells"></a>Celdas

Las celdas son objetos que representan un único elemento en el conjunto de datos que la vista de colección está presentando. Cada celda es una instancia de la `UICollectionViewCell` clase, que se compone de tres vistas diferentes, como se muestra en la ilustración siguiente:

 [![](uicollectionview-images/01-uicollectionviewcell.png "Cada celda se compone de tres vistas diferentes, como se muestra aquí.")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

La `UICollectionViewCell` clase tiene las siguientes propiedades para cada una de estas vistas:

- `ContentView`: Esta vista contiene el contenido que presenta la celda. Se representa en el orden z superior de la pantalla.
- `SelectedBackgroundView`: Las celdas tienen compatibilidad integrada para la selección. Esta vista se usa para indicar visualmente que se ha seleccionado una celda. Se representa justo debajo de `ContentView` cuando se selecciona una celda.
- `BackgroundView`: Las celdas también pueden mostrar un fondo, que presenta `BackgroundView` . Esta vista se representa debajo de `SelectedBackgroundView` .


Si se establece `ContentView` el valor de tal que es menor `BackgroundView` que `SelectedBackgroundView`y, `BackgroundView` se puede usar para enmarcar visualmente el contenido, `SelectedBackgroundView` mientras que se mostrará cuando se seleccione una celda, como se muestra a continuación:

 [![](uicollectionview-images/02-cells.png "Los diferentes elementos Cell")](uicollectionview-images/02-cells.png#lightbox)

Las celdas de la captura de pantalla anterior se crean heredando `UICollectionViewCell` de y estableciendo `ContentView`las `SelectedBackgroundView` propiedades `BackgroundView` , y, respectivamente, tal como se muestra en el código siguiente:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>Vistas complementarias

Las vistas complementarias son vistas que presentan información asociada a cada sección de `UICollectionView`un. Al igual que las celdas, las vistas complementarias están controladas por datos. Cuando las celdas presenten los datos de los elementos de un origen de datos, las vistas complementarias presentan los datos de la sección, como las categorías de libro en una estantería o el género de música en una biblioteca de música.

Por ejemplo, una vista complementaria podría usarse para presentar un encabezado para una sección determinada, tal como se muestra en la ilustración siguiente:

 [![](uicollectionview-images/02a-supplementary-view.png "Una vista complementaria que se usa para presentar un encabezado para una sección determinada, como se muestra aquí.")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Para usar una vista complementaria, primero se debe registrar en el `ViewDidLoad` método:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

A continuación, la vista debe devolverse `GetViewForSupplementaryElement`mediante, creada `DequeueReusableSupplementaryView`mediante y hereda de `UICollectionReusableView`. El siguiente fragmento de código generará el SupplementaryView que se muestra en la captura de pantalla anterior:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Las vistas complementarias son más genéricas que solo los encabezados y pies de página.
Se pueden colocar en cualquier parte de la vista de colección y pueden estar formadas por cualquier vista, por lo que su apariencia es totalmente personalizable.

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>Vistas de decoración

Las vistas de decoración son vistas meramente visuales que se pueden mostrar `UICollectionView`en un. A diferencia de las celdas y las vistas complementarias, no están controladas por datos. Siempre se crean dentro de la subclase de un diseño y, posteriormente, pueden cambiar como el diseño del contenido. Por ejemplo, una vista de decoración podría usarse para presentar una vista de fondo que se desplaza con el contenido `UICollectionView`de, como se muestra a continuación:

 [![](uicollectionview-images/02c-decoration-view.png "Vista de decoración con fondo rojo")](uicollectionview-images/02c-decoration-view.png#lightbox)

 El siguiente fragmento de código cambia el fondo a rojo en la `CircleLayout` clase Samples:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>Origen de datos

Como con otras `UITableView` partes de iOS, como y `MKMapView`, `UICollectionView` obtiene sus datos de un *origen de datos*, que se expone en Xamarin. iOS a través **`UICollectionViewDataSource`** de la clase. Esta clase es responsable de proporcionar contenido a `UICollectionView` , como:

- **Celdas** : devueltas desde `GetCell` el método.
- **Vistas complementarias** : devueltas desde `GetViewForSupplementaryElement` el método.
- **Número de secciones** : devueltas desde `NumberOfSections` el método. El valor predeterminado es 1 si no se implementa.
- **Número de elementos por sección** : devueltos desde `GetItemsCount` el método.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Para mayor comodidad, `UICollectionViewController` la clase está disponible. Esto se configura automáticamente para que sea el delegado, que se describe en la sección siguiente y el origen de datos para `UICollectionView` su vista.

Como con `UITableView`, la `UICollectionView` clase solo llamará a su origen de datos para obtener las celdas de los elementos que se encuentran en la pantalla.
Las celdas que se desplazan fuera de la pantalla se colocan en una cola para su reutilización, como se muestra en la siguiente imagen:

 [![](uicollectionview-images/03-cell-reuse.png "Las celdas que se desplazan fuera de la pantalla se colocan en una cola para su reutilización, como se muestra aquí.")](uicollectionview-images/03-cell-reuse.png#lightbox)

La reutilización de celdas `UICollectionView` se `UITableView`ha simplificado con y. Ya no es necesario crear una celda directamente en el origen de datos si no hay ninguna disponible en la cola reutilizable, ya que las celdas se registran con el sistema. Si una celda no está disponible al realizar la llamada para quitar la cola de la cola de reutilizar, iOS la creará automáticamente basándose en el tipo o NIB que se registró.
También está disponible la misma técnica para las vistas complementarias.

Por ejemplo, considere el siguiente código, que registra la `AnimalCell` clase:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Cuando necesita una celda porque su elemento está en la pantalla `UICollectionView` , llama al método de `GetCell` su origen de datos. `UICollectionView` De forma similar a como funciona con UITableView, este método es responsable de configurar una celda a partir de los datos de respaldo, `AnimalCell` que sería una clase en este caso.

En el código siguiente se muestra una `GetCell` implementación de que `AnimalCell` devuelve una instancia de:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

La llamada a `DequeReusableCell` es donde la celda se eliminará de la cola de reutilización o, si una celda no está disponible en la cola, se creará en función del tipo registrado en la llamada `CollectionView.RegisterClassForCell`a.

En este caso, al registrar la `AnimalCell` clase, iOS creará un nuevo `AnimalCell` internamente y lo devolverá cuando se realice una llamada para quitar de la cola una celda, después de la cual se configura con la imagen contenida en la clase animal y devuelta para mostrarse en el `UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>delegado

La `UICollectionView` clase utiliza un delegado de tipo `UICollectionViewDelegate` para admitir la interacción con el `UICollectionView`contenido de. Esto permite controlar:

- **Selección de celdas** : determina si una celda está seleccionada.
- Resaltado de **celdas** : determina si una celda se está tocando actualmente.
- **Menús de celda** : menú que se muestra para una celda en respuesta a un gesto largo de presionar.


Al igual que con el origen de `UICollectionViewController` datos, se configura de forma predeterminada para que sea `UICollectionView`el delegado de.

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>Resaltado de celdas

Cuando se presiona una celda, la celda pasa a un estado resaltado y no se selecciona hasta que el usuario levanta el dedo de la celda. Esto permite un cambio temporal en el aspecto de la celda antes de que se seleccione realmente. Cuando `SelectedBackgroundView` se selecciona, se muestra la celda. En la siguiente ilustración se muestra el estado resaltado justo antes de que se produzca la selección:

 [![](uicollectionview-images/04-cell-highlight.png "En esta ilustración se muestra el estado resaltado justo antes de que se produzca la selección")](uicollectionview-images/04-cell-highlight.png#lightbox)

Para implementar el resaltado `ItemHighlighted` , `ItemUnhighlighted` se `UICollectionViewDelegate` pueden usar los métodos y de. Por ejemplo, el código siguiente aplicará un fondo amarillo del `ContentView` cuando la celda esté resaltada y un fondo blanco cuando no esté resaltado, como se muestra en la imagen anterior:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>Deshabilitar selección

La selección está habilitada de `UICollectionView`forma predeterminada en. Para deshabilitar la selección `ShouldHighlightItem` , invalide y devuelva false como se muestra a continuación:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Cuando el resaltado está deshabilitado, el proceso de selección de una celda también está deshabilitado. Además, también hay un método `ShouldSelectItem` que controla la selección directamente, aunque si `ShouldHighlightItem` se implementa y devuelve false, `ShouldSelectItem` no se llama a.

 `ShouldSelectItem`permite activar o desactivar la selección para cada elemento, cuando `ShouldHighlightItem` no está implementado. También permite resaltar sin selección, si `ShouldHighlightItem` se implementa y devuelve true, mientras `ShouldSelectItem` que devuelve false.

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>Menús de celda

Cada celda de un `UICollectionView` es capaz de mostrar un menú que permita que se admita opcionalmente cortar, copiar y pegar. Para crear un menú de edición en una celda:

1. Reemplace `ShouldShowMenu` y devuelva true si el elemento debe mostrar un menú.
1. Invalide `CanPerformAction` y devuelva true para cada acción que pueda realizar el elemento, que será cualquier corte, copia o pegado.
1. Invalide `PerformAction` para realizar la operación de edición y copia de pegado.


En la captura de pantalla siguiente se muestra el menú cuando se presiona una celda larga:

 [![](uicollectionview-images/04a-menu.png "En esta captura de pantalla se muestra el menú cuando se presiona una celda larga")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>Diseño

`UICollectionView`admite un sistema de diseño que permite que el posicionamiento de todos sus elementos, celdas, vistas complementarias y vistas de decoración se administre independientemente `UICollectionView` del propio.
Con el sistema de diseño, una aplicación puede admitir diseños como el tipo de cuadrícula que se ha visto en este artículo, así como proporcionar diseños personalizados.

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>Conceptos básicos del diseño

Los diseños de `UICollectionView` se definen en una clase que hereda de `UICollectionViewLayout`. La implementación del diseño es responsable de la creación de los atributos de diseño para `UICollectionView`cada elemento de. Hay dos maneras de crear un diseño:

- Use la integrada `UICollectionViewFlowLayout` .
- Proporcione un diseño personalizado heredando de `UICollectionViewLayout` .


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>Diseño de flujo

La `UICollectionViewFlowLayout` clase proporciona un diseño basado en líneas que es adecuado para organizar el contenido en una cuadrícula de celdas como hemos visto.

Para usar un diseño de flujo:

- Cree una instancia de `UICollectionViewFlowLayout` :


```csharp
var layout = new UICollectionViewFlowLayout ();
```

- Pase la instancia al constructor de `UICollectionView` :


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

Esto es todo lo que se necesita para diseñar el contenido en una cuadrícula. Además, cuando cambia la orientación, los `UICollectionViewFlowLayout` identificadores reorganizan el contenido de forma adecuada, como se muestra a continuación:

 [![](uicollectionview-images/05-layout-orientation.png "Ejemplo de cambios de orientación")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>Margen de la sección

Para proporcionar espacio alrededor de, `UIContentView`los diseños tienen una `SectionInset` propiedad de tipo `UIEdgeInsets`. Por ejemplo, en el código siguiente se proporciona un búfer de 50 píxeles alrededor de cada `UIContentView` sección de la que dispone `UICollectionViewFlowLayout`un:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Esto da lugar a un espaciado alrededor de la sección, como se muestra a continuación:

 [![](uicollectionview-images/06-sectioninset.png "Espaciado alrededor de la sección, como se muestra aquí")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>Subclase de UICollectionViewFlowLayout

En la edición para `UICollectionViewFlowLayout` usar directamente, también se pueden crear subclases para personalizar aún más el diseño del contenido a lo largo de una línea. Por ejemplo, se puede usar para crear un diseño que no ajusta las celdas en una cuadrícula, sino que crea una sola fila con un efecto de desplazamiento horizontal, como se muestra a continuación:

 [![](uicollectionview-images/07-line-layout.png "Una sola fila con un efecto de desplazamiento horizontal")](uicollectionview-images/07-line-layout.png#lightbox)

Para implementar esto mediante subclases `UICollectionViewFlowLayout` es necesario:

- Inicializar las propiedades de diseño que se aplican al propio diseño o a todos los elementos del diseño en el constructor.
- Reemplazar `ShouldInvalidateLayoutForBoundsChange` y devolver true de modo que cuando se delimitan `UICollectionView` los cambios, se vuelva a calcular el diseño de las celdas. Esto se usa en este caso, asegúrese de que el código de transformación que se aplica a la celda centermost se aplicará durante el desplazamiento.
- Invalidar `TargetContentOffset` para que la celda centermost se ajuste al centro de la `UICollectionView` medida en que se detiene el desplazamiento.
- Reemplazar `LayoutAttributesForElementsInRect` para devolver una matriz de `UICollectionViewLayoutAttributes` . Cada `UICollectionViewLayoutAttribute` contiene información sobre cómo diseñar el elemento determinado, incluidas las propiedades como `Center` , `Size` `ZIndex` y `Transform3D` .


En el código siguiente se muestra este tipo de implementación:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>Diseño personalizado

Además de usar `UICollectionViewFlowLayout`, los diseños también se pueden personalizar completamente heredando directamente de `UICollectionViewLayout`.

Los métodos clave que se van a invalidar son:

- `PrepareLayout`: Se usa para realizar cálculos geométricos iniciales que se usarán a lo largo del proceso de diseño.
- `CollectionViewContentSize`: Devuelve el tamaño del área que se usa para mostrar el contenido.
- `LayoutAttributesForElementsInRect`: Como en el ejemplo de UICollectionViewFlowLayout mostrado anteriormente, este método se usa para proporcionar información al `UICollectionView` sobre cómo diseñar cada elemento. Sin embargo, a diferencia `UICollectionViewFlowLayout` de, al crear un diseño personalizado, puede colocar los elementos de la forma que prefiera.


Por ejemplo, el mismo contenido podría aparecer en un diseño circular, tal como se muestra a continuación:

 [![](uicollectionview-images/08-circle-layout.png "Un diseño personalizado circular tal como se muestra aquí")](uicollectionview-images/08-circle-layout.png#lightbox)

Lo más eficaz de los diseños es que para cambiar del diseño de la cuadrícula a un diseño de desplazamiento horizontal y, posteriormente, a este diseño circular, solo `UICollectionView` es necesario cambiar la clase de diseño proporcionada para. Nada en `UICollectionView`, su delegado o el código fuente de datos cambian en absoluto.


## <a name="changes-in-ios-9"></a>Cambios en iOS 9


En iOS 9, la vista de colección`UICollectionView`() ahora admite la reordenación de los elementos de forma predeterminada mediante la adición de un nuevo reconocedor de gestos predeterminado y varios métodos de compatibilidad nuevos.

Con estos nuevos métodos, puede implementar fácilmente arrastrar para reordenar en la vista de colección y tener la opción de personalizar la apariencia de los elementos durante cualquier fase del proceso de reordenación.

[![](uicollectionview-images/intro01.png "Un ejemplo del proceso de reordenación")](uicollectionview-images/intro01.png#lightbox)

En este artículo, echaremos un vistazo a la implementación de la función de arrastrar y volver a ordenar en una aplicación de Xamarin. iOS, así como algunos de los cambios que iOS 9 ha realizado en el control de vista de colección:

- [Reordenación sencilla de los elementos](#Easy-Reordering-of-Items)
    - [Ejemplo de reordenación simple](#Simple-Reordering-Example)
    - [Usar un reconocedor de gestos personalizado](#Using-a-Custom-Gesture-Recognizer)
    - [Diseños personalizados y reordenación](#Custom-Layouts-and-Reording)
- [Cambios en la vista de colección](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Reordenación de elementos

Como se indicó anteriormente, uno de los cambios más importantes en la vista de recopilación en iOS 9 fue la adición de una funcionalidad sencilla de arrastrar a reordenar de forma rápida.

En iOS 9, la manera más rápida de agregar la reordenación a una vista de colección es usar `UICollectionViewController`.
El controlador de vista de colección tiene `InstallsStandardGestureForInteractiveMovement` ahora una propiedad, que agrega un reconocedor de *gestos* estándar que admite arrastrar para reordenar los elementos de la colección.
Dado que el valor predeterminado `true`es, solo tiene que implementar el `MoveItem` método de la `UICollectionViewDataSource` clase para admitir la función de arrastrar y volver a ordenar. Por ejemplo:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Ejemplo de reordenación simple

Como ejemplo rápido, inicie un nuevo proyecto de Xamarin. iOS y edite el archivo **Main. Storyboard** . Arrastre un `UICollectionViewController` hasta la superficie de diseño:

[![](uicollectionview-images/quick01.png "Adición de un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Seleccione la vista de colección (puede que sea más fácil hacerlo desde el esquema del documento). En la pestaña diseño del Panel de propiedades, establezca los siguientes tamaños, tal como se muestra en la siguiente captura de pantalla:

- **Tamaño de celda**: Ancho: 60 | Alto: 60
- **Tamaño del encabezado**: Ancho: 0 | Alto: 0
- **Tamaño de pie de página**: Ancho: 0 | Alto: 0
- **Espaciado mínimo**: Para las celdas: 8 | Para las líneas: 8
- **Insets de sección**: Superior: 16 | Inferior: 16 | Izquierda: 16 | Derecho: 16

[![](uicollectionview-images/quick04.png "Establecer los tamaños de la vista de colección")](uicollectionview-images/quick04.png#lightbox)

A continuación, edite la celda predeterminada:
    - Cambiar el color de fondo a azul
    - Agregue una etiqueta para que actúe como título de la celda
    - Establecimiento del identificador de reutilización en la **celda**

[![](uicollectionview-images/quick02.png "Editar la celda predeterminada")](uicollectionview-images/quick02.png#lightbox)

Agregue restricciones para mantener la etiqueta centrada dentro de la celda a medida que cambia de tamaño:

En el **Panel de propiedades** de _CollectionViewCell_ y establezca la **clase** en `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Establezca la clase en TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Establezca la **vista** reutilizable de `Cell`la recopilación en:

[![](uicollectionview-images/quick06.png "Establecer la vista reutilizable de la colección en la celda")](uicollectionview-images/quick06.png#lightbox)

Por último, seleccione la etiqueta y asígnele `TextLabel`el nombre:

[![](uicollectionview-images/quick07.png "etiqueta de nombre TextLabel")](uicollectionview-images/quick07.png#lightbox)

Edite `TextCollectionViewCell` la clase y agregue las siguientes propiedades:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Aquí, `Text` la propiedad de la etiqueta se expone como el título de la celda, por lo que se puede establecer desde el código.

Agregue una nueva C# clase al proyecto y llámela `WaterfallCollectionSource`. Edite el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

Esta clase será el origen de datos para nuestra vista de colección y proporcionará la información para cada celda de la colección.
Observe que el `MoveItem` método se implementa para permitir que los elementos de la colección se arrastren de nuevo.

Agregue otra clase C# nueva al proyecto y llámela `WaterfallCollectionDelegate`. Edite este archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

Esto actuará como el delegado para nuestra vista de colección. Los métodos se han invalidado para resaltar una celda a medida que el usuario interactúa con ella en la vista de colección.

Agregue una última C# clase al proyecto y llámela `WaterfallCollectionView`. Edite este archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

Tenga en `DataSource` cuenta `Delegate` que y que hemos creado anteriormente se establecen cuando la vista de colección se construye a partir de su guion gráfico (o archivo **. Xib** ).

Edite el archivo **Main. Storyboard** de nuevo y seleccione la vista de colección y cambie a las **propiedades**. Establezca la **clase** en la clase `WaterfallCollectionView` personalizada que definimos anteriormente:

Guarde los cambios realizados en la interfaz de usuario y ejecute la aplicación.
Si el usuario selecciona un elemento de la lista y lo arrastra a una nueva ubicación, los demás elementos se animarán automáticamente cuando salgan del modo del elemento.
Cuando el usuario coloca el elemento en una nueva ubicación, se adhiere a esa ubicación. Por ejemplo:

[![](uicollectionview-images/intro01.png "Ejemplo de arrastrar un elemento a una nueva ubicación")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Usar un reconocedor de gestos personalizado

En los casos en los que no `UICollectionViewController` puede usar un y debe `UIViewController`usar un normal, o si desea tomar más control sobre el gesto de arrastrar y colocar, puede crear su propio reconocedor de gestos personalizado y agregarlo a la vista de colección cuando se cargue la vista. Por ejemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Aquí se usan varios métodos nuevos que se agregan a la vista de colección para implementar y controlar la operación de arrastre:

- `BeginInteractiveMovementForItem`: Marca el inicio de una operación de movimiento.
- `UpdateInteractiveMovementTargetPosition`-Se envía a medida que se actualiza la ubicación del elemento.
- `EndInteractiveMovement`: Marca el final de un movimiento de elemento.
- `CancelInteractiveMovement`: Marca el usuario que cancela la operación de movimiento.

Cuando se ejecuta la aplicación, la operación de arrastrar funciona exactamente igual que el reconocedor de gestos de arrastre predeterminado incluido en la vista de colección.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Diseños personalizados y reordenación

En iOS 9, se han agregado varios métodos nuevos para trabajar con los diseños de arrastrar y reordenar y personalizados en una vista de colección. Para explorar esta característica, vamos a agregar un diseño personalizado a la colección.

En primer lugar, agregue C# una nueva `WaterfallCollectionLayout` clase denominada al proyecto. Edítelo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

Esta clase se puede utilizar para proporcionar un diseño de tipo cascada personalizado de dos columnas a la vista de colección.
El código utiliza codificación de clave-valor (a `WillChangeValue` través `DidChangeValue` de los métodos y) para proporcionar el enlace de datos para nuestras propiedades calculadas en esta clase.

Después, edite `WaterfallCollectionSource` y realice los siguientes cambios y adiciones:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

Esto creará un alto aleatorio para cada uno de los elementos que se mostrarán en la lista.

A continuación, edite la `WaterfallCollectionView` clase y agregue la siguiente propiedad auxiliar:

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

Esto hará que sea más fácil obtener el origen de datos (y el alto del elemento) del diseño personalizado.

Por último, edite el controlador de vista y agregue el código siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Esto crea una instancia de nuestro diseño personalizado, establece el evento para proporcionar el tamaño de cada elemento y asocia el nuevo diseño a nuestra vista de colección.

Si ejecutamos de nuevo la aplicación de Xamarin. iOS, la vista de colección ahora tendrá el siguiente aspecto:

[![](uicollectionview-images/custom01.png "La vista de colección tendrá ahora el siguiente aspecto")](uicollectionview-images/custom01.png#lightbox)

Todavía podemos arrastrar y reordenar los elementos como antes, pero ahora los elementos cambiarán de tamaño para ajustarse a su nueva ubicación cuando se quiten.

## <a name="collection-view-changes"></a>Cambios en la vista de colección

En las secciones siguientes, se detallan los cambios realizados en cada clase en la vista de recopilación mediante iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Se han realizado los siguientes cambios o adiciones en la `UICollectionView` clase para iOS 9:

- `BeginInteractiveMovementForItem`: Marca el inicio de una operación de arrastre.
- `CancelInteractiveMovement`: Informa a la vista de colección de que el usuario ha cancelado una operación de arrastre.
- `EndInteractiveMovement`: Informa a la vista de colección de que el usuario ha finalizado una operación de arrastre.
- `GetIndexPathsForVisibleSupplementaryElements`: Devuelve el `indexPath` de un encabezado o pie de página de una sección de vista de colección.
- `GetSupplementaryView`: Devuelve el encabezado o el pie de página especificados.
- `GetVisibleSupplementaryViews`: Devuelve una lista de todos los encabezados y pies de página visibles.
- `UpdateInteractiveMovementTargetPosition`: Informa a la vista de colección que el usuario ha movido o está moviendo un elemento durante una operación de arrastre.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewController` clase en iOS 9:

- `InstallsStandardGestureForInteractiveMovement`: Si `true` se va a usar el reconocedor de gestos nuevo que admite automáticamente arrastrar a reordenar.
- `CanMoveItem`: Informa a la vista de colección si un elemento determinado se puede arrastrar por orden.
- `GetTargetContentOffset`: Se usa para obtener el desplazamiento de un elemento de vista de colección determinado.
- `GetTargetIndexPathForMove`: Obtiene el `indexPath` de un elemento determinado para una operación de arrastre.
- `MoveItem`: Mueve el orden de un elemento determinado en la lista.


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewDataSource` clase en iOS 9:

- `CanMoveItem`: Informa a la vista de colección si un elemento determinado se puede arrastrar por orden.
- `MoveItem`: Mueve el orden de un elemento determinado en la lista.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewDelegate` clase en iOS 9:

- `GetTargetContentOffset`: Se usa para obtener el desplazamiento de un elemento de vista de colección determinado.
- `GetTargetIndexPathForMove`: Obtiene el `indexPath` de un elemento determinado para una operación de arrastre.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewFlowLayout` clase en iOS 9:

- `SectionFootersPinToVisibleBounds`: Stick los pies de página a los límites visibles de la vista de colección.
- `SectionHeadersPinToVisibleBounds`: Stick los encabezados de la sección a los límites visibles de la vista de colección.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewLayout` clase en iOS 9:

- `GetInvalidationContextForEndingInteractiveMovementOfItems`: Devuelve el contexto de invalidación al final de una operación de arrastre cuando el usuario finaliza el arrastre o lo cancela.
- `GetInvalidationContextForInteractivelyMovingItems`: Devuelve el contexto de invalidación al inicio de una operación de arrastre.
- `GetLayoutAttributesForInteractivelyMovingItem`: Obtiene los atributos de diseño de un elemento determinado mientras se arrastra un elemento.
- `GetTargetIndexPathForInteractivelyMovingItem`: Devuelve el `indexPath` del elemento que se encuentra en el punto especificado al arrastrar un elemento.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewLayoutAttributes` clase en iOS 9:

- `CollisionBoundingPath`: Devuelve la ruta de acceso de colisión de dos elementos durante una operación de arrastre.
- `CollisionBoundsType`: Devuelve el tipo de colisión (como un `UIDynamicItemCollisionBoundsType`) que se ha producido durante una operación de arrastre.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewLayoutInvalidationContext` clase en iOS 9:

- `InteractiveMovementTarget`: Devuelve el elemento de destino de una operación de arrastre.
- `PreviousIndexPathsForInteractivelyMovingItems`: Devuelve el `indexPaths` de otros elementos implicados en una operación de arrastrar y reordenar.
- `TargetIndexPathsForInteractivelyMovingItems`: Devuelve el `indexPaths` de los elementos que se reordenarán como resultado de una operación de arrastrar y volver a ordenar.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Se han realizado los siguientes cambios o adiciones en la `UICollectionViewSource` clase en iOS 9:

- `CanMoveItem`: Informa a la vista de colección si un elemento determinado se puede arrastrar por orden.
- `GetTargetContentOffset`: Devuelve los desplazamientos de los elementos que se moverán a través de una operación de arrastrar y volver a ordenar.
- `GetTargetIndexPathForMove`: Devuelve el `indexPath` de un elemento que se va a desplace durante una operación de arrastrar y volver a ordenar.
- `MoveItem`: Mueve el orden de un elemento determinado en la lista.

## <a name="summary"></a>Resumen

En este artículo se han explicado los cambios en las vistas de colección en iOS 9 y se ha descrito cómo implementarlas en Xamarin. iOS.
Se ha tratado la implementación de una sencilla acción de arrastrar a reorden en una vista de colección; usar un reconocedor de gestos personalizado con arrastrar y volver a ordenar; y el modo en que la función de arrastrar a reorden afecta a un diseño de vista de colección personalizado.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Ejemplo de vista de colección](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [Eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Trabajar con tablas y celdas](~/ios/user-interface/controls/tables/index.md)
