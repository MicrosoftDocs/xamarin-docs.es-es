---
title: ¿Cuándo y cómo se debería presentar un informe de errores?
description: En este documento se describe cuándo, dónde y cómo archivar un informe de errores. También proporciona procedimientos recomendados de informes de errores que permiten a los ingenieros diagnosticar mejor el problema.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: 38a96f5f499b760aedcfc51dc9e6326c9c62e95b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457879"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>¿Cuándo y cómo se debería presentar un informe de errores?

> [!TIP]
> Use el elemento de menú **notificar un problema** en Visual Studio &ndash; . se enviará información de diagnóstico junto con el informe de errores para ayudar a resolver el problema.
>
> Hay instrucciones detalladas para [visual studio 2019 o visual studio 2017](/visualstudio/ide/how-to-report-a-problem-with-visual-studio) y [Visual Studio para Mac](/visualstudio/mac/report-a-problem).
>
> Puede buscar informes existentes en el sitio web de la [comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) .

## <a name="file-a-bug-if"></a>Archivo a un error si...

Tiene un conjunto de pasos que cree que los ingenieros podrán usar para reproducir un problema.

O BIEN

Puede describir detenidamente los síntomas visibles del problema, especialmente si también puede describir algunas circunstancias precisas relacionadas con el problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Prácticas recomendadas para ayudar a solucionar errores de forma rápida y eficaz

1. <a name="ref-1"></a>Busque en la [comunidad de desarrolladores de Visual Studio](https://developercommunity.visualstudio.com/) y en la web los informes de errores existentes o las sugerencias de uso que pueden solucionar el problema directamente. <sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2"></a>Describa el problema de la manera más clara y concisa posible, incluida una descripción de lo que sucedió y se esperaba que se produjera.

1. <a name="ref-3"></a>Incluya cualquier seguimiento de pila relevante, texto de mensaje de error o registros de bloqueo (si usa la característica **notificar un problema** , estos se pueden incluir automáticamente). <sup>[4](#note-4)</sup>

1. <a name="ref-4"></a>Anote también los mensajes de error importantes que aparecen en los datos adjuntos de la captura de pantalla como texto sin formato.

1. <a name="ref-5"></a>Incluya un pequeño caso de prueba independiente que reproduzca el error con el menor código posible.  Si no puede reproducir el problema con un proyecto nuevo (creado mediante una de las plantillas integradas), debe comprimir un proyecto que muestre el problema y adjuntarlo al informe de errores.  Haga que el proyecto de ejemplo sea lo más sencillo posible antes de adjuntarlo. <sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6"></a>Describa el entorno en el que se encontró el error, incluido el sistema operativo y [las versiones de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) y las dependencias.

## <a name="additional-details"></a>Detalles adicionales

1. <a name="note-1"></a>[*^*](#ref-1) Idealmente, la descripción de los "síntomas visibles" debe incluir suficientes detalles para que otros clientes puedan confirmar si ven el mismo problema (los mismos mensajes de error, la misma degradación del rendimiento, el mismo seguimiento de la pila de un bloqueo, _etc._). En el caso de "circunstancias precisas", un buen ejemplo sería si se puede decir algo parecido a lo siguiente: "normalmente se alcanzó el problema del 75% del tiempo, pero si se cambia esta cosa, puedo evitar el problema por completo". Otro ejemplo similar de una "circunstancia precisa" es si al degradar a una versión anterior de Xamarin se detiene el problema.

1. <a name="note-2"></a>[*^*](#ref-2) Como cabría esperar, los fragmentos de texto de error (o cualquier otro texto descriptivo) suelen ser los mejores términos de búsqueda. Si el informe de errores existente está incompleto, le agradecemos que agregue detalles o que contenga un nuevo informe de errores mejor.

1. <a name="note-3"></a>[*^*](#ref-3) Otra buena pregunta es si se ha comunicado el mismo problema para las aplicaciones Java, Objective-C o SWIFT. Si es así, el problema es muy probable que sea parte de Android o iOS en lugar de formar parte de Xamarin.

1. <a name="note-4"></a>[*^*](#ref-4) Algunos ejemplos de información para incluir:

    1. En el caso de los errores que se producen al compilar un proyecto, incluya la salida de la [compilación de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) completa en el informe de errores.

    1. En el caso de los errores que se producen al compilar o depurar un proyecto de iOS desde Visual Studio, ejecute la **ayuda > los registros de Xamarin > zip** después de encontrar el error e incluya el archivo. zip resultante en el informe de errores.

    1. En el caso de las excepciones o los bloqueos en aplicaciones Android o iOS, incluya los [registros de depuración pertinentes para las aplicaciones Xamarin. Android y Xamarin. iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5"></a>[*^*](#ref-5) Si es posible para su problema concreto, una opción es volver a crear el problema agregando un pequeño número de archivos de la solución original a una solución nueva. El equipo de Xamarin suele ser capaz de investigar los problemas incluso en casos de prueba más grandes (suponiendo que los pasos para reproducirse se explican con claridad), pero los casos de prueba más sencillos proporcionan la mejor posibilidad de que el error se resuelva rápidamente.

1. <a name="note-6"></a>[*^*](#ref-6) Si _no_ es posible reproducir el problema agregando un pequeño número de archivos a una solución nueva, puede comprimir y adjuntar toda la carpeta de la solución para la aplicación completa. Elimine las `bin` `obj` carpetas,, `Components` y `packages` para reducir el tamaño del archivo. zip. (El IDE y el proceso de compilación normalmente restaurarán o volverán a crear el contenido de estas carpetas según sea necesario). También puede eliminar todos los archivos de código y de recursos del proyecto que desee, siempre que la solución resultante muestre el problema original.