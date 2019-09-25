---
title: Generador de perfiles de Xamarin
description: En esta guía se exploran las características clave de la Xamarin Profiler. Examina los generadores, la generación de perfiles y el momento en que se deben usar, y en un flujo de trabajo estándar para la generación de perfiles de aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: conceptdev
ms.author: crdun
ms.date: 06/03/2018
ms.openlocfilehash: b567985fcfd9e133587c5c285e17d21465fda659
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250079"
---
# <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

_En esta guía se exploran las características clave de la Xamarin Profiler. Examina los generadores, la generación de perfiles y el momento en que se deben usar, y en un flujo de trabajo estándar para la generación de perfiles de aplicaciones de Xamarin._

El éxito de una aplicación depende de la experiencia del usuario final. Como desarrollador podría haber implementado algunas características realmente increíbles en la aplicación, pero si la aplicación es lenta o está llena de bloqueos, es probable que el usuario se destaque.

Históricamente, mono incluye un eficaz generador de perfiles de línea de comandos para recopilar información sobre los programas que se ejecutan en el entorno de ejecución de mono llamado [generador de perfiles de mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/). El Xamarin Profiler una interfaz gráfica para el generador de perfiles de registros mono y admite la generación de perfiles de aplicaciones de Android, iOS, tvOS y Mac en Mac, y aplicaciones Android, iOS y tvOS en Windows.

El Xamarin Profiler tiene varios instrumentos disponibles para la generación de perfiles: asignaciones, ciclos y el generador de perfiles de tiempo. En esta guía se explora lo que miden estos instrumentos y cómo analizan la aplicación, y se clarifica el significado de los datos presentados en cada pantalla.

En esta guía se examinan los escenarios comunes de generación de perfiles y se presenta el generador de perfiles como una herramienta para ayudar a analizar y optimizar las aplicaciones de iOS y Android.

## <a name="download-and-install"></a>Descargar e instalar

> [!NOTE]
> Debe ser un suscriptor de [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) para desbloquear esta característica en Visual Studio Enterprise en Windows o Visual Studio para Mac en un equipo Mac.

El Xamarin Profiler es una aplicación independiente y se integra con Visual Studio para Mac y Visual Studio para habilitar la generación de perfiles desde el IDE.

Descargue el paquete de instalación de la plataforma:

- [**Mac**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Una vez descargado, inicie el instalador para agregar el Xamarin Profiler al sistema.

## <a name="profilers-and-profiling"></a>Generación de perfiles y generación de perfiles

La generación de perfiles es un paso importante y a menudo desplazado en el desarrollo de aplicaciones. La generación de perfiles es una forma de **análisis de programas dinámicos** : analiza el programa mientras se ejecuta y está en uso. Un generador de perfiles es una herramienta de minería de datos que recopila información sobre la complejidad temporal, el uso de métodos concretos y la memoria que se va a asignar. Un generador de perfiles permite profundizar y analizar estas métricas para identificar áreas problemáticas en el código.

Al diseñar y desarrollar una aplicación, es importante no optimizar prematuramente; es decir, dedicar tiempo a desarrollar el código en áreas a las que rara vez se tendrá acceso. Esta es la eficacia de la generación de perfiles. Un generador de perfiles proporciona una visión general de las partes de código que se usan con más frecuencia y ayuda a localizar las áreas en las que debe dedicar tiempo a hacer mejoras. Los desarrolladores deben tener cuidado para saber dónde se dedica la mayor parte del tiempo a la aplicación y cómo se usa la memoria de la aplicación.

La generación de perfiles es útil en todos los tipos de desarrollo, pero es especialmente importante en el desarrollo móvil. El código no optimizado es mucho más perceptible en las plataformas móviles que en los equipos de escritorio y el éxito de la aplicación depende del código bonito y optimizado que se ejecuta de forma eficaz.

## <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

El Xamarin Profiler proporciona a los desarrolladores una manera de generar perfiles de aplicaciones desde Visual Studio para Mac o Visual Studio. El generador de perfiles recopila y muestra información sobre la aplicación, que el desarrollador puede usar para analizar el comportamiento de una aplicación. Hay varias maneras de generar perfiles de una aplicación con el Xamarin Profiler, es decir, la generación de perfiles de memoria y el muestreo estadístico. Estos se llevan a cabo a través de las asignaciones y los instrumentos del generador de perfiles Time respectivamente.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Actualmente, el Xamarin Profiler se puede usar para probar las aplicaciones de Xamarin. iOS, Xamarin. Android y Xamarin. Mac en Mac (a través de Visual Studio para Mac). El generador de perfiles es un proceso independiente del IDE y, por lo tanto, además de iniciarse desde Visual Studio para Mac, puede usarse como una aplicación independiente para examinar. `.mlpd` exe y los archivos que se han producido desde el [generador de perfiles de mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Actualmente, el Xamarin Profiler se puede usar para probar las aplicaciones de Xamarin. Android en Windows (a través de Visual Studio y Visual Studio para Mac). El generador de perfiles es un proceso independiente del IDE y, por lo tanto, además de iniciarse desde Visual Studio, se puede usar como una aplicación independiente para examinar. `.mlpd` exe y los archivos que se han producido desde el [generador de perfiles de registro de mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Compatibilidad con Profiler

La compatibilidad con la Xamarin Profiler está disponible en las siguientes plataformas:

- Visual Studio para Mac (macOS, con licencia Enterprise)
  - Android
    - Dispositivo y emulador
  - iOS
    - Dispositivo y simulador
  - tvOS (no se admite el instrumento de hora)
    - Dispositivo y simulador
  - Mac

- Visual Studio (solo la versión **Enterprise** )
  - Android
    - Dispositivo y emulador
  - iOS [experimental]
    - Dispositivo y simulador
  - tvOS
    - Dispositivo y simulador

Tenga en cuenta que **solo** puede generar perfiles de configuraciones de **depuración** .

## <a name="profiler-basics"></a>Conceptos básicos de Profiler

En esta sección se presentan las partes de la Xamarin Profiler y se describen sus características.

### <a name="allow-profiling-in-your-app"></a>Permitir la generación de perfiles en la aplicación

Antes de poder generar correctamente el perfil de la aplicación, debe permitir la generación de perfiles en las opciones de proyecto de la aplicación.

- iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilación > > de depuración de iOS habilitar generación de perfiles**

  ![Cuadro de diálogo Opciones de iOS en Visual Studio para Mac](images/ios-options-mac.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propiedades > compilación de iOS > habilitar la generación de perfiles**

  ![Cuadro de diálogo Opciones de iOS en Visual Studio](images/ios-project-options-vs.png)

-----

- Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  **Compilación > Android Debug > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo Opciones de Android en Visual Studio para Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Compilación > Android Debug > habilitar la instrumentación de desarrollador**

  ![Cuadro de diálogo Opciones de Android en Visual Studio para Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Inicio del generador de perfiles

El Xamarin Profiler se puede iniciar desde el IDE al generar perfiles de la aplicación iOS o Android, o como una aplicación independiente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Iniciar desde Visual Studio para Mac

1. En primer lugar, asegúrese de que la aplicación se ha cargado en Visual Studio para Mac y seleccione la configuración de depuración (predeterminada).
2. Vaya a **ejecutar > iniciar la generación de perfiles**en Visual Studio para Mac o **analice > Xamarin Profiler** en Visual Studio para abrir el generador de perfiles, tal como se muestra en el diagrama siguiente:

  ![Inicio del generador de perfiles desde Visual Studio para Mac](images/start-profiling-xs.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Iniciar desde Visual Studio

1. En primer lugar, asegúrese de que la aplicación se carga en Visual Studio y seleccione la configuración de depuración (predeterminada), tal como se especificó anteriormente.
2. Vaya a **analizar > Xamarin Profiler** en Visual Studio para abrir el generador de perfiles, tal como se muestra en el diagrama siguiente:

![Inicio del generador de perfiles desde Visual Studio](images/start-profiling-vs.png)

-----

Si los elementos de menú no aparecen, consulte la [Guía de solución de problemas](~/tools/profiler/troubleshooting.md).

Esto inicia el generador de perfiles e inicia automáticamente la generación de perfiles de la aplicación.

El generador de perfiles se puede usar para medir la memoria y el rendimiento. Consigue esto a través de las asignaciones y los instrumentos del generador de perfiles de tiempo, que exploraremos en detalle en la sección siguiente.

#### <a name="saving-and-loading-profiler-sessions"></a>Guardar y cargar sesiones del generador de perfiles

Para guardar una sesión de generación de perfiles en cualquier momento, elija **archivo > guardar como...** en la barra de menús del generador de perfiles. Esto guarda el archivo en formato _MLPD_ , un formato especial y muy comprimido para los datos de generación de perfiles.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Una vez instalado, el Xamarin Profiler se puede encontrar en la carpeta Aplicaciones, tal como se muestra en la siguiente captura de pantalla:

![Abrir el generador de perfiles independiente desde Mac](images/applications.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una vez instalada la aplicación Xamarin Profiler se puede encontrar en el directorio de la aplicación:

![Abrir el generador de perfiles independiente desde Windows](images/applications-vs.png)

-----

Puede cargar los archivos *. MLPD* en el generador de perfiles abriendo la aplicación independiente, seleccionando **elegir destino** y cargando el archivo.

Para obtener más información, vea [generar archivos. MLPD](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Características del generador de perfiles

El Xamarin Profiler se compone de cinco secciones, como se muestra a continuación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Secciones del generador de perfiles en Visual Studio para Mac](images/profiler-mac-sml.png)](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Secciones del generador de perfiles en Visual Studio](images/profiler-vs.png)](images/profiler-vs.png#lightbox)

-----

- **Barra de herramientas** : se encuentra en la parte superior del generador de perfiles, que ofrece opciones para iniciar o detener la generación de perfiles, seleccionar un proceso de destino, ver el tiempo de ejecución de la aplicación y seleccionar las vistas divididas que componen la aplicación del generador de perfiles.
- **Lista de instrumentos** : muestra todos los instrumentos cargados para la sesión de generación de perfiles.
- **Gráfico de trazado** : estos gráficos se relacionan horizontalmente con los instrumentos relevantes de la lista de instrumentos. Se puede usar un control deslizante (que se muestra debajo de Time Profiler) para cambiar la escala.
- **Área de detalles del instrumento** : contiene los datos que se muestran en la vista seleccionada del instrumento actual. Veremos estas vistas con más detalle en la sección siguiente.
- **Vista de inspector** : contiene secciones que el control segmentado puede seleccionar. Las secciones dependen del instrumento seleccionado e incluyen: Opciones de configuración, estadísticas, información de seguimiento de la pila y ruta de acceso a raíces.

### <a name="allocations"></a>Asignaciones

El instrumento asignaciones proporciona información detallada sobre los objetos de la aplicación a medida que se crean y se recolectan como elementos no utilizados.

En la parte superior del generador de perfiles está el gráfico asignaciones, que muestra la cantidad de memoria asignada a intervalos regulares durante la generación de perfiles. Actualmente, el gráfico asignaciones es el número total de asignaciones y no el tamaño del montón en ese momento dado. En un sentido, nunca dejará de funcionar, sino que solo aumentará. Esto incluye los objetos asignados en la pila. En función de la versión en tiempo de ejecución utilizada, el gráfico puede tener una apariencia diferente, incluso para la misma aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de asignaciones](images/allocations1.png)](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de asignaciones](images/allocations1-vs.png)](images/allocations1-vs.png#lightbox)

-----

Hay diferentes vistas de datos en el instrumento de asignaciones, que permiten a los desarrolladores analizar cómo su aplicación usa y liberan memoria. Estas vistas se describen a continuación:

- **Asignaciones** : muestra una lista de todas las asignaciones y las agrupa por nombre de clase. Esto proporciona una excelente introducción a las clases y los métodos que se usan, la frecuencia con la que se usan y el tamaño colectivo de las clases utilizadas. Si hace doble clic en una clase, se mostrará la memoria asignada: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Pestaña asignaciones](images/allocations3.png)](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Pestaña asignaciones](images/allocations2-vs.png)](images/allocations2-vs.png#lightbox)

-----

La vista del inspector para asignaciones proporciona opciones para filtrar y agrupar objetos, proporcionar estadísticas sobre la memoria asignada y las asignaciones principales, así como vistas para el seguimiento de la pila y la ruta de acceso a la raíz.

- **Árbol de llamadas** : muestra el árbol de llamadas completo de todos los subprocesos de la aplicación e incluye información sobre la memoria asignada en cada nodo. Cuando se selecciona un elemento en la lista, todos los nodos del mismo nivel aparecerán atenuados. Puede expandir el árbol o hacer doble clic en el elemento para profundizar en él. Al mostrar esta vista de datos, se puede usar la vista del inspector configuración de pantalla para cambiar la forma en que se presenta. Actualmente hay dos opciones:
    1. **Árbol de llamadas invertido** : tiene en cuenta el seguimiento de la pila de arriba abajo. Se trata de una opción de vista cómoda, ya que indica los métodos más profundos en los que la CPU ha estado gastando su tiempo.
    2. **Independiente por subproceso** : esta opción organiza el árbol de llamadas por subproceso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Pestaña árbol de llamadas](images/allocations2.png)](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Pestaña árbol de llamadas](images/allocations3-vs.png)](images/allocations3-vs.png#lightbox)

-----

- **Instantáneas** : en este panel se muestra información sobre las instantáneas de memoria. Para generarlas durante la generación de perfiles de una aplicación activa, haga clic en el botón _cámara_ en la barra de herramientas en cada punto en el que desea ver qué memoria se conserva y se libera. Después, puede hacer clic en cada instantánea para explorar lo que está ocurriendo en el capó. Tenga en cuenta que las instantáneas solo se pueden realizar cuando se genera una generación de perfiles de una aplicación en vivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Pestaña instantáneas](images/allocations4.png)](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Pestaña instantáneas](images/allocations4-vs.png)](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Tiempo de Profiler

El instrumento Time Profiler mide exactamente cuánto tiempo se dedica a cada método de una aplicación. La aplicación se pausa a intervalos regulares y se ejecuta un seguimiento de la pila en cada subproceso activo. Cada fila del área de detalles del instrumento muestra la ruta de acceso de ejecución que se ha seguido.

El gráfico de trazados, tal como se muestra en la captura de pantalla siguiente, muestra el número de muestras recibidas por la aplicación cuando se ejecuta:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Time Profiler (instrumento)](images/time1.png)](images/time1.png#lightbox) 

[![Time Profiler Instrument: lista de ejemplos](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Time Profiler (instrumento)](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Time Profiler Instrument: lista de ejemplos](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Árbol de llamadas** : muestra la cantidad de tiempo invertido en cada método:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

  [![Time Profiler Instrument: árbol de llamadas](images/time2.png)](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Time Profiler Instrument: árbol de llamadas](images/time2-vs.png)](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Comercialización

Mediante el uso de C# y F# el código administrado, puede ser bastante común y, desafortunadamente, es bastante fácil crear referencias a objetos que nunca se eliminarán. Este instrumento le permite identificar los objetos y mostrar los ciclos a los que se hace referencia en la aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Instrumento de ciclos](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumento de ciclos](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Generar perfiles de aplicaciones

Actualmente, solo se pueden crear perfiles de las configuraciones de depuración predeterminadas.

Si genera perfiles de una aplicación con cualquier otra configuración, se le presentará el siguiente cuadro de diálogo de mensaje:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Cuadro de diálogo error de generación de perfiles](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Cuadro de diálogo error de generación de perfiles](images/image1vs.png)](images/image1vs.png#lightbox) 

-----

Seleccione **Actualizar** para continuar.

### <a name="sgen-garbage-collector-and-profiling"></a>Recolector de elementos no utilizados SGen y generación de perfiles

El recolector de elementos no utilizados de [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) se usa para todas las plataformas Xamarin.

SGen es un GC de generación, que asigna objetos de una aplicación en tres montones: enfermera, montón principal y espacio de objetos grandes. Esto permite una ejecución más rápida de la recolección de elementos no utilizados. SGen es actualmente el catálogo global predeterminado para las aplicaciones unificadas de Xamarin. Android y Xamarin. iOS.

La aplicación Xamarin. iOS que usa el Classic API usar el GC Boehm: un recolector de elementos no utilizados no generacional y conservador. Como conservador, es menos probable que libere memoria disponible, lo que puede provocar resultados inexactos al usar el generador de perfiles. Por esta razón, el instrumento asignaciones no se puede usar con el recolector de elementos no utilizados de Boehm.

Aunque se le mostrará un cuadro de diálogo de mensaje si la aplicación usa el GC de Boehm, Xamarin no recomienda cambiar la aplicación existente de iOS que usa Boehm a SGen sin una investigación cuidadosa y una prueba exhaustiva. Xamarin tampoco recomienda cambiar a SGen para la generación de perfiles y, a continuación, volver a cambiar, ya que estos resultados no proporcionarán pruebas comparativas precisas de uso de memoria.

Para obtener más información sobre la administración de memoria, consulte la guía de [procedimientos recomendados de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md) .

## <a name="summary"></a>Resumen

En esta guía hemos examinado qué es la generación de perfiles y cómo es ventajosa para el desarrollador. A continuación, se ha introducido el Xamarin Profiler, lo que proporciona un historial e información sobre cómo funciona. Por último, se han recorrido las características del Xamarin Profiler y se han explorado las asignaciones y los instrumentos del generador de perfiles de tiempo.

## <a name="related-links"></a>Vínculos relacionados

- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
