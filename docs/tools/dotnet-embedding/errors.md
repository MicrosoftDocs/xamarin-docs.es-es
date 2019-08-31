---
title: Errores de inserción de .NET
description: En este documento se describen los errores generados por la inserción de .NET. Los errores se enumeran por código y se proporciona una descripción para ayudarle a solucionar problemas.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: lobrien
ms.author: laobri
ms.date: 04/11/2018
ms.openlocfilehash: e5f998105202a4143c94748efad5faae0212acba
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198780"
---
# <a name="net-embedding-errors"></a>Errores de inserción de .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: Mensajes de error de enlace

Por ejemplo, parámetros, entorno

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Error inesperado. Rellene un informe de errores en https://github.com/mono/Embeddinator-4000/issues

Se produjo una condición de error inesperada. Registre [un problema](https://github.com/mono/Embeddinator-4000/issues) con tanta información como sea posible, incluidos:

* Registros de compilación completos, con máximo nivel de detalle
* Un caso de prueba mínimo que reproduce el error
* Toda la información de versión

La forma más fácil de obtener información exacta sobre la versión es usar el menú de **Xamarin Studio** , **acerca de Xamarin Studio** elemento, mostrar el botón **detalles** y copiar y pegar la información de versión (puede usar el botón **copiar información** ).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: No se pudo crear el directorio de salida`X`

El nombre de directorio especificado `-o=DIR` por no existe y no se pudo crear. Podría ser un nombre no válido para el sistema de archivos.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: No `X` se admite la opción

La herramienta no admite la opción `X`. Es posible que otra versión de la herramienta sea compatible o que no se aplique en este entorno.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: La plataforma `X` no es válida.

La herramienta no admite la plataforma `X`. Es posible que otra versión de la herramienta sea compatible o que no se aplique en este entorno.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: El destino `X` no es válido.

La herramienta no admite el destino `X`. Es posible que otra versión de la herramienta sea compatible o que no se aplique en este entorno.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: El destino `X` de compilación no es válido.

La herramienta no admite el destino `X`de compilación. Es posible que otra versión de la herramienta sea compatible o que no se aplique en este entorno.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: No se encontró la ubicación de Xcode.

La herramienta no encontró la ubicación de Xcode seleccionada actualmente mediante el `xcode-select -p` comando. Compruebe que este comando se ejecuta correctamente y devuelve la ubicación de Xcode correcta.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: No se pudo obtener la versión del SDK para ' {SDK} '.

La herramienta no pudo obtener la versión del SDK con `xcrun --show-sdk-version --sdk {sdk}` el comando. Compruebe que este comando se ejecuta correctamente y devuelve la versión del SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: La arquitectura ' {Arch} ' no es válida para {Platform}. Las arquitecturas válidas para {Platform} son: ' {Architectures} '.

La arquitectura del mensaje de error no es válida para la plataforma de destino. Compruebe que se ha pasado a la opción--Abi una arquitectura válida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La característica `X` no está implementada actualmente por el generador

Se trata de un problema conocido que pretendemos corregir en una versión futura del generador. Las contribuciones son bienvenidos.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: No se pueden combinar los marcos ' {simulatorFramework} ' y ' {deviceFramework} ' porque el archivo ' {file} ' existe en ambos.

La herramienta no pudo fusionar mediante combinación los marcos de trabajo mencionados en el mensaje de error porque hay un archivo común entre ellos.

Esto podría indicar un error en la inserción de .NET; Registre un informe de errores en [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: El ensamblado `X` no existe.

La herramienta no pudo encontrar el ensamblado `X` especificado en los argumentos.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: El nombre `X` de ensamblado no es único

Más de un ensamblado proporcionado tienen el mismo nombre interno y no sería posible distinguirlos en tiempo de ejecución.

La causa más probable es que un ensamblado se especifique más de una vez en los argumentos de la línea de comandos. Sin embargo, un ensamblado con el nombre cambiado todavía mantiene su nombre original y no se pueden coexistir varias copias.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: No se encuentra el ensamblado ' X ', al que hace referencia ' Y '.

La herramienta no pudo encontrar el ensamblado ' X ', al que hace referencia el ensamblado ' Y '. Asegúrese de que todos los ensamblados a los que se hace referencia están en el mismo directorio que el ensamblado que se va a enlazar.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014: No se pudo encontrar {Product} (se requiere {Product} {min_version}).

No se encontró la dependencia mencionada en el mensaje de error en el sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015: No se pudo encontrar una versión válida de {Product} (se encontró {version}, pero se requiere al menos {min_version}).

La dependencia mencionada en el mensaje de error se encontró en el sistema, pero es demasiado antigua. Actualice a una versión más reciente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: No se pudo crear el symlink ' {file} '-> ' {target} ': error {Number}

No se pudo crear el symlink mencionado en el mensaje de error.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 no pudo analizar el argumento de la línea de comandos ' A ': *

La herramienta no pudo analizar la sintaxis proporcionada `A` para la opción de línea de comandos. Es probable que sea incorrecto. Consulte la documentación o la ayuda para obtener la sintaxis correcta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Error interno *. Registre un informe de errores con un caso de prueba https://github.com/mono/Embeddinator-4000/issues) (.

Este mensaje de error se indica cuando se produce un error en una comprobación de coherencia interna en la incrustación de .NET.

Esto indica un error en la inserción de .NET; Registre un informe de errores en [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un caso de prueba.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Procesamiento de código

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: No `T` se genera el tipo `X` porque no se admiten.

Esta es una **ADVERTENCIA** de que el `T` tipo se omitirá (es decir, no se generará nada) `X`porque usa, una característica que no se admite.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: No `T` se genera el tipo porque no tiene código de serialización con un homólogo nativo.

Se trata de una **ADVERTENCIA** que indica `T` que el tipo se omitirá (es decir, no se generará nada) porque expone algo de .NET Framework que requiere serialización adicional.

Nota: Esto es algo que podría ser compatible, con algunas limitaciones, en una versión futura de la herramienta.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: No `C` se genera el constructor porque no se `T` admite el tipo de parámetro.

Se trata de una **ADVERTENCIA** que indica `C` que el constructor se omitirá (es decir, no se generará nada) `T` porque no se admite un parámetro de tipo.

Debe haber una advertencia anterior que proporcione más información sobre por `T` qué no se admite el tipo.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: El `C` constructor tiene valores predeterminados para los que no se genera ningún contenedor.

Esta es una **ADVERTENCIA** de que los parámetros predeterminados del constructor `C` no generan ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. Por ejemplo, en .net es posible tener:

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

En tales casos, se crearán solo dos selectores generados `init` , ambos llamarán a mono, pero no existirá ningún contenedor para el más adelante.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: El `M` método no se genera porque no `T` se admite el tipo de valor devuelto.

Esta es una **ADVERTENCIA** de que el `M` método se omitirá (es decir, no se generará nada) porque no `T` se admite el tipo de valor devuelto.

Debe haber una advertencia anterior que proporcione más información sobre por `T` qué no se admite el tipo.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: No `M` se ha generado el método porque no `T` se admite el tipo de parámetro.

Esta es una **ADVERTENCIA** de que el `M` método se omitirá (es decir, no se generará nada) porque no `T` se admite un parámetro de tipo.

Debe haber una advertencia anterior que proporcione más información sobre por `T` qué no se admite el tipo.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: El `M` método tiene valores predeterminados para los que no se genera ningún contenedor.

Esta es una **ADVERTENCIA** de que los parámetros predeterminados del método `M` no generan ningún código adicional. La causa más común es que un método existente ya tiene la misma firma. Por ejemplo, en .net es posible tener:

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

En tales casos, se crearán solo dos selectores generados `increment` , ambos llamarán a mono, pero no existirá ningún contenedor para el más adelante.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: El `M` método no se genera porque otro método expone el operador con un nombre descriptivo.

Esta es una **ADVERTENCIA** de que el `M` método no se genera porque otro método expone el operador con un nombre descriptivo. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: El método `M` de extensión no se genera en una categoría porque no se pueden crear en `T`un tipo primitivo. Se generó un método estático normal.

Se trata de una **ADVERTENCIA** que indica que se encontró un método de extensión en `System.Int32`un tipo primivite (por ejemplo,). En Objective-C no es posible crear categorías en el tipo primitivo. En su lugar, el generador generará un método estático normal.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: No `P` se ha generado la propiedad porque no `T` se admite el tipo de parámetro.

Esta es una **ADVERTENCIA** de que se `P` omitirá la propiedad (es decir, no se generará nada) porque `T` no se admite el tipo expuesto.

Debe haber una advertencia anterior que proporcione más información sobre por `T` qué no se admite el tipo.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: No se generan `T` propiedades indizadas en porque no se admiten varias propiedades indizadas.

Esta es una **ADVERTENCIA** de que las propiedades indizadas en `T` se omitirán (es decir, no se generará nada) porque no se admiten varias propiedades indizadas.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: El `F` campo no se genera porque no se `T` admite el tipo de campo.

Esta es una **ADVERTENCIA** de que el `F` campo se omitirá (es decir, no se generará nada) porque `T` no se admite el tipo expuesto.

Debe haber una advertencia anterior que proporcione más información sobre por `T` qué no se admite el tipo.

Nota: Las características admitidas evolucionarán con las nuevas versiones de la herramienta.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Se genera en su lugar como `F` porque su nombre entra en conflicto con un selector de Objective-c importante. `E`

Esta es una **ADVERTENCIA** de que el `E` elemento se generará en su `F` lugar como porque su nombre entra en conflicto con un selector de Objective-c importante.

Los selectores de [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) tienen un significado importante en Objective-c y deben invalidarse con cuidado.

Nota: La lista de selectores reservados evolucionará con las nuevas versiones de la herramienta.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: El `E` nombre del elemento no se genera entra en conflicto con otros elementos de la misma clase.

Se trata de una **ADVERTENCIA** que `E` indica que el elemento no se genera porque su nombre entra en conflicto con otros elementos de la misma clase.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: El `E` destino no es compatible con Xamarin. iOS y Xamarin. Mac. Solo se `framework` considera que la opción se admite y se debe usar.

Se trata de una **ADVERTENCIA** que `E` indica que el destino se considera no compatible con los casos de uso de Xamarin. iOS y Xamarin. Mac. 

El uso de bibliotecas de incrustación de .NET estáticas o dinámicas puede requerir pasos de trabajo o ajustes adicionales, y debe evitarse en la mayoría de los casos de uso.

Considere la posibilidad `--target` de quitar el `--target=framework` parámetro o pasarlo en su lugar.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Generación de código

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
