---
title: Preguntas más frecuentes sobre Xamarin. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: a8549e03c96a5e21f7a235064ebd72fc671cf2b8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769281"
---
# <a name="ios-frequently-asked-questions"></a>Preguntas más frecuentes sobre iOS

## <a name="general-questions"></a>Preguntas generales

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[¿Puedo usar una máquina virtual Mac con Xamarin?](mac-vm.md)
Sí, pero solo en el hardware de Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[¿Cómo se puede cambiar a una versión de anterior de Xcode?](downgrade-xcode.md)
En esta guía se proporcionan vínculos para tener acceso a versiones anteriores de Xcode, así como a la versión más reciente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[¿Dónde puedo configurar mi ubicaciones de SDK de iOS?](ios-sdk.md)
Para la mayoría de los usuarios, se establecen automáticamente en las ubicaciones adecuadas. En esta guía se enumeran las ubicaciones predeterminadas del SDK y cómo cambiarlas si es necesario.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[¿Cómo puedo volver a habilitar las opciones del desarrollador después de actualizar iOS?](update-developer-options.md)
Un error de iOS puede hacer que las opciones del desarrollador desaparezcan después de actualizar las versiones de iOS, lo que se ha observado al cambiar a iOS 8. x. En esta guía se describe cómo se pueden volver a habilitar las opciones.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[La ubicación de usuario no funciona en iOS 8](ios8-user-location.md)
En esta guía se explica cómo editar info. plist para habilitar la ubicación del usuario en iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?](symbolicate-ios-crash.md)
En esta guía se describen los pasos básicos para los registros de bloqueo de iOS de symbolicating para ayudar a diagnosticar bloqueos. También incluye vínculos a recursos adicionales para las técnicas de símbolos más avanzadas & información sobre la interpretación de los registros de bloqueo de iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[¿Cómo se pueden establecer variables en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?](xs-mono-runtime.md)
Si tiene que establecer variables de entorno de tiempo de ejecución para mono, se pueden establecer en las **Opciones del proyecto > ejecutar > Página general** .

## <a name="publishing-questions"></a>Publicación de preguntas

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Error al enviar a la tienda de aplicaciones: "No se permite la inserción de opciones de agrupación no válidas en Bitcode en el envío".](invalid-bundle-bitcode.md)

El envío de aplicaciones que _requieran_ Bitcode, como watchos y aplicaciones de tvOS, debe realizarse con Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[¿Se puede cambiar la ruta de acceso de salida del archivo IPA?](ipa-output-path.md)
A partir de Xamarin Cycle 7, puede usar destinos de MSBuild personalizados para lograr esto.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[¿Cómo puedo copiar archivos de salida de IPA en la carpeta de entrega de TFS?](ipa-tfs.md)
Sí, en esta guía se describe cómo hacerlo.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?](modify-ipa.md)
Sí, es posible, pero normalmente será necesario volver a firmar la `.app` agrupación después de realizar el cambio. Tenga en cuenta que no `.ipa` es necesario modificar el archivo en el uso normal. Este artículo se proporciona únicamente con fines informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[¿Es posible crear un archivo. xcarchive desde Visual Studio?](create-xcarchive.md)
A `.xcarchive` partir de Xamarin 4, ahora es posible crear desde Windows estableciendo la `ArchiveOnBuild` propiedad en `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[¿Por qué se produce un error "Disallowed paths ( "iTunesMetadata.plist" ) found at ..." al enviar mi aplicación?](itunesmetadata-disallowed-paths.md)
Este error es el resultado de un cambio en el proceso de comprobación de App Store de Apple. Este error específico _no_ está relacionado con la versión concreta de Xamarin que ha instalado, por lo que la degradación _no_ servirá de ayuda. En esta guía se proporciona un vínculo a más información sobre cómo corregir el problema.

## <a name="diagnosing-specific-error-messages"></a>Diagnóstico de mensajes de error específicos

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Error de iOS Designer con RegisterServicePort](error-registerserviceport.md)
Los errores de y los mensajes de error similares a los anteriores suelen ser un problema con spyware o malware en el equipo. `RegisterServicePort` En esta guía se detallan las confirmaciones del diagnóstico y la información sobre la eliminación de spyware o malware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?](no-codesigning-keys.md)
Este mensaje de error se produce cuando el proyecto en cuestión busca credenciales de firma de código válidas pero no puede encontrarlas. La firma de código es necesaria para las pruebas y las implementaciones en dispositivos iOS físicos; Además de las compilaciones ad-hoc & App Store.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[¿Por qué se produce un error System.Exception: Failed to marshal the Objective-C object en mi aplicación para iOS 9?](exception-marshal-obj-c.md)
Los cambios de la API en iOS 9 requieren que se use un constructor de devolución de llamada al llamar a código no administrado, ya que la API subyacente espera ahora.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Error en tiempo de ejecución: The assembly mscorlib.dll was not found or could not be loaded](error-mscorlib-not-found.md)
Este problema se produce cuando faltan `.monotouch-64` las carpetas *Hidden* `.monotouch-32` y en `.xcarchive` para la creación de firmas/IPA, lo que desencadena el error de tiempo de ejecución.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[Error de compilación: No se puede codificar el desplazamiento X en la reubicación distribuida resultante](error-encode-offset-scattered-relocation.md)
Este problema se produce al compilar para arquitecturas de 32 bits, como ARMv7, cuando el archivo binario final es demasiado grande para la cadena de herramientas nativa.

## <a name="deprecated"></a>En desuso

> [!IMPORTANT]
> Los artículos siguientes se aplican a los problemas que se han resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[El archivo IPA es de 0 bytes](ipa-zero-bytes.md)
Hubo algunos problemas conocidos en las versiones anteriores de Xamarin que podrían provocar que el archivo IPA en Windows fuese 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Error de IBTool: The operation couldn’t be completed.](error-ibtool.md)
Apple [corrigió](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) este `ibtool` error en Xcode 6.1.1, por lo que la solución más sencilla es actualizar a Xcode 6.1.1 o superior.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Error MT1009: Could not copy the assembly](error-mt1009.md)
Esto afecta a los usuarios que ejecutan Xamarin. iOS 7.2.6. Este problema se debe a los permisos de archivo que necesitan más privilegios cuando Xamarin. iOS se instala con una cuenta de usuario diferente a la cuenta principal del desarrollador.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe returned ...](exception-amddevicenotificationsubscribe.md)
Este mensaje puede aparecer en un cuadro de diálogo de error cuando se inicia por primera vez `mtbserver.log` Visual Studio para Mac o en el archivo. Tenga en cuenta que se trata de un problema poco frecuente. Si Visual Studio tiene problemas para conectarse al host de compilación de Mac, hay otros errores que es más probable que aparezcan en `mtbserver.log` el archivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe no encontró rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Este error puede aparecer en el `Mac Server Log` en Visual Studio.
