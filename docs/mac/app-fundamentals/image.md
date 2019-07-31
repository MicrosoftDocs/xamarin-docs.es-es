---
title: Imágenes en Xamarin. Mac
description: En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin. Mac. Describe la creación y el mantenimiento de las imágenes necesarias para crear el icono de la aplicación y el C# uso de imágenes en el código y en los Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 197600f713527ed79d9a52768d367589d9edf2fa
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646883"
---
# <a name="images-in-xamarinmac"></a>Imágenes en Xamarin. Mac

_En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin. Mac. Describe la creación y el mantenimiento de las imágenes necesarias para crear el icono de la aplicación y el C# uso de imágenes en el código y en los Interface Builder de Xcode._

## <a name="overview"></a>Información general

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a las mismas herramientas de imagen e icono que un desarrollador que trabaja en *Objective-C* y *Xcode* .

Hay varias maneras de usar los recursos de imagen dentro de una aplicación macOS (antes conocida como Mac OS X). Desde simplemente mostrar una imagen como parte de la interfaz de usuario de la aplicación a, asignarla a un control de interfaz de usuario, como una barra de herramientas o un elemento de lista de origen, para proporcionar iconos, Xamarin. Mac facilita la tarea de agregar grandes ilustraciones a las aplicaciones macOS de las siguientes maneras: : 

- **Elementos** de la interfaz de usuario: las imágenes se pueden mostrar como fondo o como parte de la aplicación en una`NSImageView`vista de imagen ().
- **Button** : las imágenes se pueden mostrar en botones`NSButton`().
- **Celda de imagen** : como parte de un control basado en`NSTableView` tabla `NSOutlineView`(o), las imágenes se pueden usar en una`NSImageCell`celda de imagen ().
- **Elemento de barra de herramientas** : las imágenes se pueden agregar`NSToolbar`a una barra de herramientas ()`NSToolbarItem`como un elemento de la barra de herramientas de imagen ().
- **Icono de lista de origen** : como parte de una lista de origen ( `NSOutlineView`con un formato especial).
- **Icono de aplicación** : una serie de imágenes se pueden agrupar en un `.icns` conjunto y usar como el icono de la aplicación. Consulte nuestra documentación sobre el icono de la [aplicación](~/mac/deploy-test/app-icon.md) para obtener más información.

Además, macOS proporciona un conjunto de imágenes predefinidas que se pueden usar en toda la aplicación.

[![Ejemplo de ejecución de la aplicación](image-images/intro01.png "Ejemplo de ejecución de la aplicación")](image-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con imágenes e iconos en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Agregar imágenes a un proyecto de Xamarin. Mac

Cuando se agrega una imagen para su uso en una aplicación de Xamarin. Mac, hay varios lugares y formas en que el desarrollador puede incluir un archivo de imagen en el origen del proyecto:

- **Árbol de proyecto principal [desusado]** -las imágenes se pueden agregar directamente al árbol de proyectos. Cuando se llama a imágenes almacenadas en el árbol del proyecto principal desde el código, no se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Carpeta recursos [en desuso]** -la carpeta **recursos** especiales es para cualquier archivo que se convertirá en parte del paquete de la aplicación, como el icono, la pantalla de inicio o las imágenes generales (o cualquier otra imagen o archivo que el desarrollador desee agregar). Cuando se llama a imágenes almacenadas en la carpeta **recursos** desde el código, al igual que las imágenes almacenadas en el árbol del proyecto principal, no se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage.ImageNamed("tags.png")`.
- **Carpeta o subcarpeta personalizada [en desuso]** : el desarrollador puede Agregar una carpeta personalizada al árbol de origen de proyectos y almacenar allí las imágenes. La ubicación donde se agrega el archivo puede estar anidada en una subcarpeta para facilitar la organización del proyecto. Por ejemplo, si el desarrollador agrega una `Card` carpeta al proyecto y una subcarpeta de `Hearts` a esa carpeta, almacene una imagen **Jack. png** en la `Hearts` carpeta, `NSImage.ImageNamed("Card/Hearts/Jack.png")` cargará la imagen en tiempo de ejecución.
- **Conjuntos de imágenes del catálogo de recursos [preferido]** -agregado en OS X el Capitan, los **conjuntos de imágenes** de catálogos de recursos contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de escala para la aplicación. En lugar de confiar en el nombre de archivo de recursos **@1x** de **@2x** imagen (,).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a un conjunto de imágenes del catálogo de recursos

Como se indicó anteriormente, un **conjunto de imágenes** de catálogos de recursos contiene todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de escala para la aplicación. En lugar de confiar en el nombre de archivo de los recursos de imagen (consulte las imágenes independientes de la resolución y la nomenclatura de la imagen anterior), los **conjuntos de imágenes** usan el editor de recursos para especificar la imagen que pertenece a cada dispositivo o resolución.

1. En el **Panel de solución**, haga doble clic en el archivo **assets. xcassets** para abrirlo para su edición: 

    ![Seleccionar assets. xcassets](image-images/imageset01.png "Seleccionar assets. xcassets")
2. Haga clic con el botón derecho en la **lista activos** y seleccione **nuevo conjunto de imágenes**: 

    [![Agregar un nuevo conjunto de imágenes](image-images/imageset02.png "Agregar un nuevo conjunto de imágenes")](image-images/imageset02-large.png#lightbox)
3. Seleccione el nuevo conjunto de imágenes y se mostrará el editor: 

    [![Seleccionar el nuevo conjunto de imágenes](image-images/imageset03.png "Seleccionar el nuevo conjunto de imágenes")](image-images/imageset03-large.png#lightbox)
4. Desde aquí, podemos arrastrar imágenes para cada uno de los diferentes dispositivos y resoluciones necesarios. 
5. Haga doble clic en el **nombre** del nuevo conjunto de imágenes en la **lista de recursos** para editarlo: 

    [![Editar el nombre del conjunto de imágenes](image-images/imageset04.png "Editar el nombre del conjunto de imágenes")](image-images/imageset04-large.png#lightbox)
    
Una clase de **Vector** especial que se ha agregado a los **conjuntos de imágenes** que nos permite incluir una imagen de vector con formato _PDF_ en el Casset en su lugar, incluidos los archivos de mapa de bits individuales en las diferentes resoluciones. Con este método, se proporciona un archivo de vector único para **@1x** la resolución (con formato de archivo PDF vector) y **@2x** las **@3x** versiones y del archivo se generarán en tiempo de compilación y se incluirán en el paquete de la aplicación.

[![La interfaz del editor de conjuntos de imágenes](image-images/imageset05.png "La interfaz del editor de conjuntos de imágenes")](image-images/imageset05-large.png#lightbox)

Por ejemplo, si incluye un `MonkeyIcon.pdf` archivo como vector de un catálogo de recursos con una resolución de 150 PX x 150 PX, los siguientes recursos de mapa de bits se incluirán en el lote de aplicaciones final cuando se compiló:

1. **MonkeyIcon@1x.png** -150 PX x 150 PX resolución.
2. **MonkeyIcon@2x.png** -300 PX x 300 PX resolución.
3. **MonkeyIcon@3x.png** -450px x 450px resolución.

Se deben tener en cuenta las siguientes consideraciones al usar imágenes vectoriales de PDF en catálogos de recursos:

- Esto no es totalmente compatible con vectores, ya que el PDF se rasterizará en un mapa de bits en tiempo de compilación y los mapas de bits incluidos en la aplicación final.
- No se puede ajustar el tamaño de la imagen una vez que se ha establecido en el catálogo de recursos. Si intenta cambiar el tamaño de la imagen (ya sea en el código o mediante las clases de diseño automático y tamaño), la imagen se distorsionará como cualquier otro mapa de bits.

Al usar un **conjunto de imágenes** en la Interface Builder de Xcode, simplemente puede seleccionar el nombre del conjunto en la lista desplegable del **Inspector de atributos**:

![Selección de un conjunto de imágenes en la Interface Builder de Xcode](image-images/imageset06.png "Selección de un conjunto de imágenes en la Interface Builder de Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Agregar nuevas colecciones de activos

Al trabajar con imágenes en catálogos de activos, puede haber ocasiones en las que desee crear una nueva colección, en lugar de agregar todas las imágenes a la colección **assets. xcassets** . Por ejemplo, al diseñar recursos a petición.

Para agregar un nuevo catálogo de activos al proyecto:

1. Haga clic con el botón derecho en el proyecto en el **Panel de solución** y seleccione **Agregar** > **nuevo archivo..** .
2. Seleccione**Catálogo de activos**de **Mac** > , escriba un **nombre** para la colección y haga clic en el botón **nuevo** : 

    ![Adición de un nuevo catálogo de activos](image-images/asset01.png "Adición de un nuevo catálogo de activos")

Aquí puede trabajar con la colección de la misma manera que la colección **assets. xcassets** predeterminada que se incluye automáticamente en el proyecto.


### <a name="adding-images-to-resources"></a>Agregar imágenes a recursos

> [!IMPORTANT]
> Apple ha dejado de trabajar con este método de trabajo con imágenes en una aplicación de macOS. En su lugar, debe usar [conjuntos de imágenes del catálogo de recursos](#asset-catalogs) para el administrador de las imágenes de la aplicación.

Antes de poder usar un archivo de imagen en la aplicación de Xamarin. Mac ( C# ya sea en código o desde Interface Builder) debe incluirse en la carpeta de **recursos** del proyecto como un **recurso**de agrupación. Para agregar un archivo a un proyecto, haga lo siguiente:

1. Haga clic con el botón derecho en la carpeta **recursos** del proyecto en el **Panel de solución** y seleccione **Agregar** > **Agregar archivos..** .: 

    ![Agregar un archivo](image-images/add01.png "Agregar un archivo")
2. En el cuadro de diálogo **Agregar archivos** , seleccione los archivos de imagen que desea agregar al proyecto `BundleResource` , seleccione para la **acción invalidar compilación** y haga clic en el botón **abrir** :

    [![Seleccionar los archivos que se van a agregar](image-images/add02.png "Seleccionar los archivos que se van a agregar")](image-images/add02-large.png#lightbox)
3. Si los archivos no están ya en la carpeta **recursos** , se le preguntará si desea **copiar**, trasladar o **vincular** los archivos. Seleccione cada una de las cuales se adapte a sus necesidades; normalmente se copiará:

    ![Seleccionar la acción agregar](image-images/add04.png "Seleccionar la acción agregar")
4. Los nuevos archivos se incluirán en el proyecto y se leerán para su uso: 

    Los ![nuevos archivos de imagen agregados al panel de solución] Los (image-images/add03.png "nuevos archivos de imagen agregados al panel de solución")
5. Repita el proceso para los archivos de imagen necesarios.

Puede usar cualquier archivo PNG, jpg o PDF como imagen de origen en la aplicación de Xamarin. Mac. En la siguiente sección, veremos cómo agregar versiones de alta resolución de nuestras imágenes e iconos para admitir equipos Mac basados en retina.

> [!IMPORTANT]
> Si va a agregar imágenes a la carpeta **recursos** , puede dejar la **acción reemplazar compilación** establecida en **predeterminada**. La acción de compilación predeterminada para esta carpeta `BundleResource`es.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Proporcionar versiones de alta resolución de todos los recursos de gráficos de la aplicación

Cualquier recurso gráfico que agregue a una aplicación de Xamarin. Mac (iconos, controles personalizados, cursores personalizados, material gráfico personalizado, etc.) debe tener versiones de alta resolución además de sus versiones de resolución estándar. Esto es necesario para que la aplicación tenga un aspecto óptimo cuando se ejecute en un equipo Mac equipado con la visualización de retina.


### <a name="adopt-the-2x-naming-convention"></a>Adopción de @2x la Convención de nomenclatura

> [!IMPORTANT]
> Apple ha dejado de trabajar con este método de trabajo con imágenes en una aplicación de macOS. En su lugar, debe usar [conjuntos de imágenes del catálogo de recursos](#asset-catalogs) para el administrador de las imágenes de la aplicación.

Al crear las versiones estándar y de alta resolución de una imagen, siga esta Convención de nomenclatura para el par de imágenes al incluirlas en el proyecto de Xamarin. Mac:

- Resolución  - estándar**ImageName. FileName-Extension** (ejemplo: **Tags. png**)
- **Alta resolución**   -  **tags@2x.png** (**ejemplo:)ImageName@2x.filename-extension**

Cuando se agrega a un proyecto, aparecería de la siguiente manera:

![Archivos de imagen en el panel de solución](image-images/add03.png "Archivos de imagen en el panel de solución")

Cuando se asigna una imagen a un elemento de la interfaz de usuario en Interface Builder simplemente tendrá que elegir el archivo en _ImageName_ **.** _nombre de archivo: formato de extensión_ (ejemplo: **Tags. png**). Lo mismo para usar una imagen en C# el código, seleccionará el archivo en el archivo _ImageName_ **.** _nombre de archivo: formato de extensión_ .

Cuando se ejecuta la aplicación de Xamarin. Mac en un equipo Mac, el _ImageName_ **.** _nombre de archivo:_ la imagen del formato de extensión se usará en las **ImageName@2x.filename-extension** pantallas de resolución estándar. la imagen se seleccionará automáticamente en los equipos Mac de las bases de visualización de retina.


## <a name="using-images-in-interface-builder"></a>Usar imágenes en Interface Builder

Cualquier recurso de imagen que haya agregado a la carpeta de **recursos** en el proyecto de Xamarin. Mac y haya establecido la acción de compilación en **BundleResource** se mostrará automáticamente en Interface Builder y se puede seleccionar como parte de un elemento de la interfaz de usuario (si controla imágenes).

Para usar una imagen en Interface Builder, haga lo siguiente:

1. Agregue una imagen a la carpeta de **recursos** con una acción de `BundleResource` **compilación** de: 

     ![Un recurso de imagen en el panel de solución](image-images/ib00.png "Un recurso de imagen en el panel de solución")
2. Haga doble clic en el archivo **Main. Storyboard** para abrirlo y editarlo en Interface Builder: 

     [![Edición del guión gráfico principal](image-images/ib01.png "Edición del guión gráfico principal")](image-images/ib01-large.png#lightbox)
3. Arrastre un elemento de la interfaz de usuario que toma imágenes en la superficie de diseño (por ejemplo, un elemento de la **barra de herramientas de imagen**): 

     ![Editar un elemento de la barra de herramientas](image-images/ib02.png "Editar un elemento de la barra de herramientas")
4. Seleccione la imagen que ha agregado a la carpeta **recursos** en la lista desplegable **nombre de imagen** : 

     [![Seleccionar una imagen para un elemento de la barra de herramientas](image-images/ib03.png "Seleccionar una imagen para un elemento de la barra de herramientas")](image-images/ib03-large.png#lightbox)
5. La imagen seleccionada se mostrará en la superficie de diseño: 

     ![La imagen que se muestra en el editor de la barra de herramientas](image-images/ib04.png "La imagen que se muestra en el editor de la barra de herramientas")
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Los pasos anteriores funcionan para cualquier elemento de la interfaz de usuario que permita establecer su propiedad de imagen en el **Inspector de atributos**. De nuevo, si ha incluido una **@2x** versión del archivo de imagen, se usará automáticamente en los equipos Mac basados en la pantalla retina.

> [!IMPORTANT]
> Si la imagen no está disponible en la lista desplegable **nombre de imagen** , cierre el proyecto. Storyboard en Xcode y vuelva a abrirlo desde Visual Studio para Mac. Si la imagen sigue sin estar disponible, asegúrese de que la acción `BundleResource` de **compilación** es y que la imagen se ha agregado a la carpeta **recursos** .

## <a name="using-images-in-c-code"></a>Usar imágenes en C# el código

Al cargar una imagen en memoria mediante C# el uso de código en la aplicación de Xamarin. Mac, la imagen se `NSImage` almacenará en un objeto. Si el archivo de imagen se ha incluido en el paquete de aplicaciones de Xamarin. Mac (incluido en los recursos), use el código siguiente para cargar la imagen:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

En el código anterior se usa `ImageNamed("...")` el método estático `NSImage` de la clase para cargar la imagen especificada en la memoria desde la carpeta de **recursos** , si no se `null` encuentra la imagen, se devolverá. Al igual que las imágenes asignadas en Interface Builder, si **@2x** ha incluido una versión del archivo de imagen, se usará automáticamente en los equipos Mac basados en la pantalla retina.

Para cargar imágenes fuera del paquete de la aplicación (desde el sistema de archivos Mac), use el código siguiente:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

En función del diseño de la aplicación macOS, puede haber ocasiones en las que necesite personalizar un icono o imagen dentro de la interfaz de usuario para que coincida con un cambio de la combinación de colores (por ejemplo, según las preferencias del usuario).

Para lograr este efecto, cambie el _modo de representación_ del recurso de imagen a **imagen de plantilla**:

[![Configuración de una imagen de plantilla](image-images/templateimage01.png "Configuración de una imagen de plantilla")](image-images/templateimage01-large.png#lightbox)

En el Interface Builder de Xcode, asigne el recurso de imagen a un control de IU:

![Selección de una imagen en la Interface Builder de Xcode](image-images/templateimage02.png "Selección de una imagen en la Interface Builder de Xcode")

O bien, puede establecer el origen de la imagen en el código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Agregue la siguiente función pública al controlador de vistas:

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> Especialmente con la llegada del modo oscuro en MacOS Mojave, es importante evitar la API cuando `LockFocus` Reating los `NSImage` objetos representados de forma personalizada. Dichas imágenes se convierten en estáticas y no se actualizan automáticamente para que tengan en cuenta los cambios de la densidad o la apariencia.
>
> Al emplear el mecanismo basado en el controlador anterior, la rerepresentación de condiciones dinámicas se realizará automáticamente cuando `NSImage` se hospede el, por ejemplo, `NSImageView`en.

Por último, para dar tinte a una imagen de plantilla, llame a esta función con la imagen para colorear:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Usar imágenes con vistas de tabla

Para incluir una imagen como parte de la celda en una `NSTableView`, deberá cambiar la forma en que el método de `NSTableViewDelegate's` `GetViewForItem` la vista de tabla devuelve los datos para usar `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Hay algunas líneas de interés aquí. En primer lugar, para las columnas que queremos incluir una imagen, creamos una `NSImageView` nueva del tamaño y la ubicación necesarios, también creamos un `NSTextField` nuevo y colocamos su posición predeterminada en función de si usamos o no una imagen:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

En segundo lugar, es necesario incluir la nueva vista de imagen y el campo de texto `NSTableCellView`en el elemento primario:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por último, es necesario indicar al campo de texto que se puede reducir y ampliar con la celda de la vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Ejemplo de visualización de una imagen en una aplicación](image-images/tables01.png "Ejemplo de visualización de una imagen en una aplicación")](image-images/tables01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con vistas de tabla, consulte nuestra documentación de [vistas de tabla](~/mac/user-interface/table-view.md) .

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Usar imágenes con vistas de esquema

Para incluir una imagen como parte de la celda en una `NSOutlineView`, deberá cambiar la forma en que el método de `NSTableViewDelegate's` `GetView` la vista esquema devuelve los datos para usar `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Hay algunas líneas de interés aquí. En primer lugar, para las columnas que queremos incluir una imagen, creamos una `NSImageView` nueva del tamaño y la ubicación necesarios, también creamos un `NSTextField` nuevo y colocamos su posición predeterminada en función de si usamos o no una imagen:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

En segundo lugar, es necesario incluir la nueva vista de imagen y el campo de texto `NSTableCellView`en el elemento primario:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por último, es necesario indicar al campo de texto que se puede reducir y ampliar con la celda de la vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Ejemplo de una imagen que se muestra en una vista de esquema](image-images/outline01.png "Ejemplo de una imagen que se muestra en una vista de esquema")](image-images/outline01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con las vistas de esquema, consulte la documentación de las [vistas de esquema](~/mac/user-interface/outline-view.md) .


## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con imágenes e iconos en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de las imágenes, cómo usar imágenes e iconos en la Interface Builder de Xcode y cómo trabajar con imágenes e iconos en C# el código.



## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Directrices de la interfaz humana de macOS X](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Acerca de la alta resolución para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
