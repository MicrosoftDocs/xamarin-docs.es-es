---
title: Compatibilidad del lenguaje de programación en Xamarin
description: En este documento se describen varios lenguajes de programación compatibles con Xamarin. Se tratan las C#plantillas F#,, Basic.net visual portable y Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: davidortinau
ms.author: daortin
ms.date: 02/18/2018
ms.openlocfilehash: db963a38322e809d1aa82c02fbb9ae5cc4a650fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014632"
---
# <a name="programming-language-support-in-xamarin"></a>Compatibilidad del lenguaje de programación en Xamarin

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)

La versión 5 C# de presentó dos nuevas palabras clave para expresar las operaciones asincrónicas: Async y Await. Estas palabras clave permiten escribir código simple que utiliza la biblioteca TPL para ejecutar operaciones de ejecución prolongada (como el acceso a la red) en otro subproceso y acceder fácilmente a los resultados al finalizar. Las versiones más recientes de Xamarin. iOS y Xamarin. Android admiten Async y Await: este documento proporciona explicaciones y un ejemplo del uso de la nueva sintaxis con Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Características del lenguaje C# 6](~/cross-platform/platform/csharp-six.md)

La versión más reciente del C# lenguaje (versión 6) sigue evolucionando el lenguaje para que tenga menos reutilizable, mayor claridad y coherencia. La sintaxis de inicialización del limpiador, la capacidad de usar `await` en bloques de `catch/finally` y el operador `?` condicional null son especialmente útiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Compilar aplicaciones F# móviles con y Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Basic.NET visual portátil](~/cross-platform/platform/visual-basic/index.md)

Visual Studio admite la creación de bibliotecas de clases portables con Visual Basic.NET que luego se pueden incorporar en aplicaciones de Xamarin. En este artículo se muestra cómo crear un nuevo Visual Basic PCL y usarlo en una aplicación de Xamarin. iOS, Xamarin. Android y Windows Phone de ejemplo.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Compilar vistas HTML mediante plantillas de Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permite a los desarrolladores aprovechar el motor de plantillas Razor, introducido originalmente con ASP.NET MVC, C# junto con para combinar fácilmente datos con HTML, JavaScript y CSS sin las molestias de crear manualmente cadenas HTML en el código.
En este artículo se muestra cómo usar las plantillas de Razor con Xamarin para Android e iOS.
