---
title: Xamarin.FormsJerarquía de clases de controles
description: Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0087e2bb81c7c9204a782519a9eeb9891adc297a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138649"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.FormsJerarquía de clases de controles

Xamarin.Formsse compone de cientos de tipos, en varios espacios de nombres. Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una Xamarin.Forms aplicación, que residen en el `Xamarin.Forms` espacio de nombres.

Estos tipos se pueden dividir en páginas, diseños, vistas y celdas. Una Xamarin.Forms Página suele ocupar toda la pantalla y todos los tipos de página derivan de la [`Page`](xref:Xamarin.Forms.Page) clase. Las páginas normalmente contienen un diseño y todos los tipos de diseño derivan de la [`Layout`](xref:Xamarin.Forms.Layout) clase. Normalmente, un diseño contiene vistas y posiblemente otros diseños, y todos los tipos de vista se derivan en última instancia de la [`View`](xref:Xamarin.Forms.View) clase. Por último, las celdas son controles especializados que se usan en Mostrar datos en los [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) controles y. En última instancia, las páginas, los diseños, las vistas y las celdas se derivan de la [`Element`](xref:Xamarin.Forms.Element) clase.

En el diagrama de clases siguiente se muestra la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en Xamarin.Forms :

[![Xamarin.FormsDiagrama de clases de controles](class-hierarchy-images/class-diagram.png "[! Operador. Diagrama de clases de controles NO-LOC (Xamarin. Forms)]")](class-hierarchy-images/class-diagram-large.png#lightbox "[! Operador. Diagrama de clases de controles NO-LOC (Xamarin. Forms)]")

> [!NOTE]
> [Aquí](class-hierarchy-images/class-diagram-high-resolution.png)se puede descargar una versión de alta resolución del diagrama de clases. Sin embargo, tenga en cuenta que el diagrama solo muestra un tipo de Shell.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsReferencia de controles](~/xamarin-forms/user-interface/controls/index.md)
