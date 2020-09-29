---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: Comparación de controles de interfaz de usuario
description: En este documento se proporciona una comparación de los controles de interfaz de usuario entre Xamarin. Forms, Windows Forms y WPF. También se vincula a otra documentación que compara WPF con Xamarin. Forms.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: b4cffd9e95f24dea9fc5fed5a6badeec624a4e25
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453095"
---
# <a name="ui-controls-comparison"></a>Comparación de controles de interfaz de usuario

A continuación se muestra una comparación de los controles de Xamarin. Forms con Windows Forms y WPF, basados en [esta tabla](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls).

Obtenga más información sobre las [similitudes y diferencias entre WPF y Xamarin. Forms](wpf.md) para ayudar a actualizar los conocimientos de escritorio para el desarrollo de aplicaciones móviles.

|Windows Forms|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](/dotnet/api/system.windows.forms.bindingnavigator)|-|-|
|[BindingSource](/dotnet/api/system.windows.forms.bindingsource)|[CollectionViewSource](/dotnet/api/system.windows.data.collectionviewsource)|Propiedad de enlace, p. ej., BindingContext|
|[Botón](/dotnet/api/system.windows.forms.button)|[Botón](/dotnet/api/system.windows.controls.button)|Botón|
|[CheckBox](/dotnet/api/system.windows.forms.checkbox)|[CheckBox](/dotnet/api/system.windows.controls.checkbox)|Conmutador|
|[CheckedListBox](/dotnet/api/system.windows.forms.checkedlistbox)|[ListBox](/dotnet/api/system.windows.controls.listbox) con composición.|ListView con composición.|
|[ColorDialog](/dotnet/api/system.windows.forms.colordialog)|-|-|
|[ComboBox](/dotnet/api/system.windows.forms.combobox)|[ComboBox](/dotnet/api/system.windows.controls.combobox) (no es compatible con auto-complete)|Selector|
|[ContextMenuStrip](/dotnet/api/system.windows.forms.contextmenustrip)|[ContextMenu](/dotnet/api/system.windows.controls.contextmenu)|-|
|[DataGridView](/dotnet/api/system.windows.forms.datagridview)|[DataGrid](/dotnet/api/system.windows.controls.datagrid)|-|
|[DateTimePicker](/dotnet/api/system.windows.forms.datetimepicker)|[DatePicker](/dotnet/api/system.windows.controls.datepicker)|DatePicker & TimePicker|
|[DomainUpDown](/dotnet/api/system.windows.forms.domainupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) y dos controles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Control de incremento|
|[ErrorProvider](/dotnet/api/system.windows.forms.errorprovider)|-|-|
|[FlowLayoutPanel](/dotnet/api/system.windows.forms.flowlayoutpanel)|[WrapPanel](/dotnet/api/system.windows.controls.wrappanel) o [StackPanel](/dotnet/api/system.windows.controls.stackpanel)|StackLayout o FlexLayout|
|[FolderBrowserDialog](/dotnet/api/system.windows.forms.folderbrowserdialog)|-|-|
|[FontDialog](/dotnet/api/system.windows.forms.fontdialog)|-|-|
|[Forma](/dotnet/api/system.windows.forms.form)|[Ventana](/dotnet/api/system.windows.window)|Página|
|[GroupBox](/dotnet/api/system.windows.forms.groupbox)|[GroupBox](/dotnet/api/system.windows.controls.groupbox)|-|
|[HelpProvider](/dotnet/api/system.windows.forms.helpprovider)|No hay ningún control equivalente (use la información sobre herramientas).|-|
|[HScrollBar](/dotnet/api/system.windows.forms.hscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar) (el desplazamiento está integrado en los controles de contenedor)|usar ScrollView|
|[ImageList](/dotnet/api/system.windows.forms.imagelist)|-|-|
|[Label](/dotnet/api/system.windows.forms.label)|[Label](/dotnet/api/system.windows.controls.label)|Etiqueta|
|[LinkLabel](/dotnet/api/system.windows.forms.linklabel)|No hay ningún control equivalente (puede usar la clase de [hipervínculo](/dotnet/api/system.windows.documents.hyperlink) para hospedar hipervínculos dentro del contenido dinámico).|-|
|[ListBox](/dotnet/api/system.windows.forms.listbox)|[ListBox](/dotnet/api/system.windows.controls.listbox)|Usar ListView|
|[ListView](/dotnet/api/system.windows.forms.listview)|[ListView](/dotnet/api/system.windows.controls.listview)|ListView|
|[MaskedTextBox](/dotnet/api/system.windows.forms.maskedtextbox)|-|-|
|[MenuStrip](/dotnet/api/system.windows.forms.menustrip)|[Menú](/dotnet/api/system.windows.controls.menu)|Considere la posibilidad de MasterDetailPage o TabbedPage|
|[MonthCalendar](/dotnet/api/system.windows.forms.monthcalendar)|[Calendario](/dotnet/api/system.windows.controls.calendar)|-|
|[NotifyIcon](/dotnet/api/system.windows.forms.notifyicon)|-|-|
|[NumericUpDown](/dotnet/api/system.windows.forms.numericupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) y dos controles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Control de incremento|
|[OpenFileDialog](/dotnet/api/system.windows.forms.openfiledialog)|[OpenFileDialog](/dotnet/api/microsoft.win32.openfiledialog)|-|
|[PageSetupDialog](/dotnet/api/system.windows.forms.pagesetupdialog)|-|-|
|[Panel](/dotnet/api/system.windows.forms.panel)|[Lienzo](/dotnet/api/system.windows.controls.canvas)|Ver o AbsoluteLayout|
|[PictureBox](/dotnet/api/system.windows.forms.picturebox)|[Imagen](/dotnet/api/system.windows.controls.image)|Imagen|
|[PrintDialog](/dotnet/api/system.windows.forms.printdialog)|[PrintDialog](/dotnet/api/system.windows.controls.printdialog)|-|
|[PrintDocument](/dotnet/api/system.drawing.printing.printdocument)|-|-|
|[Habitualmente](/dotnet/api/system.windows.forms.printpreviewcontrol)|[DocumentViewer](/dotnet/api/system.windows.controls.documentviewer)|-|
|[PrintPreviewDialog](/dotnet/api/system.windows.forms.printpreviewdialog)|-|-|
|[ProgressBar](/dotnet/api/system.windows.forms.progressbar)|[ProgressBar](/dotnet/api/system.windows.controls.progressbar)|ProgressBar|
|[PropertyGrid](/dotnet/api/system.windows.forms.propertygrid)|-|-|
|[RadioButton](/dotnet/api/system.windows.forms.radiobutton)|[RadioButton](/dotnet/api/system.windows.controls.radiobutton)|-|
|[RichTextBox](/dotnet/api/system.windows.forms.richtextbox)|[RichTextBox](/dotnet/api/system.windows.controls.richtextbox)|El editor no admite texto enriquecido (con formato), entrada para texto de una sola línea|
|[SaveFileDialog](/dotnet/api/system.windows.forms.savefiledialog)|[SaveFileDialog](/dotnet/api/microsoft.win32.savefiledialog)|-|
|[ScrollableControl](/dotnet/api/system.windows.forms.scrollablecontrol)|[ScrollViewer](/dotnet/api/system.windows.controls.scrollviewer)|ScrollView|
|[SoundPlayer](/dotnet/api/system.media.soundplayer)|[MediaPlayer](/dotnet/api/system.windows.media.mediaplayer)|-|
|[SplitContainer (](/dotnet/api/system.windows.forms.splitcontainer)|[GridSplitter](/dotnet/api/system.windows.controls.gridsplitter)|Considere la posibilidad de MasterDetailPage|
|[StatusStrip](/dotnet/api/system.windows.forms.statusstrip)|[StatusBar](/dotnet/api/system.windows.controls.primitives.statusbar)|-|
|[TabControl](/dotnet/api/system.windows.forms.tabcontrol)|[TabControl](/dotnet/api/system.windows.controls.tabcontrol)|TabbedPage|
|[TableLayoutPanel](/dotnet/api/system.windows.forms.tablelayoutpanel)|[Grid](/dotnet/api/system.windows.controls.grid)|Cuadrícula|
|[TextBox](/dotnet/api/system.windows.forms.textbox)|[TextBox](/dotnet/api/system.windows.controls.textbox)|El editor no admite texto enriquecido (con formato)|
|[Temporizador](/dotnet/api/system.windows.forms.timer)|[DispatcherTimer](/dotnet/api/system.windows.threading.dispatchertimer)|Device. StartTime ()|
|[ToolStrip](/dotnet/api/system.windows.forms.toolstrip)|[Barra](/dotnet/api/system.windows.controls.toolbar)|Page. ToolbarItems y ToolbarItem|
|[ToolStripContainer](/dotnet/api/system.windows.forms.toolstripcontainer), [ToolStripDropDown](/dotnet/api/system.windows.forms.toolstripdropdown), [ToolStripDropDownMenu](/dotnet/api/system.windows.forms.toolstripdropdownmenu), [ToolStripPanel](/dotnet/api/system.windows.forms.toolstrippanel)|[Barra de herramientas](/dotnet/api/system.windows.controls.toolbar) con composición.|Page. ToolbarItems y ToolbarItem con composición|
|[ToolTip](/dotnet/api/system.windows.forms.tooltip)|[ToolTip](/dotnet/api/system.windows.controls.tooltip)|Usar características de accesibilidad|
|[Trackbar](/dotnet/api/system.windows.forms.trackbar)|[Control deslizante](/dotnet/api/system.windows.controls.slider)|Control deslizante|
|[TreeView](/dotnet/api/system.windows.forms.treeview)|[TreeView](/dotnet/api/system.windows.controls.treeview)|Considerar ListView jerárquica en un NavigationPage|
|[Control de usuario](/dotnet/api/system.windows.forms.usercontrol)|[Control de usuario](/dotnet/api/system.windows.controls.usercontrol)|Ver y también representadores personalizados|
|[VScrollBar](/dotnet/api/system.windows.forms.vscrollbar)|[Desplazamiento](/dotnet/api/system.windows.controls.primitives.scrollbar)|usar ScrollView|
|[WebBrowser](/dotnet/api/system.windows.forms.webbrowser)|[WebBrowser](/dotnet/api/system.windows.controls.webbrowser)|WebView|