---
title: Atributos de comprobación de Sharpie de objetivos
description: En este documento se describe el atributo [Verify] generado por Objective Sharpie. El atributo [Verify] se resalta a los desarrolladores en los que deben comprobar manualmente la salida del Sharpie de objetivos.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: d951952103a94dfc60a8083a75998611b635cda9
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940787"
---
# <a name="objective-sharpie-verify-attributes"></a>Atributos de comprobación de Sharpie de objetivos

A menudo, encontrará que los enlaces generados por Objective Sharpie se anotarán con el atributo `[Verify]`. Estos atributos indican que debe _comprobar_ que Objective Sharpie hizo lo correcto comparando el enlace con la declaración c/Objective-c original (que se proporcionará en un comentario encima de la declaración enlazada).

Se recomienda la comprobación para _todas las_ declaraciones enlazadas, pero lo más probable es que sea _necesario_ para las declaraciones anotadas con el atributo `[Verify]`. Esto se debe a que, en muchas situaciones, no hay suficientes metadatos en el código fuente nativo original para deducir cómo crear mejor un enlace. Es posible que tenga que hacer referencia a documentación o comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Una vez que haya comprobado que el enlace es correcto o lo ha corregido para que sea correcto, _Quite_ el atributo `[Verify]` del enlace.

> [!IMPORTANT]
> `[Verify]` atributos causan C# intencionadamente errores de compilación para que se le obligue a comprobar el enlace. Debe quitar el atributo `[Verify]` cuando haya revisado (y, posiblemente, corregido) el código.

## <a name="verify-hints-reference"></a>Referencia de sugerencias de comprobación

Se puede hacer referencia cruzada al argumento de sugerencia proporcionado al atributo con la documentación siguiente. La documentación de todos los atributos de `[Verify]` producidos se proporcionará en la consola de y después de que se haya completado el enlace.

|`[Verify]` sugerencia|Descripción|
|---|---|
|InferredFromPreceedingTypedef|El nombre de esta declaración fue deducido por la Convención común del `typedef` inmediatamente anterior en el código fuente nativo original. Compruebe que el nombre deducido sea correcto, ya que esta Convención es ambigua.|
|ConstantsInterfaceAssociation|No hay ningún método de prueba de engaño para determinar con qué interfaz de Objective-C se puede asociar una declaración de variable extern. Las instancias de estas se enlazan como `[Field]` propiedades en una interfaz parcial en una interfaz concreta cercana para generar una API más intuitiva y, posiblemente, eliminar la interfaz ' constantes ' por completo.|
|MethodToProperty|Un método de Objective-C se ha enlazado como una propiedad debido a la C# Convención, como no tomar ningún parámetro y devolver un valor (devolución no nula). A menudo, métodos como estos se deben enlazar como propiedades para exponer una API más agradable, pero a veces pueden producirse falsos positivos y el enlace realmente debe ser un método.|
|StronglyTypedNSArray|Un `NSArray*` nativo se ha enlazado como `NSObject[]`. Es posible que sea más seguro escribir la matriz en el enlace según las expectativas establecidas a través de la documentación de la API (por ejemplo, comentarios en el archivo de encabezado) o examinando el contenido de la matriz mediante las pruebas. Por ejemplo, un NSArray * que contiene solo NSNumber * instancescan se debe enlazar como `NSNumber[]` en lugar de `NSObject[]`.|

También puede recibir rápidamente la documentación de una sugerencia mediante la herramienta de `sharpie verify-docs`, por ejemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
