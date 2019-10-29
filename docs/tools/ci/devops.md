---
title: DevOps con Xamarin
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: davidortinau
ms.author: daortin
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: 57e3444ebcbd87607da248ecacca5e76a44f1577
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007446"
---
# <a name="devops-with-xamarin"></a>DevOps con Xamarin

Xamarin le permite crear aplicaciones móviles multiplataforma destinadas a Android, iOS y Windows con C#, .NET y Visual Studio. Xamarin permite compartir entre plataformas gran parte del código. Solo es necesario que un pequeño porcentaje sea específico de la plataforma.

El desarrollo de aplicaciones para plataformas modernas implica muchas otras actividades, además de escribir código. Estas actividades, denominadas DevOps (desarrollo + operaciones), abarcan el ciclo de vida completo de la aplicación e incluyen la planeación y el seguimiento del trabajo, el diseño y la implementación del código, la administración de un repositorio de código fuente, las compilaciones, la administración de integraciones e implementaciones continuas, las pruebas (incluidas las pruebas unitarias y las pruebas de la interfaz de usuario), la ejecución de diversas maneras de diagnóstico tanto en entornos de desarrollo como de producción y la supervisión del rendimiento de la aplicación y del comportamiento de los usuarios en tiempo real mediante telemetría y análisis.

Visual Studio, Azure DevOps Services y Team Foundation Server ofrecen una variedad de funcionalidades de DevOps. Muchas de ellas son aplicables por completo a proyectos multiplataforma. Esto es especialmente cierto con las aplicaciones de Xamarin, ya que se compilan con C# y. NET, sobre los que se compilan algunas herramientas de DevOps. Otras herramientas requieren una estrecha integración con los entornos de compilación y de tiempo de ejecución. Dado que las aplicaciones Xamarin se ejecutan en plataformas que no son de Windows y usan la implementación Mono de. NET, Xamarin ofrece herramientas especializadas para ciertas necesidades.

En las siguientes tablas se identifican qué características de DevOps de Visual Studio puede esperar que funcionen bien con un proyecto de Xamarin y cuáles presentan limitaciones. Consulte la documentación vinculada para obtener más información acerca de cada característica.

## <a name="agile-tools"></a>Herramientas de Agile

Vínculo de referencia: **[About Agile tools and Agile project management](/azure/devops/boards/backlogs/overview?view=vsts)** (Sobre herramientas y gestión de proyectos de Agile)

Comentario general: todas las características de planeación y seguimiento son independientes del tipo de proyecto y de los lenguajes de codificación.

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Administrar trabajos pendientes y sprints|Sí||
|Seguimiento del trabajo|Sí||
|Colaboración en la sala de reuniones del equipo|Sí||
|Paneles Kanban|Sí||
|Notificar y visualizar el progreso|Sí||

## <a name="modeling"></a>Modelado

Vínculo de referencia: **[Analizar y modelar la arquitectura](/visualstudio/modeling/analyze-and-model-your-architecture)**

Las características de diseño son independientes del lenguaje de codificación o funcionan con lenguajes .NET como C#. Vea [Roles de arquitectura y diagramas de modelado en el desarrollo de software](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools) para obtener información sobre los aspectos relacionados con el código.

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Diagramas de secuencia|Sí||
|Gráficos de dependencia|Sí||
|Jerarquía de llamadas|Sí||
|Diseñador de clases|Sí||
|Explorador de arquitectura|Sí||
|Diagramas UML (caso de uso, actividad, clase, componente, secuencia y DSL)|Sí||
|Diagramas de capas|Sí||
|Validación de capas|Sí||

## <a name="code"></a>Código

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|[Uso del control de versiones de Team Foundation (TFVC)](/azure/devops/repos/tfvc/overview?view=vsts) o Azure Repos.|Sí||
|[Introducción a Git en Azure Repos](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|Sí||
|[Mejorar la calidad del código](/visualstudio/test/improve-code-quality)|Sí||
|[Buscar cambios en el código y otro historial](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|Sí|Excepto en los límites específicos de la plataforma donde la implementación no se resuelve hasta el tiempo de ejecución.|
|[Usar mapas de código para depurar aplicaciones](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|Sí||

## <a name="build"></a>Compilar

Vínculo de referencia: **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Servidor TFS local|Sí|Los equipos de compilación deben tener instalado Xamarin y se pueden vincular a un equipo OSX para compilar para iOS. Vea [Usar TFVC](/azure/devops/repos/tfvc/overview?view=vsts).|
|Servidor de compilación local vinculado a Azure Pipelines|Sí|Vea [Build and release agents](/azure/devops/pipelines/agents/agents?view=vsts) (Agentes de compilación y versiones) para obtener instrucciones.|
|Servicio de controlador hospedado de Azure Pipelines|Sí|Vea [Build your Xamarin app](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts) (Crear su aplicación de Xamarin).|
|Compilar definiciones con scripts anteriores y posteriores|Sí||
|Integración continua, incluidas entradas validadas|Sí|Entradas validadas para TFVC solo cuando GIT funciona en un modelo de solicitud de extracción en lugar de entradas.|

## <a name="test"></a>Prueba

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Planear pruebas, crear casos de prueba y organizar conjuntos de pruebas|Sí||
|Pruebas manuales|Sí||
|Administrador de pruebas (grabar y reproducir pruebas)|Sí|Dispositivos Windows y emuladores de Android únicamente de Visual Studio.|
|Cobertura de código|no disponible||
|[Haga una prueba unitaria de su código](/visualstudio/test/unit-test-your-code/)|Sí|Para destinos Windows y Android, pueden usarse las herramientas integradas de MSTest. Xamarin recomienda NUnit para ejecutar pruebas unitarias en Windows, Android e iOS. Vea [Usar TFVC](/azure/devops/repos/tfvc/overview?view=vsts).|
|[Usar la automatización de la interfaz de usuario para probar el código](/visualstudio/test/use-ui-automation-to-test-your-code/)|Solo Windows|La grabadora de pruebas de interfaz de usuario de Visual Studio es solo para Windows. Para todas las plataformas, vea [Xamarin.UITest](/appcenter/test-cloud/uitest/).|

## <a name="improve-code-quality"></a>Mejorar la calidad del código

Vínculo de referencia: **[Mejorar la calidad del código](/visualstudio/test/improve-code-quality)**

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|[Analizar la calidad del código administrado](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|Sí||
|[Buscar código duplicado mediante la detección de clones de código](https://msdn.microsoft.com/library/hh205279.aspx)|Sí||
|[Medir la complejidad y el mantenimiento del código administrado](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|Sí||
|[Explorador de rendimiento](/visualstudio/profiling/performance-explorer)|No|En su lugar, utilice la [Xamarin Profiler](/xamarin/tools/profiler/) a través de Visual Studio para Mac. Tenga en cuenta que el generador de perfiles de Xamarin está actualmente en vista previa y aún no funciona para destinos de Windows.|
|[Analizar problemas de memoria de .NET Framework](https://msdn.microsoft.com/library/dn342825.aspx)|No|Las herramientas de Visual Studio no tienen enlaces al marco de trabajo de Mono para la generación de perfiles.|

## <a name="release-management"></a>Administración de versiones

Vínculo de referencia: **[Compilación y versiones en Azure Pipelines y TFS](/azure/devops/pipelines/overview?view=vsts)**

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Administrar procesos de versión|Sí||
|Implementar en servidores para la instalación de prueba mediante scripts|Sí||
|Cargar a la tienda de aplicaciones|Partial|Hay extensiones disponibles que pueden automatizar este proceso para algunas tiendas de aplicaciones.  Vea [Extensions for Azure DevOps Services](https://marketplace.visualstudio.com/VSTS) (Extensiones para Azure DevOps Services); por ejemplo, la [extensión para Google Play](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play).|

## <a name="monitor-with-hockeyapp"></a>Supervisión con HockeyApp

Vínculo de referencia: **[Supervisión con HockeyApp](https://www.hockeyapp.net/features/)**

|Característica|Compatible con Xamarin|Comentarios adicionales|
|-------------|----------------------------|-------------------------|
|Análisis de bloqueo, telemetría y distribución beta|Sí||
