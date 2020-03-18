---
title: Vista previa de XAML para Xamarin. Forms
description: En este artículo se explica cómo usar el previsor de XAML para ver los diseños de Xamarin. Forms que se representan a medida que escribe. El previsor de XAML está disponible en Visual Studio 2019 y Visual Studio 2019 para Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
ms.openlocfilehash: 465783c0771b666a276d18f47cf5d3d458d52933
ms.sourcegitcommit: 8df67f0d76ff762b517d27b8d4c217d3a3379a18
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2020
ms.locfileid: "79423933"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Vista previa de XAML para Xamarin. Forms

_Vea los diseños de Xamarin. Forms que se representan a medida que escribe._

## <a name="overview"></a>Información general

El previsor de XAML muestra cómo se verá la página XAML de Xamarin. Forms en iOS y Android. Cuando realice cambios en el código XAML, verá que se muestra una vista previa de inmediato junto con el código. El previsor de XAML está disponible en Visual Studio y Visual Studio para Mac.

## <a name="getting-started"></a>Introducción

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

Para abrir el cuadro de vista previa de XAML, haga clic en las flechas del panel vista en dos paneles. Si desea cambiar el comportamiento predeterminado de la vista en dos paneles, use las opciones de las herramientas > > cuadro de diálogo de **vista previa de XAML de xamarin > Xamarin. Forms** . En este cuadro de diálogo, puede seleccionar la vista de documento predeterminada y la orientación de división.

[![Opciones de vista previa de Xamarin. Forms en Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "Opciones de vista previa de Xamarin. Forms en Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Al abrir un archivo XAML, el editor abrirá el tamaño completo o junto al previsualizador, en función de la configuración seleccionada en las opciones de herramientas > > cuadro de diálogo de la **vista previa de XAML de xamarin > Xamarin. Forms** . Sin embargo, se puede cambiar la división de cada archivo en la ventana del editor.

#### <a name="xaml-preview-controls"></a>Controles de vista previa de XAML

Elija si desea ver el código, el previsor de XAML o ambos; para ello, seleccione estos botones en el panel vista en dos paneles. El botón central intercambia el lado del código que está en la vista previa y el código:

[![Controles de vista previa de Xamarin. Forms para cambiar entre el diseño, el origen y la vista en dos paneles en Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Controles de vista previa de Xamarin. Forms para cambiar entre el diseño, el origen y la vista en dos paneles en Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

Puede cambiar si la pantalla está dividida vertical u horizontalmente o contraer un panel por completo:

[![Controles de orientación del panel de vista previa de Xamarin. Forms en Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Controles de orientación del panel de vista previa de Xamarin. Forms en Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

#### <a name="enable-or-disable-the-xaml-previewer"></a>Habilitar o deshabilitar el previsor de XAML

Puede desactivar la vista previa de XAML en el cuadro de diálogo **herramientas > opciones > xamarin > Xamarin. Forms XAML Previewer** seleccionando **Editor XML predeterminado** como **editor XAML predeterminado**. Esto también desactiva el esquema del documento, el panel de propiedades y el cuadro de herramientas XAML. Para volver a activar la vista previa de XAML y las herramientas, cambie el **editor XAML predeterminado** a la **vista previa de Xamarin. Forms**.

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

El botón **vista previa** se muestra en el editor cuando se abre una página XAML. Para mostrar u ocultar el previsor, presione los botones **vista previa** o **dividir** en la parte inferior izquierda de cualquier ventana de documento XAML:

[![el previsor de Xamarin. Forms habilitado con el botón vista previa o dividir](xaml-previewer-images/xamlp-list-sml.png)](xaml-previewer-images/xamlp-list.png#lightbox)

> [!NOTE]
> En versiones anteriores de Visual Studio para Mac, el botón **vista previa** se encontraba en la parte superior derecha de la ventana.

#### <a name="enable-or-disable-the-xaml-previewer"></a>Habilitar o deshabilitar el previsor de XAML

Puede desactivar la vista previa de XAML en el cuadro de diálogo **preferencias de > de Visual Studio > editor de texto > XAML** seleccionando **Editor XML predeterminado** como **editor XAML predeterminado**. Esto también desactiva el esquema del documento, el panel de propiedades y el cuadro de herramientas XAML. Para volver a activar la vista previa de XAML y las herramientas, cambie el **editor XAML predeterminado** a la **vista previa de Xamarin. Forms**.

::: zone-end

## <a name="xaml-previewer-options"></a>Opciones de vista previa de XAML

Las opciones que aparecen a lo largo de la parte superior del panel de vista previa son:

* **Android** : Mostrar la versión de Android de la pantalla
* **iOS** : Mostrar la versión de iOS de la pantalla (*Nota: Si usa Visual Studio en Windows, debe [emparejarse con un equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para usar este modo*).
* Lista desplegable de dispositivos de dispositivos Android o iOS, **incluida la resolución** y el tamaño de pantalla
* **Vertical (icono)** : usa la orientación vertical para la vista previa
* **Horizontal (icono)** : usa la orientación horizontal para la vista previa

## <a name="detect-design-mode"></a>Detectar el modo de diseño

La propiedad [`DesignMode.IsDesignModeEnabled`](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) estáticas indica si la aplicación se está ejecutando en la vista previa. Si lo usa, puede especificar código que solo se ejecutará cuando la aplicación se esté ejecutando en el visor de vista previa:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

Esta propiedad es útil si Inicializa una biblioteca en el constructor de páginas que no se puede ejecutar en tiempo de diseño.

## <a name="troubleshooting"></a>Solución de problemas

Compruebe los problemas siguientes y los [foros de Xamarin](https://forums.xamarin.com/categories/xamarin-forms), si el visor de vista previa no funciona.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>El previsor XAML no se muestra o muestra un error

* Puede tardar algún tiempo en iniciarse el previsor; verá "inicializando representación" hasta que esté listo.
* Intente cerrar y volver a abrir el archivo XAML.
* Asegúrese de que la clase de `App` tenga un constructor sin parámetros.
* Compruebe la versión de Xamarin. Forms: debe ser al menos Xamarin. Forms 3,6. Puede actualizar a la versión más reciente de Xamarin. Forms a través de NuGet.
* Comprobar la instalación de JDK: la vista previa de Android requiere al menos [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Intente envolver cualquier clase inicializada en el C# código de la página subyacente en `if (!DesignMode.IsDesignModeEnabled)`.

### <a name="custom-controls-arent-rendering"></a>Los controles personalizados no se representan

Intente compilar el proyecto. El controlador de vista previa muestra la clase base del control si no puede representar el control o si el creador del control optó por la representación en tiempo de diseño. Para obtener más información, vea [representar controles personalizados en la vista previa de XAML](render-custom-controls.md).
