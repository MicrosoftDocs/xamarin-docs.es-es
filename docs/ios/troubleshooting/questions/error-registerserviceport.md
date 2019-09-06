---
title: Error de iOS Designer con RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 68c87355a2a6a081e0fff741ffe8a4466abb540a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292602"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Error de iOS Designer con RegisterServicePort

## <a name="sample-error"></a>Error de ejemplo
> System.AggregateException: Se produjeron uno o más errores---> System. SystemException: RegisterServicePort (com. Xamarin. MTHosting. 2a0b1, com. Apple. PowerManagement. control): Kernel devuelto:-308 (-308): (IPC/MIG) servidor muerto

## <a name="explanation"></a>Explicación
Los errores de y los mensajes de error similares a los anteriores suelen ser un problema con spyware o malware en el equipo. `RegisterServicePort` Considere el [Comentario sobre este informe de errores](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obtener más información, junto con el vínculo a la [discusión del Foro de Apple](https://discussions.apple.com/thread/5596008) sobre cómo quitar una posible infección. 

Para ayudar a diagnosticar el problema, abra la **consola** de la aplicación MacOS y elimine todos los archivos incluidos en la [http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy)sección **informes de diagnóstico de usuario** . A continuación, inicie Visual Studio para Mac e intente usar el diseñador. Si los nuevos archivos de registro aparecen en esta sección después de que el diseñador no se ha inicializado, guárdelos para analizarlos.  

Tenga en cuenta lo más importante que debe buscar es este archivo: 
> /usr/lib/libimckit.dylib

Con independencia de los resultados anteriores, si el archivo existe, el problema de spyware o malware mencionado está presente en el equipo.  

En el vínculo siguiente se describen los pasos para quitar este spyware o malware:[http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

