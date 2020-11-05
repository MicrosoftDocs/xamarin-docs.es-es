---
title: Xamarin.Forms Celdas
description: Xamarin.Forms las celdas se pueden agregar a los controles ListView y TableViews. En este artículo se enumeran las celdas incluidas en Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01f625d9ecfb91bc36013b7f6d45fb3d275e8bee
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370837"
---
# <a name="no-locxamarinforms-cells"></a>Xamarin.Forms Celdas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms las celdas se pueden agregar a los controles ListView y TableViews._

Una *celda* es un elemento especializado que se usa para los elementos de una tabla y describe cómo se debe representar cada elemento de una lista. La [`Cell`](xref:Xamarin.Forms.Cell) clase se deriva de [`Element`](xref:Xamarin.Forms.Element) , de la que [`VisualElement`](xref:Xamarin.Forms.Element) también deriva. Una celda no es en sí misma un elemento visual; en su lugar, se trata de una plantilla para crear un elemento visual.

`Cell` se utiliza exclusivamente con [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView) controles y. Para obtener información sobre cómo usar y personalizar las celdas, consulte [`ListView`](~/xamarin-forms/user-interface/listview/index.md) la [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentación de y.

## <a name="cells"></a>Celdas

Xamarin.Forms admite los siguientes tipos de celda:

| Tipo | Descripción | Aspecto |
| --- | --- | --- |
| `TextCell` | [`TextCell`](xref:Xamarin.Forms.TextCell)Muestra una o dos cadenas de texto. Establezca la [`Text`](xref:Xamarin.Forms.TextCell.Text) propiedad y, opcionalmente, la [`Detail`](xref:Xamarin.Forms.TextCell.Detail) propiedad en estas cadenas de texto.<br /><br />[Documentación](xref:Xamarin.Forms.TextCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) de | [![Ejemplo de TextCell](cells-images/TextCell.png "Ejemplo de TextCell")](cells-images/TextCell-Large.png#lightbox "Ejemplo de TextCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | [`ImageCell`](xref:Xamarin.Forms.ImageCell)Muestra la misma información que, [`TextCell`](xref:Xamarin.Forms.TextCell) pero incluye un mapa de bits que se establece con la [`Source`](xref:Xamarin.Forms.Image.Source) propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.ImageCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) de | [![Ejemplo de ImageCell](cells-images/ImageCell.png "Ejemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Ejemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | El [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) contiene el conjunto de texto con la [`Text`](xref:Xamarin.Forms.SwitchCell.Text) propiedad y un modificador ON/OFF inicialmente establecido con la [`On`](xref:Xamarin.Forms.SwitchCell.On) propiedad booleana. Controle el [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) evento que se va a notificar cuando `On` cambie la propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.SwitchCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#switchcell) de | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "Ejemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Ejemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref:Xamarin.Forms.EntryCell)Define una [`Label`](xref:Xamarin.Forms.EntryCell.Label) propiedad que identifica la celda y una sola línea de texto modificable en la [`Text`](xref:Xamarin.Forms.EntryCell.Text) propiedad. Controle el [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) evento que se va a notificar cuando el usuario haya completado la entrada de texto.<br /><br />[Documentación](xref:Xamarin.Forms.EntryCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#entrycell) de | [![Ejemplo de EntryCell](cells-images/EntryCell.png "Ejemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Ejemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms Ejemplo de FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de Xamarin.Forms](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Documentación de API](/dotnet/api/xamarin.forms?view=xamarin-forms)