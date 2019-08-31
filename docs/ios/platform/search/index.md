---
title: Buscar API en Xamarin. iOS
description: En este artículo se describe el uso de las nuevas API de búsqueda de aplicaciones proporcionadas por iOS 9 para permitir que los usuarios busquen información y características dentro de las aplicaciones de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 344bc8f46ecad250c5021be4682f02489aa5cb96
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198452"
---
# <a name="search-apis-in-xamarinios"></a>Buscar API en Xamarin. iOS

_En este artículo se describe el uso de las API de búsqueda de aplicaciones que proporciona iOS 9 para permitir que los usuarios busquen información y características dentro de las aplicaciones de Xamarin. iOS._

La búsqueda se ha ampliado en iOS 9 para proporcionar nuevas formas de acceder a la información y las características dentro de una aplicación de Xamarin. iOS. Con las nuevas API de búsqueda de aplicaciones, se pueden realizar búsquedas en el contenido de la aplicación a través de los resultados de búsqueda de Spotlight y de Safari, los recordatorios y las sugerencias de entrega y Siri. Esto permite a los usuarios acceder rápidamente a las actividades y a la información en la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de la búsqueda en la aplicación sin una experiencia de implementación de búsqueda anterior. Por este motivo, Apple notifica que normalmente tarda unas horas en hacer que el contenido de una aplicación de iOS 9 se pueda buscar de forma universal mediante la búsqueda de aplicaciones.

[![](images/intro01.png "Un ejemplo de contenido de la aplicación de iOS 9 que se pueden buscar universalmente mediante la búsqueda de aplicaciones")](images/intro01.png#lightbox)

La búsqueda de aplicaciones se compone de tres API independientes:

1. [**NSUserActivity**](nsuseractivity.md) : se trata de una extensión de la API de entrega que Apple lanzó en iOS 8. Se usa para que el usuario pueda buscar el historial de interacción de la aplicación de forma pública y privada).

2. [**Spotlight principal**](corespotlight.md) : permite que una aplicación indexe su contenido para que se presente en los resultados de la búsqueda. Funciona como una API de base de datos donde se pueden agregar y quitar elementos y es la mejor manera de indexar el contenido privado dentro de una aplicación.

3. [**Webmarkup**](web-markup.md) : para las aplicaciones que proporcionan acceso a su contenido a través de una interfaz web (no solo desde dentro de la aplicación). El contenido web se puede marcar con vínculos especiales que va a rastrear Apple y proporcionar un vínculo profundo a la aplicación en el dispositivo iOS 9 del usuario.

## <a name="selecting-an-app-search-approach"></a>Selección de un enfoque de búsqueda de aplicaciones

Decidir cuál de estos métodos implementar depende de los tipos de interacción proporcionados por la aplicación y el tipo de contenido que presenta.

Utilice las siguientes directrices:

- [**NSUserActivity**](nsuseractivity.md) : Use este marco de trabajo para proporcionar la búsqueda de contenido público y privado y también la búsqueda de puntos de navegación dentro de la aplicación.

- [**Principales Spotlight**](corespotlight.md) : Use este marco de trabajo para proporcionar la búsqueda de datos privados almacenados en el dispositivo.

- [**Marcado web**](web-markup.md) : Use este marco de trabajo para permitir la búsqueda de aplicaciones que presenten su contenido no solo desde dentro de la aplicación, sino también desde el sitio web de la aplicación.

Cada uno de los enfoques de búsqueda de aplicaciones es distinto y se puede usar de forma individual; sin embargo, Apple los diseñó para trabajar juntos. Al usar más de un enfoque para indizar un elemento específico, asegúrese de usar el mismo **identificador de elemento** en cada enfoque, de modo que los vínculos individuales funcionen juntos.

El uso de más de un método no solo garantiza que el usuario final pueda encontrar el contenido, sino que también ayuda a mejorar la clasificación del elemento en la búsqueda.

Aunque el proceso de clasificación en su mayor parte es transparente para el desarrollador, la interacción del usuario con un determinado elemento pesa mucho en este rango (por ejemplo, el usuario punteando un vínculo).
Al proporcionar elementos completos e informativos, puede asegurarse de que se persuadirá a un usuario para que interactúe con el contenido, lo que eleva su clasificación.

## <a name="what-content-to-index"></a>Contenido que se va a indexar

Apple proporciona las siguientes sugerencias sobre el contenido y las acciones que deben proporcionar índices de búsqueda en la aplicación:

- Cualquier contenido visualizado, creado o seleccionada por el usuario desde dentro de la aplicación.
- Puntos de navegación y características dentro de la aplicación.
- Cosas como nuevos mensajes, contenido u otros tipos de elementos mostrados por la aplicación que se han descargado recientemente en el dispositivo.

## <a name="app-search-enhancements"></a>Mejoras en las búsquedas de aplicaciones

Los principales avances en iOS 10 proporcionan varias mejoras en la búsqueda de aplicaciones, como:

- **Micromecenazgo popularidad de vínculos profundos (con privacidad diferencial)** : proporciona una manera de promover el contenido de una aplicación vinculada en profundidad en los resultados de la búsqueda.
- **Búsqueda en la aplicación** : Use la nueva `CSSearchQuery` clase para proporcionar una capacidad de búsqueda de contenido destacado en la aplicación similar a la forma en que funcionan las aplicaciones mail, messages y Notes.
- **Continuación** de la búsqueda: permite que un usuario inicie una búsqueda en Spotlight o Safari, abra una aplicación y continúe con la búsqueda.
- **Visualización de los resultados de la validación** : la herramienta de validación de la [API de búsqueda de aplicaciones](https://search.developer.apple.com/appsearch-validation-tool) de Apple ahora muestra una representación visual del marcado de un sitio web y de la vinculación profunda al realizar pruebas.
- **Uso compartido de imágenes de aplicación de mensajes** : permite que las imágenes de la aplicación populares que se proporcionan para compartir mensajes (a través de una extensión de aplicación de mensajes) aparezcan en búsquedas de Spotlight.

Para obtener más información, consulte nuestra guía de mejoras en la [búsqueda de aplicaciones](~/ios/platform/search/app-search-enhancements.md) .

### <a name="proactive-suggestions"></a>Sugerencias proactivas

iOS 10 presenta nuevas formas de impulsar la interacción con una aplicación, ya que permite que el sistema presente de forma proactiva información útil al usuario en los momentos adecuados. Del mismo modo que iOS 9 proporcionó la capacidad de agregar una búsqueda en profundidad a la aplicación mediante las sugerencias de Spotlight, de entrega y Siri, con iOS 10 una aplicación puede exponer la funcionalidad que el sistema puede presentar al usuario desde las siguientes ubicaciones:

- El conmutador de aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType 

Una aplicación expone esta funcionalidad al sistema mediante una colección de tecnologías como [NSUserActivity](xref:Foundation.NSUserActivity), marcado Web, Spotlight principal, MapKit, Media Player y UIKit.

Para obtener más información, consulte nuestra guía [proactiva de sugerencias](~/ios/platform/search/proactive-suggestions.md) .

## <a name="summary"></a>Resumen

En este artículo se han tratado las nuevas características de la API de búsqueda que iOS 9 proporciona para las aplicaciones de Xamarin. iOS. Trata de los métodos de [marcado web](web-markup.md) , [NSUserActivity](nsuseractivity.md), [Spotlight principales](corespotlight.md) y de indexación del contenido. Finalizó con una breve explicación de Cuándo debe usarse un enfoque de búsqueda determinado y qué tipos de contenido se deben indexar.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de aplicaciones](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
