---
title: Errores de Xamarin. iOS
description: En este documento se describen los distintos errores generados por Mtouch, la herramienta que se usa para agrupar las aplicaciones de Xamarin. iOS. Los errores se muestran por código y se proporciona una descripción completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 870765a7a32874dfa17a9b0cf7176e4a721abf47
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985954"
---
# <a name="xamarinios-errors"></a>Errores de Xamarin. iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: Mtouch mensajes de error

Por ejemplo, parámetros, entorno, herramientas que faltan.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
  -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: Error inesperado. Rellene un informe de errores en https://github.com/xamarin/xamarin-macios/issues/new

Se produjo una condición de error inesperada. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con tanta información como sea posible, entre las que se incluyen:

- Registros de compilación completos, con el máximo nivel `-v -v -v -v` de detalle (por ejemplo, en los **argumentos Mtouch adicionales**);
- Un caso de prueba mínimo que reproduce el error; etc
- Toda la información de versión

La forma más fácil de obtener información exacta sobre la versión es usar el menú de **Visual Studio para Mac** , **acerca de Visual Studio para Mac** elemento, mostrar el botón **detalles** y copiar y pegar la información de versión (puede usar el botón **copiar información** ). .

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-devname ' se proporcionó sin ninguna acción específica del dispositivo

Se trata de una advertencia que se emite si se pasa-devname a Mtouch cuando no se ha solicitado ninguna acción específica del dispositivo (-logdev/-installdev/-killdev/-launchdev/-listapps).

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: No se pudo analizar la variable de entorno *.

Este error se produce si se intenta establecer un par de variables de valor de clave de entorno = valor no válido. El formato correcto es:`mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: El nombre de aplicación ' *. exe ' entra en conflicto con un nombre de ensamblado de SDK o de producto (. dll).

El nombre del ensamblado ejecutable y el nombre de la aplicación no pueden coincidir con el nombre de ningún archivo dll de la aplicación. Modifique el nombre del archivo ejecutable.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: La nueva lógica de refcounting requiere que el SGen esté habilitado.

Si habilita la extensión refcounting, también debe habilitar el recolector de elementos no utilizados de SGen en las opciones de compilación de iOS del proyecto (pestaña avanzadas).

A partir de Xamarin. iOS 7.2.1 este requisito, se puede habilitar la nueva lógica refcounting con los recolectores de elementos no utilizados Boehm y SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: El directorio de salida * no existe.

Cree el directorio.

Este error ya no se genera, Mtouch creará automáticamente el directorio si no existe.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: No hay ninguna plataforma desarrollo en *, use--Platform = CHAPAs para especificar el SDK.

Xamarin. iOS no puede encontrar el directorio del SDK en la ubicación mencionada en el mensaje de error. Compruebe que la ruta de acceso es correcta.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: El ensamblado raíz * no existe.

Xamarin. iOS no puede encontrar el ensamblado en la ubicación mencionada en el mensaje de error. Compruebe que la ruta de acceso es correcta.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Debe proporcionar solo un ensamblado raíz; se encontró # assemblies: *.

Se pasó más de un ensamblado raíz a Mtouch, mientras que solo puede haber un ensamblado raíz.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Error al cargar los ensamblados: *.

Se produjo un error al cargar los ensamblados de las referencias de ensamblado raíz. Se puede proporcionar más información en la salida de la compilación.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: No se pudieron analizar los argumentos de la línea de comandos: *.

Se produjo un error al analizar los argumentos de la línea de comandos. Compruebe que todos son correctos.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * se compiló con un tiempo de ejecución más reciente (*) que el MonoTouch admite.

Normalmente, esta advertencia se indica porque el proyecto tiene una referencia a una biblioteca de clases que no se compiló mediante la BCL de Xamarin. iOS.

Del mismo modo, es posible que una aplicación que usa el SDK de .NET 4,0 no funcione en un sistema compatible solo con .NET 2,0, es posible que una biblioteca compilada con .NET 4,0 no funcione en Xamarin. iOS, puede usar la API que no está presente en Xamarin. iOS.

La solución general consiste en compilar la biblioteca como una biblioteca de clases de Xamarin. iOS. Esto puede realizarse mediante la creación de un nuevo proyecto de biblioteca de clases de Xamarin. iOS y agregarle todos los archivos de código fuente. Si no tiene el código fuente de la biblioteca, debe ponerse en contacto con el proveedor y solicitar que proporcione una versión compatible con Xamarin. iOS de su biblioteca.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Se proporcionan datos incompletos para completar *.

Este error ya no se ha comunicado en la versión actual de Xamarin. iOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: La compatibilidad con la generación de perfiles requiere que se habilite también Sgen.

SGen (--Sgen) debe estar habilitado si está habilitada la generación de perfiles (--profiling).

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: El SDK de iOS * no admite la creación de aplicaciones que tengan como destino *.

Esto puede ocurrir en las siguientes circunstancias:

- ARMv6 está habilitado y Xcode 4,5 o posterior está instalado.
- ARMv7s está habilitado y Xcode 4,4 o una versión anterior está instalado.

Compruebe que la versión instalada de Xcode admite las arquitecturas seleccionadas.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI no válido: *. Los Abi admitidos son: i386, x86_64, ARMv7, ARMv7 + LLVM, ARMv7 + LLVM + thumb2, armv7s, armv7s + LLVM, armv7s + LLVM + thumb2, arm64 y arm64 + LLVM.

Se pasó una ABI no válida a Mtouch. Especifique una ABI válida.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: La opción * está en desuso.

La opción Mtouch mencionada está en desuso y se omitirá.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Debe proporcionar un ensamblado raíz.

Es necesario especificar un ensamblado raíz (normalmente el ejecutable principal) al compilar una aplicación.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argumento de línea de comandos desconocido: *.

Mtouch no reconoce el argumento de la línea de comandos mencionado en el mensaje de error.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Solo se puede usar una opción--[log | install | Kill | Launch] dev o--[Launch | Debug] SIM Option.

Hay varias opciones para Mtouch que no se pueden usar simultáneamente:

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: Las opciones válidas para\*' ' son\*' '.

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: No se puede compilar con gcc/g + + (--use-GCC) al usar el registrador estático (este es el valor predeterminado al compilar para el dispositivo). Quite la marca--use-gcc o use el registrador dinámico (--registrar: dinámico).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: Las opciones '--unsupported--enable-Generics-in-registrador ' y '--registrar ' no son compatibles.

Quite las opciones `--unsupported--enable-generics-in-registrar` y `--registrar`. A partir de Xamarin. iOS 7.2.1, el registrador predeterminado admite genéricos.

Este error ya no se muestra (el argumento `--unsupported--enable-generics-in-registrar` de línea de comandos se ha quitado de Mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: El nombre de aplicación ' *. exe ' entra en conflicto con otro ensamblado de usuario.

El nombre del ensamblado ejecutable y el nombre de la aplicación no pueden coincidir con el nombre de ningún archivo dll de la aplicación. Modifique el nombre del archivo ejecutable.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: No se pudo encontrar el archivo requerido ' * '.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: No se proporcionó ninguna versión del SDK. Agregue `--sdk=X.Y` para especificar qué SDK de iOS debe usarse para compilar la aplicación.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: No se pudo analizar el argumento de línea de comandos ' * ': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: Las opciones '\*' y '\*' no son compatibles.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: No se puede habilitar el círculo (-circular) cuando el destino es iOS 4,1 o una versión anterior. Deshabilite el gráfico circular (-tarta: false) o establezca el destino de implementación en al menos iOS 4,2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--Enable-REPL) solo se admite en el simulador (--SIM).

REPL solo se admite si está compilando para el simulador. Esto significa que si se pasa `--enable-repl` a Mtouch, también se debe pasar `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: El nombre del archivo ejecutable\*() y el nombre de\*la aplicación () son diferentes, lo que puede impedir que los registros de bloqueo se simbólicos correctamente.

Cuando Xcode symbolicates (traduce direcciones de memoria a nombres de función y números de archivo o línea), el proceso puede producir un error si el archivo ejecutable y la aplicación tienen nombres diferentes (sin la extensión).

Para corregir este cambio, cambie "nombre de aplicación" en las opciones de la aplicación de compilación o de iOS del proyecto, o bien cambie "nombre del ensamblado" en las opciones de compilación/salida del proyecto.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Los argumentos de la línea de comandos '--Enable-Background-fetch ' y '--Launch-for-Background-fetch ' requieren también '--launchsim '.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: Se omite la opción '--debugtrack ' a menos que también se especifique '--Debug '.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Un proyecto de Xamarin. iOS debe hacer referencia A MonoTouch. dll o Xamarin. iOS. dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: No se puede incluir ' MonoTouch. dll ' y ' Xamarin. iOS. dll ' en el mismo proyecto de Xamarin. iOS\*: ' ' se hace referencia de forma\*explícita, mientras que ' * ' hace referencia a ' '.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: No se puede iniciar un * simulador para una aplicación *. Habilite las arquitecturas correctas en las opciones de compilación de iOS del proyecto (página avanzadas).

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037: MonoTouch. dll no es compatible con 64 bits. Puede hacer referencia a Xamarin. iOS. dll o no compilar para una arquitectura de 64 bits (ARM64 y/o x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Los registradores antiguos (--registrar: oldstatic | olddynamic) no se admiten cuando se hace referencia a Xamarin. iOS. dll.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Las aplicaciones que tienen como destino ARMv6 no pueden hacer referencia a Xamarin. iOS. dll.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: No se pudo encontrar el ensamblado '\*', al que hace referencia '\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: No puede hacer referencia a ' MonoTouch. dll ' y ' Xamarin. iOS. dll '.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: No se encontró ninguna referencia a MonoTouch. dll o Xamarin. iOS. dll. Se agregará una referencia a MonoTouch. dll.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: El recolector de elementos no utilizados de Boehm no se admite actualmente cuando se hace referencia a ' Xamarin. iOS. dll '. En su lugar, se ha seleccionado el recolector de elementos no utilizados de SGen.

Solo se admite el recolector de elementos no utilizados de SGen con proyectos unificados. Asegúrese de que no hay marcas Mtouch adicionales que especifiquen Boehm como recolector de elementos no utilizados.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim solo se admite con Xcode 6,0 o posterior.

Instale una versión más reciente de Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--Extension solo se admite cuando se usa el SDK de iOS 8,0 (o posterior).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: El destino de implementación mínimo para aplicaciones unificadas es 5.1.1, el destino de implementación actual es "*". Seleccione un destino de implementación más reciente en las opciones de la aplicación de iOS del proyecto.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *. Framework solo se admite si el destino de la implementación es 8,0 o posterior. * es posible que las características no funcionen correctamente.

El marco de trabajo especificado no se admite en la versión de iOS a la que hace referencia el destino de implementación. Actualice el destino de implementación a una versión más reciente de iOS o quite el uso del marco de trabajo especificado de la aplicación.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin. iOS * requiere Xcode 5,0 o posterior. La versión actual de Xcode (se encuentra en *) es *.

Instale una versión más reciente de Xcode.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: No se especificó ningún comando.

No se especificó ninguna acción para Mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: No se puede canonizar la ruta de acceso ' * ': *

Se trata de un error interno. Si ve este error, registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: La ruta de acceso de Xcode "*" no existe.

La ruta de acceso de `--sdkroot` Xcode pasada con no existe. Especifique una ruta de acceso válida.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: No se puede encontrar Xcode en la ubicación predeterminada (/Applications/Xcode.app). Instale Xcode o pase una ruta de acceso personalizada mediante--SDKRoot \<path >.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: No se puede determinar la ruta de acceso a Xcode. app desde la raíz del SDK ' * '. Especifique la ruta de acceso completa al lote de Xcode. app.

La ruta de acceso `--sdkroot` pasada mediante no especifica una aplicación Xcode válida. Especifique una ruta de acceso a una aplicación de Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode. app '\*' no es válido (el archivo '\*' no existe).

La ruta de acceso `--sdkroot` pasada mediante no especifica una aplicación Xcode válida. Especifique una ruta de acceso a una aplicación de Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: No se pudo encontrar la Xcode seleccionada actualmente en el sistema: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: No se encontró la Xcode seleccionada actualmente en el sistema. ' Xcode-Select--Print-path ' devolvió ' * ', pero ese directorio no existe.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: No se especificó Xcode. app (con--SDKRoot), con el sistema Xcode como indica "Xcode-Select--Print-path": *

Esta es una advertencia informativa, que explica qué Xcode se usará, ya que no se especificó ninguno.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: No se especificó Xcode. app (con--SDKRoot o ' Xcode-Select--Print-path ') con el Xcode predeterminado en su lugar: *

Esta es una advertencia informativa, que explica qué Xcode se usará, ya que no se especificó ninguno.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: No se encuentra el archivo ejecutable en la extensión * (no hay ninguna entrada CFBundleExecutable en su info. plist)

Cada info. plist debe tener un archivo ejecutable (mediante la entrada CFBundleExecutable); sin embargo, se debe generar automáticamente una entrada durante la compilación.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin. iOS solo admite marcos de trabajo incrustados con proyectos unificados.

Xamarin. iOS solo admite marcos de trabajo incrustados cuando se usa el Unified API; Actualice el proyecto para usar el Unified API.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin. iOS solo admite marcos insertados cuando el destino de la implementación es al menos 8,0 (destino de implementación actual: * marcos de trabajo insertados: *)

Xamarin. iOS solo admite marcos de trabajo incrustados cuando el destino de implementación es al menos 8,0 (porque las versiones anteriores de iOS no admiten Marcos incrustados).

Actualice el destino de implementación en el archivo info. plist del proyecto a 8,0 o superior.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Ensamblado de registrador de compilación no válido: *

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Registrador no válido: *

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valor no válido para la versión de .NET Framework de destino: *.

Se pasó una versión de .NET Framework de destino no válida mediante el argumento--Target-Framework. Especifique una versión de .NET Framework de destino válida.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Versión de .NET Framework de destino no válida: *. Las plataformas de destino válidas son: *.

Se pasó una versión de .NET Framework de destino no válida mediante el argumento--Target-Framework. Especifique una versión de .NET Framework de destino válida.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Plataforma desconocida: *. Esto normalmente indica un error en Xamarin. iOS; Registre un informe de errores en http://bugzilla.xamarin.com con un caso de prueba.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: No se admiten extensiones para la plataforma ' * '.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin. iOS * no admite un destino de implementación de * (el mínimo es *). Seleccione un destino de implementación más reciente en el archivo info. plist del proyecto.

El destino de implementación mínimo es el especificado en el mensaje de error; Seleccione un destino de implementación más reciente en el archivo info. plist del proyecto.

Si no es posible actualizar el destino de implementación, use una versión anterior de Xamarin. iOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin. iOS * no admite un destino de implementación mínimo de * (el máximo es *). Seleccione un destino de implementación anterior en el archivo info. plist del proyecto o actualice a una versión más reciente de Xamarin. iOS.

Xamarin. iOS no admite el establecimiento del destino de implementación mínimo en una versión posterior a la versión para la que se compiló esta versión concreta de Xamarin. iOS.

Seleccione un destino de implementación mínimo anterior en el archivo info. plist del proyecto o actualice a una versión más reciente de Xamarin. iOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Arquitectura no válida ' * ' para * proyectos. Las arquitecturas válidas son: *

Se especificó una arquitectura no válida. Compruebe que la arquitectura es válida.

<a name="MT0076" />

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076: No se especificó ninguna arquitectura (con el argumento--ABI). Se necesita una arquitectura para * proyectos.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0077" />

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077: Los proyectos de watchos deben ser extensiones.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0078" />

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078: Las compilaciones incrementales se habilitan con un destino de implementación < 8,0 (actualmente *). Esto no se admite (la aplicación resultante no se iniciará en iOS 9), por lo que el destino de implementación se establecerá en 8,0.

Se trata de una advertencia que informa de que el destino de implementación se ha establecido en 8,0 para esta compilación, de modo que las compilaciones incrementales funcionen correctamente.

Las compilaciones incrementales solo se admiten cuando el destino de implementación es al menos 8,0 (porque la aplicación resultante no se iniciará en iOS 9 de otro modo).

<a name="MT0079" />

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079: La versión de Xcode recomendada para Xamarin. iOS * es Xcode * o posterior. La versión actual de Xcode (se encuentra en *) es *.

Se trata de una advertencia que informa de que la versión actual de Xcode no es la versión recomendada de Xcode para esta versión de Xamarin. iOS.

Actualice Xcode para garantizar un comportamiento óptimo.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: La deshabilitación de NewRefCount,--New-refcount: false, está en desuso.

Se trata de una advertencia que informa de que se ha omitido la solicitud para deshabilitar el nuevo refcount (--New-refcount: false).

La nueva característica de refcount es ahora obligatoria para todos los proyectos y, por lo tanto, no es posible deshabilitarla.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: El argumento de la línea de comandos--download-Crash-Report también requiere--download-Crash-Report-to.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--Enable-REPL) solo se admite cuando no se usa la vinculación (--nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Solo ASM Bitcode no se admite en watchos. Use--Bitcode: marcador o--Bitcode: Full.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Bitcode no se admite en el simulador. No pase--Bitcode al compilar para el simulador.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: No se encontró ninguna referencia a ' * '. Se agregará automáticamente.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Se debe especificar una plataforma de destino (--Target-Framework) al compilar para TVOS o Watchos.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Las compilaciones incrementales (--fastdev) no se admiten con el GC de Boehm. Se deshabilitarán las compilaciones incrementales.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: El GC debe estar en modo cooperativo para las aplicaciones de watchos. Quite el argumento--Coop: false en Mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: La opción '\*' no puede tomar el valor\*' ' cuando el modo cooperativo está habilitado para el GC.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Esta versión de Xamarin. iOS requiere el SDK de * (incluido con Xcode *). Actualice Xcode para obtener los archivos de encabezado necesarios o establezca el comportamiento del vinculador administrado solo en los SDK de marco de enlace (para intentar evitar las nuevas API).

Xamarin. iOS requiere los archivos de encabezado, desde la versión del SDK especificada en el mensaje de error, para compilar la aplicación. La manera recomendada de corregir este error es actualizar Xcode para obtener el SDK necesario, que incluirá todos los archivos de encabezado necesarios. Si tiene varias versiones de Xcode instaladas o desea usar Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación de Xcode correcta en las preferencias del IDE.

Una solución alternativa posible es habilitar el vinculador administrado. Esto eliminará la API sin usar, incluida, en la mayoría de los casos, la nueva API en la que faltan los archivos de encabezado (o están incompletos). Sin embargo, esto no funcionará si el proyecto usa la API que se presentó en un SDK más reciente que el que proporciona Xcode.

Una solución de último recurso sería utilizar una versión anterior de Xamarin.iOS, que es compatible con el SDK de su proyecto requiere.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: No se pudo encontrar ' mlaunch '.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: No se pudieron copiar los archivos AOT en el directorio de destino {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: No se encontró ninguna referencia a Xamarin. iOS. dll.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Error interno *. Registre un informe de errores con un caso de prueba http://bugzilla.xamarin.com) (.

Este mensaje de error se indica cuando se produce un error en una comprobación de coherencia interna en Xamarin. iOS.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Destino de compilación de ensamblado no válido: ' * '. Registre un informe de errores con un caso de prueba http://bugzilla.xamarin.com) (.

Este mensaje de error se indica cuando se produce un error en una comprobación de coherencia interna en Xamarin. iOS.

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: El ensamblado ' * ' se especifica varias veces en los argumentos--Assembly-Build-Target.

El ensamblado mencionado en el mensaje de error se especifica varias veces en los argumentos--Assembly-Build-Target. Asegúrese de que cada ensamblado se menciona solo una vez.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Los ensamblados\*' ' y\*' ' tienen el mismo nombre de destino\*(' '), pero diferentes destinos\*(' ' y ' * ').

Los ensamblados que se mencionan en el mensaje de error tienen destinos de compilación en conflicto.

Por ejemplo:

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

En este ejemplo se intenta crear una biblioteca dinámica y un marco de trabajo con la misma marca`MyBinary`().

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: El objeto estático '\*' contiene más de un ensamblado (\*' '), pero cada objeto estático debe corresponderse con exactamente un ensamblado.

Todos los ensamblados que se mencionan en el mensaje de error se compilan en un solo objeto estático. Esto no se permite, cada ensamblado se debe compilar en un objeto estático diferente.

Por ejemplo:

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

En este ejemplo se intenta crear un objeto estático`MyBinary`() formado por dos ensamblados`Assembly1.dll` ( `Assembly2.dll`y), que no están permitidos.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: No se especificó ningún destino de compilación del ensamblado para ' * '.

Al especificar el destino de compilación del ensamblado mediante `--assembly-build-target`, cada ensamblado de la aplicación debe tener asignado un destino de compilación.

Este error se indica cuando el ensamblado mencionado en el mensaje de error no tiene asignado un destino de compilación de ensamblado.

Consulte la documentación sobre `--assembly-build-target` para obtener más información.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: El nombre del destino de compilación\*del ensamblado ' ' no es\*válido: no se permite el carácter ' '.

El nombre del destino de compilación del ensamblado debe ser un nombre de archivo válido.

Por ejemplo, estos valores desencadenarán este error:

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

porque `my/path.o` no es un nombre de archivo válido debido al carácter separador de directorio.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Los ensamblados\*' ' tienen diferentes optimizaciones de LLVM\*personalizadas (), lo cual no se permite cuando todos se compilan en un solo binario.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: El destino de compilación del ensamblado ' * ' no coincidía con ningún ensamblado.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: El ensamblado{0}' ' se cargó desde una ruta de acceso distinta de la ruta de acceso {1}proporcionada (ruta de {2}acceso proporcionada:, ruta de acceso real:).

Se trata de una advertencia que indica que un ensamblado al que hace referencia la aplicación se cargó desde una ubicación diferente de la solicitada.

Esto podría significar que la aplicación hace referencia a varios ensamblados con el mismo nombre, pero desde ubicaciones diferentes, lo que puede provocar resultados inesperados (solo se usará el primer ensamblado).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin. iOS no admite compilaciones incrementales en proyectos que incluyen bibliotecas de enlace de terceros y que se compilan en Bitcode.

Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin. iOS no admite compilaciones incrementales en proyectos que incluyen bibliotecas de enlace de terceros y que se compilan en Bitcode (proyectos de tvOS y watchos).

No se requiere ninguna acción por su parte; este mensaje es meramente informativo.

Para obtener más información, vea el error n.º[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Esta advertencia ya no se ha comunicado.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode se ha habilitado porque esta versión de Xamarin. iOS no admite la compilación de proyectos de watchos mediante LLVM sin habilitar Bitcode.

Bitcode se ha habilitado automáticamente porque esta versión de Xamarin. iOS no admite la compilación de proyectos de watchos mediante LLVM sin habilitar Bitcode.

No se requiere ninguna acción por su parte; este mensaje es meramente informativo.

Para obtener más información, vea el error n.º[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Se ha deshabilitado el uso compartido de código nativo porque *

Hay varias razones por las que se puede deshabilitar el uso compartido de código:

- Dado que el destino de implementación de la aplicación contenedora es anterior a iOS 8,0 (es *)).

El uso compartido de código nativo requiere iOS 8,0 porque el uso compartido de código nativo se implementa mediante marcos de usuario, que se presentó con iOS 8,0.

- Dado que la aplicación de contenedor incluye ensamblados de I18N (*).

Actualmente no se admite el uso compartido de código nativo si la aplicación contenedora incluye ensamblados I18N.

- Dado que la aplicación contenedora tiene definiciones XML personalizadas para el vinculador administrado (*).

No se admite el uso compartido de código nativo para los proyectos que usan definiciones XML personalizadas para el vinculador administrado.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Se ha deshabilitado el uso compartido de código nativo para la extensión ' * ' porque *.

- Dado que las opciones de Bitcode difieren entre\*la aplicación de contenedor (\*) y la extensión ().

  El uso compartido de código nativo requiere que las opciones de Bitcode coincidan entre los proyectos que comparten código.

- Dado que las opciones--Assembly-Build-Target son diferentes entre la aplicación\*de contenedor () y\*la extensión ().

  El uso compartido de código nativo requiere que las opciones--Assembly-Build-Target sean idénticas entre los proyectos que comparten código.

  Esta condición puede producirse si las compilaciones incrementales no están habilitadas o deshabilitadas en todos los proyectos.

- Dado que los ensamblados de i18n son diferentes entre la\*aplicación de contenedor ()\*y la extensión ().

  El uso compartido de código nativo no se admite actualmente para las extensiones que incluyen ensamblados de I18N.

- Dado que los argumentos del compilador de AOT son diferentes entre la\*aplicación de contenedor ()\*y la extensión ().

  El uso compartido de código nativo requiere que los argumentos del compilador de AOT no difieran entre los proyectos que comparten código.

- Dado que los otros argumentos del compilador de AOT son diferentes entre la\*aplicación de contenedor ()\*y la extensión ().

  El uso compartido de código nativo requiere que los argumentos del compilador de AOT no difieran entre los proyectos que comparten código.

  Esta condición se produce si "realizar todas las operaciones de punto flotante de 32 bits como Float de 64 bits" difiere entre los proyectos.

- Dado que LLVM no está habilitado o deshabilitado en la\*aplicación de contenedor ()\*y en la extensión ().

  El uso compartido de código nativo requiere que LLVM esté habilitado o deshabilitado para todos los proyectos que comparten código.

- Dado que la configuración del vinculador administrado es diferente entre la aplicación\*de contenedor () y\*la extensión ().

  El uso compartido de código nativo requiere que la configuración del vinculador administrado sea idéntica para todos los proyectos que comparten código.

- Dado que los ensamblados omitidos para el vinculador administrado son diferentes entre la aplicación\*de contenedor () y\*la extensión ().

  El uso compartido de código nativo requiere que la configuración del vinculador administrado sea idéntica para todos los proyectos que comparten código.

- Dado que la extensión tiene definiciones XML personalizadas para el vinculador administrado (*).

  No se admite el uso compartido de código nativo para los proyectos que usan definiciones XML personalizadas para el vinculador administrado.

- Dado que la aplicación contenedora no se compila para la ABI * (mientras que la extensión se está compilando para esta ABI).

  El uso compartido de código nativo requiere que la aplicación contenedora se compile para todas las arquitecturas para las que se compila cualquier extensión de aplicación.

  Por ejemplo: esta condición se produce cuando una extensión se compila para ARM64 + ARMv7, pero la aplicación de contenedor solo se compila para ARM64.

- Dado que la aplicación de contenedor se está compilando para la ABI \*, que no es compatible con la ABI de la extensión (\*).

  El uso compartido de código nativo requiere que todos los proyectos se compilan para la misma API exacta.

  Por ejemplo: esta condición se produce cuando una extensión se compila para ARMv7 + LLVM + thumb2, pero la aplicación de contenedor solo se compila para ARMv7 + LLVM.

- Dado que la aplicación contenedora hace referencia al\*ensamblado '\*' de ' ', mientras que la extensión hace referencia a una versión diferente de ' * '.

  El uso compartido de código nativo requiere que todos los proyectos que comparten código usen las mismas versiones para todos los ensamblados.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: Se recomienda hacer referencia a los símbolos dinámicos mediante código (--Dynamic-Symbol-Mode = Code) cuando Bitcode está habilitado.

Los proyectos de Xamarin. iOS a menudo hacen referencia a símbolos nativos dinámicamente, lo que significa que el vinculador nativo podría quitar estos símbolos nativos durante el proceso de vinculación nativa, ya que el vinculador nativo no ve que se usan estos símbolos.

Normalmente, Xamarin. iOS solicitará al enlazador nativo que conserve dichos símbolos ( `-u symbol` mediante la marca del enlazador), pero al compilar para Bitcode, el vinculador nativo no acepta la `-u` marca.

Xamarin. iOS ha implementado una solución alternativa: generamos código nativo adicional que hace referencia a estos símbolos y, por tanto, el vinculador nativo verá que se usan estos símbolos. Esto se hace automáticamente al compilar en Bitcode.

Si `--dynamic-symbol-mode=linker` se pasa a Mtouch, esta solución alternativa se deshabilitará y Xamarin. iOS intentará pasar `-u` al enlazador nativo. Lo más probable es que se produzcan errores nativos del enlazador.

La solución consiste en quitar el `--dynamic-symbol-mode=linker` argumento de los argumentos de Mtouch adicionales en las opciones de compilación del proyecto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Arquitectura no válida: {Arch}. no se admiten las arquitecturas de 32 bits cuando el destino de la implementación es 11 o posterior. Asegúrese de que el proyecto no se compila para una arquitectura de 32 bits.

iOS 11 no admite aplicaciones de 32 bits, por lo que no se puede compilar para una aplicación de 32 bits cuando el destino de implementación es iOS 11 o posterior.

Cambie la arquitectura de destino en las opciones de compilación de iOS del proyecto a arm64 o cambie el destino de implementación en info. plist del proyecto a una versión anterior de iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: No se puede iniciar una aplicación de 32 bits en un simulador que solo admita 64 bits.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: No se encontraron archivos AOT en el directorio esperado ' {msymdir} '.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: El ensamblado ejecutable * no hace referencia a *.

No se encontró ninguna referencia al ensamblado de plataforma (Xamarin. iOS. dll/Xamarin. TVOS. dll/Xamarin. Watchos. dll) en el ensamblado ejecutable.

Esto suele suceder cuando no hay ningún código en el proyecto ejecutable que use cualquier elemento del ensamblado de la plataforma; por ejemplo, un método Main vacío (y ningún otro código) mostraría este error:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

El uso de una API del ensamblado de plataforma resolverá el error:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: No se pudo establecer el idioma actual en ' {Lang} ' (según LANG = {LANG}): {Exception}

Se trata de una advertencia que indica que el idioma actual no se pudo establecer en el idioma del mensaje de error.

El idioma actual usará de forma predeterminada el idioma del sistema.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: El argumento de línea de comandos--Assembly-Build-Target se omite en el simulador.

No se requiere ninguna acción, este mensaje es meramente informativo.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Se han deshabilitado las compilaciones incrementales porque las compilaciones incrementales no se admiten en el simulador.

No se requiere ninguna acción, este mensaje es meramente informativo.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Las compilaciones incrementales se han deshabilitado porque esta versión de Xamarin. iOS no admite compilaciones incrementales en proyectos que incluyen más de una biblioteca de enlace de terceros.

Las compilaciones incrementales se han deshabilitado automáticamente porque esta versión de Xamarin. iOS no siempre compila proyectos con varias bibliotecas de enlace de terceros correctamente.

No se requiere ninguna acción, este mensaje es meramente informativo.

Para obtener más información, vea el error n.º[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: No se pudo tocar el archivo ' * ': *

Se produjo un error al tocar un archivo (lo que se hace para asegurarse de que las compilaciones parciales se realizan correctamente).

Es posible que se omita esta advertencia. en caso de que se produzca algún problema, presente un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) y se investigará.

<a name="MT0135" />

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135: No se ha vinculado el{0}marco de trabajo del sistema ' '{1}(al que hace referencia el ensamblado ' ') porque se {2} presentó en {2} {3}y estamos usando el {4} SDK.

Para compilar la aplicación, Xamarin. iOS debe vincularse a las bibliotecas del sistema, algunas de las cuales dependen de la versión del SDK especificada en el mensaje de error. Dado que está usando una versión anterior del SDK, es posible que se produzcan errores en las invocaciones a esas API en tiempo de ejecución.

La manera recomendada de corregir este error es actualizar Xcode para obtener el SDK necesario. Si tiene varias versiones de Xcode instaladas o desea usar Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación de Xcode correcta en las preferencias del IDE.

También puede habilitar el [vinculador](https://docs.microsoft.com/xamarin/ios/deploy-test/linker) administrado para quitar las API sin usar, incluidas (en la mayoría de los casos) las nuevas que requieren la biblioteca especificada. Sin embargo, esto no funcionará si el proyecto requiere API introducidas en un SDK más reciente que el que proporciona Xcode.

Como última solución de paja, use una versión anterior de Xamarin. iOS que no requiera que estos nuevos SDK estén presentes durante el proceso de compilación.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Mensajes de error relacionados con el proyecto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Instalador/Mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: No se encontró ninguna aplicación en el directorio especificado

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: No se pudieron crear los vínculos simbólicos, se copiaron los archivos

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: No se pudo eliminar la aplicación ' * '. Es posible que tenga que eliminar la aplicación manualmente.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: No se pudo obtener la lista de aplicaciones instaladas.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Mensajes de error relacionados con el proyecto

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: No se pudo eliminar la aplicación\*' ' en el dispositivo\*' ': *-es posible que tenga que eliminar la aplicación manualmente.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: No se pudo instalar la aplicación\*' ' en el dispositivo\*' ': *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: No se pudo iniciar la aplicación\*' ' en el dispositivo\*' ': *. Puede seguir iniciando la aplicación manualmente punteando en ella.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: No se pudo iniciar el simulador

Este error se genera si Mtouch no pudo iniciar el simulador.   Esto puede ocurrir a veces porque ya hay un proceso de simulador inactivo o obsoleto en ejecución.

El siguiente comando emitido en la línea de comandos de UNIX se puede usar para terminar los procesos del simulador bloqueado:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: No se pudo copiar el ensamblado '\*' en '\*': *

Se trata de un problema conocido en ciertas versiones de Xamarin. iOS.

Si esto ocurre, pruebe la siguiente solución alternativa:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

Sin embargo, como este problema se ha resuelto en la versión más reciente de Xamarin. iOS, registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con la información de versión completa y el resultado del registro de compilación.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: No se pudo cargar el ensamblado ' * ': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: No se pudo agregar el archivo de recursos que faltaba: ' * '

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: No se pudieron enumerar las aplicaciones en el dispositivo ' * ': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Error de seguimiento de dependencia: no hay archivos para comparar. Registre un informe de errores en http://bugzilla.xamarin.com con un caso de prueba.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: No se pudo volver a usar la versión en caché de ' * ': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: No se pudo crear el archivo ejecutable ' * ': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: No se pudo crear el archivo ejecutable ' * ': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: No se pudo crear el archivo de aviso porque ya existe un directorio con el mismo nombre.

Quite el directorio `NOTICE` del proyecto.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: No se pudo crear el archivo de notificación: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: La aplicación no pudo realizar comprobaciones de firma de código y no se pudo instalar en el dispositivo ' * '. Compruebe los certificados, los perfiles de aprovisionamiento y los ID. de agrupación. Es posible que el dispositivo no forme parte del perfil de aprovisionamiento seleccionado (error: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: La aplicación tiene derechos no admitidos por el perfil de aprovisionamiento actual y no se pudo instalar en el dispositivo ' * '. Consulte el registro del dispositivo iOS para obtener información más detallada (error: 0xe8008016).

Esto puede ocurrir si:

- La aplicación tiene derechos que el perfil de aprovisionamiento actual no admite.
  Soluciones posibles:
  - Especifique un perfil de aprovisionamiento diferente que admita los derechos que necesita la aplicación.
  - Quitar los derechos no admitidos en el perfil de aprovisionamiento actual.
- El dispositivo en el que está intentando implementar no está incluido en el perfil de aprovisionamiento que está usando.
  Soluciones posibles:
  - Cree una nueva aplicación a partir de una plantilla en Xcode, seleccione el mismo perfil de aprovisionamiento e impleméntela en el mismo dispositivo. En ocasiones, Xcode puede actualizar automáticamente los perfiles de aprovisionamiento con nuevos dispositivos (en otros casos, Xcode le preguntará qué hacer).
  -Vaya al centro de desarrollo de iOS y actualice el perfil de aprovisionamiento con el nuevo dispositivo y, a continuación, descargue el perfil de aprovisionamiento actualizado en la máquina.

En la mayoría de los casos, se imprimirá más información sobre el error en el registro de dispositivos iOS, lo que puede ayudar a diagnosticar el problema.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: No se pudo iniciar la aplicación\*' ' en el dispositivo\*' ': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: No se pudo copiar el archivo\*' ' en\*' ':{2}

No se pudo copiar un archivo. El mensaje de error de la operación de copia tiene más información sobre el error.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: No se pudo copiar el directorio\*' ' en\*' ':{2}

No se pudo copiar un directorio. El mensaje de error de la operación de copia tiene más información sobre el error.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: No se pudo establecer comunicación con el dispositivo para encontrar la aplicación ' * ': *

Se produjo un error al intentar buscar una aplicación en el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: No se pudo comprobar la firma de la aplicación en el dispositivo ' * '. Asegúrese de que el perfil de aprovisionamiento esté instalado y no haya expirado (error: 0xe8008017).

El dispositivo rechazó la instalación de la aplicación porque no se pudo comprobar la firma.

Asegúrese de que el perfil de aprovisionamiento esté instalado y no haya expirado.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: No se pudieron enumerar los informes de bloqueo en el dispositivo *.

Se produjo un error al intentar enumerar los informes de bloqueo en el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: No se pudo descargar el informe de bloqueo * del dispositivo *.

Se produjo un error al intentar descargar los informes de bloqueo del dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: No se puede usar Xcode 7 + para iniciar aplicaciones en dispositivos con iOS * (Xcode 7 solo es compatible con iOS 6 +).

No es posible usar Xcode 7 + para iniciar aplicaciones en dispositivos con la versión de iOS inferior a 6,0.

Use una versión anterior de Xcode o pulse la aplicación manualmente para iniciarla.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: Especificación de dispositivo no válida: ' * '. Se esperaba ' iOS ', ' watchos ' o ' All '.

La especificación del dispositivo pasada con--el dispositivo no es válida. Los valores válidos son: ' iOS ', ' watchos ' o ' All '.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: No se encontró ninguna aplicación en el directorio especificado: *

La ruta de acceso de la aplicación pasada a--launchdev no existe. Especifique un grupo de aplicaciones válido.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: La ejecución de aplicaciones en el dispositivo mediante un identificador de lote está en desuso. Pase la ruta de acceso completa al paquete que se va a iniciar.

Se recomienda pasar la ruta de acceso a la aplicación para que se inicie en el dispositivo en lugar de solo el identificador de paquete.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: No se pudo iniciar la aplicación\*' ' en el dispositivo\*' ' porque el dispositivo está bloqueado. Desbloquee el dispositivo e inténtelo de nuevo.

Desbloquee el dispositivo e inténtelo de nuevo.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Este ejecutable de aplicación puede ser demasiado grande (* MB) para ejecutarse en el dispositivo. Si Bitcode está habilitado, es posible que desee deshabilitarlo para el desarrollo, solo es necesario para enviar aplicaciones a Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: No se pudo desinstalar la aplicación\*' ' del dispositivo '\*': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: No se pueden incluir versiones diferentes del marco ' {name} '

No es posible vincular con versiones diferentes del mismo marco de trabajo.

Esto suele ocurrir cuando una extensión hace referencia a una versión diferente de un marco de trabajo que la aplicación contenedora (posiblemente a través de un ensamblado de enlace de terceros).

Tras este error, habrá varios errores [MT1036](#MT1036) que enumeran las rutas de acceso de cada marco de trabajo diferente.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: El marco de trabajo ' {name} ' se incluye en: {Path} (relacionado con el error anterior)

Este error solo se muestra junto con [MT1036](#MT1036). Consulte [MT1036](#MT1036) para obtener más información.

### <a name="mt11xx-debug-service"></a>MT11xx: Depurar servicio

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: No se pudo iniciar la aplicación

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: No se pudo adjuntar a la aplicación (para eliminarla): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: No se pudo desasociar

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: No se pudo enviar el paquete: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Tipo de respuesta inesperado

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: No se pudo obtener la lista de aplicaciones en el dispositivo: Se agotó el tiempo de espera de la solicitud.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: No se pudo iniciar la aplicación: *

Compruebe si el dispositivo está bloqueado.

Si va a implementar una aplicación empresarial o a usar un perfil de aprovisionamiento gratuito, puede que le confíe en el desarrollador (esto se explica <a href="https://stackoverflow.com/a/30726375/183422">aquí</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: No se encuentran las herramientas de desarrollo para este dispositivo XX (YY).

Algunas operaciones de Mtouch requieren que el `DeveloperDiskImage.dmg` archivo esté presente.   Este archivo forma parte de Xcode y normalmente se encuentra en relación con el SDK con el que se va a realizar la compilación `Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg`, en.

Este error puede producirse porque no tiene un DeveloperDiskImage. dmg que coincida con el dispositivo que ha conectado.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: No se pudo iniciar la aplicación porque el dispositivo está bloqueado. Desbloquee el dispositivo e inténtelo de nuevo.

Compruebe si el dispositivo está bloqueado.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: No se pudo iniciar la aplicación debido a restricciones de seguridad de iOS. Asegúrese de que el desarrollador es de confianza.

Si va a implementar una aplicación empresarial o a usar un perfil de aprovisionamiento gratuito, puede que le confíe en el desarrollador (esto se explica <a href="https://stackoverflow.com/a/30726375/183422">aquí</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: La aplicación se inició correctamente, pero no es posible esperar a que la aplicación se cierre como se solicitó porque no es posible detectar la terminación de la aplicación cuando se inicia mediante gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: Simulador

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: No se pudo cargar el simulador: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configuración del simulador no válida: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Especificación del simulador no válida: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Especificación de simulador no válida ' * ': no se ha especificado el tiempo de ejecución.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Especificación de simulador no válida ' * ': no se ha especificado el tipo de dispositivo.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: No se pudo encontrar el tiempo de ejecución del simulador ' * '.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: No se encontró el tipo de dispositivo simulador ' * '.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: No se pudo encontrar el tiempo de ejecución del simulador ' * '.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: No se encontró el tipo de dispositivo simulador ' * '.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Especificación del simulador \*no válida:,\*clave desconocida ' '

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: La versión del simulador '\*' no admite el tipo de simulador ' '\*

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: No se pudo crear una versión del simulador donde Type = * y Runtime = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Especificación de simulador no válida para Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Especificación de simulador no válida para Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: Se especificó un SDK no válido: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: No se pudo encontrar el simulador UDID ' * '.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: No se pudo cargar el lote de aplicaciones en ' * '.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: No se encontró ningún identificador de paquete en la aplicación en ' * '.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: No se pudo encontrar el simulador para ' * '.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: No se pudo encontrar el tiempo de ejecución del simulador más reciente para el dispositivo ' * '.

Esto suele indicar un problema con Xcode.

Cosas que intentar solucionar:

- Usar el simulador una vez en Xcode.
- Pase una versión del SDK explícita con la versión \<de SDK >.
- Vuelva a instalar Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: No se pudo encontrar el simulador de iPhone emparejado para el simulador de Watchos ' * '.

Al iniciar una aplicación de Watchos en un simulador de Watchos, también debe haber un simulador de iOS emparejado.

Los simuladores de inspección se pueden emparejar con los simuladores de iOS mediante la interfaz de usuario de dispositivos de Xcode (ventana de menú-> dispositivos).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: Se omitió\*la biblioteca `*` nativa () porque no coincide con las arquitecturas de compilación actuales (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: No se pudo extraer la biblioteca nativa ' * ' de ' + '. Asegúrese de que la biblioteca nativa se incrustó correctamente en el ensamblado administrado (si el ensamblado se compiló con un proyecto de enlace, la biblioteca nativa debe estar incluida en el proyecto y su acción de compilación debe ser ' ObjcBindingNativeLibrary ').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: No se pudo descomprimir el marco\*de trabajo nativo\*' ' de ' '. Revise el registro de compilación para obtener más información sobre el comando nativo "zip".

No se pudo descomprimir el marco nativo especificado de la biblioteca de enlace.

Revise el registro de bulid para obtener más información acerca de este error en el comando nativo "zip".

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: El marco de trabajo incrustado ' * ' en * no es válido: no contiene un archivo info. plist.

El marco de trabajo incrustado especificado no contiene un archivo info. plist y, por lo tanto, no es un marco válido.

Asegúrese de que el marco de trabajo es válido.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: La biblioteca de enlace\*' ' contiene un marco de\*usuario (), pero los marcos de usuario incrustados requieren iOS 8,0 (el destino de implementación actual es *). Establezca el destino de implementación en el archivo info. plist en 8,0 como mínimo.

La biblioteca de enlace especificada contiene un marco de trabajo incrustado, pero Xamarin. iOS solo admite marcos incrustados en iOS 8,0 o posterior.

Establezca el destino de implementación en el archivo info. plist en al menos 8,0 para resolver este error (o no utilice marcos de trabajo incrustados).

### <a name="mt14xx-crash-reports"></a>MT14xx: Informes de bloqueo

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: No se pudo abrir el servicio de informes de bloqueo: AFCConnectionOpen devuelto *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: No se pudo cerrar el servicio de informes de bloqueo: AFCConnectionClose devuelto *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: No se pudo leer la información de archivo para *: AFCFileInfoOpen devuelto *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: No se pudo leer el informe de bloqueo: AFCDirectoryOpen (*) devuelto: *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: No se pudo leer el informe de bloqueo: AFCFileRefOpen (*) devuelto: *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: No se pudo leer el informe de bloqueo: AFCFileRefRead (*) devuelto: *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: No se pudieron enumerar los informes de bloqueo: AFCDirectoryOpen (*) devuelto: *

Se produjo un error al intentar obtener acceso a los informes de bloqueos desde el dispositivo.

Cosas que intentar resolver esto:

- Elimine la aplicación del dispositivo e inténtelo de nuevo.
- Desconecte el dispositivo y vuelva a conectarlo.
- Reinicie el dispositivo.
- Reinicie el equipo Mac.
- Sincronizar el dispositivo con iTunes (esto quitará los informes de bloqueo del dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: No es una biblioteca dinámica de Mach-O (encabezado desconocido ' 0x * '): *.

Se produjo un error al procesar la biblioteca dinámica en cuestión.

Asegúrese de que la biblioteca dinámica es una biblioteca dinámica de Mach-O válida.

El formato de una biblioteca se puede comprobar con el `file` comando de un terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: No es una biblioteca estática (encabezado desconocido ' * '): *.

Se produjo un error al procesar la biblioteca estática en cuestión.

Asegúrese de que la biblioteca estática es una biblioteca estática de Mach-O válida.

El formato de una biblioteca se puede comprobar con el `file` comando de un terminal:

```
file -arch all -l /path/to/library.a
```

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: No es una biblioteca dinámica de Mach-O (encabezado desconocido ' 0x * '): *.

Se produjo un error al procesar la biblioteca dinámica en cuestión.

Asegúrese de que la biblioteca dinámica es una biblioteca dinámica de Mach-O válida.

El formato de una biblioteca se puede comprobar con el `file` comando de un terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Formato desconocido para la entrada de FAT en la posición * en *.

Se produjo un error al procesar el archivo FAT en cuestión.

Asegúrese de que el archivo FAT sea válido.

El formato de un archivo FAT se puede comprobar con el `file` comando de un terminal:

```
file -arch all -l /path/to/file
```

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: El archivo de tipo * no es un archivo MachO (*).

Se produjo un error al procesar el archivo MachO en cuestión.

Asegúrese de que el archivo es una biblioteca dinámica de Mach-O válida.

El formato de un archivo se puede comprobar con el `file` comando de un terminal:

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Mensajes de error del vinculador

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: No se pudieron vincular los ensamblados

Este error significa que el vinculador administrado ha encontrado un error inesperado, por ejemplo, una excepción, y no pudo completar o guardar el ensamblado que se está procesando. Más información sobre el error exacto formará parte del registro de compilación, por ejemplo,

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

Es importante archivar un informe de errores para estos problemas. En la mayoría de los casos, se puede proporcionar una solución alternativa hasta que se publique una corrección adecuada. La información anterior es crítica (junto con un caso de prueba o el ensamblado binairy) para resolver el problema.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: No se puede resolver la referencia: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Se omitirá la opción ' * ' porque la vinculación está deshabilitada

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: No se encontró el archivo de definiciones del vinculador adicional ' * '.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: No se pudieron analizar las definiciones de ' * '.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: No se puede cargar mscorlib. dll desde: *. Vuelva a instalar Xamarin. iOS.

Esto suele indicar que hay un problema con la instalación de Xamarin. iOS. Intente volver a instalar Xamarin. iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler `*`desconocido. Los valores válidos son HttpClientHandler (predeterminado), CFNetworkHandler o NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider `*`desconocido.  Los valores válidos son default o appletls.

El valor dado a `tls-provider=` no es un proveedor de TLS (seguridad de la capa de transporte) válido.

`default` Y`appletls` son los únicos valores válidos y ambos representan la misma opción, que consiste en proporcionar la compatibilidad con SSL/TLS mediante la API TLS nativa de Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler `*` no válido para watchos. El único valor válido es NSUrlSessionHandler.

Se trata de una advertencia que se produce porque el archivo de proyecto especifica un HttpMessageHandler no válido.

Las versiones anteriores de las herramientas de vista previa generaban de forma predeterminada un valor no válido en el archivo de proyecto.

Para corregir esta advertencia, abra el archivo de proyecto en un editor de texto y quite todos los nodos de HttpMessageHandler del XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: Opción TlsProvider `legacy` no válida. Se usará el único `appletls` valor válido.

El `legacy` proveedor, que era un proveedor solo SSLv3/TLSv1, totalmente administrado, ya no se incluye con Xamarin. iOS. Proyectos que usaban este antiguo proveedor y ahora se compilan `appletls` con el más reciente.

Para corregir esta advertencia, abra el archivo de proyecto en un editor de texto y quite todos los nodos ' MtouchTlsProvider ' ' del XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: No se pudo procesar la descripción de XML.

Esto significa que hay un error en el [archivo de configuración del vinculador XML personalizado](~/cross-platform/deploy-test/linker.md) que proporcionó. Revise el archivo.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: Se hace referencia\*al ensamblado ' ' desde dos ubicaciones diferentes:\*' ' y ' * '.

El ensamblado mencionado en el mensaje de error se carga desde varias ubicaciones. Asegúrese de usar siempre la misma versión de un ensamblado.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: No se puede cargar el ensamblado raíz ' * '

No se pudo cargar el ensamblado raíz. Compruebe que la ruta de acceso del mensaje de error hace referencia a un archivo existente y que se trata de un ensamblado .NET válido.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: No se pudo procesar `...`el optimizador de enlace.

Se produjo un error inesperado al intentar optimizar el código de enlace generado. El elemento que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado denominado (o que contiene el tipo o el método denominado) deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel `-v -v -v -v` de detalle habilitado (es decir, en los **argumentos Mtouch adicionales**).

El último dígito `x` será:
- `0`para un nombre de ensamblado;
- `1`para un nombre de tipo;
- `3`para un nombre de método;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: No se pudieron procesar `...`los recursos del usuario.

Se produjo un error inesperado al intentar quitar recursos de usuario. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

Los recursos de usuario son archivos incluidos en los ensamblados (como recursos) que deben extraerse, en tiempo de compilación, para crear el paquete de aplicaciones. Esto incluye:

- `__monotouch_content_*`y `__monotouch_pages_*` recursos; y
- Bibliotecas nativas incrustadas dentro de un ensamblado de enlace;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: No se pudo procesar `...`el establecedor predeterminado de HttpMessageHandler.

Se produjo un error inesperado al intentar `HttpMessageHandler` establecer el valor predeterminado de la aplicación. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel `-v -v -v -v` de detalle habilitado (es decir, en los **argumentos Mtouch adicionales**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: No se pudo procesar `...`el repaso del código.

Se produjo un error inesperado al intentar quitar el código de la distribución de BCL con la aplicación. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel `-v -v -v -v` de detalle habilitado (es decir, en los **argumentos Mtouch adicionales**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Error de procesamiento `...`del sellador.

Se produjo un error inesperado al intentar sellar tipos o métodos (final) o al desvirtualizar algunos métodos. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: No se pudo procesar `...`el reductor de metadatos.

Se produjo un error inesperado al intentar reducir los metadatos de la aplicación. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: Error de procesamiento `...`de MarkNSObjects.

Se produjo un error inesperado `NSObject` al intentar marcar las subclases de la aplicación. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Error de procesamiento `...`del insertador.

Se produjo un error inesperado al intentar insertar código desde la aplicación. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado ( `-v -v -v -v` es decir, en los **argumentos Mtouch adicionales**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Error de procesamiento `...`de Preservidor de conversión de enumeración inteligente.

Se produjo un error inesperado al intentar marcar los métodos de conversión de las enumeraciones inteligentes de la aplicación. El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado ( `-v -v -v -v` es decir, en los **argumentos Mtouch adicionales**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: No se puede resolver la\*referencia ' ', a la que se\*hace referencia desde el método ' ' en ' * '.

Se encontró una referencia de ensamblado no válida al procesar el método mencionado en el mensaje de error.

El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Error al procesar el método\*' ' en el ensamblado '\*': *

Se produjo un error inesperado al intentar marcar el método mencionado en el mensaje de error.

El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Error al procesar el\*ensamblado ' ': *

Se produjo un error inesperado al procesar un ensamblado.

El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: No se puede vincular{0}el ensamblado ' ' porque está en modo mixto.

El enlazador no puede procesar los ensamblados en modo mixto.

Vea https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies para obtener más información sobre los ensamblados en modo mixto.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Mensajes de error de AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: No se pudo el AOT en el ensamblado ' * '

Esto suele indicar un error en el compilador de AOT. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un proyecto que se pueda usar para reproducir el error.

A veces es posible solucionar este problema deshabilitando las compilaciones incrementales en la opción de compilación de iOS del proyecto (pero sigue siendo un error, por lo que debe notificarse de todos modos).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacksiosinternalslimitationsmdreverse-callbacks"></a>MT3002: Restricción de AOT: El método ' * ' debe ser estático, ya que se decora con [MonoPInvokeCallback]. Consulte [developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

Este mensaje de error procede del compilador de AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: En conflicto: opciones debug y--LLVM. La depuración de software está deshabilitada.

No se admite la depuración cuando LLVM está habilitado. Si necesita depurar la aplicación, deshabilite primero LLVM.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: No se pudo utilizar AOT en el ensamblado ' * ' porque no existe.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: No se encontró\*la dependencia ' ' del\*ensamblado ' '. Revise las referencias del proyecto.

Esto suele ocurrir cuando un ensamblado hace referencia a otra versión de un ensamblado de plataforma (normalmente la versión .NET 4 de mscorlib. dll).

Esto no se admite y puede que no se compile ni se ejecute correctamente (el ensamblado puede usar la API de la versión .NET 4 de mscorlib. dll que no tiene la versión de Xamarin. iOS).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: No se pudo calcular un mapa de dependencias completo para el proyecto. Esto dará lugar a tiempos de compilación más lentos porque Xamarin. iOS no puede detectar correctamente lo que se debe volver a generar (y lo que no es necesario volver a generarlo). Consulte las advertencias anteriores para obtener más detalles.

 compilar o ejecutar correctamente (el ensamblado puede usar la API de la versión .NET 4 de mscorlib. dll que no tiene la versión de Xamarin. iOS).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: Los archivos de información de depuración (*. mdb) no se cargarán cuando LLVM esté habilitado.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: La compatibilidad con Bitcode requiere el uso del back-end de LLVM AOT (--LLVM)

La compatibilidad con Bitcode requiere el uso del back-end de LLVM AOT (--LLVM).

Deshabilite la compatibilidad con Bitcode o habilite LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Mensajes de error de generación de código

### <a name="mt40xx-main"></a>MT40xx: Principal

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: No se pudo expandir la plantilla principal a `*`.

Se produjo un error al `main.m`generar. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: No se pudo compilar el código generado para los métodos P/Invoke. Registre un informe de errores en http://bugzilla.xamarin.com

No se pudo compilar el código generado para los métodos P/Invoke. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt41xx-registrar"></a>MT41xx: registrador

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: El registrador no puede crear una firma `*`para el tipo.

Se encontró un tipo en la API exportada que el runtime no sabe cómo calcular las referencias a y desde Objective-C.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: El registrador encontró un tipo `*` no válido en la `*`firma para el método. Utilice `*` en su lugar.

Actualmente, esto solo sucede con un tipo: System. DateTime. En su lugar, use el equivalente de Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: El registrador encontró un tipo `*` no válido en la `*`firma para el método: El tipo implementa INativeObject, pero no tiene un constructor que toma dos argumentos (IntPtr, bool)

Esto sucede cuando el registrador detecta un tipo en una firma con las características mencionadas. Es posible que el registrador tenga que crear nuevas instancias del tipo y, en este caso, requiere un constructor con la firma (IntPtr, bool): el primer argumento (IntPtr) especifica el identificador administrado, mientras que el segundo si el autor de la llamada tiene la propiedad de la instancia nativa identificador (si este valor es false, se llamará a "retain" en el objeto).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: El registrador no puede calcular las referencias del `*` valor devuelto para `*`el tipo en la Signatura del método.

Se encontró un tipo en la API exportada que el runtime no sabe cómo calcular las referencias a y desde Objective-C.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: El registrador no puede serializar el `*` parámetro de tipo en `*`la firma para el método.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: El registrador no puede serializar el valor `*` devuelto de la `*`estructura en la Signatura del método.

Se encontró un tipo en la API exportada que el runtime no sabe cómo calcular las referencias a y desde Objective-C.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: El registrador no puede serializar el `*` parámetro de tipo en `+`la firma para el método.

Se encontró un tipo en la API exportada que el runtime no sabe cómo calcular las referencias a y desde Objective-C.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: El registrador no puede obtener el tipo ObjectiveC para `*`el tipo administrado.

Se encontró un tipo en la API exportada que el runtime no sabe cómo calcular las referencias a y desde Objective-C.

Si cree que Xamarin. iOS debe admitir el tipo en cuestión, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: No se pudo compilar el código de registrador generado. Registre un informe de errores en http://bugzilla.xamarin.com

No se pudo compilar el código generado para el registrador. El registro de compilación contendrá la salida del compilador nativo, que explica por qué el código no se compila.

Este es siempre un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con el proyecto o un caso de prueba.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: El registrador no puede calcular las referencias del `*` parámetro de salida de `*`tipo en la firma para el método.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: El registrador no puede crear una firma `*` para el `*`tipo en el método.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrariosinternalsregistrarmd-for-more-information"></a>MT4112: El registrador encontró un tipo `*`no válido. No se admite el registro de tipos genéricos con Objective-C y puede dar lugar a comportamientos y/o bloqueos aleatorios (por compatibilidad con versiones anteriores de Xamarin. iOS es posible omitir este error pasando `--unsupported--enable-generics-in-registrar` como Mtouch adicionales? en la página Opciones de compilación de iOS del proyecto. Vea [Developer.Xamarin.com/guides/iOS/advanced_topics/registrar](~/ios/internals/registrar.md) para obtener más información).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: El registrador encontró un método genérico:\*'\*. '. No se admite la exportación de métodos genéricos y dará lugar a comportamientos y/o bloqueos aleatorios.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Error inesperado en el registrador del\*método\*'. ': registre un informe de errores en http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: No se pudo registrar el ensamblado ' * ': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: El registrador encontró un error de coincidencia de signatura en el método ' *.* '-el selector indica que el método toma * parámetros, mientras que el método administrado tiene * parámetros.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: No se pueden registrar dos tipos administrados (\*'\*' y ' ') con el mismo nombre nativo (' * ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: No se pudo registrar el selector\*' ' del miembro '\*. * ' porque el selector ya está registrado en un miembro diferente.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: El registrador encontró un tipo de campo\*desconocido ' ' en\*el campo '. * '. Registre un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: No se puede usar GCC/G + + para compilar el código generado desde el registrador estático cuando se usa el marco de cuentas (los archivos de encabezado proporcionados por Apple usados durante la compilación requieren Clang). Use Clang (--Compiler: Clang) o el registrador dinámico (--registrar: dinámico).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: No se puede usar el compilador Clang proporcionado en el *.* SDK para compilar el código generado desde el registrador estático cuando los nombres de tipo no ASCII (' * ') están presentes en la aplicación. Use GCC/G + + (--Compiler: GCC | G + +), el registrador dinámico (--registrar: dinámico) o un SDK más reciente.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: El tipo del parámetro variádicas en la función variádicas ' * ' debe ser System. IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: No se encuentra el * en ' * '. Registre un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: El registrador encontró un tipo no\*válido ' ' en la firma\*para el método ' ': La interfaz debe tener un atributo de protocolo que especifique su tipo de contenedor.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: No se pueden registrar dos protocolos administrados (\*'\*' y ' ') con el mismo nombre nativo (' * ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: No se puede registrar más de un método de interfaz para\*el método ' ' (que\*implementa ' ').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: El registrador encontró un tipo de parámetro genérico\*no válido ' ' en\*el método ' '. El parámetro genérico debe tener una restricción ' NSObject '.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: El registrador encontró un tipo de valor devuelto genérico no válido\*'\*' en el método ' '. El tipo de valor devuelto genérico debe tener una restricción ' NSObject '.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: El registrador no puede exportar métodos estáticos en clases genéricas (' * ').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: El registrador no puede exportar propiedades estáticas en clases\*genéricas ('.\*').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: El registrador encontró un tipo de valor devuelto genérico no válido\*'\*' en la propiedad ' '. El tipo de valor devuelto debe tener una restricción ' NSObject '.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: No se puede construir una instancia del tipo ' * ' desde Objective-C porque el tipo es genérico. [Excepción en tiempo de ejecución]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: La aplicación está usando el marco de trabajo "*", que no se incluye en el SDK de iOS que está usando para compilar la aplicación (este marco de trabajo se incorporó en iOS *, mientras se está creando con el SDK de iOS *). Seleccione un SDK más reciente en las opciones de compilación de iOS de la aplicación.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: El miembro '\*.\*' tiene un atributo de exportación que no especifica un selector. Se requiere un selector.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: El registrador no puede serializar el\*tipo de parámetro ' '\*del parámetro ' ' en\*el método '. * '

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: El registrador no puede serializar el\*tipo de propiedad ' '\*de la propiedad '. * '.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: El registrador no puede serializar el\*tipo de propiedad ' '\*de la propiedad '. * '. Las propiedades con el atributo [Connect] deben tener un tipo de propiedad de NSObject (o una subclase de NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: El registrador encontró un error de coincidencia de signatura en el método ' *.* '-el selector indica que el método variádicas toma * parámetros, mientras que el método administrado tiene * parámetros.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: No se puede registrar el\*selector ' ' en el\*miembro ' ' porque Xamarin. iOS registra implícitamente este selector.

Esto sucede cuando se subclase de un tipo de marco y se intenta implementar un método ' retain ', ' Release ' o ' dealloc ':

```csharp
class MyNSObject : NSObject
{
  [Export ("retain")]
  new void Retain () {}

  [Export ("release")]
  new void Release () {}

  [Export ("dealloc")]
  new void Dealloc () {}
}
```

Sin embargo, es posible invalidar estos métodos si la clase no es la primera subclase del tipo de marco:

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
  [Export ("retain")]
  new void Retain () {}

  [Export ("release")]
  new void Release () {}

  [Export ("dealloc")]
  new void Dealloc () {}
}
```

En este caso, Xamarin. `retain`iOS invalidará `dealloc` `release` y en `MyNSObject` la clase, y no habrá ningún conflicto.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: No se pudo registrar el tipo ' * '.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: No se pudo registrar la clase ObjectiveC "*", no parece derivar de ninguna clase ObjectiveC conocida (incluido NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: No se puede registrar el método ' * ' porque no tiene un Trampoline asociado. Registre un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enumeración ' * ' no válida: las enumeraciones con el atributo [Native] deben tener un tipo de enumeración subyacente de ' Long ' o ' ulong '.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: El parámetro de nombre del atributo de registrador de la\*clase ' '\*(' ') contiene un carácter no\*válido:\*' ' ().

El nombre de una clase Objectice-C no puede contener espacios en blanco, lo `Register` que significa que el atributo de la clase administrada correspondiente no puede tener un `Name` parámetro que no contenga espacios en blanco.

Compruebe que el `Register` atributo de la clase administrada mencionada en el mensaje de error no contiene ningún espacio en blanco.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Se detectó un protocolo que hereda del protocolo JSExport al usar el registrador dinámico. No es posible exportar protocolos a JavaScriptCore dinámicamente; se debe usar el registrador estático (agregue '--registrar: estático a los argumentos Mtouch adicionales en las opciones de compilación de iOS del proyecto para seleccionar el registrador estático).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: El registrador encontró un protocolo genérico: ' * '. No se admite la exportación de protocolos genéricos.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: No se puede registrar el\*método\*'. ' porque el tipo del primer parámetro (\*' ') no coincide con el tipo de categoría\*(' ').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: No se puede registrar el\*tipo ' ' porque la propiedad Type\*(' ') de su atributo de categoría no hereda de NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: No se puede registrar el tipo ' * ' porque la propiedad Type de su atributo category no está establecida.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: No se puede registrar el tipo ' * ' como una categoría porque implementa INativeObject o subclases NSObject.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: No se puede registrar el\*tipo ' ' como una categoría porque es genérico.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: No se puede registrar el\*método ' ' como un método de categoría porque es genérico.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: No se puede registrar el\*método ' ' con el\*selector ' ' como método de categoría en ' * ' porque el objetivo-C ya tiene una implementación para este selector.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: No se pueden registrar dos categorías\*(' '\*y ' ') con el mismo nombre nativo (' * ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: No se puede registrar el método\*de categoría ' ' porque se requiere al menos un parámetro (y su tipo debe coincidir\*con el tipo de categoría ' ')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: No se puede registrar el constructor * en la categoría * porque no se admiten constructores en categorías.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: No se puede registrar el método ' * ' como método de categoría porque los métodos de categoría deben ser estáticos.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Se encontró un\*carácter no\*válido ' ' ()\*en el selector\*' ' para ' '.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: El registrador encontró una estructura '\*' no admitida: Todos los campos de una estructura también deben ser estructuras (el\*campo ' ' con{2}el tipo ' ' no es una estructura).

El registrador encontró una estructura con campos no compatibles.

Todos los campos de una estructura que se expone a Objective-C también deben ser estructuras (no clases).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: El tipo '\*' (usado como * {2}) no está disponible en * * (se presentó en * *) compile\* con un SDK más reciente * (normalmente se realiza con la versión más reciente de Xcode).

El registrador encontró un tipo que no está incluido en el SDK actual.

Actualice Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Error interno en el registrador (*). Registre un informe de errores en http://bugzilla.xamarin.com

Este error indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: No se puede exportar la\*propiedad ' ' porque su\*selector ' ' es una palabra clave Objective-C. Use un nombre diferente.

El selector de la propiedad en cuestión no es un identificador válido de Objective-C.

Use un identificador válido de Objective-C como selectores.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: El registrador no pudo encontrar el tipo ' System. void ' en ninguno de los ensamblados a los que se hace referencia.

Lo más probable es que este error indique un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: No se puede registrar el\*método ' ' porque la firma contiene un\*tipo () que no es un tipo de referencia.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: No se puede registrar el\*método ' ' porque la firma contiene un tipo\*genérico () con un tipo de argumento genérico que no es una subclase NSObject (*).

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: No se puede registrar el tipo '\_{Managed name} ' porque su nombre de Objective-\_c ' {nombre exportado} ' es una palabra clave Objective-c. Use un nombre diferente.

El nombre de Objective-C para el tipo en cuestión no es un identificador válido de Objective-C.

Use un identificador válido de Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: No se pudo generar un contenedor P/Invoke para {Method}: {Message}

Xamarin. iOS no pudo generar una función contenedora P/Invoke para el mencionado.
Compruebe el mensaje de error indicado para la causa subyacente.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: El registrador no puede convertir de ' {Managed type} ' a ' {Native type} ' para el valor devuelto en el método {Method}.

Vea la descripción del error <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: El atributo Binderas del miembro {Member} no es válido: el tipo de enlace {Type} es diferente del tipo de propiedad {Type}.

Asegúrese de que el tipo del atributo Binderas coincide con el tipo del miembro al que está asociado.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: El registrador no puede convertir de ' {Native type} ' a ' {Managed type} ' para el parámetro ' {Parameter name} ' en el método {Method}.

El registrador no admite la conversión entre los tipos mencionados.

Se trata de un error en Xamarin. iOS si Xamarin. iOS proporciona la API en cuestión. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Si se encuentra en esto mientras desarrolla un proyecto de enlace para una biblioteca nativa, estamos abiertos para agregar compatibilidad con nuevas combinaciones de tipos. En este caso, envíe una solicitud de mejora en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba y la evaluaremos.

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Mensajes de error GCC y cadena de herramientas

### <a name="mt51xx-compilation"></a>MT51xx: Compilación

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Falta el compilador ' * '. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: No se pudo ensamblar el archivo ' * '. Registre un informe de errores en http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: No se pudo compilar el archivo ' * '. Registre un informe de errores en http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: No se encuentra ni el\*compilador ' ' ni\*' '. Instale el componente de las herramientas de línea de comandos de Xcode.

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: No se pudieron compilar los archivos ' * '. Registre un informe de errores en http://bugzilla.xamarin.com

Esto normalmente indica un error en Xamarin. iOS; Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt52xx-linking"></a>MT52xx: Vinculación

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Error de vinculación nativa. Revise el registro de compilación y las marcas de usuario proporcionadas a GCC: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Error de vinculación nativa. Revise el registro de compilación.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: ADVERTENCIA de vinculación nativa: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Error de vinculación nativa: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Error de vinculación nativa, símbolo sin definir: *. Compruebe que se ha hecho referencia a todos los marcos de trabajo necesarios y que las bibliotecas nativas están vinculadas correctamente en.

Esto sucede cuando el vinculador nativo no encuentra un símbolo al que se hace referencia en alguna parte. Hay varias razones por las que esto puede ocurrir:

- Un enlace de terceros requiere un marco, pero el enlace no lo especifica en su `[LinkWith]` atributo. Solución
  - Si es el autor del enlace de terceros o tiene acceso a su origen, modifique el atributo del `[LinkWith]` enlace para incluir el marco que necesita:

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - Si no puede modificar el enlace de terceros, puede vincular manualmente con el marco de trabajo necesario pasando `-gcc_flags '-framework SystemFramework'` a `mtouch` (esto se hace modificando los argumentos Mtouch adicionales en la página Opciones de compilación de iOS del proyecto). Recuerde que esto se debe hacer para cada configuración de proyecto.
- En algunos casos, un enlace administrado se compone de varias bibliotecas nativas y todas deben incluirse en los enlaces. Es posible tener más de una biblioteca nativa en cada proyecto de enlace, por lo que la solución consiste simplemente en agregar todas las bibliotecas nativas necesarias al proyecto de enlace.</li>
- Un enlace administrado hace referencia a símbolos nativos que no existen en la biblioteca nativa.
    Esto suele suceder cuando un enlace ha existido durante algún tiempo y el código nativo se ha modificado durante ese tiempo para que una clase nativa determinada se haya quitado o cambiado de nombre, mientras que el enlace no se ha actualizado.
- P/Invoke hace referencia a un símbolo nativo que no existe. A partir de Xamarin. iOS 7,4 se informará de un error <a href="#MT5214">MT5214</a> en este caso (consulte MT5214 para obtener más información).
- Una biblioteca o enlace de terceros se compiló con C++, pero el enlace no lo especifica en su `[LinkWith]` atributo. Normalmente, esto es bastante fácil de reconocer, ya que los símbolos tienen C++ símbolos alterados (un ejemplo común `__ZNKSt9exception4whatEv`es).
  - Si es el autor del enlace de terceros o tiene acceso a su origen, modifique el atributo del `[LinkWith]` enlace para establecer la `IsCxx` marca:

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - Si no puede modificar el enlace de terceros o si está vinculando manualmente con una biblioteca de terceros, puede establecer la marca equivalente pasando `-cxx` a Mtouch (esto se hace modificando los argumentos de Mtouch adicionales en la página Opciones de compilación de iOS del proyecto). . Recuerde que esto se debe hacer para cada configuración de proyecto.

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Error de vinculación nativa, clase de Objective-C \*sin definir:. No se encontró\*el símbolo ' ' en ninguna de las bibliotecas o marcos de trabajo vinculados a la aplicación.

Esto sucede cuando el vinculador nativo no puede encontrar una clase de Objective-C a la que se hace referencia en alguna parte. Hay varias razones por las que esto puede ocurrir: lo mismo que para [MT5210](#MT5210) y además:

- Un enlace de terceros enlaza un protocolo de Objective-C, pero no lo anotó con el `[Protocol]` atributo en su definición de API. Solución
  - Agregue el atributo `[Protocol]` que falta:

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Error de vinculación nativa, símbolo duplicado: *.

Esto sucede cuando el vinculador nativo detecta símbolos duplicados entre todas las bibliotecas nativas. Después de este error, puede haber uno o varios errores [MT5213](#MT5213) con la ubicación de cada aparición del símbolo. Causas posibles de este error:

- La misma biblioteca nativa se incluye dos veces.
- Dos bibliotecas nativas distintas se producen para definir los mismos símbolos.
- Una biblioteca nativa no se ha creado correctamente y contiene el mismo símbolo más de una vez.
  Para confirmarlo, use el siguiente conjunto de comandos de un terminal (reemplace i386 por x86_64/ARMv7/armv7s/arm64 según la arquitectura que esté compilando para):

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  Hay algunas formas posibles de solucionar este error:

  - Solicite que el proveedor de la biblioteca nativa lo corrija y proporcione la versión actualizada.
  - Corregirlo mediante la eliminación de los archivos de objeto adicionales (esto solo funciona si el problema es en realidad archivos de objeto duplicados)

  ```
  # Find out if the library is a fat library, and which
  # architectures it contains.
  lipo -info libNative.a

  # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
  lipo libNative.a -thin ARCH -output libNative.ARCH.a

  # Extract the object files for the offending architecture
  # This will remove the duplicates by overwriting them
  # (since they have the same filename)
  mkdir -p ARCH
  cd ARCH
  ar -x ../libNative.ARCH.a

  # Reassemble the object files in an .a
  ar -r ../libNative.ARCH.a *.o
  cd ..

  # Reassemble the fat library
  lipo *.a -create -output libNative.a
  ```

  - Pida al enlazador que quite el código no utilizado. Xamarin. iOS lo hará automáticamente si se cumplen todas las condiciones siguientes:
    - Todos los `[LinkWith]` atributos de los enlaces de terceros tienen habilitado Smartlink:

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - No `-gcc_flags` se pasa a Mtouch (en el campo de argumentos Mtouch adicionales de las opciones de compilación de iOS del proyecto).
    - También es posible solicitar directamente al enlazador que quite el código no utilizado agregando `-gcc_flags -dead_strip` a los argumentos Mtouch adicionales en las opciones de compilación de iOS del proyecto.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Símbolo duplicado en: * (ubicación relacionada con el error anterior)

Este error solo se muestra junto con [MT5212](#MT5212). Consulte [MT5212](#MT5212) para obtener más información.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Error de vinculación nativa, símbolo sin definir: *. Se hizo referencia a este símbolo en el miembro administrado *. Compruebe que se ha hecho referencia a todos los marcos de trabajo necesarios y que las bibliotecas nativas están vinculadas.

Este error se genera cuando el código administrado contiene una P/Invoke a un método nativo que no existe. Por ejemplo:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Hay algunas soluciones posibles:

- Quite las P/Invoke en cuestión del código fuente.
- Habilite el vinculador administrado para todos los ensamblados (esto se hace en las opciones de compilación de iOS del proyecto, estableciendo "comportamiento del enlazador" en "todos los ensamblados"). De este modo, se quitarán de forma efectiva todas las P/Invoke que no use desde la aplicación (de forma automática, en lugar de hacerlo manualmente como el punto anterior). El inconveniente es que esto hará que las compilaciones del simulador sean un poco más lentas y que se interrumpa la aplicación si se usa la reflexión; puede encontrar más información sobre el enlazador [aquí](~/ios/deploy-test/linker.md) ).
- Cree una segunda biblioteca nativa que contenga los símbolos nativos que faltan. Tenga en cuenta que esto es simplemente una solución alternativa (si se intenta llamar a esas funciones, se bloqueará la aplicación).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: Las referencias a ' * ' pueden requerir instrucciones-Framework = XXX o-lXXX para el vinculador nativo

Se trata de una advertencia que indica que se ha detectado una solicitud P/Invoke para hacer referencia a la biblioteca en cuestión, pero la aplicación no se vincula con ella.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Error de vinculación nativa para *. Registre un informe de errores en http://bugzilla.xamarin.com

Este error se genera al vincular la salida del compilador de AOT.

Lo más probable es que este error indique un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Posiblemente se produjo un error en la vinculación nativa porque la línea de comandos del vinculador era demasiado larga (* caracteres).

Error en la vinculación nativa y es posible que se haya producido porque el comando del enlazador era demasiado largo.

Los proyectos de Xamarin. iOS a menudo hacen referencia a símbolos nativos dinámicamente, lo que significa que el vinculador nativo podría quitar estos símbolos nativos durante el proceso de vinculación nativa, ya que el vinculador nativo no ve que se usan estos símbolos.

Normalmente, Xamarin. iOS solicitará al enlazador nativo que conserve dichos símbolos `-u symbol` mediante la marca del enlazador, pero si hay muchos símbolos de este tipo, toda la línea de comandos podría superar la longitud máxima de la línea de comandos especificada por el sistema operativo.

Existen algunos orígenes posibles para estos símbolos dinámicos:

- P/invoca a métodos en bibliotecas vinculadas estáticamente (donde el nombre de la `__Internal` dll está en el `[DllImport ("__Internal")]`atributo dllimport).
- Referencias de campo a ubicaciones de memoria en bibliotecas vinculadas estáticamente desde`[Field]` proyectos de enlace (atributos).
- Clases de Objective-C a las que se hace referencia en bibliotecas vinculadas estáticamente desde proyectos de enlace (cuando se usan compilaciones incrementales o cuando no se usa el registrador estático).

Soluciones posibles:

- Habilite el enlazador administrado (si es posible para todos los ensamblados en lugar de solo los ensamblados de SDK). Esto podría quitar suficientes orígenes de símbolos dinámicos para que la línea de comandos del vinculador no supere el máximo.
- Reduzca el número de P/invocacións, las referencias de campo y/o las clases de Objective-C.
- Vuelva a escribir los símbolos dinámicos para que tengan nombres más cortos.
- Pase `-dlsym:false` como argumento Mtouch adicional en las opciones de compilación de iOS del proyecto. Con esta opción, Xamarin. iOS generará una referencia nativa en el código compilado por el AOT y no tendrá que pedir al enlazador que mantenga este símbolo. Sin embargo, esto solo funciona para las compilaciones de dispositivos y producirá errores del vinculador si hay P/Invoke en funciones que no existen en la biblioteca estática.
- Pase `--dynamic-symbol-mode=code` como argumentos Mtouch adicionales en las opciones de compilación de iOS del proyecto. Con esta opción, Xamarin. iOS generará código nativo adicional que hace referencia a estos símbolos en lugar de solicitar al enlazador nativo que mantenga estos símbolos con los argumentos de la línea de comandos. El inconveniente de este enfoque es que aumentará el tamaño del archivo ejecutable en cierto modo.
- Habilite el registrador estático pasando `--registrar:static` como argumento Mtouch adicional en las opciones de compilación de iOS del proyecto (para las compilaciones del simulador, ya que el registrador estático ya es el valor predeterminado para las compilaciones de dispositivos). El registrador estático generará código que hace referencia a las clases de Objective-C de forma estática, por lo que no es necesario pedir al enlazador nativo que conserve dichas clases.
- Deshabilitar compilaciones incrementales (para compilaciones de dispositivos). Cuando se habilitan las compilaciones incrementales, el código generado por el registrador estático no se tendrá en cuenta en el enlazador nativo, lo que significa que Xamarin. iOS debe seguir solicitando al enlazador que mantenga las referencias a las clases de Objective-C. Por lo tanto, deshabilitar las compilaciones incrementales impedirá esta necesidad.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: No se puede omitir el símbolo dinámico {Symbol} (--ignore-Dynamic-Symbol = {Symbol}) porque no se detectó como un símbolo dinámico.

Se pasó el argumento `--ignore-dynamic-symbol=symbol` de línea de comandos, pero este símbolo no es un símbolo reconocido como símbolo dinámico que debe conservarse manualmente.

Hay dos razones principales para ello:

- El nombre del símbolo es incorrecto.
  - No anteponga un carácter de subrayado al nombre del símbolo.
  - El símbolo de las clases de Objective- `OBJC_CLASS_$_<classname>`C es.
- El símbolo es correcto, pero es un símbolo que ya se ha conservado por medios normales (algunas opciones de compilación hacen que la lista exacta de símbolos dinámicos varíe).

### <a name="mt53xx-other-tools"></a>MT53xx: Otras herramientas

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Falta la herramienta ' Strip '. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Falta la herramienta ' dsymutil '. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: No se pudieron generar los símbolos de depuración (directorio dSYM). Revise el registro de compilación.

Se produjo un error al ejecutar dsymutil en el directorio final. app para crear los símbolos de depuración. Revise el registro de compilación para ver el resultado de dsymutil y ver cómo puede corregirse.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: No se pudo quitar el archivo binario final. Revise el registro de compilación.

Se produjo un error al ejecutar la herramienta ' franja ' para quitar la información de depuración de la aplicación.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Falta la herramienta ' lipo '. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: No se pudo crear una biblioteca FAT. Revise el registro de compilación.

Se produjo un error al ejecutar la herramienta "lipo". Revise el registro de compilación para ver el error indicado por ' lipo '.

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: No se pudo firmar el archivo ejecutable. Revise el registro de compilación.

Se produjo un error al firmar la aplicación. Revise el registro de compilación para ver el error indicado por ' codesign '.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: Mtouch mensajes de error de herramientas internas

### <a name="mt600x-stripper"></a>MT600x: Eliminador

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: La versión de ejecución de Cecil no admite la eliminación de ensamblados

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: No se pudo quitar `*`el ensamblado.

Se produjo un error al quitar el código administrado (quitando el código IL) de los ensamblados de la aplicación.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [mensaje de UnauthorizedAccessException]

Error de seguridad al quitar símbolos de depuración de la aplicación.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Mensajes de error de MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: No se pudieron resolver las direcciones IP de host para la configuración del depurador de WiFi.

*Tarea MSBuild: DetectDebugNetworkConfigurationTaskBase*

Pasos para la solución de problemas:

- intente ejecutar `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (que debería proporcionar una dirección IP y no un error obviamente).
- intente ejecutar "ping \`hostname\`", que puede proporcionarle más información, como:`cannot resolve MyHost.local: Unknown host`

En algunos casos, se trata de un problema de "red local" y se puede solucionar agregando el host `127.0.0.1    MyHost.local` desconocido `/etc/hosts`en.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Este equipo no tiene ningún adaptador de red. Esto es necesario al depurar o generar perfiles en el dispositivo a través de WiFi.

*Tarea MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: La extensión de la aplicación ' * ' no contiene un archivo info. plist.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: La extensión de la aplicación ' * ' no especifica un CFBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: La extensión de la aplicación ' * ' no especifica un CFBundleExecutable.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: La extensión de la\*aplicación ' ' tiene un CFBundleIdentifier\*no válido (), no comienza con el CFBundleIdentifier del grupo de aplicaciones principal (*).

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: La extensión de la\*aplicación ' ' tiene un\*CFBundleIdentifier () que termina con el sufijo no válido '. Key '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: La extensión de la aplicación ' * ' no especifica un CFBundleShortVersionString.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: La extensión de la aplicación ' * ' tiene un archivo info. plist no válido: no contiene un diccionario de NSExtension.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: La extensión de la aplicación ' * ' tiene un archivo info. plist no válido: el Diccionario NSExtension no contiene un valor NSExtensionPointIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: La extensión de WatchKit ' * ' tiene un archivo info. plist no válido: el Diccionario NSExtension no contiene un diccionario NSExtensionAttributes.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: La extensión WatchKit ' * ' no tiene exactamente una aplicación de inspección.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: La extensión de WatchKit ' * ' tiene un archivo info. plist no válido: UIRequiredDeviceCapabilities debe contener la funcionalidad ' Watch-Companion '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: La aplicación de inspección ' * ' no contiene un archivo info. plist.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: La aplicación de inspección ' * ' no especifica un CFBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: La aplicación de inspección\*' ' tiene un CFBundleIdentifier no\*válido (), no comienza con el CFBundleIdentifier de grupo de aplicaciones principal (*).

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: La aplicación de inspección\*' ' no tiene un valor de UIDeviceFamily válido. Se esperaba ' Watch (4) ' pero se\* encontró ' (*) '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: La aplicación de inspección ' * ' no especifica un CFBundleExecutable

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: La aplicación de inspección\*' ' tiene un valor WKCompanionAppBundleIdentifier no válido\*(' '), no coincide con el CFBundleIdentifier del grupo de aplicaciones principal (' * ').

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: La aplicación de inspección ' * ' tiene un archivo info. plist no válido: la clave WKWatchKitApp debe estar presente y tener un valor de ' true '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: La aplicación de inspección ' * ' tiene un archivo info. plist no válido: la clave LSRequiresIPhoneOS no debe estar presente.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: La aplicación de inspección ' * ' no contiene una extensión de inspección.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: La extensión de inspección ' * ' no contiene un archivo info. plist.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: La extensión de inspección ' * ' no especifica un CFBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: La extensión de inspección ' * ' no especifica un CFBundleExecutable.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: La extensión de inspección\*' ' tiene un CFBundleIdentifier no\*válido (), no comienza con el CFBundleIdentifier de grupo de aplicaciones principal (*).

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: La extensión de inspección\*' ' tiene un CFBundleIdentifier\*() que termina con el sufijo no válido '. Key '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: La extensión de inspección ' * ' tiene un archivo info. plist no válido: no contiene un diccionario de NSExtension.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: La extensión de inspección ' * ' tiene un archivo info. plist no válido: NSExtensionPointIdentifier debe ser ' com. Apple. watchkit '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: La extensión de inspección ' * ' tiene un archivo info. plist no válido: el Diccionario NSExtension debe contener NSExtensionAttributes.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: La extensión de inspección "*" tiene un archivo info. plist no válido: el Diccionario NSExtensionAttributes debe contener WKAppBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: La extensión de WatchKit ' * ' tiene un archivo info. plist no válido: UIRequiredDeviceCapabilities no debe contener la funcionalidad ' Watch-Companion '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: La aplicación de inspección ' * ' no contiene un archivo info. plist.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: La aplicación de inspección ' * ' no especifica un CFBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: La aplicación de inspección\*' ' no tiene un valor de UIDeviceFamily válido. Se esperaba\*' ' pero se\* encontró\*' () '.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: La aplicación de inspección ' * ' no especifica un CFBundleExecutable.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: La extensión WatchKit ' {extensionName} ' tiene un valor de WKAppBundleIdentifier no válido\*(' '), no coincide con el CFBundleIdentifier de la aplicación Watch\*(' ').

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: La aplicación de inspección ' * ' tiene un archivo info. plist no válido: el WKCompanionAppBundleIdentifier debe existir y debe coincidir con el CFBundleIdentifier del grupo de aplicaciones principal.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: La aplicación de inspección ' * ' tiene un archivo info. plist no válido: la clave LSRequiresIPhoneOS no debe estar presente.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: El lote de aplicaciones {AppBundlePath} no contiene un archivo info. plist.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: La ruta de acceso principal de info. plist no especifica un CFBundleIdentifier.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: La ruta de acceso principal de info. plist no especifica un CFBundleExecutable.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: La ruta de acceso principal de info. plist no especifica un CFBundleSupportedPlatforms.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: La ruta de acceso principal de info. plist no especifica un UIDeviceFamily.

*Tarea MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Formato no reconocido: *.

*Tarea MSBuild: PropertyListEditorTaskBase*

Donde * puede ser:

- string
- array
- Diccionario
- booleano
- real
- integer
- date
- data

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Agregar: Entrada, *, especificada incorrectamente.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Agregar: La entrada, *, contiene un índice de matriz no válido.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: La entrada Add: * ya existe.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Agregar: No se puede Agregar la entrada, *, al elemento primario.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Eliminar: No se puede eliminar la entrada, *, del elemento primario.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Eliminar: La entrada, *, contiene un índice de matriz no válido.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Eliminar: La entrada, *, no existe.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Importar Entrada, *, especificada incorrectamente.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Importar La entrada, *, contiene un índice de matriz no válido.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Importar Error al leer el archivo: *.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Importar No se puede Agregar la entrada, *, al elemento primario.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Sin No se pueden agregar entradas de matriz a dict.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Sin La entrada especificada debe ser un contenedor.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Sin La entrada, *, contiene un índice de matriz no válido.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Sin La entrada, *, no existe.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Sin Error al leer el archivo: *.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Establezca: Entrada, *, especificada incorrectamente.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Establezca: La entrada, *, contiene un índice de matriz no válido.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Establezca: La entrada, *, no existe.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Acción del editor PropertyList desconocida: *.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Error al cargar ' * ': *.

*Tarea MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Error al guardar ' * ': *.

*Tarea MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Mensajes de error en tiempo de ejecución

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Discrepancia de versiones entre el tiempo de ejecución de Xamarin. iOS nativo y MonoTouch. dll. Vuelva a instalar Xamarin. iOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: No se encontró el método '\*' en el tipo '\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: No se pudo encontrar el método genérico cerrado\*' ' en el tipo\*' '.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: No se puede crear una instancia de * para el objeto nativo 0x * (de tipo ' * ') porque ya existe otra instancia para este objeto nativo (de tipo *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Falta la clase\*ObjectiveC nativa '\*' en el tipo de contenedor ' '.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: No se pudo encontrar el selector\*' ' en el tipo\*' '

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: No se puede obtener el descriptor de\*método para el selector '\*' en el tipo ' ', porque el selector no se corresponde con un método

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: La versión cargada de Xamarin. iOS. dll se compiló para * bits, mientras que el proceso es * bits. Registre un error en http://bugzilla.xamarin.com.

Esto indica que se ha producido un error en el proceso de compilación. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: No se encuentra el método de conversión de bloque al delegado para el método *.* ' s parámetro # *. Registre un error en http://bugzilla.xamarin.com.

Esto indica que una API no estaba enlazada correctamente. Si se trata de una API expuesta por Xamarin, registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new). Si se trata de un enlace de terceros, póngase en contacto con el proveedor.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Error de coincidencia de tamaño de tipo nativo entre Xamarin. [iOS | Mac]. dll y la arquitectura en ejecución. Xamarin. [iOS | Mac]. dll se compiló para * bits, mientras que el proceso actual es * bits.

Esto indica que se ha producido un error en el proceso de compilación. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: No se encuentra el delegado para bloquear el atributo de conversión ([DelegateProxy]) para el valor devuelto para el método *.* Registre un error en http://bugzilla.xamarin.com.

Xamarin. iOS no encontró un método necesario en tiempo de ejecución (para convertir un delegado en un bloque).

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute no válido para el valor devuelto para el método *.* : DelegateType es NULL. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy del método en cuestión no es válido.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute no válido para el valor devuelto para el método *.* : DelegateType ({2}) especifica un tipo sin un campo ' handler '. Registre un error en http://bugzilla.xamarin.com.

El `[DelegateProxy]` atributo del método en cuestión no es válido.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute no válido para el valor devuelto para el método *.* : El campo del controlador{2}de delegateType () es NULL. Registre un error en http://bugzilla.xamarin.com.

El `[DelegateProxy]` atributo del método en cuestión no es válido.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute no válido para el valor devuelto para el método *.* : El campo ' handler ' de{2}delegateType no es un delegado, es un *. Registre un error en http://bugzilla.xamarin.com.

El atributo DelegateProxy del método en cuestión no es válido.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: No se puede convertir el delegado en bloque para el valor devuelto para el método *.* , dado que la entrada no es un delegado, es un *. Registre un error en http://bugzilla.xamarin.com.

El `[DelegateProxy]` atributo del método en cuestión no es válido.

Esto normalmente indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Error de coherencia interna. Registre un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: No se pudo encontrar el ensamblado * en los ensamblados cargados.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: No se encontró el módulo con MetadataToken * en el ensamblado *.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Tipo de token implícito desconocido: *.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Se esperaba que la referencia de token * sea un *, pero es un *. Registre un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Un objeto de instancia es necesario para construir un método genérico cerrado para el método genérico abierto: * (referencia de token: *). Registre un informe de errores en http://bugzilla.xamarin.com.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: No se encontró un tipo de extensión válido para la enumeración inteligente ' {smart_type} '. Registre un error en https://bugzilla.xamarin.com.

Esto indica un error en Xamarin. iOS. Registre un nuevo problema en [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).
