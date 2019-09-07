---
title: Características de diseño de material de Xamarin. Android Designer
description: En este tema se describen las características del diseñador que facilitan a los desarrolladores la creación de diseños conformes al diseño. En esta sección se presenta y explica cómo usar la cuadrícula material, la paleta de colores material, la escala tipográfica y el editor de temas.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 67755baf18ad5277e7c2fe8be705522a9b668ff3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756821"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Características de diseño de material de Xamarin. Android Designer

_En este tema se describen las características del diseñador que facilitan a los desarrolladores la creación de diseños conformes al diseño. En esta sección se presenta y explica cómo usar la cuadrícula material, la paleta de colores material, la escala tipográfica y el editor de temas._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016: Todo el mundo puede crear aplicaciones atractivas con el diseño de materiales**

## <a name="overview"></a>Información general

Xamarin. Android Designer incluye características que facilitan la creación de diseños compatibles con el diseño de materiales. Si no está familiarizado con el diseño de material, consulte la [Introducción al diseño de materiales](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En esta guía, echaremos un vistazo a las siguientes características del diseñador:

- *Cuadrícula de materiales* &ndash; Superposición en el superficie de diseño que muestra una cuadrícula, espaciado y líneas para ayudarle a colocar widgets de diseño de acuerdo con las directrices de diseño de materiales.

- *Editor de temas* &ndash; Editor de recursos de color pequeño que permite establecer información de color para un subconjunto de un tema. Por ejemplo, puede obtener una vista previa de los colores de `colorPrimary`material `colorPrimaryDark`, como `colorAccent`, y.

Veremos cada una de estas características y proporcionamos ejemplos de cómo usarlas.

## <a name="material-design-grid"></a>Cuadrícula de diseño de materiales

El menú cuadrícula de diseño de material está disponible en la barra de herramientas de la parte superior del diseñador:

[![Cuadrícula de diseño de materiales](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Al hacer clic en el icono de la cuadrícula de diseño de material, el diseñador muestra una superposición en el Superficie de diseño que incluye los elementos siguientes:

- Líneas (líneas naranja)

- Espaciado (áreas verdes)

- Una cuadrícula (líneas azules)

Estos elementos pueden verse en la captura de pantalla anterior. Cada uno de estos elementos superpuesto es configurable. Al hacer clic en los puntos suspensivos junto al menú cuadrícula de diseño de materiales, se abre un cuadro de diálogo elemento flotante que le permite deshabilitar o habilitar la cuadrícula, configurar la ubicación de líneas y establecer espaciados. Tenga en cuenta que todos los valores `dp` se expresan en (píxeles independientes de la densidad):

[![Configuración de la cuadrícula, el KEYLINE y el espaciado](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Para agregar un nuevo KEYLINE, escriba un nuevo valor de desplazamiento en el cuadro **desplazamiento** , seleccione una ubicación (**izquierda**, **superior**, **derecha**o **inferior**) y haga clic en el icono + para agregar el nuevo KEYLINE. Del mismo modo, para agregar un nuevo espaciado, escriba el tamaño y el desplazamiento (en DP) en los cuadros **tamaño** y **desplazamiento** , respectivamente. Seleccione una ubicación (**izquierda**, **superior**, **derecha**o **inferior**) y haga clic en el icono + para agregar el nuevo espaciado.

Al cambiar estos valores de configuración, se guardan en el archivo XML de diseño y se reutilizan cuando se vuelve a abrir el diseño.

## <a name="theme-editor"></a>Editor de temas

El **Editor de temas** permite personalizar la información de color para un subconjunto de atributos de tema. Para abrir el **Editor de temas**, haga clic en el icono de pincel en la barra de herramientas:

[![Icono del editor de temas](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Aunque el **Editor de temas** es accesible desde la barra de herramientas para todos los niveles de API y versiones de Android de destino, solo hay disponible un subconjunto de las funcionalidades que se describen a continuación si el nivel de API de destino es anterior a la API 21 (Android 5,0 Lollipop).

En el panel izquierdo del editor de **temas** se muestra la lista de colores que componen el tema seleccionado actualmente (en este ejemplo, usamos el `Default Theme`):

[![Editor de temas](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Al seleccionar un color de la izquierda, el panel de la derecha proporciona las siguientes pestañas para ayudarle a editar el color:

- **Heredar** &ndash; Muestra un diagrama de herencia de estilo para el color seleccionado y muestra el color y el código de color resueltos asignados a ese color de tema.

- **Selector de colores** &ndash; Permite cambiar el color seleccionado a cualquier valor arbitrario.

- **Paleta de materiales** &ndash; Permite cambiar el color seleccionado a un valor que se ajuste al diseño del material.

- **Recursos** de &ndash; Permite cambiar el color seleccionado a uno de los otros recursos de color existentes en el tema.

Echemos un vistazo a cada una de estas pestañas en detalle.

### <a name="inherit-tab"></a>Heredar (pestaña)

Como se muestra en el ejemplo siguiente, la pestaña **heredar** muestra la herencia de estilo para el color de **fondo** del **tema predeterminado**:

[![Heredar (pestaña)](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

En este ejemplo, el **tema predeterminado** hereda de un estilo que utiliza `@color/background_material_light` pero lo reemplaza por `color/material_grey_50`, que tiene un valor de código de color de `#fffafafa`.
Para obtener más información sobre la herencia de estilos, vea [estilos y temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selector de colores

En la captura de pantalla siguiente se muestra el **selector de colores**:

[![Selector de colores](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

En este ejemplo, el color de **fondo** se puede cambiar a cualquier valor a través de varios medios:

- Hacer clic en un color directamente.
- Especificar valores de matiz, saturación y luminosidad.
- Escribir valores RGB (rojo, verde y azul) en formato decimal.
- Establecer el valor alfa (opacidad) del color seleccionado.
- Escribir directamente el código de color hexadecimal.

El color que elija en el selector de colores *no* está restringido a las directrices de diseño de materiales ni al conjunto de recursos de color disponibles.

### <a name="resources"></a>Recursos

La pestaña **recursos** ofrece una lista de recursos de color que ya están presentes en el tema:

[![Recursos](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

El uso de la pestaña **recursos** restringe las opciones a esta lista de colores. Tenga en cuenta que si elige un recurso de color que ya está asignado a otra parte del tema, dos elementos adyacentes de la interfaz de usuario pueden "ejecutarse juntos" (porque tienen el mismo color) y resultan difíciles de distinguir para el usuario.

### <a name="material-palette"></a>Paleta de materiales

La pestaña **paleta de materiales** abre la paleta de colores de diseño de **materiales**. Al elegir un valor de color de esta paleta se restringe su elección de color para que sea coherente con las directrices de diseño de materiales:

[![Paleta de materiales](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

En la parte superior de la paleta de colores se muestran los colores del diseño del material principal, mientras que la parte inferior de la paleta muestra un intervalo de tonos para el color primario seleccionado. Por ejemplo, al seleccionar **Indigo**, se muestra una colección de los tonos de **Indigo** en la parte inferior del cuadro de diálogo.
Al seleccionar un matiz, el color de la propiedad cambia al matiz seleccionado. En el ejemplo siguiente, el `Background Tint` del botón cambia a *Indigo 500*:

![Seleccionar Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint`está establecido en el código de color para *Indigo 500* (`#ff3f51b5`) y el diseñador actualiza el color de fondo para reflejar este cambio:

[![Matiz de fondo cambiado](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Para obtener más información acerca de la paleta de colores de diseño de materiales, consulte la [Guía de paleta de colores](https://material.io/design/color/)de diseño de materiales.

### <a name="creating-a-new-theme"></a>Crear un nuevo tema

En el ejemplo siguiente, usaremos la paleta de materiales para crear un nuevo tema personalizado. En primer lugar, cambiaremos el color de **fondo** a *azul 900*:

![Cambiar fondo a azul 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Cuando se cambia un recurso de color, aparece un mensaje con el mensaje, *el tema actual tiene cambios sin guardar*:

[![ADVERTENCIA de cambios no guardados](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

El color de **fondo** del diseñador ha cambiado a la nueva selección de color, pero este cambio aún no se ha guardado. En este momento, puede realizar una de las siguientes acciones:

- Haga clic en **descartar cambios** para descartar la opción de nuevo color (o opciones) y revertir el tema a su estado original.

- Presione <kbd>Ctrl + S</kbd> para guardar los cambios en el tema actual.

En el ejemplo siguiente, se presionó <kbd>Ctrl + S</kbd> para que los cambios se guardaran en **AppTheme**:

[![Cambios guardados en AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Resumen

En este tema se describen las características de diseño de materiales disponibles en Xamarin. Android Designer. Se explicó cómo habilitar y configurar la cuadrícula de diseño de material y se explicó cómo usar el editor de temas para crear nuevos temas personalizados que se ajusten a las directrices de diseño de materiales.
Para obtener más información sobre la compatibilidad de Xamarin. Android con el diseño de material, consulte el [tema material](~/android/user-interface/material-theme.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En esta guía, echaremos un vistazo a las siguientes características del diseñador:

- *Cuadrícula de diseño de materiales* &ndash; Superposición en el superficie de diseño que muestra una cuadrícula, espaciado y líneas para ayudarle a colocar widgets de diseño de acuerdo con las directrices de diseño de materiales.

- *Paleta de colores de diseño de materiales* &ndash; Cuadro de diálogo del panel de propiedades que le ayuda a elegir un color de la paleta de diseño de material oficial.

- *Escala tipográfica* Cuadro de diálogo del panel de propiedades que proporciona una opción de configuración compatible con el diseño de `textAppearance` material para la propiedad de los campos de texto. &ndash;

- *Editor de temas* &ndash; Editor de recursos de color pequeño que permite establecer información de color para un subconjunto de un tema. Por ejemplo, puede obtener una vista previa de los colores de `colorPrimary`material `colorPrimaryDark`, como `colorAccent`, y.

Veremos cada una de estas características y proporcionamos ejemplos de cómo usarlas.

## <a name="material-design-grid"></a>Cuadrícula de diseño de materiales

El menú cuadrícula de diseño de material está disponible en la barra de herramientas de la parte superior del diseñador:

[![Cuadrícula de diseño de materiales](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Al hacer clic en el icono de la cuadrícula de diseño de material, el diseñador muestra una superposición en el Superficie de diseño que incluye los elementos siguientes:

- Líneas (líneas naranja)

- Espaciado (áreas verdes)

- Una cuadrícula (líneas azules)

Estos elementos pueden verse en la siguiente captura de pantalla:

[![KEYLINE, espaciado y cuadrícula](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Cada uno de estos elementos superpuesto es configurable. Al hacer clic en los puntos&hellip;suspensivos () junto al menú cuadrícula de diseño de materiales, se abre un cuadro de diálogo elemento flotante que le permite deshabilitar o habilitar la cuadrícula, configurar la ubicación de líneas y establecer el espaciado. Tenga en cuenta que todos los valores `dp` se expresan en (píxeles independientes de la densidad):

[![Configuración de la cuadrícula, el KEYLINE y el espaciado](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Para agregar un nuevo KEYLINE, escriba un nuevo valor de desplazamiento en el cuadro **desplazamiento** , seleccione una ubicación (**izquierda**, **superior**, **derecha**o **inferior**) y haga clic en el icono + (que aparece a la derecha cuando se especifica un valor) para agregar el nuevo KEYLINE. Del mismo modo, para agregar un nuevo espaciado, escriba el tamaño y el desplazamiento (en DP) en los cuadros **tamaño** y **desplazamiento** , respectivamente. Seleccione una ubicación (**izquierda**, **superior**, **derecha**o **inferior**) y haga clic en el icono + para agregar el nuevo espaciado.

Al cambiar estos valores de configuración, se guardan en el archivo XML de diseño y se reutilizan cuando se vuelve a abrir el diseño.

## <a name="material-design-color-palette"></a>Paleta de colores de diseño de materiales

Todos los elementos del panel de propiedades que aceptan un color ahora tienen un icono de paleta adicional que puede usar para abrir la paleta de colores de diseño del material, tal como se muestra en esta captura de pantalla:

[![Icono de color](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Al hacer clic en este icono, se abre un cuadro de diálogo elemento flotante que le permite configurar el color de esa propiedad desde la paleta de colores del diseño del material:

[![Paleta de colores de diseño de materiales](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

En la parte superior de la paleta de colores se muestran los colores del diseño del material principal, mientras que la parte inferior de la paleta muestra un intervalo de tonos para el color primario seleccionado. Por ejemplo, al seleccionar **Indigo**, se muestra una colección de los tonos de **Indigo** en la parte inferior del cuadro de diálogo.
Al seleccionar un matiz, el color de la propiedad cambia al matiz seleccionado. En el ejemplo siguiente, el `Background Tint` del botón cambia a *Indigo 500*:

[![Elija Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint`está establecido en el código de color para *Indigo 500* (`#ff3f51b5`) y el diseñador actualiza el color de fondo del botón para reflejar este cambio:

[![Cambios de matiz de fondo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Para obtener más información acerca de la paleta de colores de diseño de materiales, consulte la [Guía de paleta de colores](https://material.io/design/color/)de diseño de materiales.

## <a name="typographic-scale"></a>Escala tipográfica

La sección **apariencia del texto** de la pestaña **estilo** del panel de **propiedades** tiene un icono que le permite `TextAppearance` seleccionar un estilo que se ajusta a la especificación de diseño del material:

[![Pestaña estilo](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Al hacer clic en este icono, se abre el cuadro de diálogo elemento flotante de **escala tipográfica** , que presenta una lista de estilos de texto preconfigurados entre los que puede elegir:

[![Selector de estilo de texto](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

En el ejemplo siguiente, al hacer clic en **Mostrar 1** , el texto del botón cambia a la fuente más grande de la **pantalla 1**:

[![Mostrar un estilo](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

El estilo de texto en el cuadro de diálogo **escala tipográfica** sigue la configuración del **tema** . Por ejemplo, si se elige el tema **claro** en el diseñador, la lista de estilos de texto disponibles refleja el tema **claro** :

[![Tema claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor de temas

El **Editor de temas** permite personalizar la información de color para un subconjunto de atributos de tema. Para abrir el **Editor de temas**, haga clic en el icono de pincel en la barra de herramientas:

[![Icono del editor de temas](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Aunque el **Editor de temas** es accesible desde la barra de herramientas para todos los niveles de API y versiones de Android de destino, solo hay disponible un subconjunto de las funcionalidades que se describen a continuación si el nivel de API de destino es anterior a la API 21 (Android 5,0 Lollipop).

En el panel izquierdo del editor de **temas** se muestra la lista de colores que componen el tema seleccionado actualmente (en este ejemplo, usamos el `Default Theme`):

[![Editor de temas](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Al seleccionar un color de la izquierda, el panel de la derecha proporciona las siguientes pestañas para ayudarle a editar el color:

- **Heredar** &ndash; Muestra un diagrama de herencia de estilo para el color seleccionado y muestra el color y el código de color resueltos asignados a ese color de tema.

- **Selector de colores** &ndash; Permite cambiar el color seleccionado a cualquier valor arbitrario.

- **Paleta de materiales** &ndash; Permite cambiar el color seleccionado a un valor que se ajuste al diseño del material.

- **Recursos** de &ndash; Permite cambiar el color seleccionado a uno de los otros recursos de color existentes en el tema.

Echemos un vistazo a cada una de estas pestañas en detalle.

### <a name="inherit-tab"></a>Heredar (pestaña)

Como se muestra en el ejemplo siguiente, la pestaña **heredar** muestra la herencia de estilo para el color de **fondo** del **tema predeterminado**:

[![Heredar (pestaña)](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

En este ejemplo, el **tema predeterminado** hereda de un estilo que utiliza `@color/background_material_dark` pero lo reemplaza por `color/material_grey_850`, que tiene un valor de código de color de `#ff303030`.
Para obtener más información sobre la herencia de estilos, vea [estilos y temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Selector de colores

En la captura de pantalla siguiente se muestra el **selector de colores**:

[![Selector de colores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

En este ejemplo, el color de **fondo** se puede cambiar a cualquier valor a través de varios medios:

- Hacer clic en un color directamente.
- Especificar valores de matiz, saturación y luminosidad.
- Escribir valores RGB (rojo, verde y azul) en formato decimal.
- Establecer el valor alfa (opacidad) del color seleccionado.
- Escribir directamente el código de color hexadecimal.

El color que elija en el selector de colores *no* está restringido a las directrices de diseño de materiales ni al conjunto de recursos de color disponibles.

### <a name="resources"></a>Recursos

La pestaña **recursos** ofrece una lista de recursos de color que ya están presentes en el tema:

[![Recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

El uso de la pestaña **recursos** restringe las opciones a esta lista de colores. Tenga en cuenta que si elige un recurso de color que ya está asignado a otra parte del tema, dos elementos adyacentes de la interfaz de usuario pueden "ejecutarse juntos" (porque tienen el mismo color) y resultan difíciles de distinguir para el usuario.

### <a name="material-palette"></a>Paleta de materiales

La pestaña **paleta de materiales** abre la paleta de colores de diseño del **material** que se ha descrito [anteriormente](#material-design-color-palette). Al elegir un valor de color de esta paleta se restringe su elección de color para que sea coherente con las directrices de diseño de materiales.

[![Paleta de materiales](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Crear un nuevo tema

En el ejemplo siguiente, usaremos la paleta de materiales para crear un nuevo tema personalizado. En primer lugar, cambiaremos el color de **fondo** a *azul 900*:

[![Cambiar fondo a azul 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Cuando se cambia un recurso de color, aparece un mensaje con el mensaje, *el tema actual tiene cambios sin guardar*:

[![ADVERTENCIA de cambios no guardados](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

Se ha realizado el cambio de color en el diseñador, pero este cambio aún no se ha guardado. En este momento, puede realizar una de las siguientes acciones:

- Haga clic en **descartar cambios** para descartar la opción de nuevo color (o opciones) y revertir el tema a su estado original.

- Presione  **&#8984; + S** para guardar los cambios en un nuevo tema denominado **personalizado**.

## <a name="summary"></a>Resumen

En este tema se describen las características de diseño de materiales disponibles en Xamarin. Android Designer. Se explicó cómo habilitar y configurar la cuadrícula de diseño de material, cómo usar la paleta de colores de diseño de materiales para editar las propiedades de color y cómo usar el selector de escala tipográfica para configurar las propiedades de texto. También se muestra cómo usar el editor de temas para crear nuevos temas personalizados que se ajusten a las directrices de diseño de materiales. Para obtener más información sobre la compatibilidad de Xamarin. Android con el diseño de material, consulte el [tema material](~/android/user-interface/material-theme.md).

-----

## <a name="related-links"></a>Vínculos relacionados

- [Tema de materiales](~/android/user-interface/material-theme.md)
- [Introducción al diseño de materiales](https://material.io/design/introduction)
