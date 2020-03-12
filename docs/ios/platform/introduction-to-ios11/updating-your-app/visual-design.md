---
title: Actualizaciones del diseño visual en iOS 11
description: En este documento se describen las actualizaciones de diseño visual que se introdujeron en iOS 11. Se describen los cambios en las barras de navegación, los controladores de búsqueda, los márgenes, el contenido de pantalla completa y las vistas de tabla.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: e5a61af4cd8a09df3ffddb74658f646aa8edfa1f
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088974"
---
# <a name="visual-design-updates-in-ios-11"></a>Actualizaciones del diseño visual en iOS 11

En iOS 11, Apple presentó nuevos cambios visuales, como las actualizaciones de la barra de navegación, la barra de búsqueda y las vistas de tabla. Además, se han realizado mejoras para permitir una mayor flexibilidad sobre los márgenes y el contenido de pantalla completa. Estos cambios se describen en esta guía. 

Para obtener información específica sobre cómo diseñar para iPhone X, vea el vídeo sobre el diseño de Apple [para iPhone x](https://developer.apple.com/videos/play/fall2017/801/) .

## <a name="uikit"></a>UIKit

Las barras de UIKit se han adaptado en iOS 11 para que sea más accesible para los usuarios finales.

Uno de estos cambios es una nueva pantalla de HUD que aparece cuando un usuario presiona larga en un elemento de barra. Para habilitarlo, establezca la propiedad `largeContentSizeImage` en `UIBarItem` y agregue una imagen más grande a través de un [Catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegación
iOS 11 presentó una nueva funcionalidad para facilitar la lectura de los títulos de las barras de navegación. Las aplicaciones pueden mostrar este título más grande asignando la propiedad `PrefersLargeTitles` a true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Establecer títulos mayores en la aplicación hace que _todos los_ títulos de las barras de navegación de la aplicación parezcan mayores, como se muestra en la siguiente captura de pantalla:

![Título de navegación grande](visual-design-images/image7.png)

Para controlar el momento en que se muestra un título grande en una barra de navegación, establezca el `LargeTitleDisplayMode` del elemento de navegación en `Always`, `Never`o `Automatic`.

### <a name="search-controller"></a>Controlador de búsqueda

iOS 11 ha facilitado la adición de un controlador de búsqueda directamente a la barra de navegación. Una vez que haya creado un controlador de búsqueda, agréguelo a la barra de navegación con la propiedad `SearchController`:

```csharp
NavigationItem.SearchController = searchController;
```

[![título de navegación grande con barra de búsqueda](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

En función de la funcionalidad de la aplicación, puede que desee o no que la barra de búsqueda se oculte cuando un usuario se desplace a través de una lista. Puede ajustarlo mediante la propiedad `HidesSearchBarWhenScrolling`.

## <a name="margins"></a>Márgenes

Apple ha creado una nueva propiedad, `directionalLayoutMargins`, que se puede usar para establecer el espacio entre las vistas y las subvistas. Use `directionalLayoutMargins` con insets de `leading` o `trailing`. Independientemente de si el sistema es un idioma de izquierda a derecha o de derecha a izquierda, iOS establece el espaciado en la aplicación de forma adecuada.

En iOS 10 y anteriores, todas las vistas tenían un tamaño de margen mínimo al que se alinearían. iOS 11 presentó la opción de invalidar ese uso de `ViewRespectsSystemMinimumLayoutMargins`. Por ejemplo, si se establece esta propiedad en false, se pueden ajustar los indefinidos perimetrales a cero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![Imagen que muestra el margen con cero en iOS 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 [presentó](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` y `bottomLayoutGuide` como una manera de restringir las vistas para que no estén ocultas por barras de UIKit y estén en un área visible de la pantalla. Están en desuso en iOS 11 en favor del _área segura_.

El área segura es una nueva manera de pensar en el espacio visible de la aplicación y en cómo se agregan las restricciones entre una vista y una vista superior. Por ejemplo, considere la siguiente imagen:

[Guía de ![de área segura frente a la guía de diseño superior e inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Anteriormente, si hubiera agregado una vista y quería que estuviera visible en el área verde anterior, la limitaría a la _parte inferior_ del `TopLayoutGuide` y la _parte superior_ del `BottomLayoutGuide`. En iOS 11, en su lugar se debe restringir a la _parte superior_ e _inferior_ del área segura. A continuación, encontrará un ejemplo:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Vista de tabla

UITableView ha tenido varios cambios pequeños, pero significativos, en iOS 11.

De forma predeterminada, los encabezados, pies de página y celdas ahora tienen el tamaño automáticamente en función de su contenido. Para no participar en este comportamiento de ajuste de tamaño automático, establezca el `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`o `EstimatedSectionFooterHeight` en cero.

Sin embargo, en algunas circunstancias (por ejemplo, al agregar UITableViewController en el diseñador de iOS o al usar Storboards existentes en Interface Builder), es posible que tenga que habilitar manualmente las celdas autoajustables. Para ello, asegúrese de que ha establecido las propiedades siguientes en la vista de tabla para las celdas, encabezados y pies de página, respectivamente:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 ha ampliado la funcionalidad de las acciones de fila. `UISwipeActionsConfiguration` se presentó para definir un conjunto de acciones que deben realizarse cuando el usuario se desliza rápidamente en cualquier dirección de una fila de una vista de tabla. Este comportamiento es similar al de Native mail. app. Para obtener más información, consulte la guía de [acciones de fila](~/ios/user-interface/controls/tables/row-action.md) .

Las vistas de tabla tienen compatibilidad para arrastrar y colocar en iOS 11. Para obtener más información, vea la guía de [arrastrar y colocar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) .

## <a name="related-links"></a>Vínculos relacionados

- [Novedades de iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto de App Store actualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Diseño para iPhone X (Apple) (vídeo)](https://developer.apple.com/videos/play/fall2017/801/)
- [Actualización de la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
