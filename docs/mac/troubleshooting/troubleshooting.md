---
title: Sugerencias para la solución de problemas de Xamarin. Mac
description: En este documento se describen los métodos para resolver problemas que se producen al desarrollar aplicaciones de Xamarin. Mac. También se describen las formas de obtener soporte técnico.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8714297c4948dbb65c521d6a32bac3e437b40733
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725445"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Sugerencias para la solución de problemas de Xamarin. Mac

## <a name="overview"></a>Información general

A veces, todos se bloquean mientras se trabaja en un proyecto, ya sea en la imposibilidad de obtener una API para trabajar de la forma deseada o para intentar solucionar un error. Nuestro objetivo en Xamarin es que tenga éxito al escribir las aplicaciones móviles y de escritorio, y que hemos proporcionado algunos recursos para ayudarle.

Con cualquiera de estos recursos, hay algunos pasos que puede seguir para ayudarles a resolver el problema rápidamente:

- Determine la causa raíz del problema lo mejor posible para informar sobre bloqueos:

  - "Mi aplicación se bloquea" es difícil de diagnosticar. "Mi aplicación se bloquea cuando devuelva una matriz vacía a esta llamada" es mucho más fácil de trabajar en la corrección.

  - "No puedo obtener NSTable to Work" es menos útil que "ninguno de los métodos de mi NSTableDelegate parece ser llamado en este caso".

- Si es posible, proporcione un pequeño programa de ejemplo que muestre el problema. Si se profundiza en las páginas de código fuente que buscan el problema, se realizarán pedidos de magnitud más tiempo y esfuerzo.

- Saber qué cambios ha realizado en la aplicación para que aparezca un problema puede reducir rápidamente el origen del problema. Teniendo en cuenta si ha actualizado recientemente versiones de Xamarin. Mac, recorte las secciones de la aplicación para encontrar la parte que causa el problema o probar compilaciones anteriores para averiguar qué cambio ha introducido el problema puede ser muy útil.

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Qué hacer cuando la aplicación se bloquea sin salida

En la mayoría de los casos, el depurador de Visual Studio para Mac detectará las excepciones y los bloqueos de la aplicación y le ayudará a realizar un seguimiento de la causa principal. Sin embargo, hay algunos casos en los que la aplicación se rebotará en el Dock y, a continuación, se cerrará con poca o ninguna salida. Estos pueden incluir:

- Problemas de firma de código.
- Ciertos bloqueos de tiempo de ejecución de mono.
- Algunas excepciones y bloqueos de Objective-c.
- Algunos bloqueos son muy tempranos en la duración del proceso.
- Algunos desbordamientos de la pila.
- La versión de macOS indicada en **info. plist** es más reciente que la versión de MacOS instalada actualmente o no es válida.

La depuración de estos programas puede ser frustrante, ya que la búsqueda de la información necesaria puede ser difícil. Estos son algunos de los métodos que pueden ayudar:

- Asegúrese de que la versión de macOS indicada en **info. plist** sea la misma que la versión de MacOS instalada actualmente en el equipo.
- Compruebe la Visual Studio para Mac salida de la aplicación (**Ver** los **paneles** de ->  -> salida de la **aplicación**) para los seguimientos de la pila o la salida en rojo del coco que puede describir la salida.
- Ejecute la aplicación desde la línea de comandos y examine el resultado (en la aplicación **terminal** ) mediante:

  `MyApp.app/Contents/MacOS/MyApp` (donde `MyApp` es el nombre de la aplicación)
- Puede aumentar la salida agregando "MONO_LOG_LEVEL" al comando en la línea de comandos, por ejemplo:

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Puede adjuntar un depurador nativo (`lldb`) al proceso para ver si proporciona más información (esto requiere una licencia de pago). Por ejemplo, haga lo siguiente.

  1. Escriba `lldb MyApp.app/Contents/MacOS/MyApp` en el terminal.
  2. Escriba `run` en el terminal.
  3. Escriba `c` en el terminal.
  4. Salir cuando finalice la depuración.
- Como último recurso, antes de llamar a `NSApplication.Init` en el método de `Main` (o en otros lugares según sea necesario), puede escribir texto en un archivo en una ubicación conocida para realizar un seguimiento del paso en el que se está ejecutando.

## <a name="known-issues"></a>Problemas conocidos

En las secciones siguientes se tratan los problemas conocidos y sus soluciones.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>No se puede conectar al depurador en aplicaciones en espacio aislado

El depurador se conecta a las aplicaciones de Xamarin. Mac a través de TCP, lo que significa que, de forma predeterminada, cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, se obtiene un error que indica que *no se puede conectar al depurador*.

[![Editar los derechos](troubleshooting-images/debug01.png "Editar los derechos")](troubleshooting-images/debug01-large.png#lightbox)

El permiso **permitir conexiones de red salientes (cliente)** es el requerido para el depurador, lo que permite la depuración de forma normal. Dado que no se puede depurar sin él, se ha actualizado el `CompileEntitlements` destino de `msbuild` para agregar automáticamente ese permiso a los derechos de cualquier aplicación que esté en un espacio aislado para las compilaciones de depuración únicamente. Las compilaciones de versión deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: no hay datos disponibles para la codificación 437

Al incluir bibliotecas de terceros en la aplicación de Xamarin. Mac, podría obtener un error con el formato "System. NotSupportedException: no hay datos disponibles para la codificación 437" al intentar compilar y ejecutar la aplicación. Por ejemplo, las bibliotecas, como `Ionic.Zip.ZipFile`, pueden producir esta excepción durante la operación.

Para solucionarlo, abra las opciones del proyecto de Xamarin. Mac, vaya a la **compilación de mac** > **internacionalización** y Compruebe la internacionalización **occidental** :

[![Edición de las opciones de compilación](troubleshooting-images/issue01.png "Edición de las opciones de compilación")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>No se pudo compilar (mm5103)

Este error suele producirse cuando se lanza una nueva versión de Xcode y se ha instalado la nueva versión, pero aún no se ha ejecutado. Antes de intentar compilar con una nueva versión de Xcode, primero debe ejecutar esa versión al menos una vez.

La primera vez que se ejecuta una nueva versión de Xcode, se instalan varias herramientas de línea de comandos necesarias para Xamarin. Mac. Además, debe realizar una compilación limpia después de actualizar Xcode o la versión de Xamarin. Mac.

Si no puede resolver este problema, envíe [un error](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Faltan los derechos. plist

La versión más reciente de Visual Studio para Mac ha quitado la sección derechos del editor **info. plist** y la ha colocado en el editor de **derechos independientes. plist** (para una mejor compatibilidad entre plataformas con Xamarin. iOS).

Con el nuevo Visual Studio para Mac instalado, cuando cree un nuevo proyecto de aplicación de Xamarin. Mac, se agregará automáticamente un archivo **contitles. plist** al árbol del proyecto:

![Seleccionar derechos](troubleshooting-images/entitlements01.png "Seleccionar derechos")

Si hace doble clic en el archivo **contitles. plist** , se mostrará el editor de derechos:

[![Editar los derechos](troubleshooting-images/entitlements02.png "Editar los derechos")](troubleshooting-images/entitlements02-large.png#lightbox)

En el caso de los proyectos existentes de Xamarin. Mac, tendrá que crear manualmente el archivo **contitles. plist** haciendo clic con el botón derecho en el proyecto en el **Panel de solución** y seleccionando **Agregar** > **nuevo archivo..** .. A continuación, seleccione **Xamarin. Mac** > **lista de propiedades vacía**:

![Agregar una nueva lista de propiedades](troubleshooting-images/entitlements03.png "Agregar una nueva lista de propiedades")

Escriba `Entitlements` como nombre y haga clic en el botón **nuevo** . Si el proyecto incluye previamente un archivo de derechos, se le pedirá que lo agregue al proyecto en lugar de crear un nuevo archivo:

[![Comprobar la sobrescritura de un archivo](troubleshooting-images/entitlements04.png "Comprobar la sobrescritura de un archivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Soporte técnico de la comunidad en los foros

La comunidad de desarrolladores que usan productos de Xamarin es sorprendente y muchos visitan nuestros [foros de Xamarin. Mac](https://forums.xamarin.com/categories/xamarin-mac) para compartir experiencias y sus conocimientos. Además, los ingenieros de Xamarin visitan periódicamente el foro para ayudarle.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Presentar un error

Sus comentarios son importantes. Si encuentra algún problema con Xamarin. Mac:

- Busque en el [repositorio de problemas](https://github.com/xamarin/xamarin-macios/issues).
- Antes de la migración a GitHub, los problemas de Xamarin se recopilaban en [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Busque allí para ver si están los mismos problemas.
- Si no encuentra un problema, registre uno nuevo en el [repositorio de problemas de GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Los problemas de GitHub son públicos. No es posible ocultar comentarios ni datos adjuntos.

De la siguiente información, incluya toda la que pueda:

- Un ejemplo sencillo que reproduzca el problema. Siempre que sea posible, esta información es **muy útil**.
- El seguimiento de la pila completo del bloqueo.
- El código de C# del bloqueo.
