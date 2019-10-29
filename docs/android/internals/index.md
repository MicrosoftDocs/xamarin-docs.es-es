---
title: Conceptos avanzados y funcionamiento interno
description: Arquitectura subyacente detrás de Xamarin. Android y su diseño de API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/21/2018
ms.openlocfilehash: 97382243ac5f767d94a782b895401c1f2f8ae554
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027855"
---
# <a name="advanced-concepts-and-internals"></a>Conceptos avanzados y funcionamiento interno

_Esta sección contiene temas que explican la arquitectura, el diseño de API y las limitaciones de Xamarin. Android. Además, incluye temas que explican la implementación de la recolección de elementos no utilizados y los ensamblados que están disponibles en Xamarin. Android. Como Xamarin. Android es de [código abierto](https://github.com/xamarin/xamarin-android), también es posible entender el funcionamiento interno de Xamarin. Android examinando su código fuente._

## <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitectura](~/android/internals/architecture.md)

En este artículo se explica la arquitectura subyacente subyacente a una aplicación de Xamarin. Android. Explica cómo se ejecutan las aplicaciones de Xamarin. Android dentro de un entorno de ejecución mono junto con la máquina virtual en tiempo de ejecución de Android y explica los conceptos clave como contenedores a los que se puede llamar de Android y contenedores a los que se puede llamar administrados. 

## <a name="api-designandroidinternalsapi-designmd"></a>[Diseño de API](~/android/internals/api-design.md)

Además de las bibliotecas de clases base básicas que forman parte de mono, Xamarin. Android incluye enlaces para varias API de Android que permiten a los desarrolladores crear aplicaciones nativas de Android con mono.

En el núcleo de Xamarin. Android hay un motor de interoperabilidad que C# une el mundo con el mundo de Java y ofrece a los desarrolladores acceso a C# las API de Java desde u otros lenguajes de .net.

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Ensamblados](~/cross-platform/internals/available-assemblies.md)

Xamarin. Android se incluye con varios ensamblados. Del mismo modo que Silverlight es un subconjunto extendido de los ensamblados .NET de escritorio, Xamarin. Android es también un subconjunto extendido de varios ensamblados .NET de escritorio y Silverlight. 
