---
title: API de macOS para desarrolladores de Xamarin. Mac
description: En este documento se describe cómo leer selectores de Objective-C y cómo buscar C# sus métodos correspondientes en una aplicación de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: cd427d13bb79fd31e1e814726aaaf61788ae10ec
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306276"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>API de macOS para desarrolladores de Xamarin. Mac

## <a name="overview"></a>Información general

Para gran parte del tiempo que desarrolla con Xamarin. Mac, puede pensar, leer y escribir C# sin tener que preocuparse por las API de Objective-C subyacentes. Sin embargo, a veces tendrá que leer la documentación de la API de Apple, traducir una respuesta de Stack Overflow a una solución para el problema o comparar con un ejemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Lectura de los objetivos-C suficientes para ser peligrosos

A veces será necesario leer una llamada de método o una definición de Objective-C y convertirla en el C# método equivalente. Echemos un vistazo a la definición de una función de Objective-C y divida las partes. Este método (un *selector* en Objective-C) se puede encontrar en `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La declaración se puede leer de izquierda a derecha:

- El prefijo `-` significa que es un método de instancia (no estático). + significa que es un método de clase (estático)
- `(BOOL)` es el tipo de valor devuelto C#(bool en)
- `canDragRowsWithIndexes` es la primera parte del nombre.
- `(NSIndexSet *)rowIndexes` es el primer parámetro y su tipo. El primer parámetro tiene el formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` es el segundo parámetro y su tipo. Cada parámetro después del primero tiene el formato: `selectorPart:(Type) pararmName`
- El nombre completo de este selector de mensajes es: `canDragRowsWithIndexes:atPoint:`. Tenga en cuenta que el `:` al final es importante.
- El enlace real de Xamarin C# . Mac es: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Esta invocación del selector se puede leer de la misma manera:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- La instancia `v` tiene su selector de `canDragRowsWithIndexes:atPoint` denominado con dos parámetros, `set` y `point`, pasados.
- En C#, la invocación del método tiene el siguiente aspecto: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Buscar el C# miembro de un selector determinado

Ahora que ha encontrado el selector de Objective-C que debe invocar, el paso siguiente consiste en asignarlo al miembro C# equivalente. Hay cuatro métodos que puede probar (continuando con el ejemplo `NSTableView CanDragRows`):

1. Use la lista de finalización automática para buscar algo del mismo nombre. Como sabemos que es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - `x.` [Ctrl + espacio si no aparece la lista).
    - `CanDrag` [entrar]
    - Haga clic con el botón derecho en el método, vaya a declaración para abrir el explorador de ensamblados, donde puede comparar el atributo de `Export` con el selector en cuestión.

2. Busque en el enlace de clase completo. Como sabemos que es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - Haga clic con el botón derecho en `NSTableView`, vaya a declaración al explorador de ensamblados.
    - Buscar el selector en cuestión

3. Puede usar la [documentación en línea de la API de Xamarin. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel proporciona una vista "Rosetta" de las API de Xamarin. Mac [aquí](https://tirania.org/tmp/rosetta.html) donde puede buscar una API determinada. Si la API no es específica de AppKit o macOS, puede encontrarla allí.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
