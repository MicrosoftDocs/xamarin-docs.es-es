---
title: Error de iOS Designer con RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: b76de0f0dc8f1fd2124eac9cfe03b872c71095b1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940952"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Error de iOS Designer con RegisterServicePort

## <a name="sample-error"></a>Error de ejemplo
> System. AggregateException: se produjeron uno o varios errores---> System. SystemException: RegisterServicePort (com. Xamarin. MTHosting. 2a0b1, com. Apple. PowerManagement. control): el kernel devolvió:-308 (-308): (IPC/MIG) servidor muerto

## <a name="explanation"></a>Explicación
Los errores con `RegisterServicePort` y mensajes de error similares como los anteriores suelen ser un problema con spyware o malware en el equipo. Considere el [Comentario sobre este informe de errores](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obtener más información, junto con el vínculo a la [discusión del Foro de Apple](https://discussions.apple.com/thread/5596008) sobre cómo quitar una posible infección. 

Para ayudar a diagnosticar el problema, abra la **consola** de la aplicación MacOS y elimine todos los archivos incluidos en la sección **informes de diagnóstico de usuario** [https://screencast.com/t/y9i3NKcuMy](https://screencast.com/t/y9i3NKcuMy). A continuación, inicie Visual Studio para Mac e intente usar el diseñador. Si los nuevos archivos de registro aparecen en esta sección después de que el diseñador no se ha inicializado, guárdelos para analizarlos.  

Tenga en cuenta lo más importante que debe buscar es este archivo: 
> /usr/lib/libimckit.dylib

Con independencia de los resultados anteriores, si el archivo existe, el problema de spyware o malware mencionado está presente en el equipo.  

En el vínculo siguiente se describen los pasos para quitar este spyware o malware: [https://www.thesafemac.com/arg-genieo/](https://www.thesafemac.com/arg-genieo/)  
