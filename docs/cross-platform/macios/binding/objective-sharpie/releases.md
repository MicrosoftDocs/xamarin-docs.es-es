---
title: Historial de versiones de Objective Sharpie
description: En este documento se describe el historial de versiones de Objective Sharpie, la herramienta que se usa para C# automatizar la creación de enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: b5362c0a809423e2782ee60faa96658cf132d752
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290855"
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
* [Conserva las opciones de envío de telemetría](https://twitter.com/Symbiatch/status/760373403878559744) de `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3,2 (14 de junio de 2016)

[Descargar v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Compatibilidad con Xcode 8 Beta 1, iOS 10, macOS 10,12, tvOS 10 y watchos 3.

## <a name="31-may-31-2016"></a>3,1 (31 de mayo de 2016)

[Descargar v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Compatibilidad con CocoaPods 1,0
* Confiabilidad de enlace de CocoaPods mejorada al compilar primero un nativo `.framework` y luego enlazar eso
* Copiar CocoaPods `.framework` y la definición de enlace `Binding` en un directorio para facilitar la integración con los proyectos de enlace de Xamarin. iOS y Xamarin. Mac

## <a name="30-october-5-2015"></a>3,0 (5 de octubre de 2015)

[Descargar v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Compatibilidad con las nuevas características del lenguaje Objective-C, incluidos los genéricos ligeros y la nulabilidad, tal como se ha introducido en Xcode 7
* Compatibilidad con los SDK de iOS y Mac más recientes.
* Compatibilidad con la creación y el análisis de proyectos de Xcode. Ahora puede pasar un proyecto de Xcode completo a Objective Sharpie, lo que hará lo mejor para averiguar lo que hay que hacer (por ejemplo `sharpie bind Project.xcodeproj -sdk ios`,).
* Compatibilidad con [CocoaPods](https://cocoapods.org) Ahora puede configurar, compilar y enlazar CocoaPods directamente desde Objective Sharpie (por `sharpie pod init ios AFNetworking && sharpie pod bind`ejemplo,).
* Al enlazar marcos de trabajo, los módulos Clang se habilitarán si el marco los admite, lo que da como resultado el análisis más correcto de un marco, ya que la estructura `module.modulemap`del marco de trabajo se define por su.
* En el caso de los proyectos de Xcode que compilan un producto de marco, analice el producto en lugar de los destinos de producto intermedios, ya que los destinos que no son de marco en un proyecto de Xcode todavía pueden tener ambigüedades que no se pueden resolver automáticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 de marzo de 2015)

[Descargar v 2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Enlace de expresión de operador binario fijo: el lado izquierdo de la expresión se intercambió incorrectamente con el lado derecho (por ejemplo `1 << 0` , se enlazaba incorrectamente como `0 << 1`). Gracias a Adam Kemp para observar esto.
* Se ha corregido `NSInteger` un `NSUInteger` problema con y `int` se ha enlazado `nint` como `nuint` y `uint` en lugar de y en i386; ahora se pasa a Clang para que el `objc/NSObjCRuntime.h` análisis funcione según lo esperado en i386. `-DNS_BUILD_32_LIKE_64`
* La arquitectura predeterminada para los SDK de Mac OS X ( `-sdk macosx10.10`por ejemplo,) ahora es x86_64 en lugar de `-arch` i386, por lo que se puede omitir, a menos que se desee invalidar el valor predeterminado.

## <a name="210-march-15-2015"></a>2.1.0 (15 de marzo de 2015)

[Descargar v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Asegúrese `using ObjCRuntime;` de que se `ArgumentSemantic` produce cuando se usa.
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Asegúrese `using System.Runtime.InteropServices;` de que se `DllImport` produce cuando se usa.
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): De `DllImport` forma predeterminada, se `__Internal`cargan símbolos desde.
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Omitir las declaraciones de contenedor de Objective-C declaradas por el avance.
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Enlazar tipos de protocolo con una sola calificación como interfaces`id<Foo>` concretas `Foo` (como en `Foundation.NSObject<Foo>`lugar de).
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): `UInt32`Enlazar `UInt64`los `Int32` `uL` literales `Int64` `u` , y para quitar los sufijos y/o cuando los valores se pueden ajustar de forma segura. `Int32`
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Corrija la asignación del nombre de enumeración cuando el nombre `k` nativo original comienza con un prefijo.
* `sizeof`Las expresiones de C cuyo tipo de argumento no se C# asigna a un tipo primitivo se evaluarán en Clang y se enlazarán como un literal entero C#para evitar la generación de valores no válidos.
* Corrección de la sintaxis de Objective-C para las propiedades cuyo tipo es un bloque (el código de Objective-C aparece en los comentarios sobre las declaraciones enlazadas).
* Enlazar los tipos devueltos como su`int[]` tipo original ( `int*` decaer en el análisis semántico en Clang, pero enlácelo como el original como `int[]` escrito).

Gracias mucho a Dave Dunkin por informar de muchos de los errores corregidos en esta versión puntual.

## <a name="200-march-9-2015"></a>2.0.0: 9 de marzo de 2015

[Descarga de v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objective Sharpie 2,0 es una versión principal que incluye un controlador y un analizador basados en Clang mejorados y un nuevo motor de enlace basado en NRefactory. Estos componentes mejorados proporcionan _muchos_ mejores enlaces, especialmente en torno al enlace de tipos. Se han realizado muchas otras mejoras que son internas a Sharpie objetivas, que producirán muchas características visibles para el usuario en futuras versiones.

Objective Sharpie 2,0 se basa en Clang 3.6.1.

### <a name="type-binding-improvements"></a>Mejoras en el enlace de tipos

* Ahora se admiten los bloques de Objective-C. Esto incluye los bloques anónimos/inline y los `typedef`bloques denominados mediante. Los bloques anónimos se enlazarán `System.Func` como `System.Action` delegados o, mientras que los bloques con `delegate` nombre se enlazarán como tipos con nombre seguro.

* Existe una heurística de nomenclatura mejorada para enumeraciones anónimas precedidas inmediatamente por una `typedef` resolución a un tipo entero integrado `long` como o `int`.

* Los punteros de C ahora se C# `unsafe` enlazan como punteros `System.IntPtr`en lugar de. Esto da como resultado más claridad en el enlace para cuando es posible que desee convertir los parámetros `out` de `ref` puntero en parámetros o. No es posible deducir siempre si un parámetro debe ser `out` o `ref`, por lo que el puntero se conserva en el enlace para permitir una auditoría más sencilla.

* Una excepción al enlace de puntero anterior es cuando se encuentra un puntero de 2 rangos a un objeto de Objective-C como parámetro. En estos casos, la Convención es predominante y el parámetro se enlazará como `out` ( `NSError **error` por ejemplo, `out NSError error`→).

### <a name="verify-attribute"></a>Comprobar atributo

A menudo, encontrará que los enlaces generados por Objective Sharpie se anotarán con el `[Verify]` atributo. Estos atributos indican que debe _comprobar_ que Objective Sharpie hizo lo correcto comparando el enlace con la declaración c/Objective-c original (que se proporcionará en un comentario encima de la declaración enlazada).

Se _recomienda_ la comprobación para todas las declaraciones enlazadas, pero lo más probable es que sea _necesario_ para las `[Verify]` declaraciones anotadas con el atributo. Esto se debe a que, en muchas situaciones, no hay suficientes metadatos en el código fuente nativo original para deducir cómo crear mejor un enlace. Es posible que tenga que hacer referencia a documentación o comentarios de código dentro de los archivos de encabezado para tomar la mejor decisión de enlace.

Consulte la documentación de [Verify Attributes](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) para obtener más detalles.

### <a name="other-notable-improvements"></a>Otras mejoras importantes

* `using`Ahora, las instrucciones se generan en función de los tipos enlazados. Por ejemplo, si se `NSURL` enlaza un, también `using Foundation;` se generará una instrucción.

* `struct`ahora `union` se enlazarán las declaraciones y usando el truco `[FieldOffset]` para las uniones.

* Los valores de enumeración con inicializadores de expresión constantes ahora se enlazarán correctamente; la expresión completa se convierte en C#.

* Ahora se enlazan los métodos y los bloques de variádicas.

* Los marcos de trabajo ahora se admiten a través de la `-framework` opción. Para más información, consulte la documentación sobre el [enlace de marcos nativos](~/cross-platform/macios/binding/objective-sharpie/index.md) .

* El código fuente de Objective-C se detectará automáticamente ahora, lo que debe eliminar la necesidad `-ObjC` de `-xobjective-c` pasar o Clang manualmente.

* El uso del módulo`@import`Clang () ahora se detecta automáticamente, lo que debe eliminar la necesidad `-fmodules` de pasar a Clang manualmente para las bibliotecas que usan la nueva compatibilidad de módulo en Clang.

* Xamarin Unified API es ahora el destino de enlace predeterminado; Use la `-classic` opción para destinar el Classic API solo de 32 bits.

### <a name="notable-bug-fixes"></a>Correcciones de errores importantes

* Corregir `instancetype` el enlace cuando se usa en una categoría de Objective-C
* Nombres completos de las categorías de Objective-C
* Prefijo de los protocolos Objective `I` -C con `INSCopying` (por ejemplo `NSCopying`, en lugar de)

## <a name="1135-december-21-2014"></a>1.1.35: 21 de diciembre de 2014

[Descargar v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correcciones de errores menores.

## <a name="111-december-15-2014"></a>1.1.1: 15 de diciembre de 2014

[Descarga v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 fue la primera versión principal después de 1,5 años de uso interno y desarrollo en Xamarin después de la versión preliminar inicial de Objective Sharpie en abril de 2013. Esta versión es la primera que se considera generalmente estable y utilizable para una amplia variedad de bibliotecas nativas, que incluye un nuevo back-end de Clang.

