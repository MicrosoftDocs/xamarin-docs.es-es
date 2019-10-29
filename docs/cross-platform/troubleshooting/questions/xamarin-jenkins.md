---
title: ¿Por qué no es compatible con Jenkins de Microsoft?
description: En este documento se describe, en un nivel alto, la interacción de Xamarin con el sistema de CI de Jenkins. También se describen algunos problemas comunes que surgen al trabajar con Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 7be60eaa1135284522ef1e6ce81c911a68ff9915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012225"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>¿Por qué no es compatible con Jenkins de Microsoft?

## <a name="jenkins-support-explanation"></a>Explicación de compatibilidad con Jenkins

Jenkins es un conjunto de elementos de CI de código abierto. debido a esto, muchos problemas causados directamente por el *propio* Jenkins deberán archivarse como problemas con respecto a dónde se recibió el código. como el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins)o el repositorio de [Jenkins. app](https://github.com/stisti/jenkins-app).

La excepción a esto es para los problemas que se pueden aislar de errores concretos en las herramientas de Xamarin; Si sospecha que este es el caso, puede comprobar las [Opciones de soporte técnico](~/cross-platform/troubleshooting/support-options.md), pero de nuevo, el problema podría ser algo ajeno a lo que el equipo de soporte técnico de Xamarin puede ayudarle *directamente* .

## <a name="setup-jenkins-with-xamarin"></a>Configuración de Jenkins con Xamarin

Aunque como se indicó anteriormente, el equipo no admite directamente los problemas de Jenkins; la guía [uso de Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) se puede usar para configurar un servidor de CI de Jenkins que esté integrado con Xamarin. 

## <a name="fixes-for-common-issues"></a>Correcciones para problemas comunes

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins no encuentra la Android SDK

El mensaje de error para este problema es similar al siguiente:

> error XA5205: no se encontró el directorio de Android SDK. Establezca a través de/p: AndroidSdkDirectory

Las opciones para establecer la ubicación del SDK pueden variar en función del complemento de Android de Jenkins exacto que esté usando. un buen lugar para buscar cómo establecer esto es en la guía de complementos. Por ejemplo, el [complemento de Android Emulator](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) busca automáticamente el SDK, pero si no lo encuentra; la ubicación también se puede establecer a través de la página de configuración del sistema Jenkins para ese complemento. 

## <a name="deprecated-errors"></a>Errores en desuso

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins informa de una licencia de Xamarin no válida
Los mensajes de error para este problema suelen ser similares a

> Error XA9008: la compilación desde la línea de comandos requiere una licencia empresarial

o

> Error: la edición de inicio de Xamarin. iOS no admite la compilación fuera de Xamarin Studio 

La causa más común de este escenario es el uso de Jenkins iniciando sesión con una cuenta de usuario que no está asociada a la licencia de Xamarin. La forma más sencilla de resolverlo es instalar Jenkins como una aplicación directamente a través de la cuenta de usuario. Este proceso y algunas consideraciones adicionales se describen aquí: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
