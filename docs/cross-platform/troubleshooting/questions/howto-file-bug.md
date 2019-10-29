---
title: ¿Cuándo y cómo se debería presentar un informe de errores?
description: En este documento se describe cuándo, dónde y cómo archivar un informe de errores. También proporciona procedimientos recomendados de informes de errores que permiten a los ingenieros diagnosticar mejor el problema.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: df00eebe682d2d06b99721a2d3c3b90d13454c75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014002"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>¿Cuándo y cómo se debería presentar un informe de errores?

> [!TIP]
> Use el elemento de menú **notificar un problema** en Visual Studio &ndash; esta opción enviará información de diagnóstico junto con el informe de errores para ayudar a resolver el problema.
>
> Hay instrucciones detalladas para [visual studio 2019 o visual studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio) y [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem).
>
> Puede buscar informes existentes en el sitio web de la [comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) .

## <a name="file-a-bug-if"></a>Archivo a un error si...

Tiene un conjunto de pasos que cree que los ingenieros podrán usar para reproducir un problema.

O

Puede describir detenidamente los síntomas visibles del problema, especialmente si también puede describir algunas circunstancias precisas relacionadas con el problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Prácticas recomendadas para ayudar a solucionar errores de forma rápida y eficaz

1. <a name="ref-1" />busque en la [comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) y en la web los informes de errores existentes o las sugerencias de uso que podrían solucionar el problema directamente. <sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />describir el problema de la manera más clara y concisa posible, incluida una descripción de lo que sucedió y se esperaba que se produjera.

1. <a name="ref-3" />incluyen cualquier seguimiento de pila relevante, texto de mensaje de error o registros de bloqueo (si usa la característica **notificar un problema** , estos se pueden incluir automáticamente). <sup>[4](#note-4)</sup>

1. <a name="ref-4" />anotar los mensajes de error importantes que aparecen en los datos adjuntos de la captura de pantalla como texto sin formato.

1. <a name="ref-5" />incluye un pequeño caso de prueba independiente que reproduce el error con el menor código posible.  Si no puede reproducir el problema con un proyecto nuevo (creado mediante una de las plantillas integradas), debe comprimir un proyecto que muestre el problema y adjuntarlo al informe de errores.  Haga que el proyecto de ejemplo sea lo más sencillo posible antes de adjuntarlo. <sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />describir el entorno en el que se encontró el error, incluido el sistema operativo y [las versiones de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) y las dependencias.

## <a name="additional-details"></a>Detalles adicionales

1. <a name="note-1" />[ *^* ](#ref-1) idealmente, la descripción de los "síntomas visibles" debe incluir suficientes detalles para que otros clientes puedan confirmar si ven el mismo problema (los mismos mensajes de error, la misma degradación del rendimiento, el mismo seguimiento de la pila de un bloqueo, _etc._ ). En el caso de "circunstancias precisas", un buen ejemplo sería si se puede decir algo parecido a lo siguiente: "normalmente se alcanzó el problema del 75% del tiempo, pero si se cambia esta cosa, puedo evitar el problema por completo". Otro ejemplo similar de una "circunstancia precisa" es si al degradar a una versión anterior de Xamarin se detiene el problema.

1. <a name="note-2" />[ *^* ](#ref-2) como cabría esperar, los fragmentos de texto de error (o cualquier otro texto descriptivo) suelen ser los mejores términos de búsqueda. Si el informe de errores existente está incompleto, le agradecemos que agregue detalles o que contenga un nuevo informe de errores mejor.

1. <a name="note-3" />[ *^* ](#ref-3) otra buena pregunta es si se ha comunicado el mismo problema para las aplicaciones Java, Objective-C o SWIFT. Si es así, el problema es muy probable que sea parte de Android o iOS en lugar de formar parte de Xamarin.

1. <a name="note-4" />[ *^* ](#ref-4) algunos ejemplos de información para incluir:

    1. En el caso de los errores que se producen al compilar un proyecto, incluya la salida de la [compilación de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) completa en el informe de errores.

    1. En el caso de los errores que se producen al compilar o depurar un proyecto de iOS desde Visual Studio, ejecute la **ayuda > los registros de Xamarin > zip** después de encontrar el error e incluya el archivo. zip resultante en el informe de errores.

    1. En el caso de las excepciones o los bloqueos en aplicaciones Android o iOS, incluya los [registros de depuración pertinentes para las aplicaciones Xamarin. Android y Xamarin. iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5" />[ *^* ](#ref-5) si es posible para su problema concreto, una opción es volver a crear el problema agregando un pequeño número de archivos de la solución original a una solución nueva. El equipo de Xamarin suele ser capaz de investigar los problemas incluso en casos de prueba más grandes (suponiendo que los pasos para reproducirse se explican con claridad), pero los casos de prueba más sencillos proporcionan la mejor posibilidad de que el error se resuelva rápidamente.

1. <a name="note-6" />[ *^* ](#ref-6) si _no_ es posible reproducir el problema agregando un pequeño número de archivos a una solución nueva, puede comprimir y adjuntar toda la carpeta de la solución para la aplicación completa. Elimine las carpetas `bin`, `obj`, `Components`y `packages` para reducir el tamaño del archivo. zip. (El IDE y el proceso de compilación normalmente restaurarán o volverán a crear el contenido de estas carpetas según sea necesario). También puede eliminar todos los archivos de código y de recursos del proyecto que desee, siempre que la solución resultante muestre el problema original.
