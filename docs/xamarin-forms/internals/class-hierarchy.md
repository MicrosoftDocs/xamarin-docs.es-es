---
title: Xamarin.Forms Jerarquía de clases de controles
description: Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed3fc6d7aab48886f0c6166390b0ff224f66da60
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115209"
---
# <a name="no-locxamarinforms-controls-class-hierarchy"></a>Xamarin.Forms Jerarquía de clases de controles

Xamarin.Forms se compone de cientos de tipos, en varios espacios de nombres. Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una Xamarin.Forms aplicación, que residen en el `Xamarin.Forms` espacio de nombres.

Estos tipos se pueden dividir en páginas, diseños, vistas y celdas. Una Xamarin.Forms Página suele ocupar toda la pantalla y todos los tipos de página derivan de la [`Page`](xref:Xamarin.Forms.Page) clase. Las páginas normalmente contienen un diseño y todos los tipos de diseño derivan de la [`Layout`](xref:Xamarin.Forms.Layout) clase. Normalmente, un diseño contiene vistas y posiblemente otros diseños, y todos los tipos de vista se derivan en última instancia de la [`View`](xref:Xamarin.Forms.View) clase. Por último, las celdas son controles especializados que se usan en Mostrar datos en los [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) controles y. En última instancia, las páginas, los diseños, las vistas y las celdas se derivan de la [`Element`](xref:Xamarin.Forms.Element) clase.

En el diagrama de clases siguiente se muestra la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en Xamarin.Forms :

[![::: no-LOC (Xamarin. Forms)::: Controls diagrama de clases](class-hierarchy-images/class-diagram.png "::: no-LOC (Xamarin. Forms)::: Controls diagrama de clases")](class-hierarchy-images/class-diagram-large.png#lightbox "::: no-LOC (Xamarin. Forms)::: Controls diagrama de clases")

Sin embargo, tenga en cuenta que el diagrama solo muestra un tipo de Shell.

> [!NOTE]
> [Aquí](class-hierarchy-images/class-diagram-high-resolution.png)se puede descargar una versión de alta resolución del diagrama de clases.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms Referencia de controles](~/xamarin-forms/user-interface/controls/index.md)
