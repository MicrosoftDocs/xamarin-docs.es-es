---
title: Recarga activa de XAML para Xamarin.Forms
description: Vuelva a cargar los cambios en el archivo XAML al instante en la aplicación en ejecución para que no tenga que compilar el Xamarin.Forms proyecto después de cada cambio de XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/01/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aefd0ded8d9d0076a55f6a878761e5bb25ed69c9
ms.sourcegitcommit: 278963e21bd07fe4b0bf0ed71b32ccb53dd95eff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2021
ms.locfileid: "101841119"
---
# <a name="xaml-hot-reload-for-xamarinforms"></a>Recarga activa de XAML para Xamarin.Forms

La recarga activa de XAML se conecta al flujo de trabajo existente para aumentar su productividad y ahorrar tiempo. Sin la recarga activa de XAML, debe compilar e implementar la aplicación cada vez que quiera ver un cambio de XAML. Con la recarga activa, al guardar el archivo XAML, los cambios se reflejan en directo en la aplicación en ejecución. Además, se mantendrán el estado y los datos de navegación, lo que le permitirá iterar rápidamente en la interfaz de usuario sin perder su lugar en la aplicación. Por lo tanto, con la recarga activa de XAML, pasará menos tiempo recompilando e implementando sus aplicaciones para validar los cambios de la interfaz de usuario.

> [!NOTE]
> Si está escribiendo una aplicación nativa de UWP o WPF, y no usa Xamarin.Forms , consulte [recarga activa de XAML para UWP y WPF](/visualstudio/debugger/xaml-hot-reload).

## <a name="system-requirements"></a>Requisitos del sistema

| IDE/marco de trabajo | Versión mínima requerida |
|------|------------------|
|Visual Studio 2019 | 16,9 para el modo de solo cambios, 16,4 para el modo de página completa
Visual Studio 2019 para Mac | 8,9 para el modo de solo cambios, 8,4 para el modo de página completa
Xamarin.Forms | 5,0 para el modo de solo cambios; 4,1 para el modo de página completa

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>Habilitar la recarga activa de XAML para Xamarin.Forms

Si se inicia desde una plantilla, la recarga activa de XAML está activada de forma predeterminada y el proyecto está configurado para funcionar sin ninguna configuración adicional. Depure la aplicación Android, iOS o UWP en un emulador o un dispositivo físico y cambie el código XAML para desencadenar una recarga activa de XAML.

Si está trabajando desde una solución existente Xamarin.Forms , no se requiere ninguna instalación adicional para usar la recarga activa de XAML, pero es posible que tenga que comprobar la configuración para garantizar la mejor experiencia. En primer lugar, habilítelo en la configuración del IDE:

* En Windows, active la casilla **Habilitar recarga activa de XAML** (y las plataformas necesarias) en **herramientas**  >  **Opciones**  >  **depurar**  >  **recargas activas**.
  * En versiones anteriores de Visual Studio 2019, la casilla se encuentra en **herramientas**  >  **Opciones** de  >    >  **recarga en caliente** de Xamarin.
* En Mac, active la casilla **Habilitar la recarga activa de Xamarin** en herramientas de preferencias de **Visual Studio**  >    >  para la  >  **recarga activa** de Xamarin XAML.
  * En versiones anteriores de Visual Studio para Mac, la casilla se encuentra en el menú de preferencias de **Visual Studio** proyectos de la  >    >    >  **recarga en caliente de Xamarin**.

Después, en la configuración de compilación de iOS y Android, compruebe que el enlazador está establecido en "no vincular" o "vincular ninguno". Para usar la recarga activa de XAML con un dispositivo iOS físico, también tiene que activar **la casilla habilitar el intérprete mono** (visual Studio 16,4 y versiones posteriores) o Add **--Interpreter** para **Mtouch args adicionales** (Visual Studio 16,3 y versiones anteriores).

Puede usar el siguiente diagrama de flujo para comprobar la configuración del proyecto existente para su uso con la recarga activa de XAML:

![Configuración de recarga activa de XAML](hot-reload-images/hotreloadflowchart.png "Diagrama de flujo de instalación de recarga activa de XAML")

<!-- https://aka.ms/xamarin-hot-reload-mode points to this section -->
## <a name="hot-reload-modes"></a>Modos de recarga activa

La recarga activa de XAML puede funcionar en dos modos diferentes: el *modo de solo cambios* más reciente y el *modo de página completa* anterior.

Desde Visual Studio 16,9 y Visual Studio para Mac 8,9, el comportamiento predeterminado es para el modo de solo cambios que se va a usar para todas las aplicaciones que usan Xamarin.Forms 5,0 o posterior. En versiones anteriores de Xamarin.Forms , se utiliza el modo de página completa. Sin embargo, puede forzar el uso del modo de página completa para todas las aplicaciones en la configuración del IDE de recarga en caliente (opciones de **herramientas**  >    >  **depurar**  >  **recargas activas** en Windows o   >    >  **herramientas de preferencias de** Visual Studio para la  >  **recarga activa** de Xamarin XAML en Mac).

El *modo solo cambios* analiza el código XAML para ver exactamente lo que cambió al realizar una edición y envía solo los cambios a la aplicación en ejecución. Se trata de la misma tecnología que se usa para la recarga activa de WPF y UWP. Conserva el estado de la interfaz de usuario, ya que no vuelve a crear la interfaz de usuario para la página completa, solo actualiza las propiedades cambiadas en los controles afectados por las ediciones. El modo solo cambia el uso del [árbol visual dinámico](live-visual-tree.md).

De forma predeterminada, con solo cambios en el modo, no es necesario guardar el archivo para ver los cambios. las actualizaciones se aplican inmediatamente, a medida que escribe.
Sin embargo, puede cambiar este comportamiento para que solo se actualice al guardar archivos. Para ello, active la casilla **aplicar recarga activa de XAML al guardar el documento** (actualmente solo está disponible en Windows) en la configuración del IDE de recarga en caliente. En ocasiones, la actualización en el almacenamiento de documentos puede ser útil si se realizan actualizaciones de XAML más grandes y no se desea que se muestren hasta que se completan.

El *modo de página completa* envía el archivo XAML completo a la aplicación en ejecución después de realizar las modificaciones y guardar. La aplicación en ejecución vuelve a cargar la página y vuelve a crear sus controles; verá la actualización de la interfaz de usuario.

El modo de solo cambios es el futuro de la recarga activa y se recomienda usarlo siempre que sea posible. Es rápido, conserva el estado de la interfaz de usuario y admite el [árbol visual dinámico](live-visual-tree.md). Todavía se proporciona el modo de página completa para las aplicaciones que aún no se han actualizado a Xamarin.Forms 5,0.

> [!NOTE]
> Deberá reiniciar la sesión de depuración al cambiar de modo.

## <a name="xaml-errors"></a>Errores de XAML

*Solo cambia el modo*: Si realiza un cambio, el analizador de XAML de recarga en caliente considera como no válido, mostrará el error subrayado en el editor e incluirlo en la ventana de errores. Estos errores de recarga activa tienen un código de error que empieza por "XHR" (para la recarga activa de XAML). Si hay algún error en la página, la recarga activa no aplicará los cambios, aunque se realice en otras partes de la página. Corrija todos los errores de recarga activa para empezar a trabajar de nuevo en la página.

*Modo de página completa*: Si realiza un cambio que no se puede volver a cargar la recarga activa de XAML, se mostrará el error subrayado en el editor y se incluirá en la ventana errores. Estos cambios, conocidos como ediciones forzadas, incluyen escribir indirectamente el código XAML o conectar un control a un controlador de eventos que no existe. Incluso con una edición forzada, puede seguir recargando sin reiniciar la aplicación: haga otro cambio en otra parte del archivo XAML y presione guardar. La edición forzada no se recargará, pero se seguirán aplicando los demás cambios.

## <a name="reload-on-multiple-platforms-at-once"></a>Recarga en varias plataformas a la vez

La recarga activa de XAML admite la depuración simultánea en Visual Studio y Visual Studio para Mac. Puede implementar un dispositivo Android y un destino iOS al mismo tiempo para ver los cambios reflejados en ambas plataformas a la vez. Para depurar en varias plataformas, consulte:

* **Windows** [Cómo: establecer proyectos de inicio múltiples](/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [establecer varios proyectos de inicio](/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>Restricciones conocidas

* Xamarin.Forms los destinos más allá de Android, iOS y UWP (por ejemplo, macOS) no se admiten actualmente.
* El uso de [XamlCompilation (XamlCompilationOptions. SKIP)], deshabilitar la compilación de XAML, no se admite y puede causar problemas con el árbol visual dinámico.
* No se pueden agregar, quitar ni cambiar el nombre de archivos o paquetes NuGet durante una sesión de recarga activa de XAML. Si agrega o quita un archivo o un paquete de NuGet, vuelva a compilar y volver a implementar la aplicación para seguir usando la recarga activa de XAML.
* Establezca el enlazador en **no vincular** o **vincular ninguno** para obtener la mejor experiencia. La configuración de **Link SDK solo** funciona la mayor parte del tiempo, pero puede producir un error en ciertos casos. La configuración del enlazador se puede encontrar en las opciones de compilación de iOS y Android.
* La depuración en un iPhone físico requiere que el intérprete use la recarga activa de XAML. Para ello, abra la configuración del proyecto, seleccione la pestaña compilación de iOS y asegúrese **de que la opción Habilitar el intérprete mono** está habilitada. Es posible que tenga que cambiar la opción de **plataforma** en la parte superior de la página de propiedades a **iPhone**.
* La recarga activa de XAML no puede volver a cargar el código de C#, incluidos los controladores de eventos, los controles personalizados, el código subyacente de la página y las clases adicionales.


## <a name="troubleshooting"></a>Solución de problemas

* Abra la salida de recarga activa de XAML para ver los mensajes de estado, lo que puede ayudar a solucionar problemas:
  * **Windows**: muestra la salida con   >  la **salida** de vista y selecciona la **recarga activa de Xamarin** en **Mostrar salida de:** en la parte superior   
  * **Mac**: Mantenga el mouse sobre la **recarga activa de XAML** en la barra de estado para mostrar ese panel
* Si no se puede inicializar la recarga activa de XAML:
  * Actualice su Xamarin.Forms versión.
  * Asegúrese de que se encuentre en la versión más reciente del IDE.
  * Establezca la configuración del enlazador de Android o iOS en **no vincular** en la configuración de compilación del proyecto.
* Si no ocurre nada al guardar el archivo XAML, asegúrese de que la recarga activa de XAML está habilitada en el IDE.
* Si realiza la depuración en un iPhone físico y la aplicación deja de responder, compruebe que el intérprete está habilitado. Para activarla, Active **la casilla habilitar el intérprete mono** (Visual Studio 16.4/8.4 y versiones arriba) o Add **--Interpreter** en el campo **Mtouch argumentos adicionales** (Visual Studio 16.3/8.3 y anterior) en la configuración de compilación de iOS.

Para notificar un error, use la **ayuda**  >  **Enviar comentarios**  >  **notificar un problema** en Windows y **ayudar a**  >  **notificar un problema** en Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Árbol visual dinámico](live-visual-tree.md)
- [Sugerencias y trucos para la recarga activa de XAML](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
- [Recarga activa de XAML para Xamarin.Forms en profundidad: el programa Xamarin](https://www.youtube.com/watch?v=crhjjPjzknk)
