---
title: Trabajo con tablas en iOS Designer
description: En las secciones anteriores hemos explorado el desarrollo con tablas. En esta sección, la quinta y la última sección, agregaremos lo que hemos aprendido hasta ahora y crearemos una aplicación de lista de tareas básicas con un guion gráfico.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 963151c8f5f88373fd6d71a2bb74bd2dbe5d6ab5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430083"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Trabajo con tablas en iOS Designer

Los guiones gráficos son una manera WYSIWYG de crear aplicaciones de iOS y se admiten en Visual Studio en Mac y Windows. Para obtener más información sobre los guiones gráficos, consulte el documento [Introducción a los guiones gráficos](~/ios/user-interface/storyboards/index.md) . Los guiones gráficos también permiten editar los diseños de celda *de* la tabla, lo que simplifica el desarrollo con tablas y celdas.

Al configurar las propiedades de una vista de tabla en el diseñador de iOS, hay dos tipos de contenido de celda entre los que puede elegir: contenido del prototipo **dinámico** o **estático** .

<a name="Prototype_Content"></a>

## <a name="dynamic-prototype-content"></a>Contenido del prototipo dinámico

Un `UITableView` objeto con contenido de prototipo suele estar pensado para mostrar una lista de datos en la que la celda de prototipo (o las celdas, como se puede definir más de una) se reutilizan para cada elemento de la lista. No es necesario crear una instancia de las celdas, ya que se obtienen en el `GetView` método llamando al `DequeueReusableCell` método de `UITableViewSource` .

 <a name="Static_Content"></a>

## <a name="static-content"></a>Contenido estático

`UITableView`s con contenido estático permiten diseñar tablas directamente en la superficie de diseño. Las celdas se pueden arrastrar a la tabla y personalizar cambiando las propiedades y agregando controles.

 <a name="Creating_a_Storyboard-driven_app"></a>

## <a name="creating-a-storyboard-driven-app"></a>Creación de una aplicación controlada por guiones gráficos

El ejemplo StoryboardTable contiene una aplicación de maestro-detalle simple que usa ambos tipos de UITableView en un guion gráfico. En el resto de esta sección se describe cómo crear un pequeño ejemplo de lista de tareas pendientes que tendrá un aspecto similar al siguiente:

 [![Pantallas de ejemplo](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

La interfaz de usuario se compilará con un guion gráfico y ambas pantallas usarán un UITableView. En la pantalla principal se usa el *contenido del prototipo* para diseñar la fila y en la pantalla de detalles se usa *contenido estático* para crear un formulario de entrada de datos mediante diseños de celda personalizados.

## <a name="walkthrough"></a>Tutorial

Cree una nueva solución en Visual Studio con **(crear) nuevo proyecto... > aplicación de vista única (C#)** y llámela _StoryboardTables_.

 [![Cuadro de diálogo Crear un proyecto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

La solución se abrirá con algunos archivos de C# y un `Main.storyboard` archivo ya creado. Haga doble clic en el `Main.storyboard` archivo para abrirlo en el diseñador de iOS.

<a name="Modifying_the_Storyboard"></a>

## <a name="modifying-the-storyboard"></a>Modificar el guión gráfico

El guion gráfico se editará en tres pasos:

- En primer lugar, debe diseñar los controladores de vista necesarios y establecer sus propiedades.
- En segundo lugar, cree la interfaz de usuario arrastrando y colocando objetos en la vista
- Por último, agregue la clase UIKit requerida a cada vista y asigne un nombre a varios controles para que se pueda hacer referencia a ellos en el código.

Una vez completado el guión gráfico, se puede agregar código para hacer todo el trabajo.

<a name="Layout_The_View_Controllers"></a>

### <a name="layout-the-view-controllers"></a>Diseño de los controladores de vista

El primer cambio en el guión gráfico es eliminar la vista de detalles existente y reemplazarla por un UITableViewController. Siga estos pasos:

1. Seleccione la barra situada en la parte inferior del controlador de vista y elimínela.
2. Arrastre un **controlador de navegación** y un **controlador de vista de tabla** al guion gráfico desde el cuadro de herramientas. 
3. Cree un segue desde el controlador de vista raíz para el segundo controlador de vista de tabla que acaba de agregar. Para crear segue, control + arrastre *desde la celda detail* hasta la UITableViewController recién agregada. Elija la opción  **Mostrar** en  **selección de segue**. 
4. Seleccione el nuevo segue que creó y asígnele un identificador para hacer referencia a este segue en el código. Haga clic en segue y escriba `TaskSegue` para el  **identificador** en el  **Panel de propiedades**, como se indica a continuación:    
  [![Asignar nombres a segue en el panel de propiedades](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. A continuación, configure las dos vistas de tabla seleccionándola y usando el Panel de propiedades. Asegúrese de seleccionar ver y no ver controlador: puede usar el esquema del documento como ayuda para la selección.

6. Cambie el controlador de vista raíz para que sea  **contenido: prototipos dinámicos** (la vista en el superficie de diseño se etiquetará como  **contenido del prototipo** ):

    [![Establecer la propiedad de contenido en prototipos dinámicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. Cambie el nuevo **UITableViewController** para que sea  **contenido: celdas estáticas**. 

8. El nuevo UITableViewController debe tener el nombre de clase y el identificador establecido. Seleccione el controlador de vista y escriba _TaskDetailViewController_ para la **clase** en el **Panel de propiedades** : se creará un nuevo `TaskDetailViewController.cs` archivo en el panel de solución. Escriba **StoryboardID** como _detalle_, como se muestra en el ejemplo siguiente. Se usará más adelante para cargar esta vista en el código de C#:  

    [![Establecer el identificador de guión gráfico](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. La superficie de diseño de guion gráfico debe tener ahora el siguiente aspecto (el título del elemento de navegación del controlador de vista raíz se ha cambiado a "tablón de tareas"):

    [![Superficie de diseño](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  

<a name="Create_the_UI"></a>

### <a name="create-the-ui"></a>Creación de la interfaz de usuario

Ahora que las vistas y objetos segue están configuradas, es necesario agregar los elementos de la interfaz de usuario.

#### <a name="root-view-controller"></a>Controlador de vista raíz

En primer lugar, seleccione la celda prototype en el controlador de vista del maestro y establezca el **identificador** como _taskcell_, como se muestra a continuación. Se usará más adelante en el código para recuperar una instancia de este UITableViewCell:

 [![establecer el identificador de la celda](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

A continuación, deberá crear un botón que agregará nuevas tareas, como se muestra a continuación:

[![elemento de botón de barra en la barra de navegación](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Siga estos pasos: 

- Arrastre un **elemento de botón de barra** del cuadro de herramientas a la _parte derecha de la barra de navegación_.
- En el **Panel de propiedades**, en **elemento de botón de barra** , seleccione  **identificador: agregar** (para convertirlo en un *+* botón más). 
- Asígnele un nombre para que se pueda identificar en el código en una fase posterior. Tenga en cuenta que deberá asignar al controlador de vista raíz un nombre de clase (por ejemplo, **ItemViewController**) para que pueda establecer el nombre del elemento del botón de barra.

#### <a name="taskdetail-view-controller"></a>Controlador de vista TaskDetail

La vista de detalle requiere mucho más trabajo. Las celdas de la vista de tabla deben arrastrarse hasta la vista y, a continuación, se pueden rellenar con etiquetas, vistas de texto y botones. En la captura de pantalla siguiente se muestra la interfaz de usuario finalizada con dos secciones. Una sección tiene tres celdas, tres etiquetas, dos campos de texto y un modificador, mientras que la segunda sección tiene una celda con dos botones:

 [![diseño de vista de detalle](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Los pasos para crear el diseño completo son:

Seleccione la vista de tabla y abra el **Panel de propiedades**. Actualice las siguientes propiedades:

- **Secciones**: _2_ 
- **Estilo**: _agrupado_
- **Separador**: _ninguno_
- **Selección**: _no hay selección_

Seleccione la sección superior y, en **propiedades > sección** de la vista de tabla, cambie **las filas** a _3_, como se muestra a continuación:

 [![establecer la sección superior en tres filas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada celda, abra el **Panel de propiedades** y establezca:

- **Estilo**:  _personalizado_
- **Identificador**: elija un identificador único para cada celda (por ejemplo, "_title_", "_notas_", "_Done_").
- Arrastre los controles necesarios para generar el diseño que se muestra en la captura de pantalla (Coloque **UILabel**, **campo** y **UISwitch** en las celdas correctas y establezca las etiquetas adecuadamente, por ej. Título, notas y listo).

En la segunda sección, establezca **las filas** en _1_ y arrastre el controlador de tamaño inferior de la celda para que sea más alto.

- **Establezca el identificador**en un valor único (por ejemplo, "guardar"). 
- **Establezca fondo**:  _Borrar color_ .
- Arrastre dos botones a la celda y establezca sus títulos correctamente (por ejemplo, _Guardar_ y _eliminar_), como se muestra a continuación:

   [![establecer dos botones en la sección inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

En este momento, puede que también desee establecer restricciones en las celdas y controles para garantizar un diseño adaptable.

### <a name="adding-uikit-class-and-naming-controls"></a>Agregar clase UIKit y nombrar controles

Hay algunos pasos finales para crear nuestro guion gráfico. En primer lugar, debemos dar a cada uno de nuestros controles un nombre en **identidad > nombre** para que se puedan usar en el código más adelante. Asígnele el siguiente nombre:

- **Título campo** : _TitleText_
- **Notas campo** : _NotesText_
- **UISwitch** : _DoneSwitch_
- **Eliminar botón** : _DeleteButton_
- **Guardar botón** : _SaveButton_

<a name="Adding_Code"></a>

## <a name="adding-code"></a>Agregar código

El resto del trabajo se realizará en Visual Studio en Mac o Windows con C#. Tenga en cuenta que los nombres de propiedad utilizados en el código reflejan los establecidos en el tutorial anterior.

En primer lugar, queremos crear una `Chores` clase, que proporcionará una manera de obtener y establecer el valor de ID, Name, Notes y el booleano Done, de modo que podamos usar esos valores en toda la aplicación.

En la `Chores` clase, agregue el código siguiente:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

A continuación, cree una `RootTableSource` clase que herede de `UITableViewSource` . 

La diferencia entre esta y una vista de tabla que no es Storyboard es que el `GetView` método no necesita crear una instancia de las celdas: el `theDequeueReusableCell` método siempre devolverá una instancia de la celda prototipo (con el identificador coincidente).

El código siguiente procede del `RootTableSource.cs` archivo:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Para usar la `RootTableSource` clase, cree una nueva colección en el `ItemViewController` constructor de:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

En `ViewWillAppear` , pase la colección al origen y asígnela a la vista de tabla:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Si ejecuta la aplicación ahora, ahora se cargará la pantalla principal y se mostrará una lista de dos tareas. Cuando se toca una tarea, el segue definido por el guion gráfico hará que aparezca la pantalla de detalles, pero no se mostrará ningún dato en ese momento.

Para "enviar un parámetro" en un segue, invalide el `PrepareForSegue` método y establezca las propiedades de `DestinationViewController` ( `TaskDetailViewController` en este ejemplo). Se ha creado una instancia de la clase de controlador de vista de destino, pero aún no se muestra al usuario: Esto significa que puede establecer propiedades en la clase pero no modificar los controles de interfaz de usuario:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

En `TaskDetailViewController` el `SetTask` método, asigna sus parámetros a las propiedades para que se pueda hacer referencia a ellos en ViewWillAppear. No se pueden modificar las propiedades del control en `SetTask` porque puede que no exista cuando `PrepareForSegue` se llame a:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Segue abrirá ahora la pantalla de detalles y mostrará la información de la tarea seleccionada. Desafortunadamente, no hay ninguna implementación para los botones **Guardar** y **eliminar** . Antes de implementar los botones, agregue estos métodos a **ItemViewController.CS** para actualizar los datos subyacentes y cierre la pantalla de detalles:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

A continuación, deberá agregar el `TouchUpInside` controlador de eventos del botón al `ViewDidLoad` método de **TaskDetailViewController.CS**. La `Delegate` referencia de la propiedad a `ItemViewController` se creó específicamente para que podamos llamar a `SaveTask` y `DeleteTask` , que cierran esta vista como parte de su operación:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

La última parte de la funcionalidad restante que se va a compilar es la creación de nuevas tareas. En **ItemViewController.CS** , agregue un método que cree nuevas tareas y abra la vista de detalles. Para crear una instancia de una vista de un guion gráfico, use el `InstantiateViewController` método con `Identifier` para esa vista; en este ejemplo, será ' detail ':

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Por último, conecte el botón en la barra de navegación del método de **ItemViewController.CS** `ViewDidLoad` para llamarlo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Esto completa el ejemplo de guión gráfico: la aplicación finalizada tiene el siguiente aspecto:

[![Aplicación finalizada](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

En el ejemplo se muestra:

- Crear una tabla con contenido de prototipo donde se definen las celdas para reutilizarlas para mostrar listas de datos. 
- Crear una tabla con contenido estático para crear un formulario de entrada. Esto incluye cambiar el estilo de tabla y agregar secciones, celdas y controles de interfaz de usuario. 
- Cómo crear un segue e invalidar el  `PrepareForSegue` método para notificar a la vista de destino los parámetros que requiere. 
- Cargar vistas de guion gráfico directamente con el  `Storyboard.InstantiateViewController` método.

## <a name="related-links"></a>Vínculos relacionados

- [StoryboardTable (ejemplo)](/samples/xamarin/ios-samples/storyboardtable)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)