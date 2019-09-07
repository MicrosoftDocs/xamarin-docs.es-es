---
title: Plataforma de Apple (iOS y Mac)
description: 'En este documento se describen varios temas relacionados con el desarrollo de Xamarin. iOS y Xamarin. Mac: uso compartido de código, Unified API, bibliotecas de Objective-C de enlace, referencias nativas, tipos nativos y mucho más.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 10ab9b379344ab6c514eba84f1ef3fd9c7400b73
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765544"
---
# <a name="apple-platform-ios-and-mac"></a>Plataforma de Apple (iOS y Mac)

## <a name="code-sharing"></a>Uso compartido de código

En el caso de los elementos del código que no tienen elementos de interfaz de usuario, la mejor manera de compartir el código entre iOS y Mac sigue siendo el uso de [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md).

Para el código que tiene que hacer algún trabajo de la interfaz de usuario y, a pesar de ello, desea compartir, debe usar [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) que le permitan colocar código para compartirlo en un solo proyecto y compilarlo con Mac e iOS cuando se haga referencia a ellos.

## <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

El Unified API para los proyectos de iOS y Mac usa los mismos espacios de nombres para los marcos de modo que se pueda usar el mismo archivo de código en ambas plataformas, para el uso compartido de código sin problemas. También permite compilar 32 y 64 bits. El Unified API ha sido el valor predeterminado de la plantilla desde la primera 2015 y se recomienda para todos los proyectos nuevos, *solo* Unified API proyectos se pueden enviar a la tienda de aplicaciones.

### <a name="classic-apis"></a>API clásicas

> [!NOTE]
> **Desuso del perfil clásico:** A medida que se agregan nuevas plataformas en Xamarin. iOS, comenzamos a dejar de usar gradualmente las características del perfil clásico (MonoTouch. dll). Por ejemplo, se ha quitado la opción non-NRC (New-Ref-Count). NRC siempre se ha habilitado para todas las aplicaciones unificadas (es decir, no NRC nunca era una opción) y no tiene problemas conocidos. Las versiones futuras eliminarán la opción de usar Boehm como recolector de elementos no utilizados. Esto también era una opción que nunca estaba disponible para las aplicaciones unificadas. La eliminación completa del soporte técnico clásico está programada para el lanzamiento de 2016 con la versión de Xamarin. iOS 10,0.

Las API de Xamarin. iOS y Xamarin. Mac originales (no unificadas) han simplificado el uso compartido de código, ya que `MonoTouch.` los `MonoMac.` marcos nativos tenían prefijos de espacio de nombres o.  Proporcionamos algunos espacios de nombres vacíos que permiten a los desarrolladores compartir `using` código mediante la adición de instrucciones que hacen referencia a espacios de nombres MonoMac y MonoTouch en el mismo archivo, pero esto no era tan agradable. El Classic API solo debe seguir utilizándose en las aplicaciones heredadas que se distribuyen internamente (se recomienda actualizar al Unified API).

### <a name="updating-from-classic-to-the-unified-api"></a>Actualización del modelo clásico al Unified API

Hay instrucciones detalladas para actualizar cualquier aplicación del modelo clásico al Unified API.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Enlace de bibliotecas de Objective-C](binding/index.md)

Xamarin permite incorporar bibliotecas nativas a las aplicaciones con enlaces. En esta sección se explica:

- Cómo funcionan los enlaces,
- cómo compilar manualmente un proyecto de enlace que permite incorporar código de Objective-C a Xamarin y
- Cómo usar nuestra herramienta **Sharpie objetiva** para ayudar a automatizar el proceso.

## <a name="native-referencesnative-referencesmd"></a>[Referencias nativas](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos de Mac/iOS](nativetypes.md)

Para admitir el código de 32 y 64 bits de forma C# transparente F#desde y, estamos introduciendo nuevos tipos de datos.   Obtenga más información aquí.

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Compilación de aplicaciones de 32 y 64 bits](32-and-64/index.md)

Lo que necesita saber para admitir aplicaciones de 32 y 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabajo con tipos nativos en aplicaciones multiplataforma](native-types-cross-platform.md)

En este artículo se explica el uso de los nuevos tipos`nint`nativos `nuint`de `nfloat`Unified API de iOS (,,) en una aplicación multiplataforma donde el código se comparte con dispositivos que no son iOS como Android o Windows Phone os.
Proporciona información sobre cuándo se deben usar los tipos nativos y proporciona varias soluciones posibles a los casos en los que se debe usar el nuevo tipo con código multiplataforma.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Selector de pila HttpClient e implementación de SSL/TLS](http-stack.md)

El nuevo selector de pila HttpClient controla qué implementación de HttpClient se va a usar en la aplicación Xamarin. iOS, Xamarin. tvOS y Xamarin. Mac. Ahora puede cambiar a una implementación que usa los transportes nativos de iOS, tvOS o OS X (`NSUrlSession` o `CFNetwork` según el sistema operativo).

SSL (capa de sockets seguros) y su sucesor, TLS (seguridad de la capa de transporte), proporcionan compatibilidad con HTTP `System.Net.Security.SslStream`y otras conexiones de red a través de. La nueva opción de compilación de implementación de SSL/TLS cambia entre la pila de TLS propia de mono y una basada en la pila TLS de Apple presente en Mac e iOS.
