---
title: "la recarga activa de XAML para Xamarin.Forms " Description: "vuelve a cargar los cambios en el archivo XAML al instante en la aplicación en ejecución para que no tenga que compilar el Xamarin.Forms proyecto después de cada cambio de XAML".
MS. Prod: Xamarin ms. AssetID: E220F054-32EE-424C-A7E5-6156BE271519 ms. Technology: Xamarin-Forms Author: maddyleger1 ms. Author: maleger ms. Date: 03/14/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-hot-reload-for-xamarinforms"></a>Recarga activa de XAML paraXamarin.Forms

La recarga activa de XAML se conecta al flujo de trabajo existente para aumentar su productividad y ahorrar tiempo. Sin la recarga activa de XAML, debe compilar e implementar la aplicación cada vez que quiera ver un cambio de XAML. Con la recarga activa, al guardar el archivo XAML, los cambios se reflejan en directo en la aplicación en ejecución. Además, se mantendrán el estado y los datos de navegación, lo que le permitirá iterar rápidamente en la interfaz de usuario sin perder su lugar en la aplicación. Por lo tanto, con la recarga activa de XAML, pasará menos tiempo recompilando e implementando sus aplicaciones para validar los cambios de la interfaz de usuario.

> [!NOTE]
> Si está escribiendo una aplicación de WPF o de UWP, consulte [recarga en caliente de XAML para UWP y WPF](/visualstudio/debugger/xaml-hot-reload).
>
> La recarga activa de XAML Xamarin.Forms para _no_ funciona actualmente en los proyectos de Xamarin.Forms UWP.

## <a name="system-requirements"></a>Requisitos del sistema

| IDE/marco de trabajo | Versión necesaria |
|------|------------------|
|Visual Studio 2019 | 16,4 o superior
Visual Studio 2019 para Mac | 8,4 o superior
Xamarin.Forms | 4,1 o superior

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>Habilitar la recarga activa de XAML paraXamarin.Forms

Si se inicia desde una plantilla, la recarga activa de XAML está activada de forma predeterminada y el proyecto está configurado para funcionar sin ninguna configuración adicional. Depure la aplicación en un emulador, simulador o dispositivo físico Android o iOS, cambie el código XAML y guarde el archivo para desencadenar una recarga activa de XAML.

Si está trabajando desde una solución existente Xamarin.Forms , no se requiere ninguna instalación adicional para usar la recarga activa de XAML, pero es posible que tenga que comprobar la configuración para garantizar la mejor experiencia. En primer lugar, habilítelo en la configuración del IDE:

* En Windows, active la casilla **Habilitar recarga activa de Xamarin** en **herramientas**  >  **Opciones**,  >  **Xamarin**  >  **recarga en caliente**de Xamarin.
* En Mac, active la casilla **Habilitar la recarga activa de Xamarin** en herramientas de preferencias de **Visual Studio**  >  **Preferences**  >  para la**Tools for Xamarin**  >  **recarga activa**de Xamarin XAML.
  * En versiones anteriores de Visual Studio para Mac, el menú se encuentra en **Visual Studio**  >  **Preferences**  >  **Projects**  >  **Xamarin Hot Reload**.

Después, en la configuración de compilación de iOS y Android, compruebe que el enlazador está establecido en "no vincular" o "vincular ninguno". Para usar la recarga activa de XAML con un dispositivo iOS físico, también tiene que activar **la casilla habilitar el intérprete mono** (visual Studio 16,4 y versiones posteriores) o Add **--Interpreter** para **Mtouch args adicionales** (Visual Studio 16,3 y versiones anteriores).

Puede usar el siguiente diagrama de flujo para comprobar la configuración del proyecto existente para su uso con la recarga activa de XAML:

![Configuración de recarga activa de XAML](hot-reload-images/hotreloadflowchart.png "Diagrama de flujo de instalación de recarga activa de XAML")

## <a name="resilient-reloading"></a>Recarga resistente

Si realiza un cambio que no se puede volver a cargar la recarga activa de XAML, se mostrará un mensaje de error con IntelliSense. Estos cambios, conocidos como ediciones forzadas, incluyen escribir indirectamente el código XAML o conectar un control a un controlador de eventos que no existe. Incluso con una edición forzada, puede seguir recargando sin reiniciar la aplicación: haga otro cambio en otra parte del archivo XAML y presione guardar. La edición forzada no se recargará, pero se seguirán aplicando los demás cambios.

## <a name="reload-on-multiple-platforms-at-once"></a>Recarga en varias plataformas a la vez

La recarga activa de XAML admite la depuración simultánea en Visual Studio y Visual Studio para Mac. Puede implementar un dispositivo Android y un destino iOS al mismo tiempo para ver los cambios reflejados en ambas plataformas a la vez. Para depurar en varias plataformas, consulte:
* **Windows** [Cómo: establecer proyectos de inicio múltiples](https://docs.microsoft.com/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [establecer varios proyectos de inicio](https://docs.microsoft.com/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>Restricciones conocidas

* Xamarin.FormsTodavía *no* se admiten otros destinos, como UWP y MacOS. [Aquí](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html)puede realizar un seguimiento del progreso del soporte técnico de UWP.
* No se pueden agregar, quitar ni cambiar el nombre de archivos o paquetes NuGet durante una sesión de recarga activa de XAML. Si agrega o quita un archivo o un paquete de NuGet, vuelva a compilar y volver a implementar la aplicación para seguir usando la recarga activa de XAML.
* Establezca el enlazador en **no vincular** o **vincular ninguno** para obtener la mejor experiencia. La configuración de **Link SDK solo** funciona la mayor parte del tiempo, pero puede producir un error en ciertos casos. La configuración del enlazador se puede encontrar en las opciones de compilación de iOS y Android.
* La depuración en un iPhone físico requiere que el intérprete use la recarga activa de XAML. Para ello, abra la configuración del proyecto, seleccione la pestaña compilación de iOS y asegúrese **de que la opción Habilitar el intérprete mono** está habilitada. Es posible que tenga que cambiar la opción de **plataforma** en la parte superior de la página de propiedades a **iPhone**.
* Las referencias creadas mediante la asignación de un control a otro campo o propiedad utilizando su `x:Name` valor no se volverán a cargar.
* La actualización de la jerarquía visual de la aplicación de Shell en AppShell. Xaml puede producir problemas al mantener el estado de la aplicación. Si tiene problemas, vuelva a compilar la aplicación para continuar con la recarga.
* La recarga activa de XAML no puede volver a cargar el código de C#, incluidos los controladores de eventos, los controles personalizados, el código subyacente de la página y las clases adicionales.

## <a name="more-resources"></a>Más recursos

* [Sugerencias y trucos para la recarga activa de XAML](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [Recarga activa de XAML para Xamarin.Forms en profundidad: el programa Xamarin](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>Solución de problemas

* Si no se puede inicializar la recarga activa de XAML:
  * Actualice su Xamarin.Forms versión.
  * Asegúrese de que se encuentre en la versión más reciente del IDE.
  * Establezca la configuración del enlazador de Android o iOS en **no vincular** en la configuración de compilación del proyecto.
* Si no ocurre nada al guardar el archivo XAML, asegúrese de que la recarga activa de XAML está habilitada en el IDE.
* Si realiza la depuración en un iPhone físico y la aplicación deja de responder, compruebe que el intérprete está habilitado. Para activarla, Active **la casilla habilitar el intérprete mono** (Visual Studio 16.4/8.4 y versiones arriba) o Add **--Interpreter** en el campo **Mtouch argumentos adicionales** (Visual Studio 16.3/8.3 y anterior) en la configuración de compilación de iOS.

Para notificar un error, use la **ayuda**  >  **Enviar comentarios**  >  **notificar un problema** en Windows y **ayudar a**  >  **notificar un problema** en Mac.
