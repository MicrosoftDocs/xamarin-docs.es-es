---
title: Sugerencias para la solución de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 8855c80dd7478813408abaf2cfec68d48eced3bc
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571836"
---
# <a name="troubleshooting-tips"></a>Sugerencias para la solución de problemas

## <a name="getting-diagnostic-information"></a>Obtención de información de diagnóstico

En Xamarin.Android hay algunos lugares en los que buscar cuando se realiza el seguimiento de varios errores.
Se incluyen los siguientes:

1. Salida de MSBuild de diagnóstico.
2. Registros de implementación de dispositivos.
3. Salida del registro de depuración de Android.

<a name="Diagnostic_MSBuild_Output"></a>

## <a name="diagnostic-msbuild-output"></a>Salida de MSBuild de diagnóstico

El diagnóstico de MSBuild puede contener información adicional relacionada con la generación de paquetes, además de algunos datos sobre implementación de paquetes.

Para habilitar el resultado de diagnóstico de MSBuild en Visual Studio:

1. Haga clic en **Herramientas > Opciones...**
2. En la vista de árbol de la izquierda, seleccione **Proyectos y soluciones > Compilación y ejecución**.
3. En el panel de la derecha, establezca la lista desplegable Detalles de la salida de la compilación de MSBuild en Diagnóstico.
4. Haga clic en **Aceptar**.
5. Limpie y recompile el paquete.
6. La salida de diagnóstico se muestra en el panel Salida.

Para habilitar la salida de diagnóstico de MSBuild desde Visual Studio para Mac/OS X:

1. Haga clic en **Visual Studio para Mac > Preferencias...**
2. En la vista de árbol de la izquierda, seleccione **Proyectos > Compilar**
3. En el panel de la derecha, establezca el menú desplegable Nivel de detalle de registro en Diagnóstico.
4. Haga clic en **Aceptar**.
5. Reinicie Visual Studio para Mac.
6. Limpie y recompile el paquete.
7. Los resultados de diagnóstico se pueden ver en el panel de errores (**Ver > Paneles > Errores**) si hace clic en el botón Salida de la compilación.

## <a name="device-deployment-logs"></a>Registros de implementación de dispositivos

Para habilitar el registro de implementación de dispositivos en Visual Studio:

1. **Herramientas > Opciones...** >
2. En la vista de árbol de la izquierda, seleccione **Xamarin > Configuración de Android**.
3. En el panel de la derecha, active la casilla [X] **Registro de depuración de extensión (escribe monodroid.log en el escritorio)** .
4. Los mensajes de registro se escriben en el archivo monodroid.log en el escritorio.

Visual Studio para Mac escribe siempre los registros de implementación del dispositivo. Buscarlos es algo más difícil; se crea un archivo de registro *AndroidUtils* para cada día y hora a la que se produce una implementación, por ejemplo: **AndroidTools-2012-10-24_12-35-45.log**.

- En Windows, los archivos de registro se escriben en `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
- En OS X, los archivos de registro se escriben en `$HOME/Library/Logs/XamarinStudio-{VERSION}`.

## <a name="android-debug-log-output"></a>Salida del registro de depuración de Android

Android escribirá muchos mensajes en el [Registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android usa las propiedades del sistema de Android para controlar la generación de mensajes adicionales en el Registro de depuración de Android. Las propiedades del sistema de Android se pueden establecer mediante el comando *setprop* dentro de [Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Las propiedades del sistema se leen durante el inicio del proceso y, por tanto, se deben establecer antes de que se inicie la aplicación, o bien la aplicación se tiene que reiniciar después de cambiar las propiedades del sistema.

### <a name="xamarinandroid-system-properties"></a>Propiedades del sistema de Xamarin.Android

Xamarin.Android admite las propiedades del sistema siguientes:

- *debug.mono.debug*: si es una cadena no vacía, es equivalente a `*mono-debug*`.

- *debug.mono.env*: una lista de variables de entorno separadas por barras verticales (" *|* ") que se van a exportar durante el inicio de la aplicación, *antes* de que se haya inicializado mono. Esto permite establecer variables de entorno que controlan el registro de mono.

  > [!NOTE]
  > Como el valor se separa mediante " *|* ", debe tener un nivel adicional de comillas, ya que el comando \`*adb shell*\` quitará un conjunto de comillas.

  > [!NOTE]
  > Los valores de propiedades del sistema de Android no pueden tener más de 92 caracteres de longitud.

  Ejemplo:

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug.mono.log*: una lista separada por comas (" *,* ") de los componentes que deben imprimir mensajes adicionales en el Registro de depuración de Android. De forma predeterminada, no se establece nada. Entre los componentes se incluyen los siguientes:

  - *all*: se imprimen todos los mensajes.
  - *gc*: se imprimen los mensajes relacionados con GC.
  - *gref*: se imprimen mensajes de asignación y desasignación de referencias (débiles, globales).
  - *lref*: se imprimen mensajes de asignación y desasignación de referencias locales.

  > [!NOTE]
  > Estos son muy *detallados*. No los habilite a menos que realmente sea necesario.

- *debug.mono.trace*: permite establecer el valor [mono --trace](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`.

## <a name="deleting-bin-and-obj"></a>Eliminación de `bin` y `obj`

En el pasado, en Xamarin.Android se ha sufrido una situación como la siguiente:

- Se produce un error extraño en tiempo de compilación o ejecución.
- Aplica `Clean`, `Rebuild`, o bien elimina de forma manual los directorios `bin` y `obj`.
- El problema desaparece.

Hemos invertido mucho en solucionar problemas como estos debido a su impacto en la productividad de los desarrolladores.

Si se produce un problema como este:

1. Anótelo mentalmente. ¿Cuál fue la última acción que provocó este estado en el proyecto?
1. Guarde el registro de compilación actual. Intente compilar de nuevo y registre un [registro de compilación de diagnóstico](#diagnostic-msbuild-output).
1. Envíe un [informe de errores][bug].

Antes de eliminar los directorios `bin` y `obj`, comprímalos y guárdelos para su posterior diagnóstico, si es necesario. Probablemente solo pueda aplicar `Clean` al proyecto de aplicación de Xamarin.Android para que todo vuelva a funcionar.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android no puede resolver System.ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15.1 o anterior) solo es compatible con **System.ValueTuple NuGet 4.3.0** (o anterior).

- **Visual Studio 2017 Update 2** (versión 15.2 o posterior) solo es compatible con **System.ValueTuple NuGet 4.3.1** (o posterior).

Elija el valor System.ValueTuple NuGet correcto que se corresponda con la instalación de Visual Studio 2017.

## <a name="gc-messages"></a>Mensajes de GC

Los mensajes de componentes de GC se pueden ver si se establece la propiedad del sistema debug.mono.log en un valor que contenga gc.

Los mensajes de GC se generan cada vez que se ejecuta la recolección de elementos no utilizados (GC) y proporcionan información sobre la cantidad de trabajo que ha realizado la GC:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Se puede generar información adicional de GC, como la de control de tiempo, si se establece la variable de entorno `MONO_LOG_LEVEL` en `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Esto producirá (muchos) mensajes de Mono adicionales, incluidos estos tres de consecuencia:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

En el mensaje `GC_BRIDGE`, `num-objects` es el número de objetos puente que se consideran en este paso y `num_hash_entries` es el número de objetos procesados durante esta invocación del código puente.

En los mensajes `GC_MINOR` y `GC_MAJOR`, `total` es la cantidad de tiempo durante el que se pausa todo (no se ejecutan subprocesos), mientras que `bridge` es la cantidad de tiempo que tarda el código de procesamiento de puente (que trabaja con la máquina virtual Java). El mundo *no* se pausa mientras se produce el procesamiento del puente.

 *En general*, cuanto mayor sea el valor de `num_hash_entries`, más tiempo tardarán las colecciones `bridge` y mayor será el tiempo `total` empleado en la recolección.

## <a name="global-reference-messages"></a>Mensajes de referencia global

Para habilitar el registro de referencias globales (GREF), la propiedad del sistema *debug.mono.log* debe contener *gref*, por ejemplo:

```shell
adb shell setprop debug.mono.log gref
```

En Xamarin.Android se usan referencias globales de Android para proporcionar asignaciones entre instancias de Java y las instancias administradas asociadas, como al invocar un método de Java, que es necesario proporcionar una instancia de Java a Java.

Desafortunadamente, los emuladores de Android solo permiten la existencia de 2000 referencias globales a la vez. El hardware tiene un límite mucho más alto de 52 000 referencias globales. El límite más bajo puede ser un problema cuando se ejecutan aplicaciones en el emulador, por lo que saber de *dónde* proviene la instancia puede ser muy útil.

> [!NOTE]
> El recuento de referencias globales es interno de Xamarin.Android y no incluye (ni puede hacerlo) referencias globales tomadas por otras bibliotecas nativas cargadas en el proceso. Use el recuento de referencias globales como una estimación.

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

- Creación de referencias globales: son las líneas que comienzan con *+g+* y que proporcionarán un seguimiento de la pila para la ruta de acceso del código de creación.
- Destrucción de referencias globales: son las líneas que comienzan con *-g-* y que proporcionarán un seguimiento de la pila para la ruta de acceso del código que elimina la referencia global. Si la recolección de elementos no utilizados va a eliminar la referencia global, no se proporcionará ningún seguimiento de la pila.
- Creación de referencias globales débiles: son las líneas que comienzan con *+w+* .
- Destrucción de referencias globales débiles: son las líneas que comienzan con *-w-* .

En todos los mensajes, el valor *grefc* es el recuento de referencias globales que ha creado Xamarin.Android, mientras que el valor *grefwc* es el recuento de referencias globales débiles. *handle* u *obj-handle* es el valor de identificador de JNI y el carácter que aparece después de " */* " es el tipo de valor de identificador: */L* para la referencia local, */G* para las referencias globales y */W* para las referencias globales débiles.

Como parte del proceso de recolección de elementos no utilizados, las referencias globales (+g+) se convierten en referencias globales débiles (lo que genera +w+ y -g-), se inicia una recolección de elementos no utilizados en el lado de Java y, después, se comprueba la referencia global débil para ver si se ha recopilado. Si todavía está activa, se crea una referencia global en torno a la referencia débil (+g+, -w-); en caso contrario, la referencia débil se destruye (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Creación y encapsulación de una instancia de Java mediante un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Realización de una recolección de elementos no utilizados...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>El objeto sigue estando activo, como identificador != null
## <a name="wref-turned-back-into-a-gref"></a>wref se vuelve a convertir en gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>El objeto está inactivo, como identificador == null
## <a name="wref-is-freed-no-new-gref-created"></a>Se libera wref, no se crea ninguna gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Aquí hay un aspecto "interesante": en los destinos que ejecutan versiones de Android anteriores a 4.0, el valor de gref es igual a la dirección del objeto Java en la memoria del runtime de Android. (Es decir, la recolección de elementos no utilizados es un recopilador estático y conservador, que entrega referencias directas a esos objetos). Por tanto, después de una secuencia +g+, +w+, -g-, +g+, -w-, la referencia global resultante tendrá el mismo valor que la original. Esto hace que el uso de grep entre los registros sea bastante sencillo.

Pero Android 4,0 cuenta con un recolector dinámico, que ya no entrega referencias directas a objetos de máquina virtual del runtime de Android. Por tanto, después de una secuencia +g+, +w+, -g-, +g+, -w-, el valor de gref *será diferente*. Si el objeto sobrevive a varias operaciones de recolección de elementos no utilizados, tendrá varios valores de referencia global, lo que dificultará saber desde dónde se ha asignado realmente una instancia.

### <a name="querying-programmatically"></a>Consultas mediante programación

Puede consultar los recuentos de GREF y WREF si consulta el objeto `JniRuntime`.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`: recuento de referencias globales

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`: recuento de referencias débiles

## <a name="android-debug-logs"></a>Registros de depuración de Android

Los [registros de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md) pueden proporcionar contexto adicional relacionado con los errores en tiempo de ejecución que se ven.

## <a name="floating-point-performance-is-terrible"></a>El rendimiento del punto flotante es terrible

Como alternativa, "Mi aplicación se ejecuta 10 veces más rápido con la compilación de depuración que con la compilación de versión"

Xamarin.Android admite varias ABI de dispositivo: *armeabi*, *armeabi-v7a* y *x86*. Las ABI de dispositivo se pueden especificar en **Propiedades del proyecto > pestaña Aplicación > Arquitecturas admitidas**.

Las compilaciones de depuración usan un paquete de Android que proporciona todas las ABI y, por tanto, usará la más rápida para el dispositivo de destino.

Las compilaciones de versión solo incluirán las ABI seleccionadas en la pestaña Propiedades del proyecto. Se puede seleccionar más de una.

*armeabi* es la ABI predeterminada y tiene la compatibilidad con dispositivos más amplia. *Pero* armeabi no es compatible con los dispositivos con varias CPU y el punto flotante de hardware, entre otras cosas. Por tanto, las aplicaciones que usan el runtime Release de armeabi se asociarán a un solo núcleo y usarán una implementación flotante. Las dos pueden contribuir a un rendimiento considerablemente más lento de la aplicación.

Si la aplicación requiere un rendimiento de punto flotante aceptable (por ejemplo, los juegos), debe habilitar la ABI *armeabi-v7a*. Es posible que solo quiera admitir el runtime de *armeabi-v7a*, aunque esto significa que los dispositivos más antiguos que solo admitan *armeabi* no podrán ejecutar la aplicación.

## <a name="could-not-locate-android-sdk"></a>No se pudo encontrar Android SDK

En Google hay dos descargas disponibles para Android SDK para Windows.
Si elige el instalador .exe, escribirá las claves del Registro que indican a Xamarin.Android dónde se ha instalado. Si elige el archivo .zip y lo descomprime, Xamarin.Android no sabe dónde buscar el SDK. Puede indicar a Xamarin.Android dónde está el SDK en Visual Studio; para ello, vaya a **Herramientas > Opciones > Xamarin > Configuración de Android**:

[![Ubicación de Android SDK en la configuración de Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>El IDE no muestra el dispositivo de destino

A veces, intentará implementar la aplicación en un dispositivo, pero el dispositivo en el que quiere hacerlo no se muestra en el cuadro de diálogo Seleccionar dispositivo. Esto puede ocurrir cuando Android Debug Bridge decide irse de vacaciones.

Para diagnosticar este problema, busque el [programa adb](~/android/deploy-test/debugging/android-debug-log.md) y, después, ejecute:

```shell
adb devices
```

Si el dispositivo no aparece, tendrá que reiniciar el servidor de Android Debug Bridge para poder encontrarlo:

```shell
adb kill-server
adb start-server
```

El software HTC Sync puede impedir que **adb start-server** funcione correctamente. Si el comando **adb start-server** no imprime en qué puerto se inicia, salga del software HTC Sync e intente reiniciar el servidor adb.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>No se pudo ejecutar la tarea ejecutable especificada "keytool"

Esto significa que la variable PATH no contiene el directorio donde se encuentra el directorio bin del SDK de Java. Compruebe que ha seguido los pasos de la guía de [instalación](~/android/get-started/installation/index.md).

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe o aresgen.exe se ha cerrado con el código 1

Para ayudarle a depurar este problema, vaya a Visual Studio y cambie el nivel de detalle de MSBuild; para ello, seleccione: **Herramientas > Opciones > Proyecto** y **Soluciones > Compilar**, después **Ejecutar > Detalles de la salida de la compilación del proyecto de MSBuild**, y establezca este valor en **Normal**.

Vuelva a compilar y compruebe el panel Salida de Visual Studio, que debe contener el error completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>No hay suficiente espacio de almacenamiento en el dispositivo para implementar el paquete

Esto sucede cuando no se inicia el emulador desde dentro de Visual Studio. Al iniciar el emulador fuera de Visual Studio, debe pasar las opciones `-partition-size 512`, por ejemplo,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Asegúrese de usar el nombre correcto del simulador, es decir, [el nombre que haya usado al configurarlo](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>INSTALL\_FAILED\_INVALID\_APK al instalar un paquete

Los nombres de paquetes Android *deben* contener un punto (" *.* "). Modifique el nombre del paquete para que contenga un punto.

- Desde Visual Studio:
  - Haga clic con el botón derecho en el proyecto > Propiedades.
  - Haga clic en la pestaña Manifiesto de Android de la izquierda.
  - Actualice el campo Nombre del paquete.
    - Si ve el mensaje &ldquo;No se ha encontrado ningún archivo AndroidManifest.xml. Haga clic para agregar uno.&rdquo;, haga clic en el vínculo y, después, actualice el campo Nombre del paquete.
- Desde Visual Studio para Mac:
  - Haga clic con el botón derecho en el proyecto > Opciones.
  - Vaya a la sección Compilar / Aplicación de Android.
  - Cambie el campo Nombre del paquete para que contenga un ".".

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>INSTALL\_FAILED\_MISSING\_SHARED\_LIBRARY al instalar un paquete

En este contexto, una "biblioteca compartida" *no* es un archivo de biblioteca compartida nativa (*libfoo.so*); en su lugar, es una biblioteca que se debe instalar por separado en el dispositivo de destino, como Google Maps.

El paquete de Android especifica qué bibliotecas compartidas son necesarias con el elemento `<uses-library/>`. Si una biblioteca *necesaria* no está presente en el dispositivo de destino (por ejemplo, `//uses-library/@android:required` es *true*, que es el valor predeterminado), se producirá un error en la instalación del paquete con *INSTALL\_FAILED\_MISSING\_SHARED\_LIBRARY*.

Para determinar qué bibliotecas compartidas son necesarias, vea el archivo **AndroidManifest.xml** *generado*
 (por ejemplo, **obj\\Debug\\android\\AndroidManifest.xml**) y busque los elementos `<uses-library/>`. Los elementos `<uses-library/>` se pueden agregar de forma manual en el archivo **Propiedades\\AndroidManifest.xml** del proyecto y a través del [atributo personalizado UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Por ejemplo, si se agrega una referencia de ensamblado a *Mono.Android.GoogleMaps.dll* se agregará de forma implícita `<uses-library/>` para la biblioteca compartida de Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>INSTALL\_FAILED\_UPDATE\_INCOMPATIBLE al instalar un paquete

Los paquetes de Android tienen tres requisitos:

- Deben contener un "." (vea la entrada anterior)
- Deben tener un nombre de paquete de cadena único (de ahí la convención de TLD invertido que se ve en los nombres de aplicaciones Android, por ejemplo, com.android.chrome para la aplicación Chrome).
- Al actualizar los paquetes, el paquete debe tener la misma clave de firma.

Por tanto, imagínese este escenario:

1. Compila e implementa la aplicación como una aplicación de depuración
2. Cambia la clave de firma, por ejemplo, para usarla como una aplicación de versión (o porque no le gusta la clave de firma de depuración que se proporciona de forma predeterminada).
3. Instala la aplicación sin quitarla primero, por ejemplo, Depurar > Iniciar sin depurar en Visual Studio

Cuando esto sucede, se producirá un error en la instalación del paquete con un error INSTALL\_FAILED\_UPDATE\_INCOMPATIBLE, porque no se ha cambiado el nombre del paquete y la clave de firma sí. El [Registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md) también contendrá un mensaje similar al siguiente:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corregir este error, quite completamente la aplicación del dispositivo antes de volverla a instalar.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>INSTALL\_FAILED\_UID\_CHANGED al instalar un paquete

Cuando se instala un paquete de Android, se le asigna un *identificador de usuario* (UID).
*A veces*, por motivos que en la actualidad se desconocen, al realizar la instalación sobre una aplicación ya instalada, se produce un error en la instalación con `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para solucionar este problema, *desinstale completamente* el paquete de Android, ya sea mediante la instalación de la aplicación desde la GUI del destino de Android o con `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NO USE** `adb uninstall -k`, ya que esto *conservará* datos de la aplicación y, por tanto, el UID en conflicto en el dispositivo de destino.

## <a name="release-apps-fail-to-launch-on-device"></a>Las aplicaciones de versión no se pueden iniciar en el dispositivo

¿El Registro de depuración de Android contiene un mensaje similar al siguiente?

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

En ese caso, hay dos causas posibles:

1. El archivo .apk no proporciona una ABI admitida por el dispositivo de destino.
    Por ejemplo, el archivo .apk solo contiene archivos binarios armeabi-v7a y el dispositivo de destino solo admite armeabi.

2. Un [error de Android](https://code.google.com/p/android/issues/detail?id=21670). En este caso, desinstale la aplicación, cruce los dedos y vuelva a instalarla.

Para solucionarlo (1), edite las opciones o propiedades del proyecto y [agregue compatibilidad para la ABI necesaria a la lista de ABI admitidas](~/android/app-fundamentals/cpu-architectures.md). Para determinar qué ABI tiene que agregar, ejecute el siguiente comando adb en el dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

La salida contendrá la ABI principal (y las secundarias opcionales).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La propiedad OutPath no está establecida para &ldquo;MyApp.csproj&rdquo; del proyecto

Por lo general, esto significa que tiene un equipo HP y que la variable de entorno &ldquo;Plataforma&rdquo; se ha establecido en algo como MCD o HPD. Esto entra en conflicto con la propiedad Plataforma MSBuild que normalmente se establece en &ldquo;Cualquier CPU&rdquo; o &ldquo;x86&rdquo;. Tendrá que quitar esta variable de entorno del equipo para que MSBuild pueda funcionar:

- Panel de control > Sistema > Opciones avanzadas > Variables de entorno

Reinicie Visual Studio o Visual Studio para Mac e intente volver a compilar. Ahora todo debería funcionar según lo previsto.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject no se puede convertir a...

Xamarin.Android 4.x no serializa correctamente las referencias de tipos genéricos anidados. Por ejemplo, considere el siguiente código de C\# en el que se usa [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

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

El problema es que Xamarin.Android serializa incorrectamente los tipos genéricos anidados. `List<IDictionary<string, object>>` se serializa en un objeto [java.lang.ArrrayList](xref:Java.Util.ArrayList), pero `ArrayList` contiene instancias de `mono.android.runtime.JavaObject` (que hacen referencia a las instancias de `Dictionary<string, object>`) en lugar de algo que implemente [java.util.Map](xref:Java.Util.IMap), lo que genera la siguiente excepción:

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

La solución consiste en usar los [tipos de colección de Java](~/android/internals/api-design.md) proporcionados en lugar de los tipos `System.Collections.Generic` para los tipos &ldquo;internos&rdquo;. Esto generará los tipos de Java adecuados al serializar las instancias. (El código siguiente es más complicado de lo necesario para reducir la duración de gref. Se puede simplificar para modificar el código original a través de `s/List/JavaList/g` y `s/Dictionary/JavaDictionary/g` si las duraciones de gref no son una preocupación).

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

[Este problema se corregirá en futuras versiones](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).

## <a name="unexpected-nullreferenceexceptions"></a>Excepciones NullReferenceException inesperadas

En ocasiones, el [Registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md) mencionará excepciones NullReferenceException que &ldquo;no se pueden producir&rdquo;, o bien que proceden de Mono para el código del runtime de Android poco antes de que la aplicación desaparezca:

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

Esto puede suceder cuando el runtime de Android decide anular el proceso, lo que puede ocurrir por diversos motivos, como alcanzar el límite de GREF del destino o hacer algo &ldquo;erróneo&rdquo; con JNI.

Para ver si este es el caso, compruebe en el Registro de depuración de Android si hay un mensaje del proceso similar al siguiente:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Anular debido al agotamiento de referencia global

La capa JNI del runtime de Android solo admite un número limitado de referencias de objeto JNI válidas en un momento dado. Cuando se supera este límite, todo se interrumpe.

El límite de GREF (*referencia global*) es de 2000 referencias en el emulador y de aproximadamente 52 000 en el hardware.

Sabe que va a empezar a crear demasiadas referencias globales cuando vea mensajes como este en el Registro de depuración de Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Cuando se alcanza el límite de referencias globales, se imprime un mensaje como el siguiente:

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

En el ejemplo anterior (que, casualmente, proviene del [error 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) el problema es que se crean demasiadas instancias de Android.Graphics.Point; vea el [comentario \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obtener una lista de las correcciones de este error concreto.

Normalmente, una solución útil consiste en encontrar el tipo que tiene demasiadas instancias asignadas (Android.Graphics.Point en el volcado de memoria anterior) y, después, buscar dónde se han creado en el código fuente y eliminarlos de la forma adecuada (para reducir su duración como objetos de Java). Esto no siempre es adecuado (\#685215 es multiproceso, por lo que la solución trivial evita la llamada a Dispose), pero es lo primero que hay que tener en cuenta.

Puede habilitar el [registro de GREF](~/android/troubleshooting/index.md) para ver cuándo se crean las referencias globales y cuántas existen.

## <a name="abort-due-to-jni-type-mismatch"></a>Anulación debido a un error de coincidencia de tipos de JNI

Si desarrolla código de JNI de forma manual, es posible que los tipos no coincidan correctamente, por ejemplo, si intenta invocar `java.lang.Runnable.run` en un tipo que no implementa `java.lang.Runnable`. Cuando esto suceda, habrá un mensaje similar al siguiente en el Registro de depuración de Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Compatibilidad con código dinámico

### <a name="dynamic-code-does-not-compile"></a>El código dinámico no se compila

Para usar código de C\# dinámico en la aplicación o biblioteca, tendrá que agregar System.Core.dll, Microsoft.CSharp.dll y Mono.CSharp.dll al proyecto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>En la compilación de versión, se inicia una excepción MissingMethodException para el código dinámico en tiempo de ejecución.

- Es probable que el proyecto de aplicación no tenga referencias a System.Core.dll, Microsoft.CSharp.dll o Mono.CSharp.dll. Asegúrese de que se haga referencia a esos ensamblados.

  - Tenga en cuenta que el código dinámico siempre supone un coste. Si necesita código eficaz, considere la posibilidad de no usar código dinámico.

- En la primera versión preliminar, se excluyen esos ensamblados a menos que el código de aplicación use de forma explícita los tipos de cada ensamblado. Vea lo siguiente para obtener una solución alternativa: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Proyectos compilados con el bloqueo de AOT y LLVM en dispositivos x86

Al implementar una aplicación compilada con [AOT y LLVM](~/android/deploy-test/release-prep/index.md) en dispositivos basados en x86, es posible que vea un mensaje de error de excepción similar al siguiente:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Se trata de un problema conocido: la solución consiste en deshabilitar LLVM.
