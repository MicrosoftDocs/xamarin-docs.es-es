---
title: Aspectos básicos de Xamarin. Android Designer
description: En este tema se presentan las características de Xamarin. Android Designer, se explica cómo iniciar el diseñador, se describe el Superficie de diseño y se detalla cómo usar el panel Propiedades para editar las propiedades del widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/05/2018
ms.openlocfilehash: 2d5f20326de56bca77dd8fdd742515e003f996e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029514"
---
# <a name="xamarinandroid-designer-basics"></a>Aspectos básicos de Xamarin. Android Designer

_En este tema se presentan las características de Xamarin. Android Designer, se explica cómo iniciar el diseñador, se describe el Superficie de diseño y se detalla cómo usar el panel Propiedades para editar las propiedades del widget._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Iniciar el diseñador

El diseñador se inicia automáticamente cuando se crea un diseño o se puede iniciar haciendo doble clic en un archivo de diseño existente. Por ejemplo, si hace doble clic en **activity_main. axml** en la carpeta **recursos > diseño** , se cargará el diseñador tal y como se verá en esta captura de pantalla:

[pantalla del diseñador de![en Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Del mismo modo, puede Agregar un nuevo diseño haciendo clic con el botón secundario en la carpeta **diseño** del **Explorador de soluciones** y seleccionando **Agregar > nuevo elemento... > Diseño de Android**:

[![cuadro de diálogo Agregar nuevo elemento](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Esto crea un nuevo archivo de diseño **. axml** y lo carga en el diseñador.

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

## <a name="designer-features"></a>Características del diseñador

El diseñador se compone de varias secciones que admiten sus diversas características, como se muestra en la siguiente captura de pantalla:

[![diagrama de paneles del diseñador](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Al editar un diseño en el diseñador, se usan las siguientes características para crear y dar forma a un diseño:

- **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario, ya que proporciona una representación editable de cómo aparecerá el diseño en el dispositivo. El **superficie de diseño** se muestra en el **Panel de diseño** (con la **barra de herramientas del diseñador** colocada encima).

- El **Panel origen** &ndash; proporciona una vista del origen XML subyacente que corresponde al diseño presentado en el **superficie de diseño**.

- &ndash; de la **barra de herramientas del diseñador** muestra una lista de selectores: configuración de **dispositivo**, **versión**, **tema**, diseño y barra de acciones. La **barra de herramientas del diseñador** también incluye iconos para iniciar el editor de temas y habilitar la cuadrícula de diseño del material.

- En el **cuadro de herramientas** &ndash; se proporciona una lista de widgets y diseños que se pueden arrastrar y colocar en el **superficie de diseño**.

- En la **ventana propiedades** &ndash; se enumeran las propiedades del widget seleccionado para su visualización y edición.

- **Esquema del documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento del árbol para que se seleccione en el **superficie de diseño**. Además, al hacer clic en un elemento del árbol, se cargan las propiedades del elemento en la ventana **propiedades** .

## <a name="design-surface"></a>Superficie de diseño

El diseñador permite arrastrar y colocar widgets desde el cuadro de herramientas hasta el **superficie de diseño**. Al interactuar con widgets en el diseñador (agregando nuevos widgets o cambiando la posición de los existentes), se muestran las líneas verticales y horizontales para marcar los puntos de inserción disponibles. En el ejemplo siguiente, se está arrastrando un nuevo widget de `Button` al **superficie de diseño**:

[![líneas de inserción de ejemplo en Superficie de diseño](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Además, se pueden copiar widgets: puede usar copiar y pegar para copiar un widget, o puede arrastrar y colocar un widget existente mientras presiona la tecla <kbd>Ctrl</kbd> .

### <a name="designer-toolbar"></a>Barra de herramientas del diseñador

La **barra de herramientas del diseñador** (situada sobre el **superficie de diseño**) presenta los selectores de configuración y los menús de herramientas:

[![diagrama de la barra de herramientas del diseñador](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

La **barra de herramientas del diseñador** proporciona acceso a las siguientes características:

- El **selector de diseño alternativo** &ndash; permite seleccionar diferentes versiones de diseño.

- El **selector de dispositivos** &ndash; define un conjunto de calificadores (como el tamaño de la pantalla, la resolución y la disponibilidad del teclado) asociados a un dispositivo determinado. También puede Agregar y eliminar nuevos dispositivos.

- El **selector de versión de android** &ndash; la versión de Android que tiene como destino el diseño. El diseñador representará el diseño según la versión de Android seleccionada.

- **Selector de temas** &ndash; selecciona el tema de la interfaz de usuario para el diseño.

- **Selector de configuración** &ndash; selecciona la configuración del dispositivo, como *vertical* u *horizontal*.

- **Opciones del calificador de recursos** &ndash; abre un cuadro de diálogo que presenta menús desplegables para seleccionar el *idioma*, el modo de la *interfaz de usuario*, el *modo nocturno*y las opciones de *pantalla redondeada* .

- **Barra de acciones configuración** &ndash; configura los valores de barra de acciones para el diseño.

- El **Editor de temas** &ndash; abre el editor de *temas*, lo que permite personalizar los elementos del tema seleccionado.

- **Cuadrícula de diseño de materiales** &ndash; habilita o deshabilita la *cuadrícula de diseño del material*. El elemento de menú desplegable adyacente a la cuadrícula de diseño del material abre un cuadro de diálogo que le permite personalizar la cuadrícula.

Cada una de estas características se explica con más detalle en estos temas:

- [Opciones de visualización y calificadores de recursos](~/android/user-interface/android-designer/resource-qualifiers.md) proporciona información detallada sobre el **selector de dispositivos**, el selector de **versiones de Android**, el selector de **temas**, el **selector de configuración**y los requisitos de **recursos Opciones**y **barra de acciones configuración**.

- En las [vistas de diseño alternativas](~/android/user-interface/android-designer/alternative-layout-views.md) se explica cómo usar el **selector de diseño alternativo**.

- [Las características de diseño de material de Xamarin. Android Designer](~/android/user-interface/android-designer/material-design-features.md) proporcionan información general detallada sobre el **Editor de temas** y la cuadrícula de **diseño de materiales**.

### <a name="context-menu-commands"></a>Comandos del menú contextual

Un menú contextual está disponible tanto en el **superficie de diseño** como en el **esquema del documento**. Este menú muestra los comandos que están disponibles para el widget seleccionado y su contenedor, lo que facilita la realización de operaciones en contenedores (que no siempre son fáciles de seleccionar en el **superficie de diseño**). A continuación se muestra un ejemplo de un menú contextual:

[![menú contextual de ejemplo al hacer clic con el botón secundario en el Superficie de diseño](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

En este ejemplo, al hacer clic con el botón secundario en un `TextView`, se abre un menú contextual que proporciona varias opciones:

- **LinearLayout** &ndash; abre un submenú para editar el `LinearLayout` primario del `TextView`.

- **Eliminar**, **copiar**y **cortar** &ndash; operaciones que se aplican al `TextView` al que se hace clic con el botón secundario.

### <a name="zoom-controls"></a>Controles de zoom

El **superficie de diseño** admite la ampliación a través de varios controles, como se muestra a continuación:

[Diagrama de![de los controles de zoom de Superficie de diseño](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Estos controles facilitan la visualización de ciertas áreas de la interfaz de usuario en el diseñador:

- **Resaltar contenedores** &ndash; resalta los contenedores en el **superficie de diseño** de modo que sean más fáciles de encontrar al acercar y alejar.

- **Tamaño Normal** &ndash; representa el diseño de píxel a píxel para que pueda ver cómo se verá el diseño en la resolución del dispositivo seleccionado.

- **Ajustar a la ventana** &ndash; establece el nivel de zoom para que todo el diseño esté visible en el superficie de diseño.

- **Acerque** &ndash; acerca de forma incremental con cada clic y aumentando el diseño.

- **Alejar** &ndash; reduce de forma incremental con cada clic, lo que hace que el diseño aparezca más pequeño en el superficie de diseño.

Tenga en cuenta que la configuración de zoom elegida no afecta a la interfaz de usuario de la aplicación en tiempo de ejecución.

## <a name="switching-between-design-and-source-panes"></a>Cambiar entre los paneles de diseño y de origen

En la franja central situada entre los paneles **diseño** y **origen** , hay varios botones que se usan para modificar el modo en que se muestran los paneles de **diseño** y de **origen** :

[![panel muestra las ubicaciones del botón](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Estos botones hacen lo siguiente:

- **Diseño** &ndash; este botón de nivel superior, **diseño**, selecciona el panel de **diseño** . Al hacer clic en este botón, se habilitan los paneles **cuadro de herramientas** y **propiedades** y no se muestra la barra de herramientas del **Editor de texto** . Cuando se hace clic en el botón **contraer** (vea más abajo), el panel de **diseño** se presenta solo sin el panel de **origen** .

- **Intercambiar paneles** &ndash; este botón (que se parece a dos flechas opuestas) intercambia los paneles de **diseño** y de **origen** para que el panel de **origen** esté a la izquierda y el panel de **diseño** esté a la derecha. Al hacer clic en él, se vuelven a intercambiar los paneles con sus ubicaciones originales.

- **Origen** &ndash; este botón (que se asemeja a dos corchetes angulares opuestos) selecciona el panel de **origen** . Al hacer clic en este botón, los paneles **cuadro de herramientas** y **propiedades** se deshabilitan y la barra de herramientas del **Editor de texto** se hace visible en la parte superior de Visual Studio. Cuando se haga clic en el botón **contraer** (vea más abajo), al hacer clic en el botón **origen** se muestra el panel **origen** en lugar del panel **diseño** .

- **División vertical** &ndash; este botón (que es similar a una barra vertical), muestra los paneles de **diseño** y de **origen** en paralelo. Esta es la disposición predeterminada.

- **División horizontal** &ndash; este botón (que es similar a una barra horizontal), muestra el panel de **diseño** sobre el panel de **origen** . Se puede hacer clic en los **paneles de intercambio** para colocar el panel de **origen** encima del panel de **diseño** .

- **Contraer panel** &ndash; este botón (que se parece a dos corchetes angulares de señal derecha) "contrae" la presentación de dos paneles del **diseño** y el **origen** en una sola vista de uno de estos paneles.
    Este botón se convierte en el botón de **Panel de expansión** (similar a dos corchetes angulares que apuntan a la izquierda), en el que se puede hacer clic para devolver la vista al modo de presentación de doble panel (**diseño** y **origen**).

Cuando se hace clic en el **Panel de contraer** , solo se muestra el panel de **diseño** . Sin embargo, puede hacer clic en el botón **origen** para ver en su lugar solo el panel **origen** . Vuelva a hacer clic en el botón **diseño** para volver al panel de **diseño** .

## <a name="source-pane"></a>Panel origen

En el panel **origen** se muestra el origen XML que subyace al diseño mostrado en el **superficie de diseño**. Dado que las dos vistas están disponibles al mismo tiempo, es posible crear un diseño de la interfaz de usuario si va entre una representación visual del diseño y el origen XML subyacente para el diseño:

[![origen XML de ejemplo en el panel origen](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

Los cambios realizados en el origen XML se representan inmediatamente en el **superficie de diseño**; los cambios realizados en el **superficie de diseño** hacen que el origen XML mostrado en el panel de **origen** se actualice en consecuencia. Al realizar cambios en XML en el panel **origen** , las características de autocompletar e IntelliSense están disponibles para acelerar el desarrollo de la interfaz de usuario basada en XML, tal como se explica a continuación.

Para una mayor facilidad de navegación al trabajar con archivos XML largos, el panel de **origen** es compatible con la barra de desplazamiento de Visual Studio (como se mostró a la derecha en la captura de pantalla anterior). Para obtener más información sobre la barra de desplazamiento, vea [Cómo realizar un seguimiento del Código personalizando la barra de desplazamiento](https://msdn.microsoft.com/library/dn237345.aspx).

### <a name="autocompletion"></a>Autocompletar

Cuando empiece a escribir el nombre de un atributo para un widget, puede presionar <kbd>CTRL + barra espaciadora</kbd> para ver una lista de posibles finalizaciones. Por ejemplo, después de escribir `android:lay` en el ejemplo siguiente (seguido de presionar <kbd>CTRL + barra espaciadora</kbd>), se presenta la siguiente lista:

[![Autocompletar el atributo de diseño](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Presione <kbd>entrar</kbd> para aceptar la primera finalización de la lista o utilice las teclas de dirección para desplazarse a la finalización deseada y presione <kbd>entrar</kbd>. Como alternativa, puede usar el mouse para desplazarse y hacer clic en la finalización deseada.

### <a name="intellisense"></a>IntelliSense

Después de escribir un nuevo atributo para un widget y de empezar a asignarle un valor, IntelliSense aparece después de escribir un carácter desencadenador y proporciona una lista de valores válidos que se usarán para ese atributo. Por ejemplo, después de especificar la primera comilla doble para `android:layout_width` en el ejemplo siguiente, aparece un selector de finalización automática que proporciona la lista de opciones válidas para este ancho:

[![ejemplo de IntelliSense para el ancho del diseño](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

En la parte inferior de este menú emergente hay dos botones (como se indica en rojo en la captura de pantalla anterior). Al hacer clic en el botón **recursos del proyecto** de la izquierda, la lista se restringe a los recursos que forman parte del proyecto de la aplicación, mientras que al hacer clic en el botón **recursos del marco** de la derecha se restringe la lista para mostrar los recursos disponibles en el marco.
Estos botones se activan o desactivan: puede hacer clic en ellos de nuevo para deshabilitar la acción de filtrado que proporciona cada una.

## <a name="properties-pane"></a>Panel Propiedades

El diseñador admite la edición de las propiedades del widget a través del panel de **propiedades** : 

![Captura de pantalla del ventana Propiedades](designer-basics-images/vs/08-property-pad.png)

Las propiedades enumeradas en el panel **propiedades** cambian en función del widget seleccionado en el **superficie de diseño**.

### <a name="default-values"></a>Valores predeterminados

Las propiedades de la mayoría de los widgets estarán en blanco en la ventana **propiedades** porque sus valores se heredan del tema Android seleccionado.
La ventana **propiedades** solo mostrará los valores que se establecen explícitamente para el widget seleccionado; no se mostrarán los valores que se heredan del tema.

### <a name="referencing-resources"></a>Referencia a recursos

Algunas propiedades pueden hacer referencia a los recursos que se definen en archivos que no sean el archivo layout **. axml** . Los casos más comunes de este tipo son `string` y `drawable` recursos. Sin embargo, las referencias también se pueden usar para otros recursos, como `Boolean` valores y dimensiones. Cuando una propiedad admite referencias de recursos, se muestra un icono de exploración (un cuadrado) junto a la entrada de texto de la propiedad. Al hacer clic en este botón, se abre un selector de recursos.

Por ejemplo, en la siguiente captura de pantalla se muestran las opciones disponibles al hacer clic en el cuadrado oscurecido a la derecha del campo de texto para un `Text` widget en la ventana **propiedades** :

[![lista de opciones de texto de ejemplo](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Cuando se hace clic en el **recurso** , se muestra el cuadro de diálogo **seleccionar recurso** :

[![captura de pantalla de recursos de ejemplo con varios recursos enumerados](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

En esta lista, puede seleccionar un recurso de texto para usarlo en el widget en lugar de codificar de forma rígida el texto en el panel de **propiedades** . En el ejemplo siguiente se muestra el selector de recursos para la propiedad `Src` de una `ImageView`:

[![recurso de icono de lista del selector de recursos para un ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Al hacer clic en el cuadrado en blanco a la derecha de la propiedad `Src`, se abre el cuadro de diálogo **seleccionar recurso** con una lista de recursos que van desde los colores (como se mostró anteriormente) a drawables.

### <a name="boolean-property-references"></a>Referencias de propiedades booleanas

Las propiedades *booleanas* se seleccionan normalmente como marcas de verificación junto a una propiedad en el ventana Propiedades. Puede designar un valor `true` o `false` activando o desactivando esta casilla, o bien puede seleccionar una referencia de propiedad haciendo clic en el cuadrado con relleno oscuro situado a la derecha de la propiedad. En el ejemplo siguiente, el texto se cambia a todas las mayúsculas al hacer clic en la referencia de propiedad booleana **All capss de texto** asociada a la `TextView` seleccionada:

![Ejemplo de configuración de propiedades booleanas](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Editar propiedades en línea

El Android Designer admite la edición directa de ciertas propiedades en el **superficie de diseño** (por lo que no tiene que buscar estas propiedades en la lista de propiedades). Entre las propiedades que se pueden editar directamente se incluyen texto, margen y tamaño.

### <a name="text"></a>Text

Las propiedades de texto de algunos widgets (como `Button` y `TextView`) se pueden editar directamente en el **superficie de diseño**. Al hacer doble clic en un widget, se pone en modo de edición, como se muestra a continuación:

![Recurso de texto para la cadena Hello](designer-basics-images/vs/12-text-resource.png "Recurso de texto")

Puede escribir un nuevo valor de texto o escribir una nueva cadena de recursos. En el ejemplo siguiente, el recurso de `@string/hello` se reemplaza por el texto `CLICK THIS BUTTON`:

![Mayús + Entrar para vincular automáticamente el texto a un nuevo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

Este cambio se almacena en la propiedad `text` del widget; no modifica el valor asignado a la `@string/hello` recurso.
Al escribir una clave en una nueva cadena de texto, puede presionar <kbd>mayús</kbd>  +
<kbd>entrar</kbd> para vincular automáticamente el texto especificado a un nuevo recurso.

### <a name="margin"></a>Margen

Al seleccionar un widget, el diseñador muestra los identificadores que le permiten cambiar el tamaño o margen del widget de forma interactiva. Al hacer clic en el widget mientras está seleccionado, se alterna entre el modo de edición de márgenes y el modo de edición de tamaño.

Al hacer clic en un widget por primera vez, se muestran los controladores de margen. Si mueve el mouse a uno de los controladores, el diseñador muestra la propiedad que cambiará el identificador (como se muestra a continuación para la propiedad `layout_marginLeft`):

![Captura de pantalla que muestra los identificadores de margen en el diseñador](designer-basics-images/vs/15-margin-handles.png)

Si ya se ha establecido un margen, se muestran líneas de puntos, lo que indica el espacio que ocupa el margen:

![Ejemplo de líneas de puntos que marcan el espacio alrededor de un botón](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Tamaño

Como se mencionó anteriormente, puede cambiar al modo de edición de tamaño haciendo clic en un widget mientras ya está seleccionado. Haga clic en el controlador triangular para establecer el tamaño de la dimensión indicada en `wrap_content`:

![Ajustar el contenido y cambiar el tamaño de los controladores](designer-basics-images/vs/17-wrap-content.png)

Al hacer clic en el controlador **ajustar contenido** , se reduce el widget en esa dimensión para que no sea más grande de lo necesario para incluir el contenido delimitado. En este ejemplo, el texto del botón se reduce horizontalmente tal y como se muestra en la siguiente captura de pantalla.

Cuando el valor de tamaño se establece para **ajustar el contenido**, el diseñador muestra un controlador triangular que apunta a la dirección opuesta para cambiar el tamaño a `match_parent`:

![Coincidir con identificador primario](designer-basics-images/vs/18-match-parent.png)

Al hacer clic en el identificador **principal de coincidencia** , se restaura el tamaño de esa dimensión para que sea el mismo que el del widget primario.

Además, puede arrastrar el controlador de tamaño circular (como se muestra en las capturas de pantallas anteriores) para cambiar el tamaño del widget a un valor de `dp` arbitrario. Cuando lo haga, se mostrarán los identificadores **ajustar contenido** y **coincidir con** la dimensión:

![Controladores de tamaño circular](designer-basics-images/vs/19-resize-dp.png)

No todos los contenedores permiten editar el `Size` de un widget. Por ejemplo, observe que en la siguiente captura de pantalla con la `LinearLayout` seleccionada, no aparecen los controladores de tamaño:

![Sin controladores de tamaño](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>Esquema del documento

El **esquema del documento** muestra la jerarquía del widget del diseño.
En el ejemplo siguiente, se selecciona el widget contenedor `LinearLayout`:

![Ejemplo de esquema de documento](designer-basics-images/vs/21-document-outline.png)

El contorno del widget seleccionado (en este caso, un `LinearLayout`) también se resalta en el **superficie de diseño**. El widget seleccionado en el esquema del documento permanece sincronizado con su homólogo en el **superficie de diseño**. Esto resulta útil para seleccionar grupos de vistas, que no siempre son fáciles de seleccionar en el **superficie de diseño**.

El **esquema del documento** admite copiar y pegar, o puede usar arrastrar y colocar. Arrastrar y colocar se admite desde el **esquema del documento** hasta el **superficie de diseño** , así como desde el **superficie de diseño** al **esquema del documento**. Además, al hacer clic con el botón secundario en un elemento del **esquema del documento** , se muestra el menú contextual de ese elemento (el mismo menú contextual que aparece al hacer clic con el botón secundario en ese mismo widget en el **superficie de diseño**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="launching-the-designer"></a>Iniciar el diseñador

El diseñador se inicia automáticamente cuando se crea un diseño o se puede iniciar haciendo doble clic en un archivo. axml existente. Por ejemplo, si hace doble clic en **Main. axml** en la carpeta **recursos > diseño** , se cargará el diseñador como se muestra a continuación:

[pantalla del diseñador de![en Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Del mismo modo, puede Agregar un nuevo diseño haciendo clic con el botón secundario en la carpeta **diseño** del **Panel de solución** y seleccionando **Agregar > nuevo archivo > el diseño de > Android**:

[![cuadro de diálogo Agregar nuevo archivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Esto crea un nuevo archivo. axml y lo carga en el Superficie de diseño.

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

## <a name="designer-features"></a>Características del diseñador

El diseñador se compone de varias secciones que admiten sus diversas características, como se muestra en la siguiente captura de pantalla:

[![diagrama de paneles del diseñador](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Al editar un diseño en el diseñador, se usan las siguientes características para crear y dar forma a un diseño:

- **Superficie de diseño** &ndash; facilita la construcción visual de la interfaz de usuario, ya que proporciona una representación editable de cómo aparecerá el diseño en el dispositivo.

- En la **barra de herramientas** &ndash; se muestra una lista de selectores: configuración de **dispositivo**, **versión**, **tema**, diseño y barra de acciones. La barra de herramientas también incluye iconos para iniciar el editor de temas y habilitar la cuadrícula de diseño de material.

- En el **cuadro de herramientas** &ndash; se proporciona una lista de widgets y diseños que se pueden arrastrar y colocar en el superficie de diseño.

- **Panel de propiedades** &ndash; muestra las propiedades del widget seleccionado para su visualización y edición.

- **Esquema del documento** &ndash; muestra el árbol de widgets que componen el diseño. Puede hacer clic en un elemento del árbol para que se seleccione en el diseñador. Además, al hacer clic en un elemento del árbol, se cargan las propiedades del elemento en el panel de propiedades.

## <a name="toolbar"></a>ToolBar

La barra de herramientas (situada sobre el Superficie de diseño) presenta los selectores de configuración y los menús de herramientas:

[![diagrama de la barra de herramientas del diseñador](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

La barra de herramientas proporciona acceso a las siguientes características:

- El **selector de diseño alternativo** &ndash; permite seleccionar diferentes versiones de diseño.

- El **selector de dispositivos** &ndash; define un conjunto de calificadores asociados a un dispositivo determinado, como el tamaño de la pantalla, la resolución y la disponibilidad del teclado. También puede Agregar y eliminar nuevos dispositivos.

- El **selector de versión de android** &ndash; la versión de Android que tiene como destino el diseño. El diseñador representará el diseño según la versión de Android seleccionada.

- **Selector de temas** &ndash; selecciona el tema de la interfaz de usuario para el diseño.

- **Selector de configuración** &ndash; selecciona la configuración del dispositivo, como *vertical* u *horizontal*.

- **Opciones del calificador de recursos** &ndash; abre un cuadro de diálogo que presenta menús desplegables para seleccionar el *idioma*, el modo de la *interfaz de usuario*, el *modo nocturno*y las opciones de *pantalla redondeada* .

- **Barra de acciones configuración** &ndash; configura los valores de barra de acciones para el diseño.

- El **Editor de temas** &ndash; abre el editor de *temas*, lo que permite personalizar los elementos del tema seleccionado.

- **Cuadrícula de diseño de materiales** &ndash; habilita o deshabilita la *cuadrícula de diseño del material*. El elemento de menú desplegable adyacente a la cuadrícula de diseño del material abre un cuadro de diálogo que le permite personalizar la cuadrícula.

Cada una de estas características se explica con más detalle en estos temas:

[Opciones de visualización y calificadores de recursos](~/android/user-interface/android-designer/resource-qualifiers.md) proporciona información detallada sobre el **selector de dispositivos**, el selector de **versiones de Android**, el selector de **temas**, el **selector de configuración**y los requisitos de **recursos Opciones**y **barra de acciones configuración**.

En las [vistas de diseño alternativas](~/android/user-interface/android-designer/alternative-layout-views.md) se explica cómo usar el **selector de diseño alternativo**.

[Las características de diseño de materiales](~/android/user-interface/android-designer/material-design-features.md) proporcionan información general detallada sobre el **Editor de temas** y la cuadrícula de **diseño de materiales**.

## <a name="design-surface"></a>Superficie de diseño

El diseñador permite arrastrar y colocar widgets desde el cuadro de herramientas hasta el Superficie de diseño. Al interactuar con widgets en el diseñador (agregando nuevos widgets o cambiando la posición de los existentes), se muestran las líneas verticales y horizontales para marcar los puntos de inserción disponibles. En el ejemplo siguiente, se está arrastrando un nuevo widget de `Button` al Superficie de diseño:

[![líneas de inserción de ejemplo en Superficie de diseño](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Además, se pueden copiar widgets: puede usar copiar y pegar para copiar un widget, o puede arrastrar y colocar un widget existente mientras presiona la tecla <kbd>Ctrl</kbd> .

### <a name="context-menu-commands"></a>Comandos del menú contextual

Un menú contextual está disponible tanto en el Superficie de diseño como en el esquema del documento. Este menú muestra los comandos que están disponibles para el widget seleccionado y su contenedor, lo que facilita la realización de operaciones en contenedores (que no siempre son fáciles de seleccionar en el Superficie de diseño). A continuación se muestra un ejemplo de un menú contextual:

[![menú contextual de ejemplo al hacer clic con el botón secundario en el Superficie de diseño](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

En este ejemplo, al hacer clic con el botón secundario en un `Button`, se abre un menú contextual que proporciona varias opciones:

- **LinearLayout** &ndash; abre un submenú para editar el `LinearLayout` primario del `Button`.

- **Cortar**, **copiar**y **eliminar** operaciones de &ndash; que se aplican al `Button` en el que se hizo clic con el botón secundario.

### <a name="zoom-controls"></a>Controles de zoom

El Superficie de diseño admite la ampliación a través de varios controles, como se muestra a continuación:

[Diagrama de![de los controles de zoom de Superficie de diseño](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Estos controles facilitan la visualización de ciertas áreas de la interfaz de usuario en el diseñador:

- **Resaltar contenedores** &ndash; resalta los contenedores en el superficie de diseño de modo que sean más fáciles de encontrar al acercar y alejar.

- **Tamaño Normal** &ndash; representa el diseño de píxel a píxel para que pueda ver cómo se verá el diseño en la resolución del dispositivo seleccionado.

- **Ajustar a la ventana** &ndash; establece el nivel de zoom para que todo el diseño esté visible en el superficie de diseño.

- **Acerque** &ndash; acerca de forma incremental con cada clic y aumentando el diseño.

- **Alejar** &ndash; reduce de forma incremental con cada clic, lo que hace que el diseño aparezca más pequeño en el superficie de diseño.

Tenga en cuenta que la configuración de zoom elegida no afecta a la interfaz de usuario de la aplicación en tiempo de ejecución.

## <a name="property-pad"></a>Panel de propiedades

El diseñador admite la edición de las propiedades del widget a través del **Panel de propiedades**. Las propiedades enumeradas en el panel de propiedades cambian en función del widget seleccionado en la superficie del diseñador. Cuando se selecciona la `Button` en el ejemplo anterior, se muestran las propiedades de ese widget `Button`:

[![captura de pantalla del panel de propiedades](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Secciones del panel de propiedades

El panel de propiedades se divide en varias secciones que agrupan propiedades similares &ndash; esto facilita la búsqueda de propiedades de interés:

- **Widget** &ndash; propiedades principales del widget, como `id`, `visibility`, `text`, etc. Las propiedades para administrar el contenido del widget normalmente se colocan aquí.

- **Estilo** &ndash; propiedades que cambian la apariencia visual del widget, como `font`, `text color`, `background`, etc.

- **Diseño** &ndash; propiedades que establecen la ubicación y el tamaño del widget.

- **Desplazar** &ndash; propiedades de desplazamiento.

- **Comportamiento** &ndash; marcas que establecen cómo se comporta el widget.

### <a name="default-values"></a>Valores predeterminados

Las propiedades de la mayoría de los widgets estarán en blanco en el **Panel de propiedades** porque sus valores se heredan del tema Android seleccionado. El **Panel de propiedades** solo mostrará los valores que se establecen explícitamente para el widget seleccionado; no se mostrarán los valores que se heredan del tema.

### <a name="referencing-resources"></a>Referencia a recursos

Algunas propiedades pueden hacer referencia a los recursos que se definen en archivos que no sean el archivo layout **. axml** . Los casos más comunes de este tipo son `string` y `drawable` recursos. Sin embargo, las referencias también se pueden usar para otros recursos, como `Boolean` valores y dimensiones.
Cuando una propiedad admite referencias de recursos, se muestra un icono de exploración (puntos suspensivos &hellip;) junto a la entrada de texto de la propiedad.
Al hacer clic en este botón, se abre un selector de recursos.

Por ejemplo, en la siguiente captura de pantalla se muestran los recursos disponibles al hacer clic en los puntos suspensivos situados a la derecha del campo de texto para un `Button` widget en el **Panel de propiedades**:

[![captura de pantalla de recursos de ejemplo con dos recursos enumerados](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

En el ejemplo siguiente se muestra el selector de recursos para la propiedad `Src` de una `ImageView`:

[![recurso de icono de lista del selector de recursos para un ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Referencias de propiedades booleanas

Las propiedades *booleanas* normalmente se muestran como una casilla en el panel de propiedades. Cuando una propiedad `Boolean` admite referencias de recursos, aparece una pequeña casilla junto a la propiedad. Una casilla activada significa `true` y un cuadro vacío significa `false`. También puede especificar directamente un valor como `true` o `false`. Al mantener el mouse sobre la entrada, se abre un pequeño icono de campo de texto. Puede hacer clic en él si desea escribir el valor manualmente.

[![ejemplo de configuración de propiedades booleanas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>Propiedades agrupadas

Algunos widgets tienen propiedades de varios valores que se agrupan (como `Padding`, por ejemplo). Estos valores de propiedad se muestran en el **Panel de propiedades** en una sola fila expansible. Algunas de estas propiedades se pueden editar directamente en la fila agrupada, como la `Padding` propiedad que se muestra a continuación:

[![configuración de ejemplo para la propiedad padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Editar propiedades en línea

El Android Designer admite la edición directa de ciertas propiedades en el Superficie de diseño (por lo que no tiene que buscar estas propiedades en la lista de propiedades). Entre las propiedades que se pueden editar directamente se incluyen texto, margen y tamaño.

### <a name="text"></a>Text

Las propiedades de texto de algunos widgets (como `Button` y `TextView`) se pueden editar directamente en el Superficie de diseño. Al hacer doble clic en un widget, se pone en modo de edición, como se muestra a continuación:

[![recurso de texto para la cadena Hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

Puede escribir un nuevo valor de texto o escribir una nueva cadena de recursos. En el ejemplo siguiente, el recurso de `@string/hello` se reemplaza por el texto `CLICK THIS BUTTON`:

[![Mayús + Entrar para vincular automáticamente el texto a un nuevo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Este cambio se almacena en la propiedad `text` del widget; no modifica el valor asignado a la `@string/hello` recurso.
Al escribir una clave en una nueva cadena de texto, puede presionar <kbd>mayús</kbd>  +
<kbd>entrar</kbd> para vincular automáticamente el texto especificado a un nuevo recurso.

### <a name="margin"></a>Margen

Al seleccionar un widget, el diseñador muestra los identificadores que le permiten cambiar el tamaño o margen del widget de forma interactiva. Al hacer clic en el widget mientras está seleccionado, se alterna entre el modo de edición de márgenes y el modo de edición de tamaño.

Al hacer clic en un widget por primera vez, se muestran los controladores de margen. Si mueve el mouse a uno de los controladores, el diseñador muestra la propiedad que cambiará el identificador (como se muestra a continuación para la propiedad `layout_marginLeft`):

[![captura de pantalla que muestra los identificadores de margen en el diseñador](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Si ya se ha establecido un margen, se muestran líneas de puntos, lo que indica el espacio que ocupa el margen:

[![ejemplo de líneas de puntos que marcan el espacio alrededor de un botón](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Tamaño

Como se mencionó anteriormente, puede cambiar al modo de edición de tamaño haciendo clic en un widget mientras ya está seleccionado. Haga clic en el controlador triangular para establecer el tamaño de la dimensión indicada en `wrap_content`:

[![ajustar el contenido y cambiar el tamaño de los controladores](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Al hacer clic en el controlador **ajustar contenido** , se reduce el widget en esa dimensión para que no sea más grande de lo necesario para incluir el contenido delimitado. En este ejemplo, el texto del botón se reduce horizontalmente tal y como se muestra en la siguiente captura de pantalla.

Cuando el valor de tamaño se establece para **ajustar el contenido**, el diseñador muestra un controlador triangular que apunta a la dirección opuesta para cambiar el tamaño a `match_parent`:

[![identificador primario coincidente](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Al hacer clic en el identificador **principal de coincidencia** , se restaura el tamaño de esa dimensión para que sea el mismo que el del widget primario.

Además, puede arrastrar el controlador de tamaño circular (como se muestra en las capturas de pantallas anteriores) para cambiar el tamaño del widget a un valor de `dp` arbitrario. Cuando lo haga, se mostrarán los identificadores **ajustar contenido** y **coincidir con** la dimensión:

[![controladores de tamaño circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

No todos los contenedores permiten editar el `Size` de un widget. Por ejemplo, observe que en la siguiente captura de pantalla con la `LinearLayout` seleccionada, no aparecen los controladores de tamaño:

[![no hay controladores de tamaño](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Esquema del documento

El **esquema del documento** muestra la jerarquía del widget del diseño.
En el ejemplo siguiente, se selecciona el widget contenedor `LinearLayout`:

[![esquema del documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

El contorno del widget seleccionado (en este caso, un `LinearLayout`) también se resalta en el Superficie de diseño. El widget seleccionado en el esquema del documento permanece sincronizado con su homólogo en el Superficie de diseño. Esto resulta útil para seleccionar grupos de vistas, que no siempre son fáciles de seleccionar en el Superficie de diseño.

El esquema del documento admite copiar y pegar, o puede usar arrastrar y colocar. Arrastrar y colocar se admite desde el esquema del documento hasta el Superficie de diseño, así como desde el Superficie de diseño al esquema del documento. Además, al hacer clic con el botón secundario en un elemento del esquema del documento, se muestra el menú contextual de ese elemento (el mismo menú contextual que aparece al hacer clic con el botón secundario en ese mismo widget en el Superficie de diseño).

-----
