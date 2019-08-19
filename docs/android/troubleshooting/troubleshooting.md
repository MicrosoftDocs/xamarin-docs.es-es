---
title: Sugerencias para la solución de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f57736238fc01d865d7655d72da427e2c18dca59
ms.sourcegitcommit: e02b725e48af867eb2c53ac9e17805f778fbbc8c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2019
ms.locfileid: "68757241"
---
# <a name="troubleshooting-tips"></a>Sugerencias para la solución de problemas


## <a name="getting-diagnostic-information"></a>Obtención de información de diagnóstico

Xamarin. Android tiene algunos lugares en los que buscar cuando se realiza un seguimiento de varios errores.
Entre ellas se incluyen las siguientes:

1.  Salida de MSBuild de diagnóstico.
2.  Registros de implementación de dispositivos.
3.  Salida del registro de depuración de Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Salida de MSBuild de diagnóstico

El diagnóstico de MSBuild puede contener información adicional relacionada con la generación de paquetes y puede contener información de implementación de paquetes.

Para habilitar el resultado de diagnóstico de MSBuild en Visual Studio:

1.  Haga clic en **herramientas > opciones...**
2.  En la vista de árbol del lado izquierdo, seleccione **proyectos y soluciones > compilar y ejecutar** .
3.  En el panel derecho, establezca la lista desplegable detalle de salida de compilación de MSBuild en diagnóstico.
4.  Haga clic en **Aceptar**.
5.  Limpie y recompile el paquete.
6.  La salida de diagnóstico es visible en el panel de salida.


Para habilitar la salida del diagnóstico de MSBuild en Visual Studio para Mac/OS X:

1.  Haga clic en **Visual Studio para Mac preferencias de >...**
2.  En la vista de árbol del lado izquierdo, seleccione **proyectos > compilación** .
3.  En el panel derecho, establezca la lista desplegable de nivel de detalle del registro en diagnóstico.
4.  Haga clic en **Aceptar**.
5.  Reinicie Visual Studio para Mac.
6.  Limpie y recompile el paquete.
7.  La salida de diagnóstico es visible en el panel de errores (**ver > paneles > errores** ), haciendo clic en el botón generar resultado.




## <a name="device-deployment-logs"></a>Registros de implementación de dispositivos

Para habilitar el registro de implementación de dispositivos en Visual Studio:

1.  **Herramientas > opciones...** >
2.  En la vista de árbol del lado izquierdo, seleccione **Xamarin > Android Settings** .
3.  En el panel derecho, active la casilla [X] registro de depuración de la **extensión (escribe monodroid. log en el escritorio)** .
4.  Los mensajes de registro se escriben en el archivo monodroid. log en el escritorio.


Visual Studio para Mac escribe siempre los registros de implementación del dispositivo. Buscarlos es algo más difícil. se crea un archivo de registro de *AndroidUtils* para cada día + hora en que se produce una implementación, por ejemplo: **AndroidTools-2012-10-24_12-35-45.log**.

-  En Windows, los archivos de registro se `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`escriben en.
-  En OS X, los archivos de registro se `$HOME/Library/Logs/XamarinStudio-{VERSION}`escriben en.




## <a name="android-debug-log-output"></a>Salida del registro de depuración de Android

Android escribirá muchos mensajes en el [registro](~/android/deploy-test/debugging/android-debug-log.md)de depuración de Android.
Xamarin. Android usa las propiedades del sistema de Android para controlar la generación de mensajes adicionales en el registro de depuración de Android. Las propiedades del sistema de Android se pueden establecer mediante el comando *setprop* en el [Android Debug Bridge (ADB)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Las propiedades del sistema se leen durante el inicio del proceso y, por tanto, se deben establecer antes de que se inicie la aplicación o se debe reiniciar la aplicación después de cambiar las propiedades del sistema.



### <a name="xamarinandroid-system-properties"></a>Propiedades del sistema de Xamarin.Android

Xamarin. Android admite las siguientes propiedades del sistema:

-   *debug.mono.debug*: Si es una cadena no vacía, es equivalente a `*mono-debug*`.

-   *debug.mono.env*: Una lista de variables de entorno *|* separadas por canalización (' ') que se van a exportar durante el inicio de la aplicación, *antes* de que se haya inicializado mono. Esto permite establecer variables de entorno que controlan el registro de mono.

    - *Nota*: Dado que el valor está *|* separado por "", el valor debe tener un nivel adicional de cotización, ya \`que el comando de *Shell* \` ADB quitará un conjunto de Comillas.

    - *Nota*: Los valores de propiedad del sistema Android no pueden tener más de 92 caracteres de longitud.

    - Ejemplo:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*: Una lista separada por comas (' *,* ') de los componentes que deben imprimir mensajes adicionales en el registro de depuración de Android. De forma predeterminada, no se establece nada. Los componentes incluyen:

    -   *all*: Imprimir todos los mensajes
    -   *gc*: Imprime mensajes relacionados con GC.
    -   *gref*: Mensajes de asignación y desasignación de referencia de impresión (débil, global).
    -   *lref*: Imprimir mensajes de asignación y desasignación de referencia local.

    *Nota*: Estos son *muy* detallados. No habilite a menos que realmente sea necesario.

-   *debug.mono.trace*: Permite establecer la configuración de [seguimiento](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` mono.

## <a name="deleting-bin-and-obj"></a>`bin` Eliminar y`obj`

Xamarin. Android ha sufrido en el pasado desde una situación como:

- Se produce un error de compilación o en tiempo de ejecución extraño.
- Usted `Clean` `bin` `obj` , `Rebuild`o elimina manualmente los directorios y.
- El problema desaparece.

Hemos invertido mucho en solucionar problemas como estos debido a su impacto en la productividad de los desarrolladores.

Si se produce un problema como este:

1. Cree una nota mental. ¿Cuál fue la última acción que obtuvo el proyecto en este estado?
1. Guarde el registro de compilación actual. Intente compilar de nuevo y grabe un [registro de compilación de diagnóstico](#diagnostic-msbuild-output).
1. Enviar un [Informe de errores][bug].

Antes de eliminar los `bin` directorios `obj` y, cópielos y guárdelos para su posterior diagnóstico, si es necesario. Probablemente, puede simplemente `Clean` usar el proyecto de aplicación de Xamarin. Android para que funcionen de nuevo.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin. Android no puede resolver System. ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15,1 o anterior) solo es compatible con el **sistema. ValueTuple NuGet 4.3.0** (o anterior).

- **Visual Studio 2017 Update 2** (la versión 15,2 o posterior) solo es compatible con el **sistema. ValueTuple NuGet 4.3.1** (o posterior).

Elija el NuGet System. ValueTuple correcto que se corresponda con la instalación de Visual Studio 2017.


## <a name="gc-messages"></a>Mensajes GC

Los mensajes del componente GC se pueden ver estableciendo la propiedad del sistema Debug. mono. log en un valor que contenga GC.

Los mensajes GC se generan cada vez que se ejecuta el GC y proporciona información acerca de la cantidad de trabajo que hizo el GC:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Se puede generar información adicional de GC, como la información de tiempo `MONO_LOG_LEVEL` , si se `debug`establece la variable de entorno en:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Esto producirá (muchos) mensajes mono adicionales, incluidos estos tres de consecuencia:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

En el `GC_BRIDGE` mensaje, `num-objects` es el número de objetos de puente que este paso está considerando `num_hash_entries` y es el número de objetos procesados durante esta invocación del código del puente.

En los `GC_MINOR` mensajes `GC_MAJOR` y, `total` es la cantidad de tiempo durante el que el mundo se pausa (no se ejecuta ningún subproceso) `bridge` , mientras que es la cantidad de tiempo que se tarda en el código de procesamiento del puente (que se encarga de la máquina virtual Java). El mundo *no* está en pausa mientras se produce el procesamiento del puente.

 *En general*, cuanto mayor sea el valor `num_hash_entries`de, más tiempo tardarán `bridge` las colecciones y más grande será el `total` tiempo dedicado a la recopilación.



## <a name="global-reference-messages"></a>Mensajes de referencia global

Para habilitar el registro de loggig de referencia global (GREF), la propiedad del sistema *Debug. mono. log* debe contener *Gref*, por ejemplo:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin. Android usa referencias globales de Android para proporcionar asignaciones entre instancias de Java y las instancias administradas asociadas, como cuando se invoca un método Java, es necesario proporcionar una instancia de Java a Java.

Desafortunadamente, los emuladores de Android solo permiten la existencia de 2000 referencias globales a la vez. El hardware tiene un límite mucho más alto de 52000 referencias globales. El límite inferior puede ser problemático al ejecutar aplicaciones en el emulador, por lo que saber de *dónde* procede la instancia puede ser muy útil.

 *Nota*: el recuento de referencias globales es interno a Xamarin. Android y no (y no puede) incluir referencias globales tomadas por otras bibliotecas nativas cargadas en el proceso. Usar el recuento de referencias globales como una estimación.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Hay cuatro mensajes de consecuencia:

-  Creación de referencia global: son las líneas que comienzan por *+ g +* y proporcionarán un seguimiento de la pila para la ruta de acceso del código de creación.
-  Destrucción de referencia global: son las líneas que comienzan con *-g-* y pueden proporcionar un seguimiento de la pila para la ruta de código que elimina la referencia global. Si el GC está desechando el Gref, no se proporcionará ningún seguimiento de la pila.
-  Creación de referencia global débil: estas son las líneas que comienzan con *+ w +* .
-  Destrucción de referencia global débil: son líneas que comienzan por *-w-* .


En todos los mensajes, el valor de *grefc* es el recuento de referencias globales que ha creado Xamarin. Android, mientras que el valor de *grefwc* es el recuento de referencias globales débiles que ha creado Xamarin. Android. El *identificador* o el valor *de identificador de obj* es el valor de identificador de JNI y el carácter */* que aparece después de ' ' es el tipo de valor de identificador: */l* para la referencia local, */g* para las referencias globales y */w* para las referencias globales débiles.

Como parte del proceso de GC, las referencias globales (+ g +) se convierten en referencias globales débiles (lo que produce + w + y-g-), se inicia un GC de Java y, a continuación, se comprueba la referencia global débil para ver si se recopiló. Si todavía está activo, se crea un nuevo Gref en torno a la referencia débil (+ g +,-w-); en caso contrario, se destruye la referencia débil (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Una instancia de Java se crea y se encapsula mediante un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Se está realizando un GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>El objeto sigue estando activo, como handle! = null
## <a name="wref-turned-back-into-a-gref"></a>Wref se vuelve a convertir en Gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>El objeto está inactivo, como identificador = = null
## <a name="wref-is-freed-no-new-gref-created"></a>Wref se libera, no se crea ningún nuevo Gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Aquí hay una arruga "interesante": en los destinos que ejecutan Android anteriores a 4,0, el valor de Gref es igual a la dirección del objeto Java en la memoria del Runtime de Android. (Es decir, el GC es un recopilador sin movimiento y conservador, y está entregando referencias directas a esos objetos). Por lo tanto, después de a + g +, + w +,-g-, + g +,-w-Sequence, el Gref resultante tendrá el mismo valor que el valor de Gref original. Esto hace que grepping a través de los registros sea bastante sencillo.

Sin embargo, Android 4,0 tiene un recolector en movimiento y ya no entrega referencias directas a objetos de máquina virtual en tiempo de ejecución de Android. Por consiguiente, después de a + g +, + w +,-g-, + g +,-w-Sequence, el valor de Gref será *diferente*. Si el objeto sobrevive a varios catálogos globales, irá entre varios valores de Gref, lo que dificultará la determinación del lugar desde el que se asignó realmente una instancia.

### <a name="querying-programmatically"></a>Consultar mediante programación

Puede consultar los recuentos Gref y WREF consultando el `JniRuntime` objeto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-Recuento de referencias globales

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-Recuento de referencias débiles



## <a name="android-debug-logs"></a>Registros de depuración de Android

Los [registros](~/android/deploy-test/debugging/android-debug-log.md) de depuración de Android pueden proporcionar contexto adicional respecto a los errores en tiempo de ejecución que se ven.



## <a name="floating-point-performance-is-terrible"></a>El rendimiento del punto flotante es terrible.

Como alternativa, "mi aplicación se ejecuta 10 veces más rápido con la compilación de depuración que con la compilación de versión"

Xamarin. Android admite varios dispositivos Abi: *armeabi*, *armeabi-v7a*y *x86*. Los dispositivos Abi se pueden especificar en **las propiedades del proyecto > pestaña aplicación > arquitecturas admitidas**.

Las compilaciones de depuración usan un paquete de Android que proporciona todas Abi y, por tanto, usará la ABI más rápida para el dispositivo de destino.

Las compilaciones de versión solo incluirán el Abi seleccionado en la pestaña propiedades del proyecto. Se puede seleccionar más de uno.

*armeabi* es la ABI predeterminada y tiene la compatibilidad más amplia del dispositivo. *Sin embargo*, armeabi no es compatible con los dispositivos de varias CPU y el punto flotante de hardware, Amont otras cosas. Por consiguiente, las aplicaciones que usan el tiempo de ejecución de la versión armeabi se asociarán a un solo núcleo y usarán una implementación de punto flotante. Ambos pueden contribuir a un rendimiento considerablemente más lento de la aplicación.

Si su aplicación requiere un rendimiento de punto flotante aceptable (por ejemplo, juegos), debe habilitar la ABI *armeabi-v7a* . Es posible que quiera admitir solo el tiempo de ejecución *de armeabi-v7a* , aunque esto significa que los dispositivos más antiguos que solo admiten *armeabi* no podrán ejecutar la aplicación.



## <a name="could-not-locate-android-sdk"></a>No se pudo encontrar Android SDK

Hay 2 descargas disponibles de Google para el Android SDK para Windows.
Si elige el instalador. exe, escribirá las claves del registro que indican a Xamarin. Android dónde se instaló. Si elige el archivo. zip y lo descomprime, Xamarin. Android no sabe dónde buscar el SDK. Puede indicar a Xamarin. Android Dónde está el SDK en Visual Studio; para ello, vaya a **herramientas > opciones > Xamarin > configuración de Android**:

[![Android SDK ubicación en la configuración de Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>El IDE no muestra el dispositivo de destino

A veces, intentará implementar la aplicación en un dispositivo, pero el dispositivo en el que desea realizar la implementación no se muestra en el cuadro de diálogo Seleccionar dispositivo. Esto puede ocurrir cuando el Android Debug Bridge decide entrar en vacaciones.

Para diagnosticar este problema, busque el [programa ADB](~/android/deploy-test/debugging/android-debug-log.md)y luego ejecute:

```shell
adb devices
```

Si el dispositivo no está presente, deberá reiniciar el servidor de Android Debug Bridge para poder encontrar el dispositivo:

```shell
adb kill-server
adb start-server
```

El software de sincronización HTC puede evitar que **ADB Start-Server** funcione correctamente. Si el comando **ADB Start-Server** no imprime en qué puerto comienza, salga del software de sincronización HTC e intente reiniciar el servidor ADB.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>No se pudo ejecutar el ejecutable de tarea especificado "keytool"

Esto significa que la ruta de acceso no contiene el directorio donde se encuentra el directorio bin del SDK de Java. Compruebe que ha seguido los pasos de la guía de [instalación](~/android/get-started/installation/index.md) .


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid. exe o aresgen. exe se cerró con el código 1

Para ayudarle a depurar este problema, vaya a Visual Studio y cambie el nivel de detalle de MSBuild; para ello, seleccione: **Herramientas > opciones > proyectos** y **soluciones >** compilar y **Ejecutar > el detalle** de la salida de la compilación del proyecto de MSBuild y establecer este valor en **normal**.

Vuelva a generar y compruebe el panel de resultados de Visual Studio, que debe contener el error completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>No hay suficiente espacio de almacenamiento en el dispositivo para implementar el paquete

Esto sucede cuando no se inicia el emulador desde dentro de Visual Studio. Al iniciar el emulador fuera de Visual Studio, debe pasar las `-partition-size 512` opciones, por ejemplo,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Asegúrese de usar el nombre correcto del simulador, es decir, [el nombre que usó al configurar el simulador](~/android/get-started/installation/windows.md#device).


## <a name="install_failed_invalid_apk-when-installing-a-package"></a>\_Error\_deinstalación\_de APK no válido al instalar un paquete

Los nombres de paquete de Android *deben* contener un punto (' *.* '). Edite el nombre del paquete para que contenga un punto.

-   En Visual Studio:
    -   Haga clic con el botón derecho en el proyecto > propiedades
    -   Haga clic en la pestaña manifiesto de Android de la izquierda.
    -   Actualice el campo Nombre del paquete.
        -   Si ve el mensaje &ldquo;no se encontró archivo AndroidManifest. Xml. Haga clic para agregar uno. &rdquo;, haga clic en el vínculo y, a continuación, actualice el campo Nombre del paquete.
-   Dentro de Visual Studio para Mac:
    -   Haga clic con el botón derecho en el proyecto > opciones.
    -   Vaya a la sección compilación/aplicación Android.
    -   Cambie el campo Nombre del paquete para que contenga un '. '.




## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>No\_se\_pudo\_instalar\_la biblioteca compartida que faltaba al instalar un paquete

En este contexto, una "biblioteca compartida" *no* es un archivo de biblioteca compartida nativa (*libfoo.so*); en su lugar, es una biblioteca que se debe instalar por separado en el dispositivo de destino, como Google Maps.

El paquete de Android especifica qué bibliotecas compartidas son `<uses-library/>` necesarias con el elemento. Si una biblioteca *requerida* no está presente en el dispositivo de destino (por `//uses-library/@android:required` ejemplo, es *true*, que es el valor predeterminado), la instalación del paquete producirá un error con la instalación con *errores\_\_de instalación\_que falta compartido\_ Biblioteca*.

Para determinar qué bibliotecas compartidas son necesarias, vea el archivo**archivo AndroidManifest. XML** *generado*
(p. ej., **\\obj Debug\\Android\\archivo AndroidManifest. XML**) y busque el `<uses-library/>` elementos. `<uses-library/>`los elementos se pueden agregar manualmente en el archivo **archivo AndroidManifest\\. XML de las propiedades** del proyecto y a través del [atributo personalizado UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Por ejemplo, al agregar una referencia de ensamblado a *mono. Android. GoogleMaps. dll* , se `<uses-library/>` agregará implícitamente un para la biblioteca compartida de Google Maps.



## <a name="install_failed_update_incompatible-when-installing-a-package"></a>No\_se\_pudo\_instalar la actualización no compatible al instalar un paquete

Los paquetes de Android tienen tres requisitos:

-   Deben contener un '. ' (vea la entrada anterior)
-   Deben tener un nombre de paquete de cadena único (por lo tanto, la Convención inverso-TLD que se ha detectado en los nombres de aplicaciones Android, por ejemplo, com. Android. Chrome para la aplicación Chrome).
-   Al actualizar los paquetes, el paquete debe tener la misma clave de firma.

Por lo tanto, Imagínese este escenario:

1.  Compilar & implementar la aplicación como una aplicación de depuración
2.  Puede cambiar la clave de firma, por ejemplo, para usarla como una aplicación de versión (o porque no le guste la clave de firma de depuración proporcionada por el valor predeterminado).
3.  La aplicación se instala sin quitarla primero, por ejemplo, depurar > iniciar sin depurar en Visual Studio


Cuando esto sucede, se producirá un error en la\_instalación\_del\_paquete debido a un error de instalación no compatible con la actualización, ya que el nombre del paquete no ha cambiado mientras se realizaba la clave de firma. El [registro](~/android/deploy-test/debugging/android-debug-log.md) de depuración de Android también contendrá un mensaje similar al siguiente:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corregir este error, Quite completamente la aplicación del dispositivo antes de volver a instalar.


## <a name="install_failed_uid_changed-when-installing-a-package"></a>\_Error\_deinstalacióndeUIDmodificadaalinstalarunpaquete\_

Cuando se instala un paquete de Android, se le asigna un *identificador de usuario* (UID).
*A veces*, por motivos actualmente desconocidos, al instalar en una aplicación ya instalada, se producirá un error en la instalación con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para solucionar este problema, desinstale *completamente* el paquete de Android, ya sea mediante la instalación de la aplicación desde la GUI del destino de `adb`Android o con:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**no usar** , ya que esto conservará los datos de la aplicación y, por tanto, conservará el UID en conflicto en el dispositivo de destino. `adb uninstall -k`



## <a name="release-apps-fail-to-launch-on-device"></a>Las aplicaciones de versión no se pueden iniciar en el dispositivo

La salida del registro de depuración de Android contendrá un mensaje similar al siguiente:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Si es así, hay dos causas posibles:

1.  El. apk no proporciona una ABI que el dispositivo de destino admite.
    Por ejemplo,. apk solo contiene archivos binarios armeabi-v7a y el dispositivo de destino solo admite armeabi.

2.  Un [error de Android](http://code.google.com/p/android/issues/detail?id=21670). En este caso, desinstale la aplicación, cruce los dedos y vuelva a instalar la aplicación.

Para corregir (1), edite las opciones y propiedades del proyecto y [agregue compatibilidad para la ABI necesaria a la lista de ABI admitidos](~/android/app-fundamentals/cpu-architectures.md). Para determinar qué ABI necesita agregar, ejecute el siguiente comando ADB en el dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

La salida contendrá el Abi principal (y el secundario opcional).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La propiedad outpath no está establecida para el &ldquo;proyecto MyApp. csproj&rdquo;

Por lo general, esto significa que tiene un equipo HP y &ldquo;la&rdquo; plataforma variable de entorno se ha establecido en algo como MCD o HPD. Esto entra en conflicto con la propiedad de la plataforma MSBuild que &ldquo;normalmente se&rdquo; establece en&rdquo;cualquier CPU o &ldquo;x86. Tendrá que quitar esta variable de entorno de la máquina para que MSBuild pueda funcionar:

-   Panel de control > variables de entorno avanzadas del sistema > >

Reinicie Visual Studio o Visual Studio para Mac e intente volver a compilar. Ahora, las cosas deberían funcionar según lo previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>Java. lang. ClassCastException: mono. Android. Runtime. JavaObject no se puede convertir en...

Xamarin. Android 4. x no calcula correctamente las referencias de tipos genéricos anidados. Por ejemplo, considere el siguiente código\# de C mediante [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


El problema es que Xamarin. Android serializa incorrectamente tipos genéricos anidados. `List<IDictionary<string, object>>` se está serializando en [java.lang.ArrrayList](xref:Java.Util.ArrayList), pero `ArrayList` contiene instancias `mono.android.runtime.JavaObject` (que hacen referencia a las instancias `Dictionary<string, object>`) en lugar de algo que implementa [java.util.Map](xref:Java.Util.IMap), por lo que se produce la siguiente excepción:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

La solución consiste en usar los [tipos de colección de Java](~/android/internals/api-design.md) proporcionados en `System.Collections.Generic` lugar de los &ldquo;tipos&rdquo; para los tipos internos. Esto producirá los tipos de Java adecuados al calcular las referencias de las instancias. (El código siguiente es más complicado de lo necesario para reducir la duración de Gref. Se puede simplificar para modificar el código original a través `s/List/JavaList/g` de `s/Dictionary/JavaDictionary/g` y si las duraciones de Gref no son preocupadas).

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Esto se corregirá en una versión futura](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>Excepciones NullReferenceException inesperado

En ocasiones, el [registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md) mencionará excepciones NullReferenceException que &ldquo;no se pueden producir&rdquo; o que provienen de mono para el código en tiempo de ejecución de Android justo antes de que la aplicación sufra un problema:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

o

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Esto puede ocurrir cuando el tiempo de ejecución de Android decide anular el proceso, lo que puede ocurrir por diversos motivos, como alcanzar el límite de Gref de destino &ldquo;o&rdquo; realizar algún error con JNI.

Para ver si este es el caso, compruebe en el registro de depuración de Android si hay un mensaje de su proceso similar al siguiente:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Anular debido a agotamiento de referencia global

La capa de JNI del Runtime de Android solo admite un número limitado de referencias de objeto JNI para ser válidas en un momento dado. Cuando se supera este límite, se interrumpen las cosas.

El límite de GREF (*referencia global*) es 2000 referencias en el emulador y ~ 52000 referencias en el hardware.

Sabe que va a empezar a crear demasiadas GREFs cuando vea mensajes como este en el registro de depuración de Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Cuando se alcanza el límite de GREF, se imprime un mensaje como el siguiente:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


En el ejemplo anterior (que, casualmente, proviene del [error 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)), el problema es que se están creando demasiadas instancias de Android. Graphics. Point; vea [el \#comentario 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obtener una lista de las correcciones de este error concreto.

Normalmente, una solución útil es encontrar el tipo que tiene demasiadas instancias asignadas &ndash; Android. Graphics. Point en el volcado &ndash; anterior y, a continuación, buscar el lugar en el que se crearon en el código fuente y desecharlos de forma adecuada (de modo que su La duración de los objetos de Java se acorta). Esto no siempre es adecuado (\#685215 es multiproceso, por lo que la solución trivial evita la llamada a Dispose), pero es lo primero que hay que tener en cuenta.

Puede habilitar el [registro de Gref](~/android/troubleshooting/index.md) para ver cuándo se crean GREFs y cuántos existen.


## <a name="abort-due-to-jni-type-mismatch"></a>Anular debido a que el tipo de JNI no coincide

Si realiza la entrega manual de código JNI, es posible que los tipos no coincidan correctamente, por ejemplo, si intenta invocar `java.lang.Runnable.run` en un tipo que no implementa. `java.lang.Runnable` Cuando esto suceda, habrá un mensaje similar al siguiente en el registro de depuración de Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Compatibilidad con código dinámico

### <a name="dynamic-code-does-not-compile"></a>El código dinámico no se compila

Para usar C\# Dynamic en la aplicación o biblioteca, tiene que agregar System. Core. dll, Microsoft. CSharp. dll y mono. CSharp. dll al proyecto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>En la compilación de versión, MissingMethodException se produce para el código dinámico en tiempo de ejecución.

-   Es probable que el proyecto de aplicación no tenga referencias a System. Core. dll, Microsoft. CSharp. dll o mono. CSharp. dll. Asegúrese de que se hace referencia a esos ensamblados.

    -   Tenga en cuenta que el código dinámico siempre cuesta. Si necesita código eficaz, considere la posibilidad de no usar código dinámico.

-   En la primera vista previa, se excluyen esos ensamblados a menos que el código de aplicación use explícitamente los tipos de cada ensamblado. Vea lo siguiente para obtener una solución alternativa:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Proyectos compilados con el bloqueo de AOT + LLVM en dispositivos x86

Al implementar una aplicación compilada con [AOT + LLVM](~/android/deploy-test/release-prep/index.md) en dispositivos basados en x86, es posible que vea un mensaje de error de excepción similar al siguiente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Se trata de un problema conocido tal y como se indicó en [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). La solución consiste en deshabilitar LLVM.
