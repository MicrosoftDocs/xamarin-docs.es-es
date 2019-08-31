---
title: Tipos nativos para iOS y macOS
description: En este documento se describe cómo Unified API de Xamarin asigna tipos .NET a tipos nativos de 32 bits y 64 bits, según sea necesario en función de la arquitectura de destino de la compilación.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 6b6a7b8bb2efdd099d7fde6bb5397e6330258bcc
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199393"
---
# <a name="native-types-for-ios-and-macos"></a>Tipos nativos para iOS y macOS

Las API de Mac e iOS usan tipos de datos específicos de la arquitectura que siempre son de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo, Objective-C asigna el `NSInteger` tipo de datos `int32_t` a en sistemas de 32 bits y `int64_t` a en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, vamos a reemplazar los `int` usos anteriores de (que en .net se definen `System.Int32`como siempre) a un nuevo tipo `System.nint`de datos:. Puede pensar en "n" como "Native", por lo que el tipo de entero nativo de la plataforma.

Con estos nuevos tipos de datos, el mismo código fuente se compila para arquitecturas de 32 bits y de 64 bits, en función de las marcas de compilación.

## <a name="new-data-types"></a>Nuevos tipos de datos

En la tabla siguiente se muestran los cambios en los tipos de datos para que coincidan con este nuevo mundo de 32 y 64 bits:

|Tipo nativo|tipo de respaldo de 32 bits|tipo de respaldo de 64 bits|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Hemos elegido esos nombres para permitir que C# el código tenga un aspecto más o menos similar al que tendría hoy.

### <a name="implicit-and-explicit-conversions"></a>Conversiones implícitas y explícitas

El diseño de los nuevos tipos de datos está diseñado para permitir que C# un único archivo de código fuente use naturalmente el almacenamiento 32 o 64 bits en función de la plataforma del host y la configuración de compilación.

Esto nos necesitaba diseñar un conjunto de conversiones implícitas y explícitas a y desde los tipos de datos específicos de la plataforma a los tipos de datos enteros y de punto flotante de .NET.

Se proporcionan operadores de conversiones implícitas cuando no es posible la pérdida de datos (los valores de bit de 32 se almacenan en un espacio de 64 bits).

Se proporcionan operadores de conversiones explícitas cuando existe una posibilidad de pérdida de datos (el valor de bit 64 se almacena en una ubicación de almacenamiento 32 o potencialmente 32).

`int``nint` `nuint` y se pueden convertir implícitamente en, y `nfloat` como 32 bits siempre caben en los bits 32 o 64. `float` `uint`

`nint``ulong` `double` y se pueden convertir`long`implícitamente en, y los valores de bit 32 o 64 siempre caben en el almacenamiento de 64 bits. `nfloat` `nuint`

`nint`Debe usar conversiones explícitas de, `nuint` y `nfloat` en `int`, `uint` y `float` dado que los tipos nativos pueden contener 64 bits de almacenamiento.

`long`Debe usar conversiones explícitas de, `ulong` y `double` en `nint`, `nuint` y `nfloat` dado que los tipos nativos solo pueden contener 32 bits de almacenamiento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Los tipos de datos de punto, tamaño y rectángulo que se usan con CoreGraphics usan 32 o 64 bits en función del dispositivo en el que se ejecutan.  Cuando enlazamos originalmente las API de iOS y Mac, usamos estructuras de datos existentes que se encontraban con los tamaños de la plataforma de host `System.Drawing`(los tipos de datos de).

Al pasar a **Unified**, deberá reemplazar las instancias de `System.Drawing` por sus `CoreGraphics` homólogos, tal y como se muestra en la tabla siguiente:

|Tipo antiguo en System. Drawing|Nuevo tipo de datos CoreGraphics|DESCRIPCIÓN|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Contiene información sobre los rectángulos de punto flotante.|
|`SizeF`|`CGSize`|Contiene información de tamaño de punto flotante (ancho, alto)|
|`PointF`|`CGPoint`|Contiene un punto flotante, información de punto (X, Y)|

Los tipos de datos antiguos utilizados flotan para almacenar los elementos de las estructuras de datos, mientras que el `System.nfloat`nuevo utiliza.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
