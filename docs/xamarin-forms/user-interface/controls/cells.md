---
title: :::no-loc(Xamarin.Forms):::Celdas
description: ':::no-loc(Xamarin.Forms):::las celdas se pueden agregar a los controles ListView y TableViews. En este artículo se enumeran las celdas incluidas en :::no-loc(Xamarin.Forms)::: .'
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 21fca31ca9e1cf39843e04c3c381bb41ef77335f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997285"
---
# <a name="no-locxamarinforms-cells"></a>:::no-loc(Xamarin.Forms):::Celdas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_:::no-loc(Xamarin.Forms):::las celdas se pueden agregar a los controles ListView y TableViews._

Una *celda* es un elemento especializado que se usa para los elementos de una tabla y describe cómo se debe representar cada elemento de una lista. La [`Cell`](xref::::no-loc(Xamarin.Forms):::.Cell) clase se deriva de [`Element`](xref::::no-loc(Xamarin.Forms):::.Element) , de la que [`VisualElement`](xref::::no-loc(Xamarin.Forms):::.Element) también deriva. Una celda no es en sí misma un elemento visual; en su lugar, se trata de una plantilla para crear un elemento visual.

`Cell`se utiliza exclusivamente con [`ListView`](xref::::no-loc(Xamarin.Forms):::.ListView) [`TableView`](xref::::no-loc(Xamarin.Forms):::.TableView) controles y. Para obtener información sobre cómo usar y personalizar las celdas, consulte [`ListView`](~/xamarin-forms/user-interface/listview/index.md) la [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentación de y.

## <a name="cells"></a>Celdas

:::no-loc(Xamarin.Forms):::admite los siguientes tipos de celda:

| Tipo | Descripción | Aspecto |
| --- | --- | --- |
| `TextCell` | [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell)Muestra una o dos cadenas de texto. Establezca la [`Text`](xref::::no-loc(Xamarin.Forms):::.TextCell.Text) propiedad y, opcionalmente, la [`Detail`](xref::::no-loc(Xamarin.Forms):::.TextCell.Detail) propiedad en estas cadenas de texto.<br /><br />[Documentación](xref::::no-loc(Xamarin.Forms):::.TextCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) de | [![Ejemplo de TextCell](cells-images/TextCell.png "Ejemplo de TextCell")](cells-images/TextCell-Large.png#lightbox "Ejemplo de TextCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | [`ImageCell`](xref::::no-loc(Xamarin.Forms):::.ImageCell)Muestra la misma información que, [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) pero incluye un mapa de bits que se establece con la [`Source`](xref::::no-loc(Xamarin.Forms):::.Image.Source) propiedad.<br /><br />[Documentación](xref::::no-loc(Xamarin.Forms):::.ImageCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) de | [![Ejemplo de ImageCell](cells-images/ImageCell.png "Ejemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Ejemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | El [`SwitchCell`](xref::::no-loc(Xamarin.Forms):::.SwitchCell) contiene el conjunto de texto con la [`Text`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.Text) propiedad y un modificador ON/OFF inicialmente establecido con la [`On`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.On) propiedad booleana. Controle el [`OnChanged`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.OnChanged) evento que se va a notificar cuando `On` cambie la propiedad.<br /><br />[Documentación](xref::::no-loc(Xamarin.Forms):::.SwitchCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#switchcell) de | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "Ejemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Ejemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | [`EntryCell`](xref::::no-loc(Xamarin.Forms):::.EntryCell)Define una [`Label`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Label) propiedad que identifica la celda y una sola línea de texto modificable en la [`Text`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Text) propiedad. Controle el [`Completed`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Completed) evento que se va a notificar cuando el usuario haya completado la entrada de texto.<br /><br />[Documentación](xref::::no-loc(Xamarin.Forms):::.EntryCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#entrycell) de | [![Ejemplo de EntryCell](cells-images/EntryCell.png "Ejemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Ejemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Vínculos relacionados

- [:::no-loc(Xamarin.Forms):::Ejemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de :::no-loc(Xamarin.Forms):::](https://docs.microsoft.com/samples/browse/?products=xamarin&term=:::no-loc(Xamarin.Forms):::)
- [:::no-loc(Xamarin.Forms):::Documentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
