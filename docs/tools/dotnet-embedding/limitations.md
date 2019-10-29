---
title: Limitaciones de la inserción de .NET
description: En este documento se describen las limitaciones de la incrustación de .NET, la herramienta que permite consumir código .NET en otros lenguajes de programación.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 4e2b653365a747b30016a1fbd42b8a01c4c87848
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029743"
---
# <a name="net-embedding-limitations"></a>Limitaciones de la inserción de .NET

En este documento se explican las limitaciones de la inserción de .NET y, siempre que sea posible, se proporcionan soluciones para ellos.

## <a name="general"></a>General

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar más de una biblioteca incrustada en un proyecto

No es posible tener dos entornos de ejecución mono coexistentes dentro de la misma aplicación. Esto significa que no puede usar dos bibliotecas generadas por la incrustación .NET diferentes dentro de la misma aplicación.

**Solución alternativa:** Puede usar el generador para crear una biblioteca única que incluya varios ensamblados (de proyectos diferentes).

### <a name="subclassing"></a>Subclases

La inserción de .NET facilita la integración del entorno de ejecución de mono dentro de las aplicaciones al exponer un conjunto de API listas para usar para el lenguaje de destino y la plataforma.

Sin embargo, esto no es una integración bidireccional; por ejemplo, no se puede crear subclases de un tipo administrado y esperar que el código administrado vuelva a llamar dentro del código nativo, ya que el código administrado no es consciente de esta coexistencia.

En función de sus necesidades, es posible que se puedan solucionar las partes de esta limitación; por ejemplo,

* el código administrado puede ser p/Invoke en el código nativo. Esto requiere la personalización del código administrado para permitir la personalización desde código nativo.

* Use productos como Xamarin. iOS y exponga una biblioteca administrada que permita a Objective-C (en este caso) subclase de algunas subclases administradas de NSObject.

## <a name="objective-c-generated-code"></a>Código generado por Objective-C

### <a name="nullability"></a>Nulabilidad

No hay metadatos en .NET que nos indiquen si una referencia nula es aceptable o no para una API. La mayoría de las API iniciarán `ArgumentNullException` si no pueden hacer frente a un argumento `null`. Esto es problemático, ya que la administración de las excepciones de Objective-C es algo mejor que se evita.

Dado que no se pueden generar anotaciones de nulabilidad precisas en los archivos de encabezado y desea minimizar las excepciones administradas, se usan argumentos que no son NULL (`NS_ASSUME_NONNULL_BEGIN`) y se agregan algunos específicos, cuando es posible la precisión, las anotaciones de nulabilidad.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Actualmente, la incrustación de .NET no es compatible con Bitcode en iOS, que está habilitado para algunas plantillas de proyecto de Xcode. Tendrá que deshabilitarse para vincular correctamente los marcos generados.

![Opción Bitcode](images/ios-bitcode-option.png)
