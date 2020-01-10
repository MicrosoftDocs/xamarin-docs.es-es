---
title: Jerarquía de clases de controles de Xamarin. Forms
description: Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717315"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Jerarquía de clases de controles de Xamarin. Forms

Xamarin. Forms se compone de cientos de tipos, en varios espacios de nombres. Los desarrolladores deben estar familiarizados con la jerarquía de tipos que se usan para crear la interfaz de usuario de una aplicación de Xamarin. Forms, que residen en el espacio de nombres `Xamarin.Forms`.

Estos tipos se pueden dividir en páginas, diseños, vistas y celdas. Una página de Xamarin. Forms generalmente ocupa toda la pantalla y todos los tipos de página derivan de la clase [`Page`](xref:Xamarin.Forms.Page) . Las páginas normalmente contienen un diseño y todos los tipos de diseño derivan de la clase [`Layout`](xref:Xamarin.Forms.Layout) . Normalmente, un diseño contiene vistas y posiblemente otros diseños, y todos los tipos de vista derivan en última instancia de la clase [`View`](xref:Xamarin.Forms.View) . Por último, las celdas son controles especializados que se usan en Mostrar datos en los controles [`TableView`](xref:Xamarin.Forms.TableView) y [`ListView`](xref:Xamarin.Forms.ListView) . En última instancia, las páginas, los diseños, las vistas y las celdas se derivan de la clase [`Element`](xref:Xamarin.Forms.Element) .

En el diagrama de clases siguiente se muestra la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en Xamarin. Forms:

[![Diagrama de clase de controles de Xamarin. Forms](class-hierarchy-images/class-diagram.png "Diagrama de clase de controles de Xamarin. Forms")](class-hierarchy-images/class-diagram-large.png#lightbox "Diagrama de clase de controles de Xamarin. Forms")

> [!NOTE]
> [Aquí](class-hierarchy-images/class-diagram-high-resolution.png)se puede descargar una versión de alta resolución del diagrama de clases. Sin embargo, tenga en cuenta que el diagrama solo muestra un tipo de Shell.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de controles de Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
