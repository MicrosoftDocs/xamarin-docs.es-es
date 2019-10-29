---
title: Crear enlaces con Sharpie objetivo
description: En esta sección se proporciona una introducción a Objective Sharpie, la herramienta de línea de comandos de Xamarin que se usa para automatizar el proceso de creación de un enlace a una biblioteca de Objective-C.
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 4d6ab6cf48c5c365a4d8d05ef108a4d3a5d16134
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016190"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Crear enlaces con Sharpie objetivo

_En esta sección se proporciona una introducción a Objective Sharpie, la herramienta de línea de comandos de Xamarin que se usa para automatizar el proceso de creación de un enlace a una biblioteca de Objective-C._

- [Información general](#overview) & [historial](#history)
- [Introducción](get-started.md)
- [Herramientas y comandos](tools.md)
- [Características](platform/index.md)
- [Ejemplos](examples/index.md)
- [Tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md)
- [Historial de versiones](releases.md)

## <a name="overview"></a>Información general

Objective Sharpie es una herramienta de línea de comandos que ayuda a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un proceso que previamente se hizo manualmente).

Objective Sharpie usa archivos de encabezado de análisis de Clang, por lo que el enlace es lo más exacto y completo posible. Esto puede reducir en gran medida el tiempo y el esfuerzo que se tarda en generar un enlace de calidad.

> [!IMPORTANT]
> Objective Sharpie es una herramienta para desarrolladores de Xamarin experimentados con conocimiento avanzado de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca de Objective-C, debe tener un conocimiento sólido de cómo crear la biblioteca nativa en la línea de comandos (y una buena comprensión de cómo funciona la biblioteca nativa).

## <a name="history"></a>Historial

Hemos estado evolucionando y usando el objetivo Sharpie internamente en Xamarin durante los últimos tres años. Como testimonio de la eficacia de Sharpie de objetivos, las API introducidas en Xamarin. iOS y Xamarin. Mac desde iOS 8, Mac OS X 10,10 y watchos 2,0 se han arrancado por completo con el objetivo Sharpie. Xamarin se basa en gran medida en el objetivo Sharpie internamente para crear sus propios productos.

Sin embargo, Objective Sharpie es una herramienta muy avanzada que requiere un conocimiento avanzado de Objective-C y C, cómo usar el compilador Clang en la línea de comandos y, en general, cómo se colocan las bibliotecas nativas. Debido a esta alta barra, pensamos que tener un asistente de GUI establece las expectativas equivocadas y, por tanto, el objetivo Sharpie actualmente solo está disponible como herramienta de línea de comandos.

## <a name="related-links"></a>Vínculos relacionados

- [Descarga de Sharpie de objetivos](https://aka.ms/objective-sharpie)
- [Tutorial: enlazar una biblioteca de Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
