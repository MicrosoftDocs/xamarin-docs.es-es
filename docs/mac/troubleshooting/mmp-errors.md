---
title: Mensajes de error de Xamarin. Mac (MMP)
description: En este documento se enumeran los errores generados por MMP, la herramienta que se usa para empaquetar los ensamblados compilados en una aplicación Mac ejecutable.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: b1125ab554bd9c73917c106c7486b742f16e628a
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940362"
---
# <a name="xamarinmac-error-messages-mmp"></a>Mensajes de error de Xamarin. Mac (MMP)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: MMP mensajes de error

Por ejemplo parámetros, entorno, herramientas que faltan.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000: error inesperado. archivo un informe de errores en https://github.com/xamarin/xamarin-macios/issues/new

Se produjo una condición de error inesperada. Registre [un informe de errores](https://github.com/xamarin/xamarin-macios/issues/new) con tanta información como sea posible, incluidos:

* Registros de compilación completos, con el máximo nivel de detalle (por ejemplo, `-v -v -v -v` en los **argumentos MMP adicionales**);
* Un caso de prueba mínimo que reproduce el error; etc
* Toda la información de versión

La forma más fácil de obtener información exacta sobre la versión es usar el menú de **Xamarin Studio** , **acerca de Xamarin Studio** elemento, mostrar el botón **detalles** y copiar y pegar la información de versión (puede usar el botón **copiar información** ).

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: esta versión de Xamarin. Mac requiere {0} mono (la versión de mono actual es {1}). Actualice mono. Framework desde http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: el nombre de la aplicación '{0}. exe ' está en conflicto con un nombre de ensamblado de SDK o de producto (. dll).

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: el ensamblado raíz '{0}' no existe

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: debe proporcionar solo un ensamblado raíz, encontrado {0} ensamblados: '{1}'

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009: error al cargar los ensamblados: *.

Se produjo un error al cargar los ensamblados de las referencias de ensamblado raíz. Se puede proporcionar más información en la salida de la compilación.

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: no se pudieron analizar los argumentos de la línea de comandos: {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: la opción '{0}' está en desuso.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: debe proporcionar un ensamblado raíz.

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: argumento de línea de comandos desconocido: '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: las opciones válidas para '{0}' son '{1}'.

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: el nombre de la aplicación '{0}. exe ' entra en conflicto con otro ensamblado de usuario.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: no se pudo analizar el argumento de la línea de comandos '{0}': {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: no se admite el recolector de elementos no utilizados Boehm. En su lugar, se ha seleccionado el recolector de elementos no utilizados de SGen.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: no se puede proporcionar un ensamblado raíz si se pasa--no-root-Assembly.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: se requiere un directorio de salida (--Output) si se pasa--no-root-Assembly.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: no se puede deshabilitar el nuevo refcount con el Unified API.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: no se puede encontrar Xcode en ninguna de las ubicaciones predeterminadas. Instale Xcode o pase una ruta de acceso personalizada mediante--SDKRoot =\<ruta de acceso >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: no se encontró la Xcode seleccionada actualmente en el sistema: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: no se encontró la Xcode seleccionada actualmente en el sistema. ' Xcode-Select--Print-path ' devolvió '{0}', pero ese directorio no existe.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: valor no válido para la versión de .NET Framework de destino: {0}.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: plataforma desconocida: *. Normalmente, esto indica un error en Xamarin. Mac. Registre un informe de errores en https://bugzilla.xamarin.com con un caso de prueba.

Normalmente, esto indica un error en Xamarin. Mac. Registre un informe de errores en [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) con un caso de prueba.

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073: Xamarin. Mac \* no admite un destino de implementación de \* (el mínimo es \*). Seleccione un destino de implementación más reciente en el archivo info. plist del proyecto.

El destino de implementación mínimo es el especificado en el mensaje de error; Seleccione un destino de implementación más reciente en el archivo info. plist del proyecto.

Si no es posible actualizar el destino de implementación, use una versión anterior de Xamarin. Mac.

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074: Xamarin. Mac \* no admite un destino de implementación de \* (el máximo es \*). Seleccione un destino de implementación anterior en el archivo info. plist del proyecto o actualice a una versión más reciente de Xamarin. Mac.

Xamarin. Mac no admite el establecimiento del destino de implementación mínimo en una versión posterior a la versión para la que se compiló esta versión concreta de Xamarin. Mac.

Seleccione un destino de implementación mínimo anterior en el archivo info. plist del proyecto o actualice a una versión más reciente de Xamarin. Mac.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: error interno: no se ha copiado ningún ejecutable en el lote de aplicaciones. Póngase en contacto con 'support@xamarin.com'.

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: deshabilitar NewRefCount,--New-refcount: false, está en desuso.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: esta versión de Xamarin. Mac requiere el SDK de \* (incluido en Xcode \*). Actualice Xcode para obtener los archivos de encabezado necesarios o use el registrador dinámico o establezca el comportamiento del vinculador administrado en vincular solo los SDK de plataforma o vínculo (para evitar las nuevas API).

Xamarin. Mac requiere los archivos de encabezado, desde la versión del SDK especificada en el mensaje de error, para compilar la aplicación con el registrador estático. La manera recomendada de corregir este error es actualizar Xcode para obtener el SDK necesario, que incluirá todos los archivos de encabezado necesarios. Si tiene varias versiones de Xcode instaladas o desea usar Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación de Xcode correcta en las preferencias del IDE.

Una solución alternativa posible es habilitar el vinculador administrado. Esto eliminará la API sin usar, incluida, en la mayoría de los casos, la nueva API en la que faltan los archivos de encabezado (o están incompletos). Sin embargo, esto no funcionará si el proyecto usa la API que se presentó en un SDK más reciente que el que proporciona Xcode.

En su lugar, una segunda solución alternativa posible es usar el registrador dinámico. Esto impondrá un costo de inicio al registrar los tipos dinámicamente, pero quitar el requisito de archivo de encabezado.

Una solución última sería usar una versión anterior de Xamarin. Mac, una que admita el SDK que requiere el proyecto.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: no se puede encontrar el archivo Machine. config '{0}'.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: la compilación de AOT solo está disponible en Unified

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MM0099: {0}de error interno. Registre un informe de errores con un caso de prueba (https://bugzilla.xamarin.com).

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: la compilación de AOT híbrida requiere que todos los ensamblados estén compilados por AOT.

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129: el archivo de símbolos de depuración para ' * ' no coincide con el ensamblado y se omite.

No se pudieron cargar los símbolos de depuración, ya sea. pdb (solo PDB portable) o un archivo. mdb, para el ensamblado especificado.

Normalmente, esto significa que el ensamblado es más reciente o más antiguo que los símbolos. Dado que no coinciden, no se pueden usar y se omiten los símbolos.

Esta advertencia no afectará a la aplicación que se está compilando; sin embargo, es posible que no pueda depurarla completamente (en particular, el código del ensamblado especificado). Además, las excepciones, los seguimientos de la pila y los informes de bloqueos podrían faltar alguna información.

Notifique este problema al publicador del paquete de ensamblado (por ejemplo, autor de NuGet), por lo que se puede corregir en sus versiones futuras.

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130: no se encontraron ensamblados raíz. Debe proporcionar al menos un ensamblado raíz.

Al ejecutar `--runregistrar`, se debe proporcionar al menos un ensamblado raíz.

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131: no se encuentra el ensamblado del producto '{0}' en la lista de ensamblados: '{1}'

Al ejecutar `--runregistrar`, la lista de ensamblados debe incluir el ensamblado del producto, Xamarin. Mac, XamMac.

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132: optimización desconocida: \*. Los valores válidos son: \*

No se reconoció la optimización especificada.

El formato aceptado es `[+|-]optimization-name`, donde `optimization-name` es uno de los valores enumerados en el mensaje de error.

Consulte [optimizaciones de compilación](~/cross-platform/macios/optimizations.md) para obtener una descripción completa de cada optimización.

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133: se encontró más de un ensamblado que coincida con '{0}' eligiendo primero: '{1}'

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134: las aplicaciones de 32 bits se deben migrar a 64 bits.

Apple ha anunciado que no permitirá el envío de la tienda de aplicaciones de macOS de aplicaciones de 32 bits (a partir del 2018 de enero).

Además, las aplicaciones de 32 bits no se ejecutarán en la versión de macOS después del alta Sierra "sin comprometer".

Para obtener más información: https://developer.apple.com/news/?id=06282017a

Considere la posibilidad de actualizar la aplicación y las dependencias a 64 bits.

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135: no ha vinculado el marco de trabajo del sistema '{0}' (al que hace referencia el ensamblado '{1}') porque se presentó en {2} {3}y usamos el SDK de {2} {4}.

Para compilar la aplicación, Xamarin. Mac debe vincularse a las bibliotecas del sistema, algunas de las cuales dependen de la versión del SDK especificada en el mensaje de error. Dado que está usando una versión anterior del SDK, es posible que se produzcan errores en las invocaciones a esas API en tiempo de ejecución.

La manera recomendada de corregir este error es actualizar Xcode para obtener el SDK necesario. Si tiene varias versiones de Xcode instaladas o desea usar Xcode en una ubicación no predeterminada, asegúrese de establecer la ubicación de Xcode correcta en las preferencias del IDE.

También puede habilitar el [vinculador](https://docs.microsoft.com/xamarin/mac/deploy-test/linker) administrado para quitar las API sin usar, incluidas (en la mayoría de los casos) las nuevas que requieren la biblioteca especificada. Sin embargo, esto no funcionará si el proyecto requiere API introducidas en un SDK más reciente que el que proporciona Xcode.

Como última solución, use una versión anterior de Xamarin. Mac que no requiera que estos nuevos SDK estén presentes durante el proceso de compilación.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx: copia de archivos/vínculos simbólicos (relacionados con el proyecto)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: no se pudo crear el symlink ' {file} '-> ' {target} ': error {Number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: ensamblados de productos

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: falta el ensamblado '{0}' necesario en las referencias

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: el ensamblado '{0}' no es compatible con esta herramienta

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: no se pudo encontrar el {0} '{1}'. La versión de .NET Framework de destino '{0}' no se puede usar para empaquetar la aplicación.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: la plataforma de destino '{0}' no es válida.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework siempre debe tener como destino .NET Framework 4,5, no '{0}', que no es válido

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targeting-xamarinmac-45-net-framwork"></a>MM1406: la plataforma de destino '{0}' no es válida cuando el destino es Xamarin. Mac 4,5 .NET Framework.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: error de coincidencia entre la referencia de Xamarin. Mac '{0}' y la plataforma de destino seleccionada '{1}'.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: errores de recopilación de ensamblados (que no requieren vinculador)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: no se puede resolver la referencia: {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: no es una biblioteca dinámica de c O (encabezado desconocido ' 0x{0}'): {1}.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: no es una biblioteca estática (encabezado desconocido '{0}'): {1}.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: no es una biblioteca dinámica de c O (encabezado desconocido ' 0x{0}'): {1}.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: formato desconocido para la entrada FAT en la posición {0} en {1}.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: el archivo de tipo {0} no es un archivo MachO ({1}).

## <a name="mm2xxx-linker"></a>MM2xxx: vinculador

### <a name="mm20xx-linker-general-errors"></a>MM20xx: errores del vinculador (general)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: no se pudieron vincular los ensamblados

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: no se puede resolver la referencia: {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: se omitirá la opción '{0}' porque la vinculación está deshabilitada

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: no se encontró el archivo de definiciones del vinculador extra '{0}'.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: no se pudieron analizar las definiciones de '{0}'.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: se hizo referencia a la biblioteca nativa '{0}', pero no se pudo encontrar.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Xamarin. Mac Unified API en un perfil completo de .NET no admite la vinculación. Pase la marca-nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: al que hace referencia {0}.{1}\*\* este mensaje está relacionado con MM2006 \*\*

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: `{0}`de HttpMessageHandler desconocido. Los valores válidos son HttpClientHandler (predeterminado), CFNetworkHandler o NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider '{0}desconocido.  Los valores válidos son default o appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: solo se muestra la primera {0} de {1} advertencias "a las que se hace referencia". \*\* este mensaje relacionado con 2009 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: no se pudo resolver la referencia a "{0}", a la que se hace referencia en "{1}". La aplicación no incluirá el ensamblado al que se hace referencia y puede producir un error en tiempo de ejecución.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: las extensiones de Xamarin. Mac no admiten la vinculación. Se omitirá la solicitud de vinculación. \*\* este mensaje está obsoleto en XM 3.6 + \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: opción de TlsProvider `{0}` no válida. Se utilizará el único valor válido `{1}`.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: no se pudo procesar la descripción de XML: {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: no se pudo procesar el optimizador de enlace `...`.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Xamarin. Mac Classic API no admite la vinculación de plataforma.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: error al procesar el ensamblado '\*': *

Se produjo un error inesperado al procesar un ensamblado.

El ensamblado que causa el problema se denomina en el mensaje de error. Para corregir este problema, el ensamblado deberá proporcionarse en un informe de [errores](https://bugzilla.xamarin.com) junto con un registro de compilación completo con el nivel de detalle habilitado (es decir, `-v -v -v -v` en los **argumentos Mtouch adicionales**).

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: no se puede vincular el ensamblado '{0}' porque está en modo mixto.

El enlazador no puede procesar los ensamblados en modo mixto.

Consulte https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies para obtener más información sobre los ensamblados en modo mixto.

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106: no se pudo optimizar la llamada a BlockLiteral. SetupBlock [Unsafe] en \* en el desplazamiento \* porque \*.

El enlazador notifica esta advertencia cuando no puede optimizar una llamada a `BlockLiteral.SetupBlock` o `Block.SetupBlockUnsafe`.

El mensaje apuntará al método que llama a `BlockLiteral.SetupBlock[Unsafe]`y también puede proporcionar pistas sobre el motivo por el que no se pudo optimizar la llamada.

Registre un [problema](https://github.com/xamarin/xamarin-macios/issues/new) junto con un registro de compilación completo para que podamos investigar qué salió mal y, posiblemente, habilitar más escenarios en el futuro.

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107: no es seguro quitar el registrador dinámico porque {reasons}

El enlazador notifica esta advertencia cuando el desarrollador solicita la eliminación del registrador dinámico (pasando `--optimize:remove-dynamic-registrar` a MMP), pero el enlazador determina que no es seguro hacerlo.

Para quitar la advertencia, quite el argumento de optimización de MMP, o bien pase `--nowarn:2107` para omitirlo.

De forma predeterminada, esta opción se habilitará automáticamente siempre que sea posible y segura.

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108: '{0}' se quitó de las arquitecturas excepto '{1}' para cumplir con las restricciones de la tienda de aplicaciones. Esto podría interrumpir las firmas de codiseño existentes. Considere la posibilidad de quitar la biblioteca con Lipo o deshabilitarla con--Optimize =-Trim-Architectures ");

La tienda de aplicaciones ahora rechaza las aplicaciones que contienen bibliotecas y marcos que contienen variantes de 32 bits. La biblioteca se ha quitado de las arquitecturas no utilizadas cuando se copió en el lote de aplicaciones final.

Esto es seguro en general y reducirá el tamaño de la agrupación de aplicaciones como ventaja adicional. Sin embargo, cualquier marco de trabajo agrupado con firma de código tendrá su firma invalidada (y se volverá a firmar más adelante si la aplicación está firmada).

Considere la posibilidad de usar `lipo` para quitar las arquitecturas innecesarias de forma permanente de la biblioteca de origen. Si la aplicación no se publica en la tienda de aplicaciones, esta eliminación se puede deshabilitar pasando `--optimize=-trim-architectures` como argumentos MMP adicionales.

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109: Xamarin. Mac Classic API no admite la vinculación de plataforma.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: errores de AOT (general)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: no se pudo AOT el ensamblado '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: se solicitó el AOT de '{0}', pero no se encontró

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: se solicitó la exclusión del AOT de '{0}', pero no se encontró

## <a name="mm4xxx-code-generation"></a>MM4xxx: generación de código

### <a name="mm40xx-driverm"></a>MM40xx: driver. m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: no se pudo expandir la plantilla principal a `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx: registrar

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: la aplicación usa el marco de trabajo '{0}', que no se incluye en el SDK de MacOS que está usando para compilar la aplicación (este marco de trabajo se incorporó en OSX {2}, mientras está creando con el SDK de {1} de MacOS). Esta configuración no es compatible con el registrador estático (Pass--registrar: Dynamic como argumento MMP adicional en la opción de compilación de Mac del proyecto para seleccionar). También puede seleccionar un SDK más reciente en las opciones de compilación de Mac de la aplicación.

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173: el registrador no puede calcular la firma de bloque para el delegado de tipo {Delegate-Type} en el método {Method} porque *.

Se trata de una advertencia que indica que el registrador no pudo insertar la firma de bloque del método especificado en el código del registrador generado, porque el registrador no pudo calcularlo.

Esto significa que la firma de bloque tiene que calcularse en tiempo de ejecución, que es algo más lento.

Actualmente hay dos razones posibles para esta ADVERTENCIA:

1. El tipo del delegado administrado es `System.Delegate` o `System.MulticastDelegate`. Estos tipos no representan una firma específica, lo que significa que el registrador no puede calcular la firma nativa correspondiente. En este caso, la solución consiste en usar un tipo de delegado específico para el bloque (alternativamente, se puede omitir la advertencia agregando `--nowarn:4173` como argumento MMP adicional en las opciones de compilación de Mac del proyecto).
2. El registrador no encuentra el método `Invoke` del delegado. Esto no sucede, por lo que debe archivar un [problema](https://github.com/xamarin/xamarin-macios/issues/new) con un proyecto de prueba para poder corregirlo.

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174: no se encuentra el método de conversión de bloque a delegado para el parámetro {Method} del método {{Parameter}.

Se trata de una advertencia que indica que el registrador estático no pudo encontrar el método para crear un delegado para un bloque de Objective-C. Se realizará un intento en tiempo de ejecución para encontrar el método, pero probablemente también se producirá un error (con una excepción MT8009).

Una posible razón para esta advertencia es la escritura manual de enlaces para una API que usa bloques. Se recomienda usar un proyecto de enlace para enlazar código de Objective-C, en particular cuando se trata de bloques, ya que es bastante complicado hacerlo bien al hacerlo manualmente.

Si no es el caso, registre un [problema](https://github.com/xamarin/xamarin-macios/issues/new) con un caso de prueba.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC y cadena de herramientas

### <a name="mm51xx-compilation"></a>MM51xx: compilación

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: falta el compilador '{0}'. Instale el componente de las herramientas de línea de comandos de Xcode.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: no se pudo compilar. Código de error: {0}. Registre un informe de errores en http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx: vinculación

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: no se encuentra el MDK mono. Framework. Instale MDK para la versión de mono. Framework desde http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: no se encuentra libxammac. a, probablemente debido a una instalación dañada de Xamarin. Mac. Vuelva a instalar Xamarin. Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204: arquitectura no válida. x86_64 solo se admite en perfiles no clásicos.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205: arquitectura no válida '{0}'. Las arquitecturas válidas son i386 y x86_64 (When--Profile = Mobile).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: no se puede omitir el símbolo dinámico {Symbol} (--ignore-Dynamic-Symbol = {Symbol}) porque no se detectó como un símbolo dinámico.

Vea la [ADVERTENCIA Mtouch equivalente](~/ios/troubleshooting/mtouch-errors.md#MT5218).

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>MM53xx: otras herramientas

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>No se encontró MM5301: pkg-config. Instale mono. Framework desde http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: falta la herramienta ' otool '. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: faltan las dependencias. Instale el componente de las herramientas de línea de comandos de Xcode.

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: puede que no se haya aceptado el contrato de licencia de Xcode.  Inicie Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309: error de vinculación nativa con el código de error 1. Busque detalles en el registro de compilación.

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>Error de MM5310: install_name_tool con un código de error "{0}". Busque detalles en el registro de compilación.

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>Error de MM5311: lipo con un código de error '{0}'. Busque detalles en el registro de compilación.

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx: tiempo de ejecución

### <a name="mm800x-misc"></a>MM800x: varios

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: no se admite el recolector de elementos no utilizados Boehm. En su lugar, use SGen.

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025: no se pudo calcular la referencia de token para el tipo ' {Type. AssemblyQualifiedName} ' porque {reasons}

Esto indica un error en Xamarin. Mac. Registre un error en [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac).

Una posible solución alternativa sería deshabilitar la optimización de `register-protocols`, pasando `--optimize:-register-protocols` como un argumento MMP adicional en las opciones de compilación de Mac del proyecto.

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>MM8026: * no se admite cuando el registrador dinámico se ha vinculado.

Esto normalmente indica un error en Xamarin. Mac, ya que el registrador dinámico no debe estar vinculado si es necesario. Registre un error en [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

Es posible forzar que el vinculador mantenga el registrador dinámico agregando `--optimize=-remove-dynamic-registrar` a los argumentos MMP adicionales en las opciones de compilación de Mac del proyecto.
