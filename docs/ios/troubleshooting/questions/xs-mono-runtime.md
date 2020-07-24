---
title: ¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: 30585bede5569edfa50ab9f450bcb62e04c8a10d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938591"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?

Si tiene que establecer variables de entorno de tiempo de ejecución para mono, se pueden establecer en las **Opciones del proyecto > ejecutar > página general** .

Nota: las variables de entorno de recolección de elementos no utilizados para SGen ( \_ parámetros de GC GC \_ ) establecidas de esta manera solo se utilizarán al iniciar desde Xamarin Studio. Si inicia la aplicación desde el dispositivo, se omitirá la configuración de Sgen. 

Para establecer de forma permanente una variable de entorno para una aplicación, debe agregarla como argumento Mtouch adicional (para todas las configuraciones relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver las variables de entorno que se pueden establecer, consulte la página de Man mono: [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) consulte la sección titulada:`ENVIRONMENT VARIABLES`

![Establecer variables de entorno para un proyecto](xs-mono-runtime-images/environment-variables.jpg)
