---
title: Solución de problemas de enlaces
description: En esta guía se describe lo que debe hacer si tiene dificultades para enlazar una biblioteca de Objective-C. En concreto, se describen los enlaces que faltan, las excepciones de los argumentos al pasar null a un enlace y los informes de errores.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: e43e32b2ad598a7c80e04d8e28d67e85d5a0f9f5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570952"
---
# <a name="binding-troubleshooting"></a>Solución de problemas de enlaces

Algunas sugerencias para solucionar problemas de enlaces a las API de macOS (anteriormente conocido como OS X) en Xamarin. Mac.

## <a name="missing-bindings"></a>Faltan enlaces

Aunque Xamarin. Mac abarca gran parte de las API de Apple, en ocasiones es posible que tenga que llamar a algunas API de Apple que todavía no tienen un enlace. En otros casos, debe llamar a un tercero C/Objective-C que esté fuera del ámbito de los enlaces de Xamarin. Mac.

Si trabaja con una API de Apple, el primer paso es dejar que Xamarin sepa que está llegando a una sección de la API para la que aún no tenemos cobertura. [Archivo de un error](#reporting-bugs) que indica la API que falta. Usamos informes de clientes para priorizar qué API trabajamos en el siguiente. Además, si tiene una licencia empresarial o empresarial y esta falta de un enlace está bloqueando el progreso, siga también las instrucciones de [soporte técnico](https://visualstudio.microsoft.com/vs/support/) para archivar un vale. No podemos poner en peligro un enlace, pero en algunos casos podemos ayudarle a solucionarlo.

Una vez que notifique a Xamarin (si es aplicable) de su enlace que falta, el siguiente paso es tener en cuenta su enlace. [Aquí tenemos una guía completa](~/cross-platform/macios/binding/overview.md) y otra documentación no oficial para el ajuste [manual de enlaces](https://brendanzagaeski.appspot.com/xamarin/0002.html) de Objective-C. Si está llamando a una API de C, puede usar el mecanismo de P/Invoke de C#; la documentación está [aquí](https://www.mono-project.com/docs/advanced/pinvoke/).

Si decide trabajar en el enlace, tenga en cuenta que los errores en el enlace pueden producir todo tipo de bloqueos interesantes en el tiempo de ejecución nativo. En concreto, tenga cuidado de que la firma en C# coincida con la firma nativa en número de argumentos y el tamaño de cada argumento. Si no lo hace, puede dañar la memoria o la pila y puede bloquearse inmediatamente o en algún punto arbitrario del futuro o datos dañados.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Excepciones de argumentos al pasar null a un enlace

Mientras Xamarin trabaja para proporcionar enlaces de alta calidad y bien probados para las API de Apple, a veces se producen errores y errores en la remisión. A diferencia del problema más común que podría encontrarse es una API que se produce `ArgumentNullException` cuando se pasa NULL cuando la API subyacente acepta `nil` . Los archivos de encabezado nativo que definen la API no proporcionan información suficiente sobre qué API aceptan Nil y cuál se bloqueará si se pasa en.

Si ejecuta en un caso en el que pasar `null` produce una excepción `ArgumentNullException` pero cree que debería funcionar, siga estos pasos:

1. Consulte la documentación de Apple o los ejemplos para ver si puede encontrar una prueba que acepte `nil` . Si está familiarizado con Objective-C, puede escribir un pequeño programa de prueba para comprobarlo.
2. [Archivo a un error](#reporting-bugs).
3. ¿Puede solucionar el error? Si puede evitar llamar a la API con `null` , una simple comprobación nula en torno a las llamadas puede ser una solución sencilla.
4. Sin embargo, algunas API requieren pasar null para desactivar o deshabilitar algunas características. En estos casos, puede solucionar el problema mediante la introducción del explorador de ensamblados (vea [Buscar el miembro de C# para un selector determinado](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiar el enlace y quitar la comprobación de valores NULL. Asegúrese de que se ha archivado un error (paso 2): si lo hace, ya que el enlace copiado no recibirá las actualizaciones y las correcciones que se realicen en Xamarin. Mac, por lo que se debe considerar una solución alternativa a corto plazo.

<a name="reporting-bugs"></a>

## <a name="reporting-bugs"></a>Informes de errores

Sus comentarios son importantes. Si encuentra algún problema con Xamarin. Mac:

- Compruebe los [foros de Xamarin.Mac](https://forums.xamarin.com/categories/xamarin-mac).
- Busque en el [repositorio de problemas](https://github.com/xamarin/xamarin-macios/issues).
- Antes de la migración a GitHub, los problemas de Xamarin se recopilaban en [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Busque allí para ver si están los mismos problemas.
- Si no encuentra un problema, registre uno nuevo en el [repositorio de problemas de GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Los problemas de GitHub son públicos. No es posible ocultar comentarios ni datos adjuntos.

De la siguiente información, incluya toda la que pueda:

- Un ejemplo sencillo que reproduzca el problema. Siempre que sea posible, esta información es **muy útil**.
- El seguimiento de la pila completo del bloqueo.
- El código de C# del bloqueo.
