---
title: ¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/31/2017
ms.openlocfilehash: f8e3855b10a20bd4312420f8faf6c68dedde0c67
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292100"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?

Si tiene que establecer variables de entorno de tiempo de ejecución para mono, se pueden establecer en las **Opciones del proyecto > ejecutar > Página general** .

Nota: Las variables de entorno de recolección de elementos\_no\_utilizados para SGen (parámetros de GC GC) establecida de esta manera solo se utilizarán al iniciar desde Xamarin Studio. Si inicia la aplicación desde el dispositivo, se omitirá la configuración de Sgen. 

Para establecer de forma permanente una variable de entorno para una aplicación, debe agregarla como argumento Mtouch adicional (para todas las configuraciones relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver las variables de entorno que se pueden establecer, consulte la página de Man mono:  [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1))Vea la sección titulada:`ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Establecer variables de entorno para un proyecto")
