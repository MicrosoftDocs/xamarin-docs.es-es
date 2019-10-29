---
title: '¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0c777b8d5326963e959d8bb13d81d7058caa6bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030945"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?

## <a name="cause-of-the-error"></a>Causa del error

Este mensaje de error se produce cuando el proyecto en cuestión busca credenciales de firma de código válidas pero no puede encontrarlas. La firma de código es necesaria para las pruebas y las implementaciones en dispositivos iOS físicos; Además de las compilaciones ad-hoc & App Store.

### <a name="provisioning-devices"></a>Aprovisionamiento de dispositivos

Si no ha aprovisionado un dispositivo iOS antes, la guía siguiente le guiará a través del proceso completo paso a paso: Guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Error al usar el simulador de iOS

> [!NOTE]
> Este problema se ha resuelto en las versiones recientes de Xamarin para Visual Studio. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

Se produjo un error en Xamarin. Visual Studio 3,11 que hacía que el proyecto de iOS de una plantilla de Xamarin. Forms agregara los derechos de codesign. plist a las compilaciones del simulador; bloqueo eficaz de las pruebas con el simulador.

### <a name="how-to-fix"></a>Cómo corregir

Puede solucionar el problema quitando la marca de `<CodesignEntitlements>` de las compilaciones de depuración en el archivo. csproj. Puede hacerlo de la siguiente manera:

> [!WARNING]
> Los errores en los archivos. csproj pueden interrumpir el proyecto, por lo que es conveniente hacer una copia de seguridad de los archivos antes de intentar esto.

1. Haga clic con el botón derecho en el proyecto de iOS en el panel de la solución y seleccione **descargar el proyecto** .
2. Vuelva a hacer clic con el botón derecho en el proyecto y seleccione **Editar [nombreDeProyecto]. csproj**
3. Busque la propertyGroup de depuración, que debe comenzar con marcas que tengan el aspecto siguiente:
   - Depurar: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versión: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. En cada una de las compilaciones que usan el simulador, elimine o comente la propiedad siguiente: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Vuelva a cargar el proyecto y podrá implementarlo en el simulador.

### <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario .
