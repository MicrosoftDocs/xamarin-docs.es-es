---
title: Preguntas frecuentes generales
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: conceptdev
ms.author: crdun
ms.date: 05/08/2018
ms.openlocfilehash: 3b940199a1384540bcc9f7134dabf8b69ac3767e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285009"
---
# <a name="general-frequently-asked-questions"></a>Preguntas frecuentes generales

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[¿Cómo puedo ver qué bibliotecas se admiten en una PCL?](pcl-support-libraries.md)
En esta guía se enumeran los recursos y métodos para determinar si la biblioteca existente es compatible con las distintas plataformas de destino de PCL o se puede convertir en un perfil de PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[Reflection API de PCL](pcl-reflection.md)
Microsoft desarrolló una nueva API de reflexión para su uso en bibliotecas de clases portables. Si tiene algún código de reflexión existente que desea trasladar a una PCL, es posible que no funcione.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Caso práctico de PCL: ¿Cómo puedo resolver problemas relacionados con System. Diagnostics. Tracing para el paquete NuGet de flujo de entrada de TPL de Microsoft?](pcl-case-study.md)
Xamarin. iOS y Xamarin. Android no implementan el 100% de todos los perfiles de PCL que permiten como referencias. Por comodidad práctica en Visual Studio para Mac, Visual Studio y el administrador de paquetes NuGet, los proyectos de Xamarin permiten el uso de varios perfiles que solo tienen implementaciones incompletas. Por ejemplo, ni Xamarin. iOS ni Xamarin. Android incluyen actualmente una implementación completa de los tipos en el `System.Diagnostics.Tracing` espacio de nombres PCL. Puede evitar esto cambiando el proyecto de la aplicación para que haga referencia a la versión portable-net45 + win8 + WP8 + wpa81 de la biblioteca de flujos de trabajo TPL.

## <a name="nuget-packages--xamarin-components"></a>Paquetes NuGet & componentes de Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[¿Cómo puedo actualizar NuGet?](nuget-update.md)
Las actualizaciones, extensiones y complementos de NuGet se pueden encontrar en la pestaña **actualizaciones** del **Administrador de paquetes NuGet**. En esta guía se describe la navegación detallada para encontrar las actualizaciones en Visual Studio para Mac & Visual Studio.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[¿Cómo se puede cambiar a una versión anterior un paquete NuGet?](nuget-package-downgrade.md)
Visual Studio para Mac & Visual Studio tienen características para seleccionar versiones anteriores de paquetes e instalarlas automáticamente. similar al funcionamiento de los paquetes de actualización.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Error de paquetes que faltan después de actualizar paquetes Nuget](nuget-packages-missing.md)
Este problema se ha comunicado principalmente en las soluciones de aplicación de ejemplo de Xamarin. Forms, pero la posibilidad de este problema puede producirse en cualquier proyecto que use paquetes de NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificación de componentes de Google Play Services y NuGet](gps-components-nuget.md)
Solía haber varios componentes de Google Play Services y paquetes NuGet, pero para facilitar la tarea a los desarrolladores, ahora hemos unificado nuestros componentes y paquetes de NuGet en dos. En casi todos los casos, se debe utilizar Google Play Services. La única razón para usar el paquete de (Froyo) es si se dirige activamente a Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[¿Dónde se almacenan los componentes en mi equipo?](component-storage.md)
Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en las dos ubicaciones enumeradas en esta guía.


## <a name="troubleshooting"></a>Solución de problemas
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[¿Dónde se puede encontrar mi información de versión y los registros?](version-logs.md)
En esta guía se detalla dónde encontrar la mayoría de la información de diagnóstico que se puede usar para solucionar problemas de Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[¿Cuándo y cómo se debería presentar un informe de errores?](howto-file-bug.md)
En esta guía se proporcionan sugerencias para archivar informes de errores de alta calidad, de modo que nuestros ingenieros puedan determinar la causa (y las posibles correcciones) de un problema de forma más eficaz.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[¿Por qué no se admite Jenkins por Xamarin?](xamarin-jenkins.md)
Jenkins es un conjunto de elementos de CI de código abierto. debido a esto, muchos problemas causados directamente por el *propio* Jenkins deberán archivarse como problemas con respecto a dónde se recibió el código. como el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins)o el repositorio de [Jenkins. app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[¿Qué configuración del proyecto es necesaria para el depurador?](debugger-settings.md)
Para que el depurador funcione según lo esperado (puntos de interrupción, Mostrar registros de depuración, etc.), se debe habilitar la presentación de la información de depuración y la instrumentación del desarrollador. En esta guía se detalla cómo buscar y activar estos valores.

