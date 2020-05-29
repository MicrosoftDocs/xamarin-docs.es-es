---
title: Xamarin.FormsCeldas
description: Xamarin.Formslas celdas se pueden agregar a los controles ListView y TableViews. En este artículo se enumeran las celdas incluidas en Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd1a2398787fe39c0b4cbd08ccd5c5793775d5cf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137285"
---
# <a name="xamarinforms-cells"></a>Xamarin.FormsCeldas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Las celdas de Xamarin. Forms se pueden agregar a los controles ListView y TableViews._

Una *celda* es un elemento especializado que se usa para los elementos de una tabla y describe cómo se debe representar cada elemento de una lista. La [`Cell`](xref:Xamarin.Forms.Cell) clase se deriva de [`Element`](xref:Xamarin.Forms.Element) , de la que [`VisualElement`](xref:Xamarin.Forms.Element) también deriva. Una celda no es en sí misma un elemento visual; en su lugar, se trata de una plantilla para crear un elemento visual.

`Cell`se utiliza exclusivamente con [`ListView`](views.md#listview) [`TableView`](views.md#tableview) controles y. Para obtener información sobre cómo usar y personalizar las celdas, consulte [`ListView`](~/xamarin-forms/user-interface/listview/index.md) la [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentación de y.

## <a name="cells"></a>Celdas

Xamarin.Formsadmite los siguientes tipos de celda:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| [`TextCell`](xref:Xamarin.Forms.TextCell)Muestra una o dos cadenas de texto. Establezca la [`Text`](xref:Xamarin.Forms.TextCell.Text) propiedad y, opcionalmente, la [`Detail`](xref:Xamarin.Forms.TextCell.Detail) propiedad en estas cadenas de texto.<br /><br />[Documentación](xref:Xamarin.Forms.TextCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) de | [![Ejemplo de TextCell](cells-images/TextCell.png "Ejemplo de TextCell")](cells-images/TextCell-Large.png#lightbox "Ejemplo de TextCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [`ImageCell`](xref:Xamarin.Forms.ImageCell)Muestra la misma información que, [`TextCell`](#textCell) pero incluye un mapa de bits que se establece con la [`Source`](xref:Xamarin.Forms.Image.Source) propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.ImageCell)  /  de API [Guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) de | [![Ejemplo de ImageCell](cells-images/ImageCell.png "Ejemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Ejemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| El [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) contiene el conjunto de texto con la [`Text`](xref:Xamarin.Forms.SwitchCell.Text) propiedad y un modificador ON/OFF inicialmente establecido con la [`On`](xref:Xamarin.Forms.SwitchCell.On) propiedad booleana. Controle el [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) evento que se va a notificar cuando `On` cambie la propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.SwitchCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#switchcell) de | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "Ejemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Ejemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [`EntryCell`](xref:Xamarin.Forms.EntryCell)Define una [`Label`](xref:Xamarin.Forms.EntryCell.Label) propiedad que identifica la celda y una sola línea de texto modificable en la [`Text`](xref:Xamarin.Forms.EntryCell.Text) propiedad. Controle el [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) evento que se va a notificar cuando el usuario haya completado la entrada de texto.<br /><br />[Documentación](xref:Xamarin.Forms.EntryCell)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md#entrycell) de | [![Ejemplo de EntryCell](cells-images/EntryCell.png "Ejemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Ejemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsEjemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAssembl](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
