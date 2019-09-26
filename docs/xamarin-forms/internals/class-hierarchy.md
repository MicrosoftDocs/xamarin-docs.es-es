---
title: Jerarquía de clases de controles de Xamarin. Forms
description: Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2019
ms.openlocfilehash: f08146d4439ff1fc22edea71ab1cbb337f64c037
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68984397"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Jerarquía de clases de controles de Xamarin. Forms

Xamarin. Forms se compone de cientos de tipos, en varios espacios de nombres. Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una aplicación de Xamarin. Forms `Xamarin.Forms` , que reside en el espacio de nombres.

Estos tipos se pueden dividir en páginas, diseños, vistas y celdas. Una página de Xamarin. Forms generalmente ocupa toda la pantalla y todos los tipos de página derivan de la [`Page`](xref:Xamarin.Forms.Page) clase. Las páginas normalmente contienen un diseño y todos los tipos de diseño derivan [`Layout`](xref:Xamarin.Forms.Layout) de la clase. Normalmente, un diseño contiene vistas y posiblemente otros diseños, y todos los tipos de vista derivan [`View`](xref:Xamarin.Forms.View) de la clase. Por último, las celdas son controles especializados que se usan en Mostrar datos [`TableView`](xref:Xamarin.Forms.TableView) en [`ListView`](xref:Xamarin.Forms.ListView) los controles y. En última instancia, las páginas, los diseños, las vistas y las celdas se [`Element`](xref:Xamarin.Forms.Element) derivan de la clase.

En el diagrama de clases siguiente se muestra la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en Xamarin. Forms:

[![Diagrama de clase de controles de Xamarin. Forms](class-hierarchy-images/class-diagram.png "Diagrama de clase de controles de Xamarin. Forms")](class-hierarchy-images/class-diagram-large.png#lightbox "Diagrama de clase de controles de Xamarin. Forms")

> [!NOTE]
> [Aquí](class-hierarchy-images/class-diagram-high-resolution.png)se puede descargar una versión de alta resolución del diagrama de clases. Sin embargo, tenga en cuenta que el diagrama no muestra `CarouselView` actualmente `CollectionView` los tipos y. Estos se agregarán una vez que los controles no estén en versión preliminar. Además, el diagrama solo muestra un tipo de Shell.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de controles de Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
