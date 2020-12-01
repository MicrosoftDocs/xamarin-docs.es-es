---
title: aspectos básicos del diseñador de iOS
description: En esta guía se presenta el Xamarin Designer para iOS. Muestra cómo usar el diseñador de iOS para diseñar visualmente controles, cómo obtener acceso a esos controles en el código y cómo editar propiedades.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/31/2018
ms.openlocfilehash: 07f3e313a743c9d95f8baf2be7a31f16c6ca4f7e
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439482"
---
# <a name="ios-designer-basics"></a>aspectos básicos del diseñador de iOS

_En esta guía se presenta el Xamarin Designer para iOS. Muestra cómo usar el diseñador de iOS para diseñar visualmente controles, cómo obtener acceso a esos controles en el código y cómo editar propiedades._

> [!WARNING]
> IOS Designer comenzará a quedar obsoleto en Visual Studio 2019, versión 16,8 y Visual Studio 2019 para Mac versión 8,8.
> La manera recomendada de compilar interfaces de usuario de iOS es directamente en un equipo Mac con Xcode. Para obtener más información, consulte [diseñar interfaces de usuario con Xcode](../storyboards/index.md). 

El Xamarin Designer para iOS es un diseñador de interfaz visual similar al Interface Builder de Xcode y el Android Designer. Algunas de sus muchas características incluyen una integración perfecta con Visual Studio para Windows y Mac, edición de arrastrar y colocar, una interfaz para configurar controladores de eventos y la capacidad de representar controles personalizados.

## <a name="requirements"></a>Requisitos

IOS Designer está disponible en Visual Studio para Mac y Visual Studio 2017 y versiones posteriores en Windows. En Visual Studio para Windows, el diseñador de iOS requiere una conexión a un host de compilación de Mac configurado correctamente, aunque no es necesario que Xcode esté en ejecución.

En esta guía se da por supuesto que está familiarizado con el contenido que se incluye en las [guías de introducción](~/ios/get-started/index.md).

<a name="how-it-works"></a>

## <a name="how-the-ios-designer-works"></a>Cómo funciona el diseñador de iOS

En esta sección se describe cómo el diseñador de iOS facilita la creación de una interfaz de usuario y su conexión al código.

IOS Designer permite a los desarrolladores diseñar visualmente la interfaz de usuario de una aplicación. Como se describe en la guía [Introduction to storyboards (introducción a los guiones gráficos](~/ios/user-interface/storyboards/index.md) ), un guion gráfico describe las pantallas (controladores de vista) que componen una aplicación, los elementos de la interfaz (vistas) colocados en esos controladores de vista y el flujo de navegación global de la aplicación. 

Un controlador de vista tiene dos partes: una representación visual en el diseñador de iOS y una clase de C# asociada:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Un controlador de vista en el diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "Un controlador de vista en el diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Código de un controlador de vista](introduction-images/2-viewcontrollercode-vsmac.png "Código de un controlador de vista")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un controlador de vista en el diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "Un controlador de vista en el diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Código de un controlador de vista](introduction-images/2-viewcontrollercode-vs.png "Código de un controlador de vista")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

En su estado predeterminado, un controlador de vista no proporciona ninguna funcionalidad; se debe rellenar con controles. Estos controles se colocan en la vista del controlador de vista, el área rectangular que contiene todo el contenido de la pantalla. La mayoría de los controladores de vista contienen controles comunes como botones, etiquetas y campos de texto, como se muestra en la captura de pantalla siguiente, que muestra un controlador de vista que contiene un botón: 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vsmac.png "Un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vs.png "Un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Algunos controles, como las etiquetas que contienen texto estático, se pueden agregar al controlador de vista y solo a la izquierda. Sin embargo, con mayor frecuencia que no, los controles deben personalizarse mediante programación. Por ejemplo, el botón agregado anteriormente debe hacer algo cuando se Puntee, por lo que se debe agregar un controlador de eventos en el código.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para tener acceso y manipular el botón en el código, debe tener un identificador único. Proporcione un identificador único; para ello, seleccione el botón, abra el **Panel de propiedades** y establezca su campo **nombre** en un valor como "SubmitButton":

[![Establecer el nombre de un botón en el Panel de propiedades](introduction-images/4-settingbuttonname-vsmac.png "Establecer el nombre de un botón en el Panel de propiedades")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para tener acceso y manipular el botón en el código, debe tener un identificador único. Proporcione un identificador único; para ello, seleccione el botón, abra la **ventana Propiedades** y establezca su campo **nombre** en un valor como "SubmitButton":

[![Establecer el nombre de un botón en la ventana Propiedades](introduction-images/4-settingbuttonname-vs.png "Establecer el nombre de un botón en la ventana Propiedades")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Ahora que el botón tiene un nombre, se puede tener acceso a él en el código. Pero ¿cómo funciona esto?

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En el **Panel de solución**, al navegar a **ViewController.CS** y al hacer clic en el indicador de divulgación se revela que la definición de clase del controlador de vista `ViewController` abarca dos archivos, cada uno de los cuales contiene una definición de [clase parcial](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En el **Explorador de soluciones**, al navegar a **ViewController.CS** y al hacer clic en el indicador de divulgación se revela que la definición de clase del controlador de vista `ViewController` abarca dos archivos, cada uno de los cuales contiene una definición de [clase parcial](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) :

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.CS** se debe rellenar con código personalizado relacionado con la `ViewController` clase. En este archivo, la `ViewController` clase puede responder a varios métodos de ciclo de vida del controlador de vista de iOS, personalizar la interfaz de usuario y responder a los datos proporcionados por el usuario, como los grifos de botones.

- **ViewController.Designer.CS** es un archivo generado, creado por iOS Designer para asignar la interfaz construida visualmente al código. Dado que los cambios realizados en este archivo se sobrescribirán, no debe modificarse. Las declaraciones de propiedad de este archivo permiten que el código de la `ViewController` clase obtenga acceso, por **nombre**, a los controles configurados en el diseñador de iOS. Al abrir **ViewController.Designer.CS** , se revela el siguiente código:

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

La `SubmitButton` declaración de propiedad conecta toda la `ViewController` clase, no solo el archivo **ViewController.Designer.CS** , al botón definido en el guión gráfico. Dado que **ViewController.CS** define parte de la `ViewController` clase, tiene acceso a `SubmitButton` .

En la captura de pantalla siguiente se muestra que IntelliSense ahora reconoce la `SubmitButton` referencia en **ViewController.CS**:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![IntelliSense que reconoce la referencia de SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense que reconoce la referencia de SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IntelliSense que reconoce la referencia de SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense que reconoce la referencia de SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

En esta sección se muestra cómo crear un botón en el diseñador de iOS y cómo obtener acceso a ese botón en el código.

En el resto de este documento se proporciona información general sobre el diseñador de iOS.

## <a name="ios-designer-basics"></a>aspectos básicos del diseñador de iOS

En esta sección se presentan las partes del diseñador de iOS y se ofrece un paseo por sus características.

### <a name="launching-the-ios-designer"></a>Inicio del diseñador de iOS

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Los proyectos de Xamarin. iOS creados con Visual Studio para Mac incluyen un guion gráfico. Para ver el contenido de un guion gráfico, haga doble clic en el archivo. Storyboard en el **Panel de solución**:

[![Un guión gráfico abierto en el diseñador de iOS](introduction-images/7-storyboardopen-vsmac.png "Un guión gráfico abierto en el diseñador de iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

La mayoría de los proyectos de Xamarin. iOS creados con Visual Studio incluyen un guion gráfico. Para ver el contenido de un guion gráfico, haga doble clic en el archivo. Storyboard en el **Explorador de soluciones**:

[![Un guión gráfico abierto en el diseñador de iOS](introduction-images/7-storyboardopen-vs.png "Un guión gráfico abierto en el diseñador de iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"></a>

### <a name="ios-designer-features"></a>características del diseñador de iOS

IOS Designer tiene seis secciones principales:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Secciones del diseñador de iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "Secciones del diseñador de iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Superficie de diseño** : el área de trabajo principal del diseñador de iOS. En el área del documento, se habilita la construcción visual de las interfaces de usuario.
2. **Barra de herramientas restricciones** : permite cambiar entre el modo de edición de Marcos y el modo de edición de restricciones, de dos maneras diferentes para colocar elementos en una interfaz de usuario.
3. **Cuadro de herramientas** : enumera los controladores, objetos, controles, vistas de datos, reconocedores de gestos, ventanas y barras que se pueden arrastrar a la superficie de diseño y agregarlos a una interfaz de usuario.
4. **Panel de propiedades** : muestra las propiedades del control seleccionado, incluidas la identidad, los estilos visuales, la accesibilidad, el diseño y el comportamiento.
5. **Esquema del documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol, se selecciona en el diseñador de iOS y se muestran sus propiedades en el  **Panel de propiedades**. Esto resulta útil para seleccionar un control específico en una interfaz de usuario profundamente anidada.
6. **Barra de herramientas inferior** : contiene opciones para cambiar el modo en que el diseñador de iOS muestra el archivo. Storyboard o. Xib, incluidos los dispositivos, la orientación y el zoom.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Secciones del diseñador de iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "Secciones del diseñador de iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Superficie de diseño** : el área de trabajo principal del diseñador de iOS. En el área del documento, se habilita la construcción visual de las interfaces de usuario.
2. **Barra de herramientas restricciones** : permite cambiar entre el modo de edición de Marcos y el modo de edición de restricciones, de dos maneras diferentes para colocar elementos en una interfaz de usuario.
3. **Cuadro de herramientas** : enumera los controladores, objetos, controles, vistas de datos, reconocedores de gestos, ventanas y barras que se pueden arrastrar a la superficie de diseño y agregarlos a una interfaz de usuario.
4. **Ventana Propiedades** : muestra las propiedades del control seleccionado, incluidas la identidad, los estilos visuales, la accesibilidad, el diseño y el comportamiento.
5. **Esquema del documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol, se selecciona en el diseñador de iOS y se muestran sus propiedades en la  **ventana Propiedades**. Esto resulta útil para seleccionar un control específico en una interfaz de usuario profundamente anidada.
6. **Barra de herramientas inferior** : contiene opciones para cambiar el modo en que el diseñador de iOS muestra el archivo. Storyboard o. Xib, incluidos los dispositivos, la orientación y el zoom.

-----

### <a name="design-workflow"></a>Diseño del flujo de trabajo

#### <a name="adding-a-control-to-the-interface"></a>Agregar un control a la interfaz

Para agregar un control a una interfaz, arrástrelo desde el **cuadro de herramientas** y colóquelo en la superficie de diseño. Al agregar o colocar un control, las instrucciones verticales y horizontales resaltan las posiciones de diseño usadas con frecuencia como centro vertical, centro horizontal y márgenes:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![En la superficie de diseño, las instrucciones destacan las posiciones de diseño usadas con frecuencia](introduction-images/9-layoutguides-vsmac.png "En la superficie de diseño, las instrucciones destacan las posiciones de diseño usadas con frecuencia")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![En la superficie de diseño, las instrucciones destacan las posiciones de diseño usadas con frecuencia](introduction-images/9-layoutguides-vs.png "En la superficie de diseño, las instrucciones destacan las posiciones de diseño usadas con frecuencia")

-----

La línea de puntos azules en el ejemplo anterior proporciona una guía de alineación visual del centro horizontal para ayudar con la colocación de los botones.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandos del menú contextual

Un menú contextual está disponible tanto en la superficie de diseño como en el **esquema del documento**. Este menú proporciona comandos para el control seleccionado y su elemento primario, lo que resulta útil cuando se trabaja con vistas en una jerarquía anidada:

[![Menú contextual en la superficie de diseño](introduction-images/10-contextmenudesignsurface-vsmac.png "Menú contextual en la superficie de diseño")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barra de herramientas restricciones

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Barra de herramientas de condados](introduction-images/11-constraintstoolbar-vsmac.png "Barra de herramientas restricciones")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Barra de herramientas de condados](introduction-images/11-constraintstoolbar-vs.png "Barra de herramientas restricciones")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barra de herramientas restricciones se ha actualizado y ahora consta de dos controles: el modo de edición de fotogramas o el modo de edición de restricciones, y el botón actualizar restricciones/actualizar Marcos.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Alternancia del modo de edición de Marcos/modo de edición de restricciones

En las versiones anteriores del diseñador de iOS, al hacer clic en una vista ya seleccionada en la superficie de diseño, se alterna entre el modo de edición de Marcos y el modo de edición de restricciones. Ahora, un control de alternancia en la barra de herramientas de restricciones cambia entre estos modos de edición.

- Modo de edición de fotogramas:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Botón modo de edición de fotogramas](introduction-images/12a-frameeditingmode-vsmac.png "Botón modo de edición de fotogramas")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Botón modo de edición de fotogramas](introduction-images/12a-frameeditingmode-vs.png "Botón modo de edición de fotogramas")

-----

- Modo de edición de restricciones:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Botón modo de edición de restricciones](introduction-images/12b-constrainteditingmode-vsmac.png "Botón modo de edición de restricciones")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Botón modo de edición de restricciones](introduction-images/12b-constrainteditingmode-vs.png "Botón modo de edición de restricciones")

-----

#### <a name="update-constraints--update-frames-button"></a>Botón actualizar restricciones/actualizar marcos

El botón actualizar restricciones/actualizar marcos se encuentra a la derecha del botón de alternancia modo de edición de fotogramas/modo de edición de restricciones.

- En el modo de edición de fotogramas, al hacer clic en este botón, se ajustan los fotogramas de los elementos seleccionados para que coincidan con sus restricciones.
- En el modo de edición de restricciones, al hacer clic en este botón, se ajustan las restricciones de los elementos seleccionados para que coincidan con sus fotogramas.

### <a name="bottom-toolbar"></a>Barra de herramientas inferior

La barra de herramientas inferior proporciona una manera de seleccionar el dispositivo, la orientación y el zoom usados para ver un archivo Storyboard o. Xib en el diseñador de iOS:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas inferior, que se usa para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vsmac.png "La barra de herramientas inferior, que se usa para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas inferior, que se usa para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vs.png "La barra de herramientas inferior, que se usa para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo y orientación

Una vez expandida, la barra de herramientas inferior muestra todos los dispositivos, las orientaciones y las adaptaciones aplicables al documento actual. Al hacer clic en ellos, se cambia la vista mostrada en la superficie de diseño. 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas inferior, expandida para mostrar dispositivos y orientaciones](introduction-images/14-bottomtoolbarexpanded-vsmac.png "La barra de herramientas inferior, expandida para mostrar dispositivos y orientaciones")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas inferior, expandida para mostrar dispositivos y orientaciones](introduction-images/14-bottomtoolbarexpanded-vs.png "La barra de herramientas inferior, expandida para mostrar dispositivos y orientaciones")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Tenga en cuenta que la selección de un dispositivo y la orientación solo cambia el modo en que el diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección actual, las restricciones recién agregadas se aplican a todos los dispositivos y orientaciones, a menos que se haya usado el botón **Editar rasgos** para especificar lo contrario.

Cuando se [habilitan](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes) [las clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) , el botón **Editar rasgos** aparecerá en la barra de herramientas inferior expandida.  Al hacer clic en el botón **Editar rasgos** , se muestran las opciones para crear una variación de interfaz basada en la clase de tamaño representada por el dispositivo seleccionado y la orientación. Considere los siguientes ejemplos:

- Si se selecciona **iPhone se**  /  **verticalmente**, el elemento flotante proporcionará opciones para crear una variación de interfaz para la clase de ancho compacto, tamaño de alto normal. 
- Si **iPad Pro 9.7"**  /  **Landscape**  /  está seleccionada la **pantalla completa** de iPad Pro 9,7 "horizontal, el elemento flotante proporcionará opciones para crear una variación de interfaz para la clase de ancho normal, tamaño de alto normal.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas inferior que se usa para modificar una interfaz por clase de tamaño](introduction-images/15-edittraitsbutton-vsmac.png "La barra de herramientas inferior que se usa para modificar una interfaz por clase de tamaño")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas inferior que se usa para modificar una interfaz por clase de tamaño](introduction-images/15-edittraitsbutton-vs.png "La barra de herramientas inferior que se usa para modificar una interfaz por clase de tamaño")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controles de zoom

La superficie de diseño admite el zoom a través de varios controles:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Los controles de zoom en la barra de herramientas inferior](introduction-images/16-zoomcontrols-vsmac.png "Los controles de zoom en la barra de herramientas inferior")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Los controles de zoom en la barra de herramientas inferior](introduction-images/16-zoomcontrols-vs.png "Los controles de zoom en la barra de herramientas inferior")

-----

Entre los controles se incluyen los siguientes:

1. Zoom para ajustar
2. Alejamiento
3. Acercamiento
4. Tamaño real (tamaño de píxel de 1:1)

Estos controles ajustan el zoom en la superficie de diseño. No afectan a la interfaz de usuario de la aplicación en tiempo de ejecución.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

### <a name="properties-pad"></a>Panel de propiedades

Use el **Panel de propiedades** para editar la identidad, los estilos visuales, la accesibilidad y el comportamiento de un control. En la captura de pantalla siguiente se muestran las opciones de **Panel de propiedades** de un botón:

[![Panel de propiedades para un botón](introduction-images/17-buttonpropertiespad-vsmac.png "Panel de propiedades para un botón")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Panel de propiedades secciones

El **Panel de propiedades** contiene tres secciones:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Ventana Propiedades

Use la **ventana Propiedades** para editar la identidad, los estilos visuales, la accesibilidad y el comportamiento de un control. En la captura de pantalla siguiente se muestran las opciones de la **ventana Propiedades** para un botón:

[![La ventana Propiedades de un botón](introduction-images/17-buttonpropertieswindow-vs.png "La ventana Propiedades de un botón")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Secciones de la ventana Propiedades

La **ventana Propiedades** contiene tres secciones:

-----

1. **Widget** : las propiedades principales del control, como el nombre, la clase, las propiedades de estilo, etc. Las propiedades para administrar el contenido del control normalmente se colocan aquí.
2. **Diseño** : aquí se enumeran las propiedades que realizan un seguimiento de la posición y el tamaño del control, incluidas las restricciones y los marcos.
3. **Eventos** : los eventos y los controladores de eventos se especifican aquí. Resulta útil para controlar eventos como, por ejemplo, tocar, puntear, arrastrar, etc. Los eventos también se pueden administrar directamente en el código.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Editar propiedades en el Panel de propiedades

Además de la edición visual en la superficie de diseño, el diseñador de iOS admite la edición de propiedades en el **Panel de propiedades**. Las propiedades disponibles cambian en función del control seleccionado, tal como se muestra en las capturas de pantallas siguientes:

[![Propiedades del botón](introduction-images/18a-buttonpropertiespad-vsmac.png "Propiedades del botón")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Ver las propiedades del controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "Ver las propiedades del controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Editar propiedades en la ventana Propiedades

Además de la edición visual en la superficie de diseño, el diseñador de iOS admite la edición de propiedades en la **ventana Propiedades**. Las propiedades disponibles cambian en función del control seleccionado, tal como se muestra en las capturas de pantallas siguientes:

[![Propiedades del botón](introduction-images/18a-buttonpropertieswindow-vs.png "Propiedades del botón")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Ver las propiedades del controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "Ver las propiedades del controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> En la sección identidad del Panel de propiedades ahora se muestra un campo de **módulo** . Es necesario rellenar esta sección solo al interoperar con clases SWIFT. Úselo para escribir un nombre de módulo para las clases SWIFT, que están en el espacio de nombres.

#### <a name="default-values"></a>Valores predeterminados

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Muchas propiedades en el **Panel de propiedades** no muestran ningún valor ni un valor predeterminado. Sin embargo, el código de la aplicación todavía puede modificar estos valores. En el **Panel de propiedades** no se muestran los valores establecidos en el código.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Muchas propiedades de la **ventana Propiedades** no muestran ningún valor ni un valor predeterminado. Sin embargo, el código de la aplicación todavía puede modificar estos valores. La **ventana Propiedades** no muestra los valores establecidos en el código.

-----

#### <a name="event-handlers"></a>Controladores de eventos

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para especificar controladores de eventos personalizados para varios eventos, utilice la pestaña **eventos** del **Panel de propiedades**. Por ejemplo, en la siguiente captura de pantalla, un `HandleClick` método controla el evento **Touch Up** del botón en el evento:

[![El Panel de propiedades, con un controlador de eventos establecido para un botón](introduction-images/19-buttonpropertiespadevents-vsmac.png "El Panel de propiedades, con un controlador de eventos establecido para un botón")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para especificar controladores de eventos personalizados para varios eventos, utilice la pestaña **eventos** de la **ventana Propiedades**. Por ejemplo, en la siguiente captura de pantalla, un `HandleClick` método controla el evento **Touch Up** del botón en el evento:

[![La ventana Propiedades, con un controlador de eventos establecido para un botón](introduction-images/19-buttonpropertieswindowevents-vs.png "La ventana Propiedades, con un controlador de eventos establecido para un botón")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Una vez que se ha especificado un controlador de eventos, se debe agregar un método con el mismo nombre a la clase de controlador de vista correspondiente. De lo contrario, `unrecognized selector` se producirá una excepción cuando se puntee en el botón:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Excepción de selector desconocida](introduction-images/20-unrecognizedselector-vsmac.png "Excepción de selector desconocida")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Tenga en cuenta que, después de que se haya especificado un controlador de eventos en el **Panel de propiedades**, el diseñador de iOS abrirá inmediatamente el archivo de código correspondiente y la oferta para insertar la declaración del método. 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Excepción de selector desconocida](introduction-images/20-unrecognizedselector-vs.png "Excepción de selector desconocida")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Para obtener un ejemplo que usa controladores de eventos personalizados, consulte la [Guía de introducción de Hello, iOS](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>vista Esquema

El diseñador de iOS también puede mostrar la jerarquía de controles de una interfaz como un esquema. El esquema está disponible seleccionando la pestaña **esquema del documento** , como se muestra a continuación:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Esquema del documento](introduction-images/21-buttonoutlineview-vsmac.png "Esquema del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Esquema del documento](introduction-images/21-buttonoutlineview-vs.png "Esquema del documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

El control seleccionado en la vista de esquema permanece sincronizado con el control seleccionado en la superficie de diseño.  Esta característica es útil para seleccionar un elemento de una jerarquía de interfaz profundamente anidada.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Revertir a Xcode

Se puede usar el diseñador de iOS y Xcode Interface Builder indistintamente. Para abrir un guión gráfico o un archivo. Xib en Xcode Interface Builder, haga clic con el botón derecho en el archivo y seleccione **abrir con > Xcode Interface Builder**, tal como se muestra en la siguiente captura de pantalla:

[![Abrir un guion gráfico en Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Abrir un guion gráfico en Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Después de hacer modificaciones en Xcode Interface Builder, guarde el archivo y vuelva a Visual Studio para Mac. Los cambios se sincronizarán con el proyecto de Xamarin. iOS.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Revertir a Xcode

Se puede usar el diseñador de iOS y Xcode Interface Builder indistintamente, pero Xcode Interface Builder solo está disponible en Mac. Para abrir un archivo Storyboard o. Xib en Xcode Interface Builder en un equipo Mac, abra la solución que contiene el proyecto de Xamarin. iOS en [Visual Studio para Mac](/visualstudio/mac/), haga clic con el botón derecho en el archivo y seleccione **abrir con > Xcode Interface Builder**, tal como se muestra en la siguiente captura de pantalla:

[![Abrir un guion gráfico en Xcode Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Abrir un guion gráfico en Xcode Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Después de hacer modificaciones en Xcode Interface Builder, guarde el archivo y vuelva a Visual Studio para Mac. Los cambios se sincronizarán con el proyecto de Xamarin. iOS.

-----

## <a name="xib-support"></a>compatibilidad con. Xib

IOS Designer permite crear, editar y administrar archivos. Xib. Se trata de archivos XML que representar vistas personalizadas únicas que se pueden agregar a la jerarquía de vistas de una aplicación. Un archivo. Xib generalmente representa la interfaz para una vista o pantalla única en una aplicación, mientras que un guion gráfico representa muchas pantallas y las transiciones entre ellas.

Hay muchas opiniones sobre qué soluciones (archivos. Xib, guiones gráficos o código) funcionan mejor para crear y mantener una interfaz de usuario. En realidad, no hay ninguna solución perfecta y siempre merece la pena considerar la mejor herramienta para el trabajo a mano. Dicho esto, los archivos. Xib suelen ser más eficaces cuando se usan para representar una vista personalizada necesaria en varios lugares de una aplicación, como una celda de vista de tabla personalizada. 

Puede encontrar más documentación sobre el uso de archivos. Xib en las siguientes recetas:

- [Usar la plantilla View. Xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Crear un TableViewCell personalizado con un. Xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Crear una pantalla de inicio con un. Xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Para obtener más información sobre el uso de guiones gráficos, consulte la [Introducción a los guiones gráficos](~/ios/user-interface/storyboards/index.md).

Esta y otras guías relacionadas con el diseñador de iOS hacen referencia al uso de guiones gráficos como enfoque estándar para crear interfaces de usuario, ya que la mayoría de las plantillas de proyecto de Xamarin. iOS nuevas proporcionan un guión gráfico de forma predeterminada.

## <a name="summary"></a>Resumen

En esta guía se proporciona una introducción al diseñador de iOS, en la que se describen sus características y se destacan las herramientas que ofrece para diseñar interfaces de usuario atractivas.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles que se diseñan en iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Información general de Android Designer](~/android/user-interface/android-designer/index.md)
- [Clases y métodos parciales](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Profundizar en el Xamarin Designer para iOS: evolución del 2014 (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usar el diseñador de iOS para crear una pantalla de inicio (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
