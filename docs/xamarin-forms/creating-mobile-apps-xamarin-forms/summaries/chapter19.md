---
title: Resumen del capítulo 19. Vistas de colección
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 19. Vistas de colección'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bad46bead106304713bae91a7bf752db8080760a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760619"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumen del capítulo 19. Vistas de colección

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Xamarin.Forms define tres vistas que mantienen colecciones y mostrar sus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker) es una lista de elementos de cadena relativamente corta que permite al usuario elegir una
- [`ListView`](xref:Xamarin.Forms.ListView) a menudo es una larga lista de elementos, normalmente del mismo tipo y el formato, que permite al usuario elegir uno
- [`TableView`](xref:Xamarin.Forms.TableView) es una colección de *celdas* (normalmente de distintos tipos y las apariencias) para mostrar datos o administrar la entrada de usuario

Es habitual que las aplicaciones MVVM para usar el `ListView` para mostrar una colección de objetos seleccionable.

## <a name="program-options-with-picker"></a>Opciones del programa con el selector

El [ `Picker` ](xref:Xamarin.Forms.Picker) es una buena elección cuando deba permitir que el usuario elija una opción entre una lista de relativamente corta `string` elementos.

### <a name="the-picker-and-event-handling"></a>El selector y el control de eventos

El [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) ejemplo muestra cómo usar XAML para establecer el `Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title) propiedad y agregue `string` elementos a la [ `Items` ](xref:Xamarin.Forms.Picker.Items) colección. Cuando el usuario selecciona el `Picker`, muestra los elementos en el `Items` colección de una manera dependiente de la plataforma.

El [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento indica que cuando el usuario ha seleccionado un elemento. Base cero [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad indica a continuación, el elemento seleccionado. Si se selecciona ningún elemento, `SelectedIndex` es igual a &ndash;1.

También puede usar `SelectedIndex` inicializar el elemento seleccionado, pero debe establecerse después de la `Items` se rellena la colección. En XAML, esto significa que probablemente usará un elemento de propiedad para establecer `SelectedIndex`.

### <a name="data-binding-the-picker"></a>El selector de enlace de datos

El `SelectedIndex` propiedad está respaldada por una propiedad enlazable pero `Items` no lo es, por lo que usar enlace de datos con un `Picker` es difícil. Una solución consiste en usar el `Picker` en combinación con un [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como el de la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. El [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) muestra cómo funciona esto.

> [!NOTE] 
> Xamarin.Forms `Picker` ahora incluye `ItemsSource` y `SelectedItem` las propiedades que admiten el enlace de datos. Consulte [selector](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Representación de datos con ListView

El [ `ListView` ](xref:Xamarin.Forms.ListView) es la única clase que derive de [ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1) de la que hereda el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) y [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propiedades.

`ItemsSource` es de tipo `IEnumerable` pero es `null` de forma predeterminada y se debe inicializar explícitamente o (más comúnmente) se establece en una colección mediante un enlace de datos. Los elementos de esta colección pueden ser de cualquier tipo.

`ListView` define un [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad que se establece en uno de los elementos de la `ItemsSource` colección o `null` si se selecciona ningún elemento. `ListView` se desencadena la [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos cuando se selecciona un nuevo elemento.

### <a name="collections-and-selections"></a>Las colecciones y las selecciones

El [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) ejemplo rellena una `ListView` con 17 `Color` valores en un `List<Color>` colección. Los elementos son seleccionables, pero de forma predeterminada se muestran con sus poco atractivo `ToString` representaciones. Varios ejemplos de este capítulo muestran cómo corregir que muestran y que sea tan atractivas según sea necesario.

### <a name="the-row-separator"></a>El separador de filas

En iOS y Android muestra, una línea fina separa las filas. Puede controlar esto con la [ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility) y [ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor) propiedades. `SeparatorVisibility` propiedad es de tipo [ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility), una enumeración con dos miembros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), la configuración predeterminada
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>El elemento seleccionado de enlace de datos

El `SelectedItem` propiedad está respaldada por una propiedad enlazable, por lo que puede ser el origen o el destino de un enlace de datos. Su valor predeterminado `BindingMode` es `OneWayToSource`, pero generalmente es el destino de un enlace de datos bidireccional, especialmente en escenarios MVVM. El [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) muestra este tipo de enlace.

### <a name="the-observablecollection-difference"></a>La diferencia ObservableCollection

El [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) conjuntos de ejemplo la `ItemsSource` propiedad de un `ListView` a un `List<DateTime>` colección y, a continuación, progresivamente agrega un nuevo `DateTime` objeto a la colección cada segundo a través de un temporizador.

Sin embargo, el `ListView` no se actualiza automáticamente porque la `List<T>` colección no tiene un mecanismo de notificación para indicar cuando se agregan o se quita de la colección de elementos.

Una clase mucho mejor usar en estos escenarios es [ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1) definido en el `System.Collections.ObjectModel` espacio de nombres. Esta clase implementa la [ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged) interfaz y, por consiguiente, se desencadena una [ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) evento cuando se agregan o quitan de la colección, o cuando se reemplaza o se mueven dentro de elementos la colección. Cuando el `ListView` internamente detecta que una clase que implementa `INotifyCollectionChanged` se ha establecido en su `ItemsSource` propiedad, adjunta un controlador para el `CollectionChanged` eventos y su presentación se actualiza cuando cambia la colección.

El [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) ejemplo muestra el uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Las plantillas y las celdas

De forma predeterminada, un `ListView` muestra elementos en su colección con cada elemento `ToString` método. Un mejor enfoque implica definir una plantilla para mostrar los elementos.

Para experimentar con esta característica, puede usar el [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. Esta clase define un estático `All` propiedad de tipo `IList<NamedColor>` que contiene 141 `NamedColor` objetos correspondientes a los campos públicos de la `Color` estructura.

El [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) conjuntos de ejemplo la `ItemsSource` de un `ListView` a este `NamedColor.All` propiedad, pero solo los nombres de clase completo de la `NamedColor` son objetos muestra.

`ListView` necesita una plantilla para mostrar estos elementos. En el código, puede establecer el [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propiedad definida por `ItemsView<TVisual>` a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objeto utilizando el [ `DataTemplate` constructor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que hace referencia a un derivado de la [ `Cell` ](xref:Xamarin.Forms.Cell) clase. `Cell` tiene cinco derivados:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contiene dos `Label` vistas (en términos conceptuales)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; Agrega un `Image` ver a `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contiene un `Entry` ver con un `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contiene un `Switch` con un `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; puede ser cualquier `View` (probablemente con elementos secundarios)

A continuación, llame a [ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) y [ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) en el `DataTemplate` objeto para asociar valores con el `Cell` propiedades, o para establecer los enlaces de datos en el `Cell` las propiedades que hacen referencia a las propiedades de los elementos de la `ItemsSource` colección. Esto se muestra en el [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) ejemplo.

Tal como se muestra cada elemento por el `ListView`, un pequeño árbol visual se construye a partir de la plantilla y los enlaces de datos se establecen entre el elemento y las propiedades de los elementos de este árbol visual. Puede obtener una idea de este proceso mediante la instalación de controladores para la [ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing) y [ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing) eventos de la `ListView`, o mediante el uso de una alternativa [ `DataTemplate`constructor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) que usa una función que se llama cada vez que se debe crear el árbol visual de un elemento.

El [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) muestra un programa funcionalmente idéntico completamente en XAML. Un `DataTemplate` etiqueta se establece en el `ItemTemplate` propiedad de la `ListView`y, a continuación, el `TextCell` está establecido en el `DataTemplate`. Enlaces a las propiedades de los elementos de la colección se establecen directamente en el [ `Text` ](xref:Xamarin.Forms.TextCell.Text) y [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propiedades de la `TextCell`.

### <a name="custom-cells"></a>Celdas personalizadas

En XAML es posible establecer un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) a la `DataTemplate` y, a continuación, defina un árbol visual personalizado como el [ `View` ](xref:Xamarin.Forms.ViewCell.View) propiedad de `ViewCell`. (`View` es la propiedad content de `ViewCell` por lo que el `ViewCell.View` etiquetas no son obligatorios.) El [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) muestra esta técnica:

[![Captura de pantalla de lista de colores personalizada denominada triple](images/ch19fg11-small.png "personalizado denominado lista de colores")](images/ch19fg11-large.png#lightbox "personalizado denominado lista de colores")

Obtener el tamaño adecuado para todas las plataformas puede resultar complicado. El [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight) propiedad es útil, aunque en algunos casos es conveniente que recurrir a la [ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows) propiedad, que es menos eficaz pero fuerza el `ListView` para cambiar el tamaño de las filas. Para iOS y Android, debe usar una de estas dos propiedades para obtener el tamaño de fila adecuados.

### <a name="grouping-the-listview-items"></a>Agrupar los elementos de ListView

`ListView` admite la agrupación de elementos y navegar entre esos grupos. La `ItemsSource` propiedad se debe establecer en una colección de colecciones: El objeto que `ItemsSource` se establece en debe implementar `IEnumerable`y cada elemento de la colección también debe implementar `IEnumerable`. Cada grupo debe incluir dos propiedades: una descripción del grupo y una abreviatura de tres letras.

El [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca crea siete grupos de `NamedColor` objetos. El [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) ejemplo muestra cómo usar estos grupos con el [ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled) propiedad de `ListView` establecido en `true`y el [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) y [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) propiedades enlazadas a las propiedades de cada grupo.

### <a name="custom-group-headers"></a>Encabezados de grupo personalizado

Es posible crear los encabezados personalizados para el `ListView` grupos si se reemplaza el `GroupDisplayBinding` propiedad con el [ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) definición de una plantilla para los encabezados.

### <a name="listview-and-interactivity"></a>ListView e interactividad

Generalmente una aplicación obtiene la interacción del usuario con un `ListView` adjunte un identificador para el `ItemSelected` o [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento, o mediante el establecimiento de un enlace de datos en el `SelectedItem` propiedad. Pero algunos tipos de celda (`EntryCell` y `SwitchCell`) permiten la interacción del usuario, y también es posible crear celdas personalizadas que a su vez de interactuar con el usuario. El [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 instancias de [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) y permite al usuario cambiar los colores mediante un trío de `Slider` elementos. El programa también hace uso de la [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) en el [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView y MVVM

`ListView` desempeña un papel importante en escenarios MVVM. Cada vez que un `IEnumerable` colección existe en una clase ViewModel, que está enlazado a menudo un `ListView`. Además, los elementos de la colección a menudo implementan `INotifyPropertyChanged` para enlazar con propiedades en una plantilla.

### <a name="a-collection-of-viewmodels"></a>Una colección de modelos de vista

Para esto, explorar la [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca crea varias clases basadas en un [archivo de datos XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imágenes de alumnos ficticias de la escuela ficticia.

El [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) clase se deriva de [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). El [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) clase es una colección de `Student` objetos y también se deriva de `ViewModelBase`. El [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) descarga el archivo XML y ensambla todos los objetos.

El [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programa usa un `ImageCell` para mostrar a los alumnos y sus imágenes en un `ListView`:

[![Captura de pantalla triple de la lista de alumnos](images/ch19fg18-small.png "lista de alumnos")](images/ch19fg18-large.png#lightbox "lista de alumnos")

El [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) ejemplo agrega un [ `Header` ](xref:Xamarin.Forms.ListView.Header) propiedad pero solo se muestra en Android.

### <a name="selection-and-the-binding-context"></a>Selección y el contexto de enlace

El [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programa enlaza la `BindingContext` de un `StackLayout` a la `SelectedItem` propiedad de la `ListView`. Esto permite que el programa mostrar información detallada sobre el alumno seleccionado.

### <a name="context-menus"></a>Menús contextuales

Una celda puede definir un menú contextual que se implementa de manera específica de la plataforma. Para crear este menú, agregue [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) objetos a la [ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions) propiedad de la `Cell`.

`MenuItem` define las cinco propiedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) de tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) de tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) de tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) de tipo `object`

El `Command` y `CommandParameter` propiedades implican que el modelo de vista para cada elemento contiene métodos para llevar a cabo los comandos de menú deseado. En escenarios que no sean MVVM, `MenuItem` también define un [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) eventos.

El [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demuestra esta técnica. El `Command` propiedad de cada uno `MenuItem` está enlazado a una propiedad de tipo `ICommand` en el `Student` clase. Establecer el `IsDestructive` propiedad `true` para un `MenuItem` que quita o elimina el objeto seleccionado.

### <a name="varying-the-visuals"></a>Variar los objetos visuales

A veces es conveniente pequeñas variaciones en los objetos visuales de los elementos de la `ListView` basada en una propiedad. Por ejemplo, cuando grado-Media un estudiante cae por debajo de 2.0, el [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) ejemplo muestra el nombre de ese estudiante en rojo.
Esto se logra mediante el uso de un convertidor de valores de enlace, [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="refreshing-the-content"></a>Actualizando el contenido

El `ListView` es compatible con un gesto de incorporación de cambios para actualizar sus datos. El programa debe establecer el [ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) propiedad `true` para habilitar esta opción. El `ListView` responde al movimiento del desplegable estableciendo su [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) propiedad `true`y generando el [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) eventos y (para escenarios MVVM) una llamada a el `Execute` método de su [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) propiedad.

Control de código la `Refresh` eventos o la `RefreshCommand` , a continuación, actualiza, posiblemente, los datos mostrados por la `ListView` y establece `IsRefreshing` a `false`.

El [ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) ejemplo muestra cómo utilizar un [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` y `IsRefreshing` las propiedades de enlace de datos.

## <a name="the-tableview-and-its-intents"></a>El TableView y sus intenciones

Mientras el `ListView` generalmente muestra varias instancias del mismo tipo, el [ `TableView` ](xref:Xamarin.Forms.TableView) por lo general se centra en ofrecer una interfaz de usuario para varias propiedades de varios tipos. Cada elemento está asociado a su propio [ `Cell` ](xref:Xamarin.Forms.Cell) derivados para mostrar la propiedad o para proporcionar una interfaz de usuario.

### <a name="properties-and-hierarchies"></a>Las propiedades y jerarquías

`TableView` define sólo cuatro propiedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) de tipo [ `TableIntent` ](xref:Xamarin.Forms.TableIntent), una enumeración
- [`Root`](xref:Xamarin.Forms.TableView.Root) de tipo [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), la propiedad de contenido `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) de tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) de tipo `bool`

El `TableIntent` enumeración indica cómo piensa usar el `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Estos miembros también sugieren algunos usos de la `TableView`.

Algunas otras clases están implicados en la definición de una tabla:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) es una clase abstracta que derive de `BindableObject` y define un [ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title) propiedad

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) es una clase abstracta que derive de `TableSectionBase` e implementa `IList<T>` y `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) se deriva de `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) se deriva de `TableSectionBase<TableSection>`

En resumen, `TableView` tiene un `Root` propiedad que se establece en un `TableRoot` objeto, que es una colección de `TableSection` objetos, cada uno de los cuales es una colección de `Cell` objetos. Una tabla tiene varias secciones y cada sección tiene varias celdas. La propia tabla puede tener un título, y cada sección puede tener un título. Aunque `TableView` hace uso de `Cell` derivados, no hace uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Un formulario prosaico

El [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) ejemplo define un [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modelo de vista, se convierte en una instancia de los cuales el `BindingContext` de la `TableView`. Cada `Cell` derivados en su `TableSection` , a continuación, puede tener enlaces a las propiedades de la `PersonalInformation` clase.

### <a name="custom-cells"></a>Celdas personalizadas

El [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) extiende el ejemplo **EntryForm**. El [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) clase incluye una propiedad booleana que controla la aplicabilidad de las dos propiedades adicionales. Para estas dos propiedades adicionales, el programa usa un personalizado `PickerCell` según un [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) y [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) en el [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

Aunque el `IsEnabled` propiedades de las dos `PickerCell` elementos están enlazados a la propiedad booleana de `ProgrammerInformation`, esta técnica no parece funcionar, que solicita el siguiente ejemplo.

### <a name="conditional-sections"></a>Secciones condicionales

El [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) ejemplo coloca los dos elementos que son condicionales en la selección del elemento booleano en otro `TableSection`. El archivo de código subyacente quita esta sección desde el `TableView` o agrega nuevo según la propiedad booleana.

### <a name="a-tableview-menu"></a>Un menú TableView

Otro uso de un `TableView` es un menú. El [ **MenuCommands frente** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) muestra un menú que le permite mover un poco `BoxView` alrededor de la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 19 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Ejemplos de capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selector](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
