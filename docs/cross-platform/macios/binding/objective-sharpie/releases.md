---
title: Historial de versiones de Objective Sharpie
description: En este documento se describe el historial de versiones de Objective Sharpie, la herramienta que se usa para C# automatizar la creación de enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: f60be3f7dc14749f5cd58d5228c17fa85282cd78
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725355"
---
# <a name="objective-sharpie-release-history"></a>Historial de versiones de Objective Sharpie

## <a name="34-october-11-2017"></a>3,4 (11 de octubre de 2017)

[Descargar v 3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Compatibilidad con Xcode 9: iOS 11, macOS 10,13, tvOS 11 y watchos 4
* Ahora se deben corregir los problemas con SIMD y tgmath
* La telemetría se ha quitado por completo

## <a name="33-august-3-2016"></a>3,3 (3 de agosto de 2016)

[Descargar v 3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Compatibilidad con Xcode 8 beta 4, iOS 10, macOS 10,12, tvOS 10 y watchos 3.
* Actualizado a la última compilación maestra de Clang (2016-08-02)
* [Mantenga las opciones de envío de telemetría](https://twitter.com/Symbiatch/status/760373403878559744) desde `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 de junio de 2016)

[Descargar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Compatibilidad con Xcode 8 Beta 1, iOS 10, macOS 10,12, tvOS 10 y watchos 3.

## <a name="31-may-31-2016"></a>3,1 (31 de mayo de 2016)

[Descargar v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Compatibilidad con CocoaPods 1,0
* Confiabilidad de enlace de CocoaPods mejorada creando primero un `.framework` nativo y, a continuación, enlazando eso
* Copie el `.framework` de CocoaPods y la definición de enlace en un directorio `Binding` para facilitar la integración con los proyectos de enlace de Xamarin. iOS y Xamarin. Mac

## <a name="30-october-5-2015"></a>3,0 (5 de octubre de 2015)

[Descargar v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Compatibilidad con las nuevas características del lenguaje Objective-C, incluidos los genéricos ligeros y la nulabilidad, tal como se ha introducido en Xcode 7
* Compatibilidad con los SDK de iOS y Mac más recientes.
* Compatibilidad con la creación y el análisis de proyectos de Xcode. Ahora puede pasar un proyecto de Xcode completo a Objective Sharpie y lo hará mejor para averiguar lo que tiene que hacer (por ejemplo, `sharpie bind Project.xcodeproj -sdk ios`).
* Compatibilidad con [CocoaPods](https://cocoapods.org) Ahora puede configurar, compilar y enlazar CocoaPods directamente desde Objective Sharpie (por ejemplo, `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Al enlazar marcos de trabajo, los módulos Clang se habilitarán si el marco los admite, lo que da como resultado el análisis más correcto de un marco, ya que la estructura del marco de trabajo se define mediante su `module.modulemap`.
* En el caso de los proyectos de Xcode que compilan un producto de marco, analice el producto en lugar de los destinos de producto intermedios, ya que los destinos que no son de marco en un proyecto de Xcode todavía pueden tener ambigüedades que no se pueden resolver automáticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de marzo de 2015)

* Enlace de expresión de operador binario fijo: el lado izquierdo de la expresión se intercambió incorrectamente con el lado derecho (por ejemplo, `1 << 0` se enlazaba incorrectamente como `0 << 1`). Gracias a Adam Kemp para observar esto.
* Se corrigió un problema con `NSInteger` y `NSUInteger` que se enlazan como `int` y `uint` en lugar de `nint` y `nuint` en i386; a continuación, `-DNS_BUILD_32_LIKE_64` se pasa a Clang para que el análisis `objc/NSObjCRuntime.h` funcione según lo previsto en i386.
* La arquitectura predeterminada para los SDK de Mac OS X (por ejemplo, `-sdk macosx10.10`) ahora es x86_64 en lugar de i386, por lo que `-arch` se puede omitir a menos que se desee invalidar el valor predeterminado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de marzo de 2015)

[Descargar v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [BXC # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Asegúrese de que `using ObjCRuntime;` se produce cuando se utiliza `ArgumentSemantic`.
* [BXC # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Asegúrese de que `using System.Runtime.InteropServices;` se produce cuando se utiliza `DllImport`.
* [BXC # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): `DllImport` predeterminada para cargar símbolos desde `__Internal`.
* [BXC # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): omitir las declaraciones de contenedor de Objective-C declaradas por el avance.
* [BXC # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): enlazar tipos de protocolo con una sola calificación como interfaces concretas (`id<Foo>` como `Foo` en lugar de `Foundation.NSObject<Foo>`).
* [BXC # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): enlazar `UInt32`, `UInt64`y `Int64` literales como `Int32` para quitar los sufijos `u` y/o `uL` cuando los valores se pueden ajustar de forma segura a `Int32`.
* [BXC # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): se ha corregido la asignación de nombres de enumeración cuando el nombre nativo original comienza con un prefijo `k`.
* `sizeof` expresiones de C cuyo tipo de argumento no se asigna C# a un tipo primitivo se evaluará en Clang y se enlazará como un literal entero para C#evitar la generación de valores no válidos.
* Corrección de la sintaxis de Objective-C para las propiedades cuyo tipo es un bloque (el código de Objective-C aparece en los comentarios sobre las declaraciones enlazadas).
* Enlazar los tipos devueltos como su tipo original (`int[]` decaer en `int*` durante el análisis semántico en Clang, pero enlácelo como el `int[]` original escrito en su lugar).

Gracias mucho a Dave Dunkin por informar de muchos de los errores corregidos en esta versión puntual.

## <a name="200-march-9-2015"></a>2.0.0:9 de marzo de 2015

[Descarga de v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objective Sharpie 2,0 es una versión principal que incluye un controlador y un analizador basados en Clang mejorados y un nuevo motor de enlace basado en NRefactory. Estos componentes mejorados proporcionan _muchos_ mejores enlaces, especialmente en torno al enlace de tipos. Se han realizado muchas otras mejoras que son internas a Sharpie objetivas, que producirán muchas características visibles para el usuario en futuras versiones.

Objective Sharpie 2,0 se basa en Clang 3.6.1.

### <a name="type-binding-improvements"></a>Mejoras en el enlace de tipos

* Ahora se admiten los bloques de Objective-C. Esto incluye los bloques anónimos/inline y los bloques denominados mediante `typedef`. Los bloques anónimos se enlazarán como `System.Action` o delegados de `System.Func`, mientras que los bloques con nombre se enlazarán como tipos de `delegate` con nombre seguro.

* Existe una heurística de nomenclatura mejorada para las enumeraciones anónimas precedidas inmediatamente por un `typedef` resolver a un tipo entero integrado como `long` o `int`.

* Los punteros de C ahora se C# enlazan como punteros de `unsafe` en lugar de `System.IntPtr`. Esto da como resultado una mayor claridad en el enlace para cuando puede querer convertir los parámetros de puntero en `out` o `ref` parámetros. No es posible deducir siempre si un parámetro debe ser `out` o `ref`, por lo que el puntero se conserva en el enlace para permitir una auditoría más sencilla.

* Una excepción al enlace de puntero anterior es cuando se encuentra un puntero de 2 rangos a un objeto de Objective-C como parámetro. En estos casos, la Convención es predominante y el parámetro se enlazará como `out` (por ejemplo, `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Comprobar atributo

A menudo, encontrará que los enlaces generados por Objective Sharpie se anotarán con el atributo `[Verify]`. Estos atributos indican que debe _comprobar_ que Objective Sharpie hizo lo correcto comparando el enlace con la declaración c/Objective-c original (que se proporcionará en un comentario encima de la declaración enlazada).

Se _recomienda_ la comprobación para todas las declaraciones enlazadas, pero lo más probable es que sea _necesario_ para las declaraciones anotadas con el atributo `[Verify]`. Esto se debe a que, en muchas situaciones, no hay suficientes metadatos en el código fuente nativo original para deducir cómo crear mejor un enlace. Es posible que tenga que hacer referencia a documentación o comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Consulte la documentación de [Verify Attributes](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) para obtener más detalles.

### <a name="other-notable-improvements"></a>Otras mejoras importantes

* Ahora, las instrucciones de `using` se generan en función de los tipos enlazados. Por ejemplo, si se enlaza un `NSURL`, también se generará una instrucción de `using Foundation;`.

* ahora se enlazarán las declaraciones de `struct` y `union` con el truco de `[FieldOffset]` para uniones.

* Los valores de enumeración con inicializadores de expresión constantes ahora se enlazarán correctamente; la expresión completa se convierte en C#.

* Ahora se enlazan los métodos y los bloques de variádicas.

* Ahora se admiten marcos de trabajo a través de la opción `-framework`. Para más información, consulte la documentación sobre el [enlace de marcos nativos](~/cross-platform/macios/binding/objective-sharpie/index.md) .

* El código fuente de Objective-C se detectará automáticamente ahora, que debe eliminar la necesidad de pasar `-ObjC` o `-xobjective-c` a Clang manualmente.

* El uso del módulo Clang (`@import`) ahora se detecta automáticamente, lo que debe eliminar la necesidad de pasar `-fmodules` a Clang manualmente para las bibliotecas que usan la nueva compatibilidad de módulo en Clang.

* Xamarin Unified API es ahora el destino de enlace predeterminado; Use la opción `-classic` para establecer como destino el Classic API solo de 32 bits.

### <a name="notable-bug-fixes"></a>Correcciones de errores importantes

* Corrección de `instancetype` enlace cuando se usa en una categoría de Objective-C
* Nombres completos de las categorías de Objective-C
* Prefijo de los protocolos Objective-C con `I` (por ejemplo, `INSCopying` en lugar de `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35:21 de diciembre de 2014

[Descargar v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correcciones de errores leves.

## <a name="111-december-15-2014"></a>1.1.1:15 de diciembre de 2014

[Descarga v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 fue la primera versión principal después de 1,5 años de uso interno y desarrollo en Xamarin después de la versión preliminar inicial de Objective Sharpie en abril de 2013. Esta versión es la primera que se considera generalmente estable y utilizable para una amplia variedad de bibliotecas nativas, que incluye un nuevo back-end de Clang.
