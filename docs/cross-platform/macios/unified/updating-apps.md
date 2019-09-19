---
title: Actualización de las aplicaciones existentes en el Unified API
description: Este documento contiene vínculos a varias guías que describen cómo actualizar las aplicaciones de Xamarin al Unified API. Se describen las aplicaciones de Xamarin. iOS, las aplicaciones de Xamarin. Mac. Aplicaciones de Xamarin. Forms, tipos nativos en aplicaciones multiplataforma y proyectos de enlace.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: c7742622bae16e874411fad1374c3ee522dba183
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106057"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Actualización de las aplicaciones existentes en el Unified API

> [!IMPORTANT]
> La Classic API de Xamarin, que precedía a la Unified API, ha quedado en desuso.
>
> - La última versión de Xamarin. iOS para admitir el Classic API (MonoTouch. dll) era Xamarin. iOS 9,10.
> - Xamarin. Mac sigue admitiendo el Classic API, pero ya no se actualiza. Puesto que está en desuso, los desarrolladores deben trasladar sus aplicaciones a la Unified API.

## <a name="how-to-update-your-apps"></a>Actualización de las aplicaciones

Hay tres pasos para actualizar las aplicaciones:

1. Corrija cualquier advertencia del compilador en el código existente, especialmente en relación con las API desusadas.

2. Use la herramienta de migración integrada en Visual Studio para Mac para actualizar los archivos de proyecto y los espacios de nombres.

3. Corrija los demás errores del compilador relacionados con los nuevos [tipos 64](~/cross-platform/macios/nativetypes.md) y [otras API](~/cross-platform/macios/unified/overview.md#deprecated-typos) que han cambiado. Consulte [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener información adicional sobre las actualizaciones manuales que podrían ser necesarias.

Hay guías específicas disponibles para cada producto que le ayudarán a actualizar las aplicaciones al soporte Unified API y 64 bits:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Aplicaciones de Xamarin. iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Las aplicaciones de Xamarin. iOS existentes pueden actualizarse a la Unified API mediante la herramienta de migración automatizada integrada en Visual Studio para Mac. Es posible que se requieran algunas correcciones adicionales, tal y como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-ios-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Aplicaciones de Xamarin. Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Las aplicaciones de Xamarin. Mac existentes pueden actualizarse a la Unified API mediante la herramienta de migración automatizada integrada en Visual Studio para Mac. Es posible que se requieran algunas correcciones adicionales, tal y como se explica en [estas instrucciones](~/cross-platform/macios/unified/updating-mac-apps.md) y [sugerencias](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Aplicaciones de Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Siga estas instrucciones para actualizar una solución existente de Xamarin. Forms con un proyecto de iOS para usar el Unified API. La compatibilidad con Unified API solo está disponible en Xamarin. Forms 1,3 y versiones posteriores, por lo que [las instrucciones](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) también explican cómo actualizar la aplicación de Xamarin. Forms a la versión 1,3. Estas [sugerencias](~/cross-platform/macios/unified/updating-tips.md) pueden ayudarle a actualizar cualquier código de iOS nativo en representadores personalizados o servicios de dependencia.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/nativetypes.md)

En este artículo se explica el uso de los nuevos tipos nativos de Unified API de iOS (NINT, nuint, nfloat) en una aplicación multiplataforma donde el código se comparte con dispositivos que no son iOS como Android o Windows Phone os. Proporciona información sobre cuándo se deben usar los tipos nativos y proporciona varias soluciones posibles a los casos en los que se debe usar el nuevo tipo con código multiplataforma.

## <a name="update-bindings-to-the-unified-api"></a>Actualizar enlaces a la Unified API

Los clientes que hayan creado enlaces a las bibliotecas de Objective-C deberán actualizar el proyecto de enlace para reflejar los cambios en la API subyacente (donde algunos tipos ahora serán de 64 bits).
Siga estas instrucciones para [actualizar un proyecto de enlace existente para que admita el Unified API](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Vínculos relacionados

- [Actualización de aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualización de aplicaciones Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizar enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Actualizar sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
