---
title: Preguntas más frecuentes sobre Xamarin. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5e258f350256945c7794ee67f814d30d09894c9a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432169"
---
# <a name="ios-frequently-asked-questions"></a>Preguntas más frecuentes sobre iOS

## <a name="general-questions"></a>Preguntas generales

### <a name="can-i-use-a-mac-vm-with-xamarin"></a>[¿Puedo usar una máquina virtual Mac con Xamarin?](mac-vm.md)
Sí, pero solo en el hardware de Mac.

### <a name="how-can-i-downgrade-xcode"></a>[¿Cómo se puede cambiar a una versión de anterior de Xcode?](./previous-xcode.md)
En esta guía se proporcionan vínculos para tener acceso a versiones anteriores de Xcode, así como a la versión más reciente.

### <a name="where-can-i-set-my-ios-sdk-locations"></a>[¿Dónde puedo configurar mi ubicaciones de SDK de iOS?](ios-sdk.md)
Para la mayoría de los usuarios, se establecen automáticamente en las ubicaciones adecuadas. En esta guía se enumeran las ubicaciones predeterminadas del SDK y cómo cambiarlas si es necesario.

### <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>[¿Cómo puedo volver a habilitar las opciones del desarrollador después de actualizar iOS?](update-developer-options.md)
Un error de iOS puede hacer que las opciones del desarrollador desaparezcan después de actualizar las versiones de iOS, lo que se ha observado al cambiar a iOS 8. x. En esta guía se describe cómo se pueden volver a habilitar las opciones.

### <a name="user-location-not-working-in-ios-8"></a>[La ubicación de usuario no funciona en iOS 8](ios8-user-location.md)
En esta guía se explica cómo editar info. plist para habilitar la ubicación del usuario en iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>[¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?](symbolicate-ios-crash.md)
En esta guía se describen los pasos básicos para los registros de bloqueo de iOS de symbolicating para ayudar a diagnosticar bloqueos. También incluye vínculos a recursos adicionales para las técnicas de símbolos más avanzadas & información sobre la interpretación de los registros de bloqueo de iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>[¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?](xs-mono-runtime.md)
Si tiene que establecer variables de entorno de tiempo de ejecución para mono, se pueden establecer en las **Opciones del proyecto > ejecutar > página general** .

## <a name="publishing-questions"></a>Publicación de preguntas

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>[Error al enviar a la tienda de aplicaciones: "no se permiten las opciones de agrupación no válidas que se van a insertar en Bitcode en el envío".](invalid-bundle-bitcode.md)

El envío de aplicaciones que _requieran_ Bitcode, como watchos y aplicaciones de tvOS, debe realizarse con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-file"></a>[¿Se puede cambiar la ruta de acceso de salida del archivo IPA?](ipa-output-path.md)
A partir de Xamarin Cycle 7, puede usar destinos de MSBuild personalizados para lograr esto.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>[¿Cómo puedo copiar archivos de salida de IPA en la carpeta de entrega de TFS?](ipa-tfs.md)
Sí, en esta guía se describe cómo hacerlo.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>[¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?](modify-ipa.md)
Sí, es posible, pero normalmente será necesario volver a firmar la `.app` agrupación después de realizar el cambio. Tenga en cuenta que `.ipa` no es necesario modificar el archivo en el uso normal. Este artículo se proporciona únicamente con fines informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>[¿Es posible crear un archivo. xcarchive desde Visual Studio?](create-xcarchive.md)
A partir de Xamarin 4, ahora es posible crear `.xcarchive` desde Windows estableciendo la `ArchiveOnBuild` propiedad en `true` .

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>[¿Por qué se produce un error en el envío de la aplicación: "no se permiten las rutas de acceso (" iTunesMetadata. plist ") en..."?](itunesmetadata-disallowed-paths.md)
Este error es el resultado de un cambio en el proceso de comprobación de App Store de Apple. Este error específico _no_ está relacionado con la versión concreta de Xamarin que ha instalado, por lo que la degradación _no_ servirá de ayuda. En esta guía se proporciona un vínculo a más información sobre cómo corregir el problema.

## <a name="diagnosing-specific-error-messages"></a>Diagnóstico de mensajes de error específicos

### <a name="ios-designer-error-with-registerserviceport"></a>[Error de iOS Designer con RegisterServicePort](error-registerserviceport.md)
Los errores de `RegisterServicePort` y los mensajes de error similares a los anteriores suelen ser un problema con spyware o malware en el equipo. En esta guía se detallan las confirmaciones del diagnóstico y la información sobre la eliminación de spyware o malware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>[¿Por qué se produce un error en la compilación de iOS: no se encontraron claves de firma de código de iPhone válidas en la cadena de claves?](no-codesigning-keys.md)
Este mensaje de error se produce cuando el proyecto en cuestión busca credenciales de firma de código válidas pero no puede encontrarlas. La firma de código es necesaria para las pruebas y las implementaciones en dispositivos iOS físicos; Además de las compilaciones ad-hoc & App Store.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>[¿Por qué se produce un error en la aplicación de iOS 9: System. Exception: error al serializar el objeto de Objective-C?](exception-marshal-obj-c.md)
Los cambios de la API en iOS 9 requieren que se use un constructor de devolución de llamada al llamar a código no administrado, ya que la API subyacente espera ahora.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>[Error de tiempo de ejecución: no se encontró o no se pudo cargar el mscorlib.dll de ensamblado](error-mscorlib-not-found.md)
Este problema se produce cuando *hidden* `.monotouch-32` `.monotouch-64` faltan las carpetas Hidden y en `.xcarchive` para la creación de firmas/IPA, lo que desencadena el error de tiempo de ejecución.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>[Error de compilación: no se puede codificar el desplazamiento X en la reubicación distribuida resultante](error-encode-offset-scattered-relocation.md)
Este problema se produce al compilar para arquitecturas de 32 bits, como ARMv7, cuando el archivo binario final es demasiado grande para la cadena de herramientas nativa.

## <a name="deprecated"></a>Obsoleto

> [!IMPORTANT]
> Los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

### <a name="ipa-file-is-0-bytes"></a>[El archivo IPA es de 0 bytes](ipa-zero-bytes.md)
Hubo algunos problemas conocidos en las versiones anteriores de Xamarin que podrían provocar que el archivo IPA en Windows fuese 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completed"></a>[Error IBTool: no se pudo completar la operación.](error-ibtool.md)
Apple corrigió este `ibtool` error en Xcode 6.1.1, por lo que la solución más sencilla es actualizar a Xcode 6.1.1 o superior.

### <a name="error-mt1009-could-not-copy-the-assembly"></a>[Error MT1009: no se pudo copiar el ensamblado](error-mt1009.md)
Esto afecta a los usuarios que ejecutan Xamarin. iOS 7.2.6. Este problema se debe a los permisos de archivo que necesitan más privilegios cuando Xamarin. iOS se instala con una cuenta de usuario diferente a la cuenta principal del desarrollador.

### <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>[System.Exception AMDeviceNotificationSubscribe returned ...](exception-amddevicenotificationsubscribe.md)
Este mensaje puede aparecer en un cuadro de diálogo de error cuando se inicia por primera vez Visual Studio para Mac o en el `mtbserver.log` archivo. Tenga en cuenta que se trata de un problema poco frecuente. Si Visual Studio tiene problemas para conectarse al host de compilación de Mac, hay otros errores que es más probable que aparezcan en el `mtbserver.log` archivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>[MDocArchiveToMsxDocConverter.exe no encontró rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Este error puede aparecer en el `Mac Server Log` en Visual Studio.