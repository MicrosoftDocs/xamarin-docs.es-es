---
title: Resumen del capítulo 19. Vistas de colección
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 19. Vistas de colección'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032867"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumen del capítulo 19. Vistas de colección

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin. Forms ha diverge del material presentado en el libro.

Xamarin. Forms define tres vistas que mantienen colecciones y muestran sus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker) es una lista relativamente breve de elementos de cadena que permite al usuario elegir uno
- [`ListView`](xref:Xamarin.Forms.ListView) suele ser una larga lista de elementos normalmente del mismo tipo y formato, lo que también permite al usuario elegir uno
- [`TableView`](xref:Xamarin.Forms.TableView) es una colección de *celdas* (normalmente de varios tipos y apariencias) para Mostrar datos o administrar la entrada del usuario

Es común que las aplicaciones MVVM usen el `ListView` para mostrar una colección seleccionable de objetos.

## <a name="program-options-with-picker"></a>Opciones de programa con selector

El [`Picker`](xref:Xamarin.Forms.Picker) es una buena opción si necesita permitir que el usuario elija una opción entre una lista relativamente breve de elementos de `string`.

### <a name="the-picker-and-event-handling"></a>Selector y control de eventos

En el ejemplo [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) se muestra cómo usar XAML para establecer el `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) propiedad y agregar elementos de `string` a la colección de [`Items`](xref:Xamarin.Forms.Picker.Items) . Cuando el usuario selecciona el `Picker`, muestra los elementos de la colección `Items` de una manera dependiente de la plataforma.

El evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indica si el usuario ha seleccionado un elemento. La propiedad de [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de base cero indica el elemento seleccionado. Si no hay ningún elemento seleccionado, `SelectedIndex` es igual a &ndash;1.

También puede usar `SelectedIndex` para inicializar el elemento seleccionado, pero debe establecerse después de que se rellene la colección de `Items`. En XAML, esto significa que probablemente utilizará un elemento de propiedad para establecer `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Enlazar datos al selector

La propiedad `SelectedIndex` está respaldada por una propiedad enlazable, pero `Items` no lo es, por lo que es difícil usar el enlace de datos con un `Picker`. Una solución es usar el `Picker` en combinación con un [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como el de la biblioteca de [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . El [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) muestra cómo funciona esto.

> [!NOTE] 
> El `Picker` de Xamarin. Forms ahora incluye propiedades `ItemsSource` y `SelectedItem` que admiten el enlace de datos. Vea [selector](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Representar datos con ListView

La [`ListView`](xref:Xamarin.Forms.ListView) es la única clase que se deriva de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) desde la que hereda las propiedades [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) y [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) .

`ItemsSource` es de tipo `IEnumerable` pero se `null` de forma predeterminada y se debe inicializar explícitamente o (más comúnmente) establecer en una colección a través de un enlace de datos. Los elementos de esta colección pueden ser de cualquier tipo.

`ListView` define una propiedad [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) que se establece en uno de los elementos de la colección `ItemsSource` o `null` si no se selecciona ningún elemento. `ListView` activa el evento de [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) cuando se selecciona un nuevo elemento.

### <a name="collections-and-selections"></a>Recopilaciones y selecciones

El ejemplo [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) rellena un `ListView` con 17 `Color` valores en una colección de `List<Color>`. Los elementos se pueden seleccionar, pero de forma predeterminada se muestran con sus representaciones `ToString` no atractivas. Varios ejemplos de este capítulo muestran cómo corregir esa presentación y hacerla tan atractiva como se desee.

### <a name="the-row-separator"></a>El separador de filas

En las pantallas iOS y Android, una línea fina separa las filas. Puede controlar esto con las propiedades [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) y [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) . `SeparatorVisibility` propiedad es de tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility), una enumeración con dos miembros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), el valor predeterminado
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Enlazar datos al elemento seleccionado

La propiedad `SelectedItem` está respaldada por una propiedad enlazable, por lo que puede ser el origen o el destino de un enlace de datos. Su `BindingMode` predeterminada es `OneWayToSource`, pero generalmente es el destino de un enlace de datos bidireccional, especialmente en escenarios de MVVM. En el ejemplo [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) se muestra este tipo de enlace.

### <a name="the-observablecollection-difference"></a>La diferencia de ObservableCollection

En el ejemplo [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) se establece la propiedad `ItemsSource` de una `ListView` en una colección `List<DateTime>` y, a continuación, se agrega progresivamente un nuevo objeto `DateTime` a la colección cada segundo mediante un temporizador.

Sin embargo, el `ListView` no se actualiza automáticamente porque la colección de `List<T>` no tiene un mecanismo de notificación para indicar cuándo se agregan o quitan elementos de la colección.

Una clase mucho mejor de usar en estos escenarios es [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) definida en el espacio de nombres `System.Collections.ObjectModel`. Esta clase implementa la interfaz [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) y, por consiguiente, desencadena un evento [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) cuando se agregan o quitan elementos de la colección, o cuando se reemplazan o se mueven dentro de la colección. Cuando el `ListView` detecta internamente que una clase que implementa `INotifyCollectionChanged` se ha establecido en su propiedad `ItemsSource`, adjunta un controlador al evento `CollectionChanged` y actualiza su presentación cuando cambia la colección.

En el ejemplo [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) se muestra el uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Plantillas y celdas

De forma predeterminada, un `ListView` muestra los elementos de su colección utilizando el método `ToString` de cada elemento. Un enfoque mejor implica definir una plantilla para mostrar los elementos.

Para experimentar con esta característica, puede usar la clase [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) en la biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Esta clase define una propiedad `All` estática de tipo `IList<NamedColor>` que contiene 141 objetos `NamedColor` correspondientes a los campos públicos de la estructura `Color`.

En el ejemplo [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) se establece el `ItemsSource` de un `ListView` en esta propiedad `NamedColor.All`, pero solo se muestran los nombres de clase completos de los objetos `NamedColor`.

`ListView` necesita una plantilla para mostrar estos elementos. En el código, puede establecer la propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) definida por `ItemsView<TVisual>` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) mediante el [constructor`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que hace referencia a un derivado de la clase [`Cell`](xref:Xamarin.Forms.Cell) . `Cell` tiene cinco derivados:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contiene dos vistas de `Label` (conceptualmente hablando)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; agrega una vista `Image` a `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contiene una vista de `Entry` con un `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contiene un `Switch` con un `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; puede ser cualquier `View` (probablemente con elementos secundarios)

A continuación, llame a [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) y [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) en el objeto `DataTemplate` para asociar los valores a las propiedades de `Cell` o para establecer enlaces de datos en las propiedades `Cell` que hacen referencia a las propiedades de los elementos de la colección `ItemsSource`. Esto se muestra en el ejemplo [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

A medida que el `ListView`muestra cada elemento, se crea un pequeño árbol visual a partir de la plantilla y se establecen enlaces de datos entre el elemento y las propiedades de los elementos de este árbol visual. Puede hacerse una idea de este proceso mediante la instalación de controladores para los eventos [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) y [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) de la `ListView`o mediante el uso de un [constructor de`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo que usa una función a la que se llama cada vez que el árbol visual de un elemento debe crearse.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) muestra un programa funcionalmente idéntico completamente en XAML. Una `DataTemplate` etiqueta se establece en la propiedad `ItemTemplate` de la `ListView`y, a continuación, la `TextCell` se establece en el `DataTemplate`. Los enlaces a las propiedades de los elementos de la colección se establecen directamente en las propiedades [`Text`](xref:Xamarin.Forms.TextCell.Text) y [`Detail`](xref:Xamarin.Forms.TextCell.Detail) de la `TextCell`.

### <a name="custom-cells"></a>Celdas personalizadas

En XAML es posible establecer un [`ViewCell`](xref:Xamarin.Forms.ViewCell) en el `DataTemplate` y, a continuación, definir un árbol visual personalizado como la propiedad [`View`](xref:Xamarin.Forms.ViewCell.View) de `ViewCell`. (`View` es la propiedad de contenido de `ViewCell` por lo que no se requieren las etiquetas `ViewCell.View`). En el ejemplo [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) se muestra esta técnica:

[![Captura de pantalla triple de la lista de colores con nombre personalizada](images/ch19fg11-small.png "Lista de colores con nombre personalizada")](images/ch19fg11-large.png#lightbox "Lista de colores con nombre personalizada")

Obtener el derecho de tamaño para todas las plataformas puede resultar complicado. La propiedad [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) es útil pero, en algunos casos, querrá recurrir a la propiedad [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) , que es menos eficaz, pero obliga al `ListView` a ajustar el tamaño de las filas. Para iOS y Android, debe usar una de estas dos propiedades para obtener un tamaño de fila adecuado.

### <a name="grouping-the-listview-items"></a>Agrupar los elementos de ListView

`ListView` admite la agrupación de elementos y la navegación entre esos grupos. La propiedad `ItemsSource` se debe establecer en una colección de colecciones: el objeto que `ItemsSource` está establecido en debe implementar `IEnumerable`y cada elemento de la colección también debe implementar `IEnumerable`. Cada grupo debe incluir dos propiedades: una descripción de texto del grupo y una abreviatura de tres letras.

La clase [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) de la biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crea siete grupos de objetos `NamedColor`. En el ejemplo [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) se muestra cómo usar estos grupos con la propiedad [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) de `ListView` establece en `true`y las propiedades [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) y [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) enlazadas a las propiedades de cada grupo.

### <a name="custom-group-headers"></a>Encabezados de grupo personalizados

Es posible crear encabezados personalizados para los grupos de `ListView` reemplazando la propiedad `GroupDisplayBinding` por la [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) que define una plantilla para los encabezados.

### <a name="listview-and-interactivity"></a>ListView e interactividad

Generalmente, una aplicación obtiene la interacción del usuario con un `ListView` adjuntando un controlador al evento `ItemSelected` o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , o estableciendo un enlace de datos en la propiedad `SelectedItem`. Pero algunos tipos de celda (`EntryCell` y `SwitchCell`) permiten la interacción con el usuario, y también es posible crear celdas personalizadas que interactúen con el usuario. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 instancias de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) y permite que el usuario cambie cada color mediante un trío de elementos `Slider`. El programa también usa el [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) en [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView y MVVM

`ListView` desempeña un papel importante en los escenarios de MVVM. Cuando una colección de `IEnumerable` existe en un ViewModel, a menudo se enlaza a un `ListView`. Además, los elementos de la colección suelen implementar `INotifyPropertyChanged` para enlazar con las propiedades de una plantilla.

### <a name="a-collection-of-viewmodels"></a>Una colección de ViewModels

Para explorar esto, la biblioteca [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crea varias clases basadas en un [archivo de datos XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imágenes de estudiantes ficticios en esta escuela ficticia.

La clase [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) deriva de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). La clase [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) es una colección de objetos `Student` y también deriva de `ViewModelBase`. En el [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) se descarga el archivo XML y se ensamblan todos los objetos.

El programa [**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa un `ImageCell` para mostrar los estudiantes y sus imágenes en un `ListView`:

[![Captura de pantalla triple de la lista de estudiantes](images/ch19fg18-small.png "Lista de estudiantes")](images/ch19fg18-large.png#lightbox "Lista de estudiantes")

En el ejemplo [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) se agrega una propiedad [`Header`](xref:Xamarin.Forms.ListView.Header) , pero solo se muestra en Android.

### <a name="selection-and-the-binding-context"></a>Selección y contexto de enlace

El programa [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) enlaza el `BindingContext` de una `StackLayout` a la propiedad `SelectedItem` de la `ListView`. Esto permite que el programa muestre información detallada acerca del alumno seleccionado.

### <a name="context-menus"></a>Menús contextuales

Una celda puede definir un menú contextual que se implementa de forma específica de la plataforma. Para crear este menú, agregue [`MenuItem`](xref:Xamarin.Forms.MenuItem) objetos a la propiedad [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) de la `Cell`.

`MenuItem` define cinco propiedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) de tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) de tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) de tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) de tipo `object`

Las propiedades `Command` y `CommandParameter` implican que el ViewModel de cada elemento contiene métodos para llevar a cabo los comandos de menú deseados. En escenarios no MVVM, `MenuItem` también define un evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) .

El [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) muestra esta técnica. La propiedad `Command` de cada `MenuItem` se enlaza a una propiedad de tipo `ICommand` en la clase `Student`. Establezca la propiedad `IsDestructive` en `true` para una `MenuItem` que quite o elimine el objeto seleccionado.

### <a name="varying-the-visuals"></a>Variar los objetos visuales

A veces, querrá pequeñas variaciones en los objetos visuales de los elementos de la `ListView` en función de una propiedad. Por ejemplo, cuando el promedio de puntuación de un estudiante cae por debajo de 2,0, el ejemplo [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) muestra el nombre del estudiante en rojo.
Esto se logra mediante el uso de un convertidor de valores de enlace, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), en la biblioteca de [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Actualizar el contenido

El `ListView` admite un movimiento de desactivación para actualizar sus datos. El programa debe establecer la propiedad [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) en `true` para habilitarlo. El `ListView` responde al movimiento de desplegable estableciendo su propiedad [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) en `true`y elevando el evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) y (para escenarios MVVM) llamando al método `Execute` de su propiedad [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) .

El código que controla el evento de `Refresh` o el `RefreshCommand` posiblemente actualiza los datos mostrados por la `ListView` y vuelve a establecer `IsRefreshing` en `false`.

En el ejemplo [**fuente RSS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) se muestra el uso de un [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa las propiedades `RefreshCommand` y `IsRefreshing` para el enlace de datos.

## <a name="the-tableview-and-its-intents"></a>TableView y sus intenciones

Aunque el `ListView` generalmente muestra varias instancias del mismo tipo, el [`TableView`](xref:Xamarin.Forms.TableView) se centra generalmente en proporcionar una interfaz de usuario para varias propiedades de varios tipos. Cada elemento está asociado a su propio [`Cell`](xref:Xamarin.Forms.Cell) derivado para mostrar la propiedad o para proporcionarle una interfaz de usuario.

### <a name="properties-and-hierarchies"></a>Propiedades y jerarquías

`TableView` solo define cuatro propiedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) de tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent), una enumeración
- [`Root`](xref:Xamarin.Forms.TableView.Root) de tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot), la propiedad content de `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) de tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) de tipo `bool`

La enumeración `TableIntent` indica cómo desea usar el `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Estos miembros también sugieren algunos usos para el `TableView`.

Hay varias clases relacionadas con la definición de una tabla:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) es una clase abstracta que deriva de `BindableObject` y define una propiedad [`Title`](xref:Xamarin.Forms.TableSectionBase.Title)

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) es una clase abstracta que deriva de `TableSectionBase` e implementa `IList<T>` y `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva de `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva de `TableSectionBase<TableSection>`

En Resumen, `TableView` tiene una propiedad `Root` que se establece en un objeto `TableRoot`, que es una colección de objetos `TableSection`, cada uno de los cuales es una colección de objetos `Cell`. Una tabla tiene varias secciones y cada sección tiene varias celdas. La propia tabla puede tener un título y cada sección puede tener un título. Aunque `TableView` usa `Cell` derivados, no hace uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Un formulario prosaic

En el ejemplo [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) se define un modelo de vista [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) , una instancia de que se convierte en la `BindingContext` de la `TableView`. Cada `Cell` derivado en su `TableSection` puede tener enlaces a las propiedades de la clase `PersonalInformation`.

### <a name="custom-cells"></a>Celdas personalizadas

El ejemplo [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) se expande en **EntryForm**. La clase [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) incluye una propiedad booleana que rige la aplicabilidad de dos propiedades adicionales. Para estas dos propiedades adicionales, el programa usa un `PickerCell` personalizado basado en un [PickerCell. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) y [PickerCell.Xaml.CS](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) en la biblioteca de [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Aunque las propiedades de `IsEnabled` de los dos elementos `PickerCell` se enlazan a la propiedad booleana en `ProgrammerInformation`, esta técnica no parece funcionar, lo que solicita el ejemplo siguiente.

### <a name="conditional-sections"></a>Secciones condicionales

En el ejemplo [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) se colocan los dos elementos que son condicionales en la selección del elemento booleano en un `TableSection`independiente. El archivo de código subyacente quita esta sección del `TableView` o la agrega de nuevo en función de la propiedad booleana.

### <a name="a-tableview-menu"></a>Un menú TableView

Otro uso de un `TableView` es un menú. En el ejemplo [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) se muestra un menú que le permite mover un poco `BoxView` alrededor de la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 19 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Ejemplos del capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selector](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
