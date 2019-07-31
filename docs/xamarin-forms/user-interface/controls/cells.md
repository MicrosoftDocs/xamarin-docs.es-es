---
title: Celdas de Xamarin.Forms
description: Las celdas de Xamarin.Forms pueden agregarse a ListView y TableViews. En este artículo se enumera las celdas incluidas en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 1e003a80b58f783829f5af3b74801fc3c91c88e9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655617"
---
# <a name="xamarinforms-cells"></a>Celdas de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Las celdas de Xamarin.Forms pueden agregarse a ListView y TableViews._

Un *celda* es un elemento especializado utilizado para los elementos de una tabla y se describe cómo se debe representar cada elemento en una lista. El [ `Cell` ](xref:Xamarin.Forms.Cell) clase se deriva de [ `Element` ](xref:Xamarin.Forms.Element), desde el que [ `VisualElement` ](xref:Xamarin.Forms.Element) también se deriva. Una celda no es un elemento visual; en realidad es una plantilla para crear un elemento visual.

`Cell` se utiliza exclusivamente con [ `ListView` ](views.md#listView) y [ `TableView` ](views.md#tableView) controles. Para obtener información sobre cómo usar y personalizar celdas, consulte el [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) y [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentación.

## <a name="cells"></a>Celdas

Xamarin.Forms es compatible con los siguientes tipos de celda:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Un [ `TextCell` ](xref:Xamarin.Forms.TextCell) muestra uno o dos cadenas de texto. Establecer el [ `Text` ](xref:Xamarin.Forms.TextCell.Text) propiedad y, opcionalmente, el [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propiedad a estas cadenas de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.TextCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Ejemplo de TextCell](cells-images/TextCell.png "TextCell ejemplo")](cells-images/TextCell-Large.png#lightbox "TextCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| El [ `ImageCell` ](xref:Xamarin.Forms.ImageCell) muestra la misma información que [ `TextCell` ](#textCell) pero incluye un mapa de bits que establece con el [ `Source` ](xref:Xamarin.Forms.Image.Source) propiedad.<br /><br />[Documentación de API](xref:Xamarin.Forms.ImageCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Ejemplo de ImageCell](cells-images/ImageCell.png "ejemplo ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| El [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) contiene el conjunto de texto [`Text`](xref:Xamarin.Forms.SwitchCell.Text) con la propiedad y un modificador ON/OFF inicialmente establecido [`On`](xref:Xamarin.Forms.SwitchCell.On) con la propiedad booleana. Controlar la [ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged) eventos para recibir una notificación cuando la `On` los cambios de propiedad.<br /><br />[Documentación de API](xref:Xamarin.Forms.SwitchCell) / [guía](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "SwitchCell ejemplo")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| El [ `EntryCell` ](xref:Xamarin.Forms.EntryCell) define un [ `Label` ](xref:Xamarin.Forms.EntryCell.Label) propiedad que identifica la celda y una sola línea de texto editable en el [ `Text` ](xref:Xamarin.Forms.EntryCell.Text) propiedad. Controlar la [ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed) eventos para recibir una notificación cuando el usuario ha completado la entrada de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.EntryCell) / [guía](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Ejemplo de EntryCell](cells-images/EntryCell.png "EntryCell ejemplo")](cells-images/EntryCell-Large.png#lightbox "EntryCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
