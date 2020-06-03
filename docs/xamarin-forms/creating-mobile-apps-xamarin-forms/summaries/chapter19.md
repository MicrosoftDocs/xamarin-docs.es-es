---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 19. Collection views''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eafdeffb6783a0ed54fdf23e6d10de24e2b4c6f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136700"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumen del capítulo 19 Vistas de colecciones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

Xamarin.Forms define tres vistas que mantienen colecciones y muestran sus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker) es una lista relativamente breve de elementos de cadena que permite al usuario elegir uno.
- [`ListView`](xref:Xamarin.Forms.ListView) suele ser una lista larga de elementos que normalmente tienen el mismo tipo y formato, lo que también permite al usuario elegir uno.
- [`TableView`](xref:Xamarin.Forms.TableView) es una colección de *celdas* (normalmente de varios tipos y aspectos visuales) para mostrar datos o administrar la entrada del usuario.

Es común que las aplicaciones MVVM usen `ListView` para mostrar una colección seleccionable de objetos.

## <a name="program-options-with-picker"></a>Opciones de programa con el selector

El selector [`Picker`](xref:Xamarin.Forms.Picker) es una buena opción si necesita permitir que el usuario elija una opción de entre una lista relativamente breve de elementos de `string`.

### <a name="the-picker-and-event-handling"></a>Selector y control de eventos

En el ejemplo [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) se muestra cómo usar XAML para establecer la propiedad `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) y agregar elementos de `string` a la colección [`Items`](xref:Xamarin.Forms.Picker.Items). Cuando el usuario selecciona `Picker`, se muestran los elementos de la colección `Items` de una manera dependiente de la plataforma.

El evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indica cuando el usuario ha seleccionado un elemento. La propiedad [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) basada en cero indica el elemento seleccionado. Si no hay ningún elemento está seleccionado, `SelectedIndex`. es igual a &ndash;1.

También puede usar `SelectedIndex` para inicializar el elemento seleccionado, pero debe establecerse después de que se rellene la colección de `Items`. En XAML, esto significa que probablemente utilizará un elemento de propiedad para establecer `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Enlace de datos con Picker

La propiedad `SelectedIndex` está respaldada por una propiedad enlazable, pero `Items` no lo está, por lo que es difícil usar el enlace de datos con `Picker`. Una solución es usar `Picker` junto con un objeto [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como el de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit). [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) muestra cómo funciona esto.

> [!NOTE] 
> El objeto `Picker` de Xamarin.Forms ahora incluye las propiedades `ItemsSource` y `SelectedItem` que admiten el enlace de datos. Consulte [Picker](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Representación de datos con ListView

El objeto [`ListView`](xref:Xamarin.Forms.ListView) es la única clase que se deriva de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) de la que hereda las propiedades [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) y [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate).

`ItemsSource` es de tipo `IEnumerable` pero es `null` de forma predeterminada y se debe inicializar explícitamente o (más comúnmente) establecer en una colección a través de un enlace de datos. Los elementos de esta colección pueden ser de cualquier tipo.

`ListView` define una propiedad [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) que se establece en uno de los elementos de la colección de `ItemsSource` o `null` si no se selecciona ningún elemento. `ListView` activa el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) cuando se selecciona un nuevo elemento.

### <a name="collections-and-selections"></a>Colecciones y selecciones

El ejemplo [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) rellena una vista `ListView` con 17 valores `Color` en una colección de `List<Color>`. Los elementos se pueden seleccionar, pero de forma predeterminada se muestran con sus representaciones `ToString` no atractivas. Varios ejemplos de este capítulo muestran cómo corregir esa presentación y hacerla tan atractiva como se quiera.

### <a name="the-row-separator"></a>Separador de filas

En las pantallas de iOS y Android, una línea fina separa las filas. Puede controlar esto con las propiedades [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) y [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor). La propiedad `SeparatorVisibility` es de tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility), una enumeración con dos miembros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default) es la configuración predeterminada.
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Enlace de datos con el elemento seleccionado

La propiedad `SelectedItem` se complementa con una propiedad enlazable, por tanto puede ser el origen o destino de un enlace de datos. Su valor de `BindingMode` predeterminado es `OneWayToSource`, pero generalmente es el destino de un enlace de datos bidireccional, especialmente en escenarios de MVVM. En el ejemplo [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) se muestra este tipo de enlace.

### <a name="the-observablecollection-difference"></a>Diferencia de ObservableCollection

El ejemplo [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) establece la propiedad `ItemsSource` de un `ListView` en una colección `List<DateTime>` y, a continuación, agrega progresivamente un nuevo objeto `DateTime` a la colección cada segundo mediante un temporizador.

Sin embargo, la vista `ListView` no se actualiza automáticamente porque la colección de `List<T>` no tiene un mecanismo de notificación para indicar cuándo se agregan o quitan elementos de la colección.

Una clase mucho mejor para usarse en estos escenarios es [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) que se define en el espacio de nombres `System.Collections.ObjectModel`. Esta clase implementa la interfaz [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) y, por consiguiente, desencadena un evento [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) cuando se agregan o quitan elementos de la colección, o cuando se reemplazan o se mueven dentro de la colección. Cuando la vista `ListView` detecta internamente que una clase que implementa `INotifyCollectionChanged` se ha establecido en su propiedad `ItemsSource`, adjunta un controlador al evento `CollectionChanged` y actualiza su presentación cuando cambia la colección.

El ejemplo [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) muestra el uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Plantillas y celdas

De forma predeterminada, una vista `ListView` muestra los elementos de su colección mediante el método `ToString` de cada elemento. Un enfoque mejor implica definir una plantilla para mostrar los elementos.

Para experimentar con esta característica, puede usar la clase [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit). Esta clase define una propiedad `All` estática de tipo `IList<NamedColor>` que contiene 141 objetos `NamedColor` correspondientes a los campos públicos de la estructura `Color`.

En el ejemplo [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) se establece el objeto `ItemsSource` de una vista `ListView` en esta propiedad `NamedColor.All`, pero solo se muestran los nombres de clase completos de los objetos `NamedColor`.

`ListView` necesita una plantilla para mostrar estos elementos. En el código, puede establecer la propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) definida por `ItemsView<TVisual>` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) mediante el constructor [`DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que hace referencia a un derivado de la clase [`Cell`](xref:Xamarin.Forms.Cell). `Cell` tiene cinco derivados:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contiene dos vistas del objeto `Label` (conceptualmente hablando).
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; agrega una vista `Image` a `TextCell`.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contiene una vista `Entry` con un objeto `Label`.
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contiene un objeto `Switch` con un objeto `Label`.
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; puede ser cualquier objeto `View` (probablemente con elementos secundarios).

Después, llame a [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) y [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) en el objeto `DataTemplate` para asociar los valores a las propiedades `Cell` o para establecer enlaces de datos en las propiedades `Cell` que hacen referencia a las propiedades de los elementos de la colección `ItemsSource`. Esto se muestra en el ejemplo [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode).

A medida que la vista `ListView` muestra cada elemento, se crea un pequeño árbol visual a partir de la plantilla y se establecen enlaces de datos entre el elemento y las propiedades de los elementos de este árbol visual. Puede hacerse una idea de este proceso mediante la instalación de los controladores de los eventos [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) y [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) de la vista `ListView`, o bien mediante un constructor [`DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) que utiliza una función a la que se llama cada vez que se debe crear el árbol visual de un elemento.

En el ejemplo [**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) se muestra un programa idéntico funcionalmente en XAML. Se establece una etiqueta `DataTemplate` en la propiedad `ItemTemplate` de la vista `ListView` y, a continuación, el objeto `TextCell` se establece en `DataTemplate`. Los enlaces a las propiedades de los elementos de la colección se establecen directamente en las propiedades [`Text`](xref:Xamarin.Forms.TextCell.Text) y [`Detail`](xref:Xamarin.Forms.TextCell.Detail) de `TextCell`.

### <a name="custom-cells"></a>Celdas personalizadas

En XAML es posible establecer un objeto [`ViewCell`](xref:Xamarin.Forms.ViewCell) en `DataTemplate` y, a continuación, definir un árbol visual personalizado como la propiedad [`View`](xref:Xamarin.Forms.ViewCell.View) de `ViewCell`. (`View` es la propiedad de contenido de `ViewCell`, por lo que no se requieren las etiquetas `ViewCell.View`). En el ejemplo [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) se muestra esta técnica:

[![Captura de pantalla triple de la lista de colores con nombre personalizada](images/ch19fg11-small.png "Lista de colores con nombre personalizada")](images/ch19fg11-large.png#lightbox "Lista de colores con nombre personalizada")

Obtener el tamaño correcto para todas las plataformas puede resultar complicado. La propiedad [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) es útil, pero en algunos casos querrá recurrir a la propiedad [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows), que es menos eficaz, pero obliga a la vista `ListView` a ajustar el tamaño de las filas. Para iOS y Android, debe usar una de estas dos propiedades para obtener un tamaño de fila adecuado.

### <a name="grouping-the-listview-items"></a>Agrupación de los elementos de ListView

`ListView` admite la agrupación de los elementos y la navegación entre esos grupos. La propiedad `ItemsSource` se debe establecer en una colección de colecciones: El objeto que `ItemsSource` establece debe implementar `IEnumerable` y cada elemento de la colección también debe implementar `IEnumerable`. Cada grupo debe incluir dos propiedades: una descripción de texto del grupo y una abreviatura de tres letras.

La clase [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crea siete grupos de objetos `NamedColor`. En el ejemplo [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) se muestra cómo usar estos grupos con la propiedad [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) de `ListView` establecida en `true` y las propiedades [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) y [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) enlazadas a las propiedades de cada grupo.

### <a name="custom-group-headers"></a>Encabezados de grupo personalizados

Para crear encabezados personalizados para los grupos de `ListView`, reemplace la propiedad `GroupDisplayBinding` por [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) que define una plantilla para los encabezados.

### <a name="listview-and-interactivity"></a>ListView e interactividad

Por lo general, una aplicación obtiene la interacción del usuario con una vista `ListView` adjuntando un controlador al evento `ItemSelected` o [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), o estableciendo un enlace de datos en la propiedad `SelectedItem`. Pero algunos tipos de celda (`EntryCell` y `SwitchCell`) permiten la interacción con el usuario, y también es posible crear celdas personalizadas que interactúen con el usuario. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crea 100 instancias de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) y permite que el usuario cambie cada color mediante un trío de elementos `Slider`. El programa también hace uso de [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView y MVVM

`ListView` desempeña un importante rol en los escenarios de MVVM. Cuando una colección de `IEnumerable` existe en un objeto ViewModel, a menudo se enlaza a una vista `ListView`. Además, los elementos de la colección suelen implementar `INotifyPropertyChanged` para enlazar con las propiedades de una plantilla.

### <a name="a-collection-of-viewmodels"></a>Colección de ViewModels

Para explorar esto último, la biblioteca [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crea varias clases basadas en un [archivo de datos XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imágenes de alumnos ficticios en esta escuela ficticia.

La clase [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) se deriva de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). La clase [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) es una colección de objetos `Student` y también deriva de `ViewModelBase`. En [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) se descarga el archivo XML y se ensamblan todos los objetos.

El programa [**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa un objeto `ImageCell` para mostrar los alumnos y sus imágenes en una vista `ListView`:

[![Captura de pantalla triple de la lista de alumnos](images/ch19fg18-small.png "Lista de alumnos")](images/ch19fg18-large.png#lightbox "Lista de alumnos")

El ejemplo [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) agrega una propiedad [`Header`](xref:Xamarin.Forms.ListView.Header), pero solo se muestra en Android.

### <a name="selection-and-the-binding-context"></a>Selección y contexto de enlace

El programa [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) enlaza el objeto `BindingContext` de una vista `StackLayout` con la propiedad `SelectedItem` de la vista `ListView`. Esto permite que el programa muestre información detallada acerca del alumno seleccionado.

### <a name="context-menus"></a>Menús contextuales

Una celda puede definir un menú contextual que se implementa de forma específica de la plataforma. Para crear este menú, agregue objetos [`MenuItem`](xref:Xamarin.Forms.MenuItem) a la propiedad [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) de la `Cell`.

La clase `MenuItem` define cinco propiedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) de tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) de tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) de tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) de tipo `object`

Las propiedades `Command` y `CommandParameter` implican que el objeto ViewModel de cada elemento contiene métodos para llevar a cabo los comandos de menú deseados. En escenarios no MVVM, `MenuItem` también define un evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked).

El elemento [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) muestra esta técnica. La propiedad `Command` de cada elemento `MenuItem` se enlaza a una propiedad de tipo `ICommand` en la clase `Student`. Establezca la propiedad `IsDestructive` en `true` para un elemento `MenuItem` que quite o elimine el objeto seleccionado.

### <a name="varying-the-visuals"></a>Variación de los objetos visuales

A veces, querrá pequeñas variaciones de los objetos visuales de los elementos de la vista `ListView` en función de una propiedad. Por ejemplo, cuando el promedio de puntuación de un alumno cae por debajo de 2,0, la propiedad [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) muestra el nombre del alumno en rojo.
Esto se logra mediante el uso de un convertidor de valores de enlace, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

### <a name="refreshing-the-content"></a>Actualización de la memoria caché

La vista `ListView` admite un movimiento de desactivación para actualizar sus datos. El programa debe establecer la propiedad [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) en `true` para habilitarlo. La vista `ListView` responde al movimiento de la desactivación mediante el establecimiento de su propiedad [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) en `true`, mediante la activación del evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) (para escenarios de MVVM) y con la llamada al método `Execute` de su propiedad [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand).

El código que controla el evento `Refresh` o `RefreshCommand` posiblemente actualiza los datos mostrados por la vista `ListView` y vuelve a establecer la propiedad `IsRefreshing` en `false`.

En el ejemplo [**RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) se muestra el uso de un objeto [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa las propiedades `RefreshCommand` y `IsRefreshing` para el enlace de datos.

## <a name="the-tableview-and-its-intents"></a>TableView y sus intenciones

Aunque la vista `ListView` generalmente muestra varias instancias del mismo tipo, la vista [`TableView`](xref:Xamarin.Forms.TableView) generalmente se centra en proporcionar una interfaz de usuario para varias propiedades de varios tipos. Cada elemento está asociado a su propio objeto [`Cell`](xref:Xamarin.Forms.Cell) derivado para mostrar la propiedad o para proporcionarle una interfaz de usuario.

### <a name="properties-and-hierarchies"></a>Propiedades y jerarquías

`TableView` define solo cuatro propiedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) de tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent), una enumeración
- [`Root`](xref:Xamarin.Forms.TableView.Root) de tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot), la propiedad de contenido de `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) de tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) de tipo `bool`

La enumeración `TableIntent` indica cómo desea usar `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Estos miembros también sugieren algunos usos para `TableView`.

Hay varias clases relacionadas con la definición de una tabla:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) es una clase abstracta que deriva de `BindableObject` y define una propiedad [`Title`](xref:Xamarin.Forms.TableSectionBase.Title).

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) es una clase abstracta que deriva de `TableSectionBase` e implementa `IList<T>` y `INotifyCollectionChanged`.

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva de `TableSectionBase<Cell>`.

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva de `TableSectionBase<TableSection>`.

En resumen, la vista `TableView` tiene una propiedad `Root` que se establece en un objeto `TableRoot`, que es una colección de objetos `TableSection`, cada uno de los cuales es una colección de objetos `Cell`. Una tabla tiene varias secciones y cada sección tiene varias celdas. La propia tabla puede tener un título y cada sección puede tener un título. Aunque la vista `TableView` usa derivados del objeto `Cell`, no hace uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Formulario prosaico

En el ejemplo [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) se define un modelo de vista [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs), una instancia que se convierte en el objeto `BindingContext` de la vista `TableView`. Cada objeto `Cell` derivado en `TableSection` puede tener enlaces a las propiedades de la clase `PersonalInformation`.

### <a name="custom-cells"></a>Celdas personalizadas

El ejemplo [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) se expande en **EntryForm**. La clase [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) incluye una propiedad booleana que rige la aplicabilidad de dos propiedades adicionales. Para estas dos propiedades adicionales, el programa utilice un elemento `PickerCell` personalizado basado en los ejemplos [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) y [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

Aunque las propiedades `IsEnabled` de los dos elementos `PickerCell` se enlazan a la propiedad booleana en `ProgrammerInformation`, esta técnica no parece funcionar, lo que solicita el ejemplo siguiente.

### <a name="conditional-sections"></a>Secciones condicionales

El ejemplo [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) coloca los dos elementos que son condicionales en la selección del elemento booleano en un elemento `TableSection` independiente. El archivo de código subyacente quita esta sección de la vista `TableView` o la agrega de nuevo en función de la propiedad booleana.

### <a name="a-tableview-menu"></a>Menú TableView

Otro uso de una vista `TableView` es un menú. En el ejemplo [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) se muestra un menú que le permite mover un poco el elemento `BoxView` alrededor de la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 19 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Ejemplos del capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Selector](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
