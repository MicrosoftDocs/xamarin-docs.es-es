---
title: Depuración de un bloqueo nativo en una aplicación Xamarin.Mac
description: En este documento se describe cómo depurar las excepciones que se originan en el entorno de ejecución Objective-C. Se describen también los errores de aserciones, los problemas con las devoluciones de llamada, la propagación de excepciones y mucho más.
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 40d849ad403f2f47c00be9d3da7b59fc27ce8002
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725498"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>Depuración de un bloqueo nativo en una aplicación Xamarin.Mac

## <a name="overview"></a>Información general

A veces, los errores de programación pueden provocar bloqueos en el entorno de ejecución Objective-C. A diferencia de las excepciones de C#, estos no señalan una línea específica del código para poder buscarlo y corregirlo. A veces, pueden resultar muy fáciles de encontrar y corregir, pero otras, pueden ser muy difíciles de rastrear.

Vamos a repasar algunos ejemplos de bloqueos nativos reales.

## <a name="example-1-assertion-failure"></a>Ejemplo 1: Error de aserción

Estas son las primeras líneas de un bloqueo en una aplicación de prueba sencilla (esta información se presentará en el panel **Salida de la aplicación**):

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

Las líneas que tienen números como prefijo corresponden al seguimiento de la pila nativo. En ellas puede ver que el error se produjo en alguna parte de `NSTableView`, que controla los altos de las filas. Después, `NSAssertionHandler` activa un objeto `NSException (objc_exception_throw)` y vemos el error de aserción:

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

Una vez que se ve, queda muy claro que el método `NSOutlineViewDelegate` está devolviendo un número negativo. Este fue el problema:

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>Ejemplo 2: Devolución de llamada en alguna parte

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

Se trata de un problema que era mucho más difícil de rastrear. Cuando vea `at <unknown> <0xffffffff>` o `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` en la parte superior del seguimiento de la pila administrado, sugiere que estamos tratando de ejecutar algún código administrado con un objeto que se recopiló durante la recolección de elementos no utilizados. El seguimiento de la pila nativo muestra `trackMouse:inRect:ofView:untilMouseUp` en `NSCell _sendActionFrom`, por lo que, en alguna parte, estamos controlando un evento de clic, tratando de realizar una devolución de llamada en C# sin éxito.

En general, los errores como este son difíciles de rastrear. Se ha agregado `GC.Collect(2)` a un controlador de botón para ayudar a rastrear este problema (forzar una recolección de elementos no utilizados) para lograr que sea reproducible. Después de bisecar el ejemplo durante un tiempo, quitando secciones de código hasta que el problema desapareció, resultó ser [este error](https://bugzilla.xamarin.com/show_bug.cgi?id=23378):

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

El objeto `NSButton` que devolvió `StandardWindowButton()` se recopiló, aunque se registró un evento en él (es decir, el error). Al tratar de llamar a ese evento haciendo clic, si el botón se ha recopilado en una recolección de elementos no utilizados, se bloqueará.

Aunque no era la causa principal de este problema en concreto, los seguimientos de la pila como este también pueden deberse a firmas de método incorrectas en funciones exportadas (`[Export]`) a Objective-C. Por ejemplo, si un método espera que un parámetro sea un objeto `out string` y lo escribe como `string`, se puede bloquear de la misma forma.

## <a name="example-3-callbacks-and-managed-objects"></a>Ejemplo 3: Devoluciones de llamada y objetos administrados

La biblioteca realiza una devolución de llamada a muchas API de Cocoa cuando se produce un evento, lo que le da una oportunidad de responder, o cuando algunos datos necesitan llevar a cabo una tarea. Aunque es posible que piense sobre todo en los patrones **Delegate** y **DataSource**, hay una gran cantidad de API que funcionan de esta manera. Por ejemplo, al invalidar los métodos de un objeto `NSView` y, luego, insertarlo en el árbol visual, se espera que AppKit devuelva una llamada cuando se produzca un evento.

En casi todos los casos, Xamarin.Mac evitará que el destino de los objetos administrados de estas devoluciones de llamada se recopile durante la recolección de elementos no utilizado, aunque se siga pudiendo llamar a dichos objetos. Sin embargo, casi nunca los errores en enlaces pueden alterar este comportamiento. Cuando esto ocurre, puede ver bloqueos no deseados similares al siguiente:

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

Esta guía lo ayudará a rastrear los errores de este tipo, si se producen, e informar de ellos correctamente para que se puedan corregir y solucionar en su código.

### <a name="locating"></a>Búsqueda

En casi todos los casos con errores de este tipo, el síntoma principal son los bloqueos nativos, normalmente con algo similar a `mono_sigsegv_signal_handler`, o bien `_sigtrap` en los marcos superiores de la pila. Cocoa tratará de volver la llamada en su código de C#, con lo que se ejecutará un objeto recopilado durante la recolección de elementos no utilizados y se producirá el bloqueo. Sin embargo, no todos los bloqueos con estos símbolos se deben a un problema de enlace así; tendrá que realizar más investigaciones para confirmar que este es el problema.

Lo que hace que estos errores sean difíciles de rastrear es que solo se producen **después** de que una recolección de elementos no utilizados haya eliminado el objeto en cuestión. Si cree que tiene uno de estos errores, agregue el código siguiente en alguna parte de la secuencia de inicio:

```csharp
new System.Threading.Thread (() =>
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start ();
```

Se forzará la aplicación para que ejecute cada segundo el recolector de elementos no utilizados. Vuelva a ejecutar la aplicación y trate de reproducir el error. Si el bloqueo se produce inmediatamente, o de forma coherente en lugar de aleatoria, va por buen camino.

### <a name="reporting"></a>Informes

El siguiente paso es notificar el problema a Xamarin, para que pueda corregir el enlace en las versiones futuras. Si es el propietario de una licencia empresarial, cree un vale de soporte técnico en

[visualstudio.microsoft.com/vs/support/](https://visualstudio.microsoft.com/vs/support/)

En caso contrario, investigue en las siguientes fuentes si ya existe el problema:

- Compruebe los [foros de Xamarin.Mac](https://forums.xamarin.com/categories/xamarin-mac).
- Busque en el [repositorio de problemas](https://github.com/xamarin/xamarin-macios/issues).
- Antes de la migración a GitHub, los problemas de Xamarin se recopilaban en [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Busque allí para ver si están los mismos problemas.
- Si no encuentra un problema, registre uno nuevo en el [repositorio de problemas de GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Los problemas de GitHub son públicos. No es posible ocultar comentarios ni datos adjuntos.

De la siguiente información, incluya toda la que pueda:

- Un ejemplo sencillo que reproduzca el problema. Siempre que sea posible, esta información es **muy útil**.
- El seguimiento de la pila completo del bloqueo.
- El código de C# del bloqueo.   

### <a name="working-around"></a>Solución alternativa

Una vez rastreado el problema, puede resultar fácil aplicarle una revisión con una solución alternativa hasta que se corrija el enlace. La finalidad es evitar que el objeto (**View**, **Delegate**, **DataSource**) desechado incorrectamente salga de la memoria manteniendo una referencia abierta.

En los casos sencillos donde hay una sola instancia del objeto, cambie el código:

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

El objetivo es usar una variable estática como esta:

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

En los casos donde pueden crearse varias instancias, puede usarse un objeto `HashSet` estático:

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

Una vez corregido el enlace y actualizado a la versión de Xamarin.Mac que incluye la corrección, se puede quitar el código con la solución alternativa.

## <a name="exception-bubbling-and-objective-c"></a>Propagación de excepciones y Objective-C

Nunca debe permitir que una excepción de C# "escape" código administrado al método Objective-C que realiza la llamada. Si lo hace, los resultados no están definidos, pero normalmente suelen producirse bloqueos. En general, hacemos todo lo posible para propagar información útil de bloqueos nativos y administrados para ayudarlo a resolver los problemas rápidamente.

Sin hacer demasiado énfasis en los motivos técnicos, configurar la infraestructura para detectar excepciones administradas en todos los límites administrados o nativos es costoso y hay _muchas_ transiciones que se producen en numerosas operaciones comunes. Muchas operaciones, en concreto las que implican el subproceso de interfaz de usuario, deben finalizar rápidamente; si no, la aplicación sufrirá interrupciones y tendrá características de rendimiento inaceptables. Muchas de esas devoluciones de llamada realizan operaciones muy sencillas que, rara vez, tienen posibilidad de ejecutarse, por lo que, en esos casos, esta sobrecarga sería costosa e innecesaria.

Por lo tanto, no configuraremos estos bloques Try Catch en la guía. En los casos en que el código no realice operaciones sencillas (es decir, devolver valores booleanos o matemática básica), puede tratar de configurar un bloque Try Catch usted mismo.
