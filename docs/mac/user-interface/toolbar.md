---
title: Barras de herramientas en Xamarin. Mac
description: En este artículo se describe cómo trabajar con barras de herramientas en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener barras de herramientas en Xcode y Interface Builder, exponerlas al código y trabajar con ellas mediante programación.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cd2490bfad880d128f5eaeebd4aac58ad3a4d8fa
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772724"
---
# <a name="toolbars-in-xamarinmac"></a>Barras de herramientas en Xamarin. Mac

_En este artículo se describe cómo trabajar con barras de herramientas en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener barras de herramientas en Xcode y Interface Builder, exponerlas al código y trabajar con ellas mediante programación._

Los desarrolladores de Xamarin. Mac que trabajan con Visual Studio para Mac tienen acceso a los mismos controles de interfaz de usuario disponibles para los desarrolladores de macOS que trabajan con Xcode, incluido el control de barra de herramientas. Dado que Xamarin. Mac se integra directamente con Xcode, es posible usar los Interface Builder de Xcode para crear y mantener los elementos de la barra de herramientas. Estos elementos de la barra de herramientas también C#se pueden crear en.

Las barras de herramientas de macOS se agregan a la sección superior de una ventana y proporcionan un acceso fácil a los comandos relacionados con su funcionalidad. Los usuarios de una aplicación pueden ocultar, mostrar o personalizar las barras de herramientas, y pueden presentar los elementos de la barra de herramientas de varias maneras.

En este artículo se describen los aspectos básicos del trabajo con barras de herramientas y elementos de la barra de herramientas en una aplicación de Xamarin. Mac. 

Antes de continuar, lea el artículo de [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente la [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y las secciones de [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que abarca conceptos clave y técnicas que se usarán en esta guía.

Eche un vistazo a la sección [exposición de C# clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) . Se explican los atributos `Register` y `Export` que se C# usan para conectar clases a clases Objective-C.

## <a name="introduction-to-toolbars"></a>Introducción a las barras de herramientas

Cualquier ventana de una aplicación macOS puede incluir una barra de herramientas:

![Ventana de ejemplo con una barra de herramientas](toolbar-images/info01.png "Ventana de ejemplo con una barra de herramientas")

Las barras de herramientas proporcionan una manera fácil para que los usuarios de la aplicación accedan rápidamente a características importantes o usadas con frecuencia. Por ejemplo, una aplicación de edición de documentos podría proporcionar elementos de la barra de herramientas para establecer el color del texto, cambiar la fuente o imprimir el documento actual.

Las barras de herramientas pueden mostrar los elementos de tres maneras:

1. **Icono y texto** 

     ![Una barra de herramientas con iconos y texto](toolbar-images/info02.png "Una barra de herramientas con iconos y texto")

2. **Solo icono** 

     ![Barra de herramientas solo de iconos](toolbar-images/info03.png "Barra de herramientas solo de iconos")

3. **Solo texto** 

     ![Barra de herramientas de solo texto](toolbar-images/info04.png "Barra de herramientas de solo texto")

Cambie entre estos modos haciendo clic con el botón secundario en la barra de herramientas y seleccionando un modo de presentación en el menú contextual:

![Menú contextual de una barra de herramientas](toolbar-images/info05.png "Menú contextual de una barra de herramientas")

Use el mismo menú para mostrar la barra de herramientas en un tamaño más pequeño:

![Barra de herramientas con iconos pequeños](toolbar-images/info06.png "Barra de herramientas con iconos pequeños")

El menú también permite personalizar la barra de herramientas:

![Cuadro de diálogo que se usa para personalizar una barra de herramientas](toolbar-images/info07.png "Cuadro de diálogo que se usa para personalizar una barra de herramientas")

Al configurar una barra de herramientas en la Interface Builder de Xcode, un desarrollador puede proporcionar elementos de barra de herramientas adicionales que no forman parte de su configuración predeterminada. Los usuarios de la aplicación pueden personalizar la barra de herramientas, agregando y quitando estos elementos predefinidos según sea necesario. Por supuesto, la barra de herramientas se puede restablecer a su configuración predeterminada.

La barra de herramientas se conecta automáticamente al menú **Ver** , que permite a los usuarios ocultarla, mostrarla y personalizarla:

![Elementos relacionados con la barra de herramientas en el menú Ver](toolbar-images/info08.png "Elementos relacionados con la barra de herramientas en el menú Ver")

Para más información, consulte la documentación sobre la [funcionalidad de menú integrada](~/mac/user-interface/menu.md) .

Además, si la barra de herramientas está configurada correctamente en Interface Builder, la aplicación conservará automáticamente las personalizaciones de la barra de herramientas en varios inicios de la aplicación.

En las secciones siguientes de esta guía se describe cómo crear y mantener barras de herramientas con Interface Builder de Xcode y cómo trabajar con ellas en el código.

## <a name="setting-a-custom-main-window-controller"></a>Establecer un controlador de ventana principal personalizado

Para exponer elementos de la C# interfaz de usuario al código mediante salidas y acciones, la aplicación de Xamarin. Mac debe usar un controlador de ventana personalizado:

1. Abra el guión gráfico de la aplicación en la Interface Builder de Xcode.
2. Seleccione el controlador de ventana en la superficie de diseño.
3. Cambie al **Inspector de identidad** y escriba "WindowController" como **nombre de clase**: 

    [![Establecer un nombre de clase personalizado para el controlador de ventana](toolbar-images/windowcontroller01.png "Establecer un nombre de clase personalizado para el controlador de ventana")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos.
5. Se agregará un archivo **WindowController.CS** al proyecto en el **Panel de solución** en Visual Studio para Mac: 

    ![Seleccionar WindowController.cs en el Panel de solución](toolbar-images/windowcontroller02.png "Seleccionar WindowController.cs en el Panel de solución")

6. Vuelva a abrir el guion gráfico en la Interface Builder de Xcode.
7. El archivo **WindowController. h** estará disponible para su uso: 

    [![El archivo WindowController. h](toolbar-images/windowcontroller03.png "El archivo WindowController. h")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Crear y mantener barras de herramientas en Xcode

Las barras de herramientas se crean y mantienen con los Interface Builder de Xcode. Para agregar una barra de herramientas a una aplicación, edite el guión gráfico principal de la aplicación (en este caso, **Main. Storyboard**) haciendo doble clic en él en el **Panel de solución**:

![Abrir Main. Storyboard en el Panel de solución](toolbar-images/edit01.png "Abrir Main. Storyboard en el Panel de solución")

En el **Inspector de biblioteca**, escriba "herramienta" en el **cuadro de búsqueda** para que sea más fácil ver todos los elementos de la barra de herramientas disponibles:

![El inspector de biblioteca, filtrado para mostrar los elementos de la barra de herramientas](toolbar-images/edit02.png "El inspector de biblioteca, filtrado para mostrar los elementos de la barra de herramientas")

Arrastre una barra de herramientas a la ventana del editor de la **interfaz**. Con la barra de herramientas seleccionada, configure su comportamiento estableciendo las propiedades en el **Inspector de atributos**:

![Inspector de atributos de una barra de herramientas](toolbar-images/edit04.png "Inspector de atributos de una barra de herramientas")

Están disponibles las propiedades siguientes:

1. **Mostrar** : controla si la barra de herramientas muestra iconos, texto o ambos.
2. **Visible en el inicio** : si se selecciona, la barra de herramientas es visible de forma predeterminada.
3. **Personalizable** : si se selecciona, los usuarios pueden editar y personalizar la barra de herramientas.
4. **Separator** : si se selecciona, una línea horizontal fina separa la barra de herramientas del contenido de la ventana.
5. **Tamaño** : establece el tamaño de la barra de herramientas.
6. **Autoguardar** : si se selecciona esta opción, la aplicación conservará los cambios de configuración de la barra de herramientas de un usuario entre los lanzamientos de la aplicación.

Seleccione la opción **Autoguardar** y deje las demás propiedades con su configuración predeterminada. 

Después de abrir la barra de herramientas en la jerarquía de la **interfaz**, abra el cuadro de diálogo de personalización seleccionando un elemento de la barra de herramientas:

![Personalización de la barra de herramientas](toolbar-images/edit05.png "Personalización de la barra de herramientas")

Use este cuadro de diálogo para establecer las propiedades de los elementos que ya forman parte de la barra de herramientas, para diseñar la barra de herramientas predeterminada de la aplicación y para proporcionar elementos de la barra de herramientas adicionales para que un usuario seleccione al personalizar la barra de herramientas. Para agregar elementos a la barra de herramientas, arrástrelos desde el **Inspector de biblioteca**:

![Inspector de biblioteca](toolbar-images/edit06.png "Inspector de biblioteca")

Se pueden agregar los siguientes elementos de la barra de herramientas:

- **Elemento de la barra de herramientas imagen** : elemento de la barra de herramientas con una imagen personalizada como icono.
- **Elemento de la barra de herramientas espacio flexible** : espacio flexible utilizado para justificar los elementos de la barra de herramientas subsiguientes. Por ejemplo, uno o varios elementos de la barra de herramientas seguidos de un elemento de barra de herramientas de espacio flexible y otro elemento de la barra de herramientas anclarían el último elemento al lado derecho de la barra de herramientas.
- **Elemento de la barra de herramientas espacio** : espacio fijo entre los elementos de la barra de herramientas
- **Elemento de barra de herramientas separador** : un separador visible entre dos o más elementos de la barra de herramientas, para la agrupación
- **Personalizar el elemento** de la barra de herramientas: permite a los usuarios personalizar la barra de herramientas.
- **Imprimir elemento** de la barra de herramientas: permite a los usuarios imprimir el documento abierto
- **Elemento de la barra de herramientas mostrar colores** : muestra el selector de colores estándar del sistema: 

     ![Selector de colores del sistema](toolbar-images/edit07.png "Selector de colores del sistema")

- **Mostrar el elemento** de la barra de herramientas de fuente: muestra el cuadro de diálogo estándar de fuente del sistema: 

     ![Selector de fuentes](toolbar-images/edit08.png "Selector de fuentes")

> [!IMPORTANT]
> Como se verá más adelante, muchos controles de interfaz de usuario de coco estándar, como campos de búsqueda, controles segmentados y controles deslizantes horizontales, también se pueden agregar a una barra de herramientas.

### <a name="adding-an-item-to-a-toolbar"></a>Agregar un elemento a una barra de herramientas

Para agregar un elemento a una barra de herramientas, seleccione la barra de herramientas de la jerarquía de la **interfaz** y haga clic en uno de sus elementos, lo que hará que aparezca el cuadro de diálogo de personalización. A continuación, arrastre un nuevo elemento desde el **Inspector de biblioteca** hasta el área de elementos de la barra de **herramientas permitida** :

![Elementos de la barra de herramientas permitidos en el cuadro de diálogo de personalización de la barra](toolbar-images/add01.png "Elementos de la barra de herramientas permitidos en el cuadro de diálogo de personalización de la barra")

Para asegurarse de que un nuevo elemento forma parte de la barra de herramientas predeterminada, arrástrelo hasta el área de elementos de la **barra de herramientas predeterminada** : 

![Cambiar el orden de un elemento de la barra de herramientas arrastrando](toolbar-images/add02.png "Cambiar el orden de un elemento de la barra de herramientas arrastrando")

Para reordenar los elementos predeterminados de la barra de herramientas, arrástrelos hacia la izquierda o la derecha.

A continuación, use el **Inspector de atributos** para establecer las propiedades predeterminadas del elemento:

![Personalización de un elemento de barra de herramientas mediante el inspector de atributos](toolbar-images/add03.png "Personalización de un elemento de barra de herramientas mediante el inspector de atributos")

Están disponibles las propiedades siguientes:

- **Nombre de imagen** : imagen que se va a usar como icono para el elemento
- **Etiqueta** : texto que se va a mostrar para el elemento en la barra de herramientas
- **Etiqueta** de la paleta: texto que se va a mostrar para el elemento en el área de elementos de la **barra de herramientas permitida**
- **Tag** : un identificador único opcional que ayuda a identificar el elemento en el código.
- **Identifier** : define el tipo de elemento de la barra de herramientas. Un valor personalizado se puede utilizar para seleccionar un elemento de la barra de herramientas en el código.
- **Selectable** : si se activa, el elemento actuará como un botón de activación/desactivación.

> [!IMPORTANT]
> Agregue un elemento al área **elementos de barra de herramientas permitidos** , pero no a la barra de herramientas predeterminada para proporcionar opciones de personalización para los usuarios. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Agregar otros controles de interfaz de usuario a una barra de herramientas

Varios elementos de la interfaz de usuario de coco, como los campos de búsqueda y los controles segmentados, también se pueden agregar a una barra de herramientas.

Para probar esto, abra la barra de herramientas de la jerarquía de la **interfaz** y seleccione un elemento de la barra de herramientas para abrir el cuadro de diálogo de personalización. Arrastre un **campo de búsqueda** desde el **Inspector de biblioteca** hasta el área de elementos de la barra de **herramientas permitida** :

![Usar el cuadro de diálogo de personalización de la barra de herramientas](toolbar-images/add05.png "Usar el cuadro de diálogo de personalización de la barra de herramientas")

Desde aquí, utilice Interface Builder para configurar el campo de búsqueda y exponerlo al código a través de una acción o una salida.

## <a name="built-in-toolbar-item-support"></a>Compatibilidad de elementos de barra de herramientas integrada

Varios elementos de la interfaz de usuario de coco interactúan con los elementos de la barra de herramientas estándar Por ejemplo, arrastre una **vista de texto** a la ventana de la aplicación y colóquela para rellenar el área de contenido:

[![Agregar una vista de texto a la aplicación](toolbar-images/edit09.png "Agregar una vista de texto a la aplicación")](toolbar-images/edit09-large.png#lightbox)

Guarde el documento, vuelva a Visual Studio para Mac para sincronizarlo con Xcode, ejecute la aplicación, escriba algún texto, selecciónelo y haga clic en el elemento de la barra de herramientas **colores** . Tenga en cuenta que la vista de texto funciona automáticamente con el selector de colores:

![Funcionalidad de la barra de herramientas integrada con una vista de texto y un selector de colores](toolbar-images/edit10.png "Funcionalidad de la barra de herramientas integrada con una vista de texto y un selector de colores")

## <a name="using-images-with-toolbar-items"></a>Usar imágenes con elementos de barra de herramientas

Mediante el uso de un elemento de la **barra de herramientas de imagen**, cualquier imagen de mapa de bits agregada a la carpeta de **recursos** (y dada una acción de compilación de **recurso de agrupación**) se puede mostrar en la barra de herramientas como un icono:

1. En Visual Studio para Mac, en el **Panel de solución**, haga clic con el botón derecho en la carpeta **recursos** y seleccione **Agregar**  > **Agregar archivos**.
2. En el cuadro de diálogo **Agregar archivos** , vaya a las imágenes deseadas, selecciónelas y haga clic en el botón **abrir** : 

    [![Seleccionar imágenes para agregar](toolbar-images/edit11.png "Seleccionar imágenes para agregar")](toolbar-images/edit11-large.png#lightbox)

3. Seleccione **copiar**, Active **usar la misma acción para todos los archivos seleccionados**y haga clic en **Aceptar**:

    ![Selección de la acción de copia para las imágenes agregadas](toolbar-images/edit12.png "Selección de la acción de copia para las imágenes agregadas")

4. En el **Panel de solución**, haga doble clic en **MainWindow. Xib** para abrirlo en Xcode.

5. Seleccione la barra de herramientas en la jerarquía de la **interfaz** y haga clic en uno de sus elementos para abrir el cuadro de diálogo de personalización.

6. Arrastre un **elemento** de la barra de herramientas de la **biblioteca** al área de elementos de la **barra de herramientas permitidos** de la barra de herramientas: 

    ![Un elemento de la barra de herramientas de imagen agregado al área de elementos de barra de herramientas permitidos](toolbar-images/edit14.png "Un elemento de la barra de herramientas de imagen agregado al área de elementos de barra de herramientas permitidos")

7. En el **Inspector de atributos**, seleccione la imagen que acaba de agregar en Visual Studio para Mac: 

    ![Establecer una imagen personalizada para un elemento de la barra de herramientas](toolbar-images/edit15.png "Establecer una imagen personalizada para un elemento de la barra de herramientas")

8. Establezca la **etiqueta** en "papelera" y la **etiqueta** de la paleta en "borrar documento": 

    ![Establecer la etiqueta del elemento de la barra de herramientas y la etiqueta de la paleta](toolbar-images/edit16.png "Establecer la etiqueta del elemento de la barra de herramientas y la etiqueta de la paleta")

9. Arrastre un **elemento de barra de herramientas de separador** desde el **Inspector de biblioteca** hasta el área de **elementos de barra de herramientas permitidos** de la barra de herramientas 

    [![Un elemento de barra de herramientas de separador agregado al área de elementos de la barra de herramientas permitida](toolbar-images/edit17.png "Un elemento de barra de herramientas de separador agregado al área de elementos de la barra de herramientas permitida")](toolbar-images/edit17-large.png#lightbox)

10. Arrastre el elemento separador y el elemento "papelera" al área de elementos de la **barra de herramientas predeterminada** y establezca el orden de los elementos de la barra de herramientas de izquierda a derecha como se indica a continuación (colores, fuentes, separador, papelera, espacio flexible, imprimir): 

    ![Los elementos de la barra de herramientas predeterminada](toolbar-images/edit18.png "Los elementos de la barra de herramientas predeterminada")

11. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Ejecute la aplicación para comprobar que la nueva barra de herramientas se muestra de forma predeterminada:

![Barra de herramientas con elementos predeterminados personalizados](toolbar-images/edit19.png "Barra de herramientas con elementos predeterminados personalizados")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Exponer elementos de la barra de herramientas con salidas y acciones

Para tener acceso a una barra de herramientas o un elemento de la barra de herramientas en el código, se debe adjuntar a una salida o acción:

1. En el **Panel de solución**, haga doble clic en **Main. Storyboard** para abrirlo en Xcode.
2. Asegúrese de que la clase personalizada "WindowController" se ha asignado al controlador de ventana principal en el **Inspector de identidad**:

    [![Usar el inspector de identidad para establecer una clase personalizada para el controlador de ventana](toolbar-images/edit20a.png "Usar el inspector de identidad para establecer una clase personalizada para el controlador de ventana")](toolbar-images/edit20a-large.png#lightbox)

3. A continuación, seleccione el elemento de la barra de herramientas en la jerarquía de la **interfaz**: 

    ![Seleccionar el elemento de la barra de herramientas en la jerarquía de la interfaz](toolbar-images/edit20.png "Seleccionar el elemento de la barra de herramientas en la jerarquía de la interfaz")  

4. Abra la **vista asistente**, seleccione el archivo **WindowController. h** y arrastre el control desde el elemento de la barra de herramientas hasta el archivo **WindowController. h** .
5. Establezca el tipo de **conexión** en **acción**, escriba "trashDocument" como **nombre**y haga clic en el botón **conectar** : 

    [![Configurar una acción para un elemento de la barra de herramientas](toolbar-images/edit23.png "Configurar una acción para un elemento de la barra de herramientas")](toolbar-images/edit23-large.png#lightbox)

6. Exponga la **vista de texto** como una toma denominada "documentEditor" en el archivo **ViewController. h** : 

    [![Configuración de una salida para la vista de texto](toolbar-images/edit24.png "Configuración de una salida para la vista de texto")](toolbar-images/edit24-large.png#lightbox)

7. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

En Visual Studio para Mac, edite el archivo **ViewController.CS** y agregue el código siguiente:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Después, edite el archivo **WindowController.CS** y agregue el código siguiente en la parte inferior de la clase `WindowController`:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Al ejecutar la aplicación, el elemento de la barra de herramientas de la **papelera** estará activo:

![Barra de herramientas con un elemento de la papelera activa](toolbar-images/edit25.png "Barra de herramientas con un elemento de la papelera activa")

Tenga en cuenta que ahora se puede usar el elemento de la barra de herramientas de la **papelera** para eliminar texto.

## <a name="disabling-toolbar-items"></a>Deshabilitar elementos de la barra de herramientas

Para deshabilitar un elemento de una barra de herramientas, cree una clase de `NSToolbarItem` personalizada e invalide el método `Validate`. A continuación, en Interface Builder, asigne el tipo personalizado al elemento que desea habilitar o deshabilitar.

Para crear una clase de `NSToolbarItem` personalizada, haga clic con el botón derecho en el proyecto y seleccione **agregar**  > **nuevo archivo..** .. Seleccione **General**  > **clase vacía**, escriba "ActivatableItem" como **nombre**y haga clic en el botón **nuevo** : 

![Agregar una clase vacía en Visual Studio para Mac](toolbar-images/custom01.png "Agregar una clase vacía en Visual Studio para Mac")

Después, edite el archivo **ActivatableItem.CS** para que se lea de la siguiente manera:

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Haga doble clic en **Main. Storyboard** para abrirlo en Xcode. Seleccione el elemento de la barra de herramientas de la **papelera** creado anteriormente y cambie su clase a "ActivatableItem" en el **Inspector de identidad**:

![Establecer una clase personalizada para un elemento de la barra de herramientas](toolbar-images/custom02.png "Establecer una clase personalizada para un elemento de la barra de herramientas")

Cree una toma llamada `trashItem` para el elemento de la barra de herramientas de la **papelera** . Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode. Por último, Abra **MainWindow.CS** y actualice el método `AwakeFromNib` para leer de la siguiente manera:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Ejecute la aplicación y observe que el elemento **Trash** ahora está deshabilitado en la barra de herramientas:

![Barra de herramientas con un elemento de basura inactivo](toolbar-images/custom03.png "Barra de herramientas con un elemento de basura inactivo")

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con barras de herramientas y elementos de la barra de herramientas en una aplicación de Xamarin. Mac. Se describe cómo crear y mantener barras de herramientas en la Interface Builder de Xcode, cómo algunos controles de interfaz de usuario funcionan automáticamente con elementos de la barra de herramientas C# , cómo trabajar con barras de herramientas en el código y cómo habilitar y deshabilitar elementos de la barra de herramientas.

## <a name="related-links"></a>Vínculos relacionados

- [MacToolbar (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactoolbar)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Directrices de la interfaz humana para las barras de herramientas](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introducción a las barras de herramientas](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
