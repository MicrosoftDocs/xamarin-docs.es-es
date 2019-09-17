---
title: '¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: e10a04627b903c02140a6a2ead5c379c1e8bdcf6
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021389"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?

## <a name="cause-of-the-error"></a>Causa del error

Este mensaje de error se produce cuando el proyecto en cuestión busca credenciales de firma de código válidas pero no puede encontrarlas. La firma de código es necesaria para las pruebas y las implementaciones en dispositivos iOS físicos; Además de las compilaciones ad-hoc & App Store.

### <a name="provisioning-devices"></a>Aprovisionamiento de dispositivos

Si no ha aprovisionado un dispositivo iOS antes, la guía siguiente le guiará a través del proceso completo paso a paso: [Guía de aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Error al usar el simulador de iOS

> [!NOTE]
> Este problema se ha resuelto en las versiones recientes de Xamarin para Visual Studio. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

Se produjo un error en Xamarin. Visual Studio 3,11 que hacía que el proyecto de iOS de una plantilla de Xamarin. Forms agregara los derechos de codesign. plist a las compilaciones del simulador; bloqueo eficaz de las pruebas con el simulador.

### <a name="how-to-fix"></a>Cómo corregir

Puede solucionar el problema quitando la `<CodesignEntitlements>` marca de las compilaciones de depuración en el archivo. csproj. Puede hacerlo de la siguiente manera:

> [!WARNING]
> Los errores en los archivos. csproj pueden interrumpir el proyecto, por lo que es conveniente hacer una copia de seguridad de los archivos antes de intentar esto.

1. Haga clic con el botón derecho en el proyecto de iOS en el panel de la solución y seleccione **descargar el proyecto** .
2. Vuelva a hacer clic con el botón derecho en el proyecto y seleccione **Editar [nombreDeProyecto]. csproj**
3. Busque la propertyGroup de depuración, que debe comenzar con marcas que tengan el aspecto siguiente:
   - Depura`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Emisión`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. En cada una de las compilaciones que usan el simulador, elimine o comente como comentario la siguiente propiedad:`<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Vuelva a cargar el proyecto y podrá implementarlo en el simulador.

### <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario .
