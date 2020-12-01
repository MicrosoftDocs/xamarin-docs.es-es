---
title: Diseño de interfaces de usuario con Xcode
description: Obtenga información sobre la manera recomendada de crear interfaces de usuario de iOS directamente mediante Xcode en un equipo Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b25245250bd9ea17034ea8f6b11388a12fc13241
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439494"
---
# <a name="designing-user-interfaces-with-xcode"></a>Diseño de interfaces de usuario con Xcode

A partir de Visual Studio 2019 versión 16,8 y Visual Studio para Mac versión 8,8, la manera recomendada de editar archivos. Storyboard y. NIB es editarlas en Xcode Interface Builder en un equipo Mac.

> [!NOTE]
> A partir de la versión 16,9 de Visual Studio 2019, no habrá ninguna manera compatible de editar guiones gráficos de iOS en Windows. Use Visual Studio para Mac y Xcode Interface Builder para continuar con la compilación de las interfaces de usuario de Xamarin. iOS.

En este artículo se describen las soluciones comunes para crear interfaces de usuario con la Interface Builder de Xcode.  Este artículo puede ser especialmente útil si editó previamente las ius con el diseñador de Xamarin. iOS. 

Para obtener un tutorial más detallado sobre los guiones gráficos, vea [guiones gráficos en Xamarin. iOS](./indepth-storyboard.md).

## <a name="how-to-open-a-storyboard"></a>Cómo abrir un guion gráfico 

Abra un archivo de interfaz de usuario de iOS en Visual Studio para Mac; para ello, haga clic con el botón derecho en un archivo de guion gráfico y seleccione **Xcode Interface Builder**:

[![Seleccionar Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

A continuación, debería ver la ventana Xcode abierta. Cualquier modificación guardada aquí se reflejará en el proyecto de Visual Studio.

[![Ventana de Xcode](images/xcode.png)](images/xcode.png#lightbox)

Para obtener más información sobre el Interface Builder de Xcode, vea [Interface Builder integrado](https://developer.apple.com/xcode/interface-builder/).

## <a name="creating-a-new-control"></a>Crear un nuevo control

Para crear un nuevo control con la Interface Builder de Xcode, primero seleccione el guion gráfico que desea editar. A continuación, abra el cuadro de diálogo biblioteca de Xcode (**vista**  >  **Mostrar biblioteca**) y arrastre el control al guion gráfico.

[![Selector de biblioteca](images/library-picker.png)](images/library-picker.png#lightbox)

A continuación, abra el archivo de encabezado del controlador de vista correspondiente.  En el caso de una aplicación de Xamarin. iOS en blanco "vista única", el guion gráfico predeterminado se denomina **Main. Storyboard**. El archivo de controlador de vista correspondiente se denomina **ViewController.CS** en Visual Studio con un archivo de encabezado **ViewController. h** correspondiente cuando se visualiza desde Xcode.

En el Interface Builder de Xcode, abra el guión gráfico y el archivo de encabezado del controlador de vista correspondiente.  Manteniendo presionada la tecla de **control** ( **^** ), arrastre el control desde el guión gráfico hasta el archivo del controlador de vista hasta que Xcode le solicite un cuadro de diálogo.

[![Control de vínculo de demostración](images/demo-link-control.gif)](images/demo-link-control.gif#lightbox)

Como se muestra arriba, el código de C# correspondiente se generará automáticamente en el archivo de código subyacente del controlador de vista.  Ahora puede tener acceso a este control dentro del proyecto de Xamarin. iOS.

## <a name="editing-an-existing-controls-name"></a>Editar el nombre de un control existente

Para editar el nombre de un control existente desde el Interface Builder de Xcode y reflejar ese cambio en el proyecto de C#, navegue hasta el archivo de encabezado del controlador de vista adecuado, haga clic con el botón derecho en haga clic en y seleccione **refactorizar**.   

[![Refactorizar control](images/refactor-control.png)](images/refactor-control.png#lightbox)

El archivo de código subyacente se regenerará con el nuevo nombre, lo que le permitirá tener acceso al control a través del código de Visual Studio para Mac.

## <a name="known-problems"></a>Problemas conocidos

En esta sección se tratan los problemas conocidos.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>"Visual Studio no se pudo comunicar con Xcode"

En macOS Catalina o superior, puede encontrar el siguiente error:

[![no se comunica el error ERR](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

En primer lugar, en las preferencias del sistema de Mac en **seguridad & privacidad > automatización**, asegúrese de que aparece Visual Studio y se comprueba **Xcode** .

[![seguridad de macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Si se comprueba **Xcode** y aparece el mensaje de error, es posible que tenga que restablecer los permisos de privacidad Visual Studio para Mac.

Para ello, inicie una ventana de terminal y emita el siguiente comando:

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Para asegurarse de que el cambio anterior surte efecto, restablezca el PRAM de su Mac. Para obtener instrucciones, consulte [restablecer NVRAM o PRAM en el equipo Mac](https://support.apple.com/HT204063).
