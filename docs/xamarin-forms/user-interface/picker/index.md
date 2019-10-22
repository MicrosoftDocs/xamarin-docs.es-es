---
title: Selector de Xamarin. Forms
description: El selector de Xamarin. Forms muestra una lista breve de elementos, desde la que el usuario puede seleccionar un elemento. En este artículo se explica cómo usar la clase selector para seleccionar un elemento de texto de una lista de datos.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696001"
---
# <a name="xamarinforms-picker"></a>Selector de Xamarin. Forms

_La vista selector es un control para seleccionar un elemento de texto de una lista de datos._

El [`Picker`](xref:Xamarin.Forms.Picker) de Xamarin. Forms muestra una lista breve de elementos, desde la que el usuario puede seleccionar un elemento. `Picker` define las siguientes propiedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title) de tipo `string`, cuyo valor predeterminado es `null`.
- `TitleColor` de tipo [`Color`](xref:Xamarin.Forms.Color), el color usado para mostrar el texto de `Title`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) de tipo `IList`, la lista de origen de los elementos que se van a mostrar, cuyo valor predeterminado es `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de tipo `int`, el índice del elemento seleccionado, cuyo valor predeterminado es-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) de tipo `object`, el elemento seleccionado, cuyo valor predeterminado es `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) de tipo [`Color`](xref:Xamarin.Forms.Color), el color usado para mostrar el texto, cuyo valor predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), cuyo valor predeterminado es [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) de tipo `string`, cuyo valor predeterminado es `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) de tipo `double`, cuyo valor predeterminado es-1,0.
- `CharacterSpacing`, de tipo `double`, es el espaciado entre los caracteres del elemento mostrado por el `Picker`.

Todas las propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que se pueden aplicar estilos y las propiedades pueden ser destinos de los enlaces de datos. Las propiedades [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) y [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) tienen un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que pueden ser destinos de enlaces de datos en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Para obtener información sobre cómo establecer las propiedades de la fuente, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

Un [`Picker`](xref:Xamarin.Forms.Picker) no muestra ningún dato cuando se muestra por primera vez. En su lugar, el valor de su propiedad [`Title`](xref:Xamarin.Forms.Picker.Title) se muestra como un marcador de posición en las plataformas iOS y Android:

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Cuando el [`Picker`](xref:Xamarin.Forms.Picker) obtiene el foco, se muestran sus datos y el usuario puede seleccionar un elemento:

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

El [`Picker`](xref:Xamarin.Forms.Picker) activa un evento de [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) cuando el usuario selecciona un elemento. La selección siguiente muestra el elemento seleccionado en el `Picker`:

![](images/picker-after-selection.png "Picker after Selection")

Hay dos técnicas para rellenar un [`Picker`](xref:Xamarin.Forms.Picker) con datos:

- Establecer la propiedad [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) en los datos que se van a mostrar. Esta es la técnica recomendada. Para obtener más información, consulte [configuración de la propiedad ItemsSource de un selector](populating-itemssource.md).
- Agregar los datos que se van a mostrar en la colección de [`Items`](xref:Xamarin.Forms.Picker.Items) . Esta técnica era el proceso original para rellenar un [`Picker`](xref:Xamarin.Forms.Picker) con datos. Para obtener más información, vea [Agregar datos a la colección de elementos de un selector](populating-items.md).

## <a name="related-links"></a>Vínculos relacionados

- [Selector](xref:Xamarin.Forms.Picker)
