---
title: Controles estándar en Xamarin. Mac
description: En este artículo se explica cómo trabajar con los controles estándar de AppKit, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin. Mac. Describe cómo agregarlos a una interfaz con Interface Builder e interactuar con ellos en el código.
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 18ce7eb4dcd68596027f739579fd5a4b70497829
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772703"
---
# <a name="standard-controls-in-xamarinmac"></a>Controles estándar en Xamarin. Mac

_En este artículo se explica cómo trabajar con los controles estándar de AppKit, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin. Mac. Describe cómo agregarlos a una interfaz con Interface Builder e interactuar con ellos en el código._

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a los mismos controles de AppKit que un desarrollador que trabaja en *Objective-C* y *Xcode* . Dado que Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener los controles AppKit (o, opcionalmente, C# crearlos directamente en el código).

Los controles AppKit son los elementos de la interfaz de usuario que se usan para crear la interfaz de usuario de la aplicación de Xamarin. Mac. Están formados por elementos como botones, etiquetas, campos de texto, casillas y controles segmentados y producen acciones instantáneas o resultados visibles cuando un usuario los manipula.

[![](standard-controls-images/intro01.png "La pantalla principal de la aplicación de ejemplo")](standard-controls-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con los controles de AppKit en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) también, en él se explican `Export` los `Register` comandos y que se usan para C# conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introducción a los controles y vistas

macOS (anteriormente conocido como Mac OS X) proporciona un conjunto estándar de controles de interfaz de usuario a través del marco AppKit. Están formados por elementos como botones, etiquetas, campos de texto, casillas y controles segmentados y producen acciones instantáneas o resultados visibles cuando un usuario los manipula.

Todos los controles AppKit tienen una apariencia estándar integrada que será adecuada para la mayoría de los usos, algunos especifican una apariencia alternativa para su uso en un área de marco de ventana o en un contexto de _efecto de intensidad_ , como en un área de la barra lateral o en un centro de notificaciones. widget.

Apple sugiere las siguientes directrices al trabajar con controles AppKit:

- Evite mezclar tamaños de control en la misma vista.
- En general, evite cambiar el tamaño de los controles verticalmente.
- Use la fuente del sistema y el tamaño de texto adecuado dentro de un control.
- Use el espaciado adecuado entre los controles.

Para obtener más información, es agradable ver la sección [acerca de los controles y las vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) de las directrices de la [interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Usar controles en un marco de ventana

Hay un subconjunto de controles AppKit que incluyen un estilo de presentación que les permite incluirlos en el área de marco de una ventana. Para ver un ejemplo, consulte la barra de herramientas de la aplicación de correo:

[![](standard-controls-images/mailapp.png "Un marco de ventana Mac")](standard-controls-images/mailapp.png#lightbox)

- **Botón de textura redondeada** : `NSButton` con un estilo de `NSTexturedRoundedBezelStyle`.
- **Control segmentado redondeado con textura** -a `NSSegmentedControl` con un estilo de `NSSegmentStyleTexturedRounded`.
- **Control segmentado redondeado con textura** -a `NSSegmentedControl` con un estilo de `NSSegmentStyleSeparated`.
- **Menú emergente** `NSPopUpButton` con textura redonda: con un estilo de. `NSTexturedRoundedBezelStyle`
- Menú desplegable de **textura redondeada** : `NSPopUpButton` con un estilo de `NSTexturedRoundedBezelStyle`.
- **Barra de búsqueda** : `NSSearchField`un.

Apple sugiere las siguientes directrices al trabajar con controles AppKit en un marco de ventana:

- No use estilos de control específicos del marco de ventana en el cuerpo de la ventana.
- No use controles de cuerpo de ventana ni estilos en el marco de la ventana.

Para obtener más información, es agradable ver la sección [acerca de los controles y las vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) de las directrices de la [interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Crear una interfaz de usuario en Interface Builder

Cuando se crea una nueva aplicación de Cocoa de Xamarin. Mac, se obtiene una ventana en blanco de forma predeterminada. Esta ventana se define en un `.storyboard` archivo que se incluye automáticamente en el proyecto. Para editar el diseño de Windows, en el **Explorador de soluciones**, haga doble `Main.storyboard` clic en el archivo:

[![](standard-controls-images/edit01.png "Seleccionar el guión gráfico principal en el Explorador de soluciones")](standard-controls-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en la Interface Builder de Xcode:

[![](standard-controls-images/edit02.png "Editar el guion gráfico en Xcode")](standard-controls-images/edit02.png#lightbox)

Para crear la interfaz de usuario, arrastre los elementos de la interfaz de usuario (controles AppKit) desde el **Inspector de biblioteca** al editor de la **interfaz** en Interface Builder. En el ejemplo siguiente, un control de **vista de División vertical** ha sido medicamento del **Inspector de biblioteca** y se ha colocado en la ventana del editor de la **interfaz**:

[![](standard-controls-images/edit03.png "Seleccionar una vista dividida de la biblioteca")](standard-controls-images/edit03.png#lightbox)

Para obtener más información sobre cómo crear una interfaz de usuario en Interface Builder, consulte la documentación de [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) .

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Ajustar el tamaño y la posición

Una vez que se ha incluido un control en la interfaz de usuario, use el **Editor de restricciones** para establecer su ubicación y tamaño escribiendo los valores manualmente y controle el modo en que el control se coloca y se ajusta automáticamente cuando se cambia el tamaño de la ventana o la vista primaria:

[![](standard-controls-images/edit04.png "Establecer las restricciones")](standard-controls-images/edit04.png#lightbox)

Use las comillas **rojas** alrededor del exterior del cuadro **Autoresizing** para ajustar un _control a una_ ubicación determinada (x, y). Por ejemplo: 

[![](standard-controls-images/edit05.png "Editar una restricción")](standard-controls-images/edit05.png#lightbox)

Especifica que el control seleccionado (en el editor de la**interfaz**de **vista** & de jerarquía) se bloqueará en la ubicación superior y derecha de la ventana o vista a medida que se cambie el tamaño o se mueva. 

Otros elementos de las propiedades del control editor como alto y ancho:

[![](standard-controls-images/edit06.png "Establecer el alto")](standard-controls-images/edit06.png#lightbox)

También puede controlar la alineación de los elementos con restricciones mediante el editor de **alineación**:

[![](standard-controls-images/edit07.png "El editor de alineación")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> A diferencia de iOS, donde (0,0) es la esquina superior izquierda de la pantalla, en macOS (0,0) es la esquina inferior izquierda. Esto se debe a que macOS usa un sistema de coordenadas matemático con los valores numéricos que aumentan de valor hacia arriba y hacia la derecha. Debe tener esto en cuenta al colocar los controles de AppKit en una interfaz de usuario.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Establecer una clase personalizada

Hay ocasiones en las que se trabaja con controles AppKit que necesitará para la subclase y el control existente y para crear su propia versión personalizada de esa clase. Por ejemplo, la definición de una versión personalizada de la lista de origen:

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Donde la `[Register("SourceListView")]` instrucción expone la `SourceListView` clase a Objective-C para que se pueda usar en Interface Builder. Para obtener más información, consulte la sección [exposición C# de clases o métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en el que `Register` se `Export` explican los comandos y que se C# usan para conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

Con el código anterior en su lugar, puede arrastrar un control AppKit, del tipo base que está extendiendo, a la superficie de diseño (en el ejemplo siguiente, una **lista de origen**), cambiar al **Inspector de identidades** y establecer la **clase personalizada** en el nombre que expuesto a Objective-C (ejemplo `SourceListView`):

[![](standard-controls-images/edit10.png "Establecer una clase personalizada en Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Exposición de salidas y acciones

Antes de que se pueda tener acceso a un C# control AppKit en el código, debe exponerse como una **salida** o una **acción**. Para ello, seleccione el control determinado en la jerarquía de la **interfaz** o en el editor de la **interfaz** y cambie a la **vista asistente** (Asegúrese `.h` de que tiene seleccionado el de la ventana para editarlo):

[![](standard-controls-images/edit11.png "Seleccionar el archivo correcto para editarlo")](standard-controls-images/edit11.png#lightbox)

Control: arrastre desde el control AppKit hasta el archivo `.h` de entrega para empezar a crear una **salida** o **acción**:

[![](standard-controls-images/edit12.png "Arrastrar para crear una salida o acción")](standard-controls-images/edit12.png#lightbox)

Seleccione el tipo de exposición que desea crear y asigne un **nombre**a la **salida** o a la **acción** : 

[![](standard-controls-images/edit13.png "Configuración de la salida o acción")](standard-controls-images/edit13.png#lightbox)

Para obtener más información sobre cómo trabajar con **salidas** y **acciones**, consulte la sección [tomas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) de nuestra [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentación.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizar los cambios con Xcode

Al volver a Visual Studio para Mac de Xcode, los cambios que haya realizado en Xcode se sincronizarán automáticamente con el proyecto de Xamarin. Mac.

Si selecciona `SplitViewController.designer.cs` en el **Explorador de soluciones** podrá ver cómo se ha conectado la **salida** y la **acción** en nuestro C# código:

[![](standard-controls-images/sync01.png "Sincronizar cambios con Xcode")](standard-controls-images/sync01.png#lightbox)

Observe cómo la definición en el `SplitViewController.designer.cs` archivo:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Alinear con la definición en el `MainWindow.h` archivo en Xcode:

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Como puede ver, Visual Studio para Mac escucha los cambios en el `.h` archivo y, a continuación, sincroniza automáticamente los cambios en el archivo respectivo `.designer.cs` para exponerlos a la aplicación. También puede observar que `SplitViewController.designer.cs` es una clase parcial, por lo que Visual Studio para Mac no tiene que modificar `SplitViewController.cs` , lo que sobrescribiría cualquier cambio que se haya realizado en la clase.

Normalmente, nunca tendrá que abrir el `SplitViewController.designer.cs` propio, ya que se presentó aquí solo con fines educativos.

> [!IMPORTANT]
> En la mayoría de los casos, Visual Studio para Mac verá automáticamente los cambios realizados en Xcode y los sincronizará con el proyecto de Xamarin. Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Trabajar con botones

AppKit proporciona varios tipos de botón que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [botones](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Un ejemplo de los distintos tipos de botón")](standard-controls-images/buttons01.png#lightbox)

Si un botón se ha expuesto a través de una **salida**, el siguiente código responderá a que se está presionando:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

En el caso de los botones que se han expuesto `public partial` a través de **las acciones**, se creará automáticamente un método con el nombre elegido en Xcode. Para responder a la **acción**, complete el método parcial en la clase en la que se definió la **acción** . Por ejemplo:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Para los botones que tienen un estado (como **on** y **OFF**), el estado se puede comprobar o establecer con `State` la propiedad en `NSCellStateValue` la enumeración. Por ejemplo:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Donde `NSCellStateValue` puede ser:

- **Activado** : el botón se inserta o se selecciona el control (por ejemplo, una marca de verificación en una casilla).
- **Desactivado** : el botón no está presionado o el control no está seleccionado.
- **Mezclado** : una combinación de Estados **de on** y **OFF** .

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marcar un botón como predeterminado y establecer el equivalente de clave

En el caso de los botones que haya agregado a un diseño de interfaz de usuario, puede marcar ese botón como el botón _predeterminado_ que se activará cuando el usuario presione la tecla **Return/entrar** en el teclado. En macOS, este botón recibirá de forma predeterminada un color de fondo azul.

Para establecer un botón como predeterminado, selecciónelo en la Interface Builder de Xcode. A continuación, en el **Inspector de atributos**, seleccione el campo **clave equivalente** y presione la tecla **Intro/entrar** :

[![](standard-controls-images/buttons03.png "Editar la clave equivalente")](standard-controls-images/buttons03.png#lightbox)

Igualmente, puede asignar cualquier secuencia de teclas que se pueda usar para activar el botón mediante el teclado en lugar del mouse. Por ejemplo, si presiona las teclas de comando C en la imagen anterior.

Cuando se ejecuta la aplicación y la ventana con el botón es clave y se centra, si el usuario presiona el comando C, la acción del botón se activará (como si el usuario hubiera hecho clic en el botón).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Trabajar con casillas y botones de radio

AppKit proporciona varios tipos de casillas y grupos de botones de radio que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [botones](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Un ejemplo de los tipos de casilla disponibles")](standard-controls-images/buttons02.png#lightbox)

Las casillas y los botones de radio (expuestos a través de las **salidas**) tienen un estado (como **on** y **OFF**), el estado se `State` puede comprobar o `NSCellStateValue` establecer con la propiedad en la enumeración. Por ejemplo:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Donde `NSCellStateValue` puede ser:

- **Activado** : el botón se inserta o se selecciona el control (por ejemplo, una marca de verificación en una casilla).
- **Desactivado** : el botón no está presionado o el control no está seleccionado.
- **Mezclado** : una combinación de Estados **de on** y **OFF** .

Para seleccionar un botón de un grupo de botones de radio, exponga el botón de radio para seleccionar como una `State` salida y establecer su propiedad. Por ejemplo:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Para obtener una colección de botones de radio que actúe como grupo y Controle automáticamente el estado seleccionado, cree una nueva **acción** y adjunte cada botón del grupo a ella:

![](standard-controls-images/buttons04.png "Crear una nueva acción")

A continuación, asigne un `Tag` único botón de radio en el **Inspector de atributos**:

![](standard-controls-images/buttons05.png "Editar una etiqueta de botón de radio")

Guarde los cambios y vuelva a Visual Studio para Mac, agregue el código para controlar la **acción** a la que están asociados todos los botones de radio:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Puede usar la `Tag` propiedad para ver qué botón de radio está seleccionado.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Trabajar con controles de menú

AppKit proporciona varios tipos de controles de menú que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [controles de menú](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Controles de menú de ejemplo")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Proporcionar datos de control de menú

Los controles de menú disponibles para macOS pueden establecerse para rellenar la lista desplegable desde una lista interna (que puede estar predefinida en Interface Builder o rellenarse mediante código) o proporcionando su propio origen de datos externo personalizado.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Trabajar con datos internos

Además de definir elementos en Interface Builder, los controles de menú (como `NSComboBox`) proporcionan un conjunto completo de métodos que permiten agregar, editar o eliminar los elementos de la lista interna que mantienen:

- `Add`: Agrega un nuevo elemento al final de la lista.
- `GetItem`: Devuelve el elemento en el índice especificado.
- `Insert`: Inserta un nuevo elemento en la lista en la ubicación especificada.
- `IndexOf`: Devuelve el índice del elemento especificado.
- `Remove`: Quita el elemento especificado de la lista.
- `RemoveAll`: Quita todos los elementos de la lista.
- `RemoveAt`: Quita el elemento en el índice especificado.
- `Count`: Devuelve el número de elementos de la lista.

> [!IMPORTANT]
> Si usa un origen de datos externo (`UsesDataSource = true`), la llamada a cualquiera de los métodos anteriores producirá una excepción.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Trabajar con un origen de datos externo

En lugar de usar los datos internos integrados para proporcionar las filas del control de menú, puede usar opcionalmente un origen de datos externo y proporcionar su propia memoria auxiliar para los elementos (por ejemplo, una base de datos de SQLite).

Para trabajar con un origen de datos externo, creará una instancia del origen de datos del control de menú`NSComboBoxDataSource` (por ejemplo) e invalidará varios métodos para proporcionar los datos necesarios:

- `ItemCount`: Devuelve el número de elementos de la lista.
- `ObjectValueForItem`: Devuelve el valor del elemento para un índice determinado.
- `IndexOfItem`: Devuelve el índice para el valor de proporcionar elemento.
- `CompletedString`: Devuelve el primer valor de elemento coincidente para el valor del elemento con tipo parcial. Solo se llama a este método si se ha habilitado autocompletar (`Completes = true`).

Para obtener más información, consulte la sección [bases de datos y cuadros combinados](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) del documento [trabajar con bases](~/mac/app-fundamentals/databases.md) de datos.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustar la apariencia de la lista

Los siguientes métodos están disponibles para ajustar la apariencia del control de menú:

- `HasVerticalScroller`-Si `true`es, el control mostrará una barra de desplazamiento vertical. 
- `VisibleItems`: Ajusta el número de elementos que se muestran cuando se abre el control. El valor predeterminado es cinco (5).
- `IntercellSpacing`-Ajustar la cantidad de espacio alrededor de un elemento determinado proporcionando un `NSSize` objeto `Width` donde especifica los márgenes izquierdo y `Height` derecho, y especifica el espacio antes y después de un elemento.
- `ItemHeight`: Especifica el alto de cada elemento de la lista.

En el caso de los tipos `NSPopupButtons`desplegables de, el primer elemento de menú proporciona el título para el control. Por ejemplo: 

[![](standard-controls-images/menu02.png "Un control de menú de ejemplo")](standard-controls-images/menu02.png#lightbox)

Para cambiar el título, exponga este elemento como una **salida** y use código similar al siguiente:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipular los elementos seleccionados

Los siguientes métodos y propiedades le permiten manipular los elementos seleccionados en la lista del control de menú:

- `SelectItem`: Selecciona el elemento en el índice especificado.
- `Select`-Seleccionar el valor del elemento especificado.
- `DeselectItem`-Anula la selección del elemento en el índice especificado.
- `SelectedIndex`: Devuelve el índice del elemento seleccionado actualmente.
- `SelectedValue`: Devuelve el valor del elemento seleccionado actualmente.

Utilice el `ScrollItemAtIndexToTop` para presentar el elemento en el índice especificado en la parte superior de la lista `ScrollItemAtIndexToVisible` y para desplazarse a la lista hasta que el elemento en el índice especificado esté visible.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Respuesta a eventos

Los controles de menú proporcionan los siguientes eventos para responder a la interacción del usuario:

- `SelectionChanged`-Se llama a cuando el usuario ha seleccionado un valor de la lista.
- `SelectionIsChanging`-Se llama a antes de que el nuevo elemento seleccionado por el usuario se convierta en la selección activa.
- `WillPopup`-Se llama a antes de que se muestre la lista desplegable de elementos.
- `WillDismiss`-Se llama a antes de que se cierre la lista desplegable de elementos.

En `NSComboBox` el caso de los controles, incluyen todos los mismos eventos `NSTextField`que, como, `Changed` por ejemplo, el evento al que se llama cada vez que el usuario edita el valor del texto en el cuadro combinado.

Opcionalmente, puede responder a los elementos de menú de datos internos definidos en Interface Builder que se están seleccionando adjuntando el elemento a una **acción** y usando código como el siguiente para responder a la **acción** que desencadena el usuario:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Para obtener más información sobre cómo trabajar con menús y controles de menú, vea la documentación sobre los [menús](~/mac/user-interface/menu.md) y el [botón emergente y las listas desplegables](~/mac/user-interface/menu.md) .

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Trabajar con controles de selección

AppKit proporciona varios tipos de controles de selección que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [controles de selección](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Ejemplos de controles de selección")](standard-controls-images/select01.png#lightbox)

Hay dos maneras de realizar un seguimiento cuando un control de selección tiene interacción del usuario, para lo que se expone como una **acción**. Por ejemplo:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

O adjuntando un **delegado** al `Activated` evento. Por ejemplo:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Para establecer o leer el valor de un control de selección, utilice `IntValue` la propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Los controles de especialidad (como el buen color y la imagen) tienen propiedades específicas para sus tipos de valor. Por ejemplo:

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker` Tiene las siguientes propiedades para trabajar directamente con la fecha y la hora:

- **DateValue** : el valor de fecha y hora actual como `NSDate`un.
- **Local** : la ubicación del usuario como `NSLocal`.
- **TimeInterval** : el valor de hora como `Double`.
- **TimeZone** : la zona horaria del usuario como `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Trabajar con controles de indicador

AppKit proporciona varios tipos de controles de indicador que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección de [controles de indicador](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) de las directrices de interfaz humana de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Controles de indicador de ejemplo")](standard-controls-images/level01.png#lightbox)

Hay dos maneras de realizar un seguimiento cuando un control indicador tiene interacción con el usuario, ya sea mediante la exposición de una **acción** o una **salida** y la `Activated` Asociación de un delegado al evento. Por ejemplo:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Para leer o establecer el valor del control indicador, utilice la `DoubleValue` propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

Los indicadores de progreso indeterminados y asincrónicos se deben animar cuando se muestran. Use el `StartAnimation` método para iniciar la animación cuando se muestren. Por ejemplo:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Al llamar `StopAnimation` al método, se detendrá la animación.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Trabajar con controles de texto

AppKit proporciona varios tipos de controles de texto que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [controles de texto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) de las [directrices de interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Controles de texto de ejemplo")](standard-controls-images/text01.png#lightbox)

En el caso de`NSTextField`los campos de texto (), los siguientes eventos se pueden usar para realizar un seguimiento de la interacción del usuario:

- **Changed** : se desencadena cada vez que el usuario cambia el valor del campo. Por ejemplo, en cada carácter escrito.
- **EditingBegan** : se activa cuando el usuario selecciona el campo para su edición.
- **EditingEnded** : cuando el usuario presiona la tecla entrar en el campo o deja el campo.

Utilice la `StringValue` propiedad para leer o establecer el valor del campo. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

En el caso de los campos que muestran o modifican valores numéricos, puede utilizar la `IntValue` propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Un `NSTextView` proporciona un área de presentación y edición de texto con todas las características con formato integrado. Como, utilice la `StringValue` propiedad para leer o establecer el valor del área. `NSTextField`

Para ver un ejemplo de un ejemplo complejo de cómo trabajar con vistas de texto en una aplicación de Xamarin. Mac, consulte la [aplicación de ejemplo SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Trabajar con vistas de contenido

AppKit proporciona varios tipos de vistas de contenido que se pueden usar en el diseño de la interfaz de usuario. Para obtener más información, consulte la sección [vistas de contenido](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) de las directrices de la interfaz humana de Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Vista de contenido de ejemplo")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Un elemento flotante es un elemento de la interfaz de usuario transitorio que proporciona funcionalidad que está directamente relacionada con un control específico o un área en pantalla. Un elemento flotante flota por encima de la ventana que contiene el control o área con la que está relacionado, y su borde incluye una flecha para indicar el punto desde el que surgió.

Para crear un elemento flotante, haga lo siguiente:

1. Abra el `.storyboard` archivo de la ventana a la que desea agregar un elemento flotante haciendo doble clic en él en el **Explorador de soluciones**
2. Arrastre un **controlador de vista** desde el **Inspector de biblioteca** hasta el editor de la **interfaz**: 

    [![](standard-controls-images/content02.png "Seleccionar un controlador de vista de la biblioteca")](standard-controls-images/content02.png#lightbox)
3. Defina el tamaño y el diseño de la **vista personalizada**: 

    [![](standard-controls-images/content04.png "Edición del diseño")](standard-controls-images/content04.png#lightbox)
4. Control: haga clic y arrastre desde el origen del menú emergente hasta el **controlador de vista**: 

    [![](standard-controls-images/content05.png "Arrastrar para crear un segue")](standard-controls-images/content05.png#lightbox)
5. Seleccione **elemento flotante** en el menú emergente: 

    [![](standard-controls-images/content06.png "Establecimiento del tipo segue")](standard-controls-images/content06.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Vistas de pestaña

Las vistas de pestaña están formadas por una lista de pestañas (que tiene un aspecto similar a un control segmentado), combinadas con un conjunto de vistas que se denominan _paneles_. Cuando el usuario selecciona una nueva pestaña, se mostrará el panel que está asociado a ella. Cada panel contiene su propio conjunto de controles.

Al trabajar con una vista de pestaña en la Interface Builder de Xcode, use el **Inspector de atributo** para establecer el número de pestañas:

[![](standard-controls-images/content08.png "Editar el número de pestañas")](standard-controls-images/content08.png#lightbox)

Seleccione cada una de las pestañas de la jerarquía de la **interfaz** para establecer su **título** y agregar elementos de interfaz de usuario a su **Panel**:

[![](standard-controls-images/content09.png "Edición de las pestañas en Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controles AppKit de enlace de datos

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

La codificación de valores clave (KVC) es un mecanismo para tener acceso indirectamente a las propiedades de un objeto, mediante claves (cadenas con formato especial) para identificar propiedades en lugar de tener acceso a ellas a`get/set`través de variables de instancia o métodos de descriptor de acceso (). Mediante la implementación de los descriptores de acceso compatibles con el código clave-valor en la aplicación de Xamarin. Mac, obtiene acceso a otras características de macOS como la observación de clave-valor (KVO), el enlace de datos, los enlaces de cacao y la creación de scripts.

Para obtener más información, consulte la sección [enlace de datos simple](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) de nuestra documentación sobre el [enlace de datos y el código de valor de clave](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con los controles estándar de AppKit, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin. Mac. Se trata de agregarlas a un diseño de la interfaz de usuario en la Interface Builder de Xcode, que las expone al código mediante salidas y acciones, y C# al trabajar con controles AppKit en el código.

## <a name="related-links"></a>Vínculos relacionados

- [MacControls (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Acerca de los controles y las vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
