---
title: Solución de problemas de Xamarin Profiler
description: En este documento se proporciona información sobre la solución de problemas relacionados con el Xamarin Profiler. Describe los problemas relacionados con el registro y los diagnósticos, el IDE y otros temas.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: davidortinau
ms.author: daortin
ms.date: 10/27/2017
ms.openlocfilehash: 915f7df80e3ae29ab3c598ea95fabbc054e916dd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019209"
---
# <a name="xamarin-profiler-troubleshooting"></a>Solución de problemas de Xamarin Profiler

## <a name="logging-and-diagnostics"></a>Registro y diagnósticos

El equipo de Xamarin puede ayudar a realizar un seguimiento de los problemas si nos proporciona información, lo que incluye:

- Una presentación en pantalla del problema, el bloqueo o el error y el flujo de trabajo que conduce a él.
- Resultados del registro (vea más abajo).
- El **. MLPD** que se genera para la sesión de generación de perfiles (vea más abajo).

### <a name="getting-log-outputs"></a>Obtención de salidas de registro

En los registros de Mac se guardan en `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

En Windows, estos se guardan en `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` incluya el registro más reciente cada vez que envíe un problema.

Estamos agregando más registros a medida que avanzamos, por lo que esta salida debe crecer y ser más útil con el tiempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generar archivos. MLPD

Un archivo **. MLPD** es la salida comprimida del generador de perfiles en tiempo de ejecución de mono. La GUI Xamarin Profiler Lee los datos de un **. MLPD** y los muestra para el usuario. los archivos **. MLPD** son herramientas de depuración útiles para Xamarin porque ayudan a nuestros ingenieros a diagnosticar los problemas que puede tener el generador de perfiles con los datos.

El **. MLPD** de la sesión actual se guarda automáticamente en el directorio de `/tmp` de su Mac y se puede identificar mediante la marca de tiempo. Si activa el registro, el primer resultado será la ruta de acceso al archivo **. MLPD** . El archivo **. MLPD** se guardará normalmente en el directorio a partir de ~/var/Folders...

También se puede guardar el archivo **. MLPD** para una sesión actual eligiendo **archivo > Guardar como..** . en el menú del generador de perfiles:

**Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Saving .mlpd file in Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Saving .mlpd file in Visual Studio")

Es importante tener en cuenta que **. MLPD** contiene mucha información y el tamaño del archivo será grande.

## <a name="troubleshooting"></a>Solución de problemas

La lista siguiente muestra los problemas comunes, las soluciones alternativas y las sugerencias y trucos para usar el generador de perfiles.

> [!NOTE]
> Debe ser un suscriptor de Visual Studio **Enterprise** para desbloquear esta característica en Visual Studio Enterprise en Windows o Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>No veo la opción de iOS Profiler o está atenuada [Visual Studio y Visual Studio para Mac]

Compruebe la siguiente configuración para resolver este procedimiento:

- Asegúrese de que está usando la configuración de depuración
- Asegúrese de que está usando el recolector de elementos no utilizados de SGen.
- Asegúrese de que se [admite](~/tools/profiler/index.md#Profiler_Support)la plataforma.
- Asegúrese de que tiene la licencia correcta.
- Asegúrese de que ha iniciado sesión y que se ha autenticado correctamente.
- [Visual Studio] Debe usar [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) y tener una licencia Enterprise válida.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Obtengo un error al intentar iniciar el generador de perfiles

Si se ejecuta este cuadro de error al usar el generador de perfiles en Visual Studio:

![](troubleshooting-images/error.png "Error box when using the profiler in Visual Studio")

Normalmente, se debe a que no puede iniciarse en el simulador o en el emulador. Intente ejecutar la aplicación normalmente, corrija los problemas que proporciona y, a continuación, intente volver a usar el generador de perfiles.

#### <a name="to-watch-a-specific-thread"></a>Para ver un subproceso concreto

Si tiene un subproceso que desea inspeccionar específicamente, sería idóneo asignar un nombre al subproceso al principio de su creación para obtener `ThreadName` en lugar de `0x0`. Por ejemplo, para establecer el nombre del subproceso como `UI`, puede usar el código siguiente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Tutorial: uso del Xamarin Profiler](~/tools/profiler/index.md)
- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
