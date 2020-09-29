---
title: SiriKit en Xamarin. iOS
description: En este artículo se muestra cómo usar SiriKit en una aplicación de Xamarin. iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c50d02b4fc806a9ce466da450cfeb6afaa68df93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433697"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit en Xamarin. iOS

_En este artículo se muestra cómo usar SiriKit en una aplicación de Xamarin. iOS para proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS._

Novedad de iOS 10, SiriKit permite que una aplicación de iOS proporcione servicios que son accesibles para el usuario mediante Siri y la aplicación Maps en un dispositivo iOS con las extensiones de aplicación y **los nuevos marcos** de interfaz de usuario de intents y **intents** .

Siri funciona con el concepto de **dominios**, grupos de acciones conocidas para tareas relacionadas. Cada interacción que tiene una aplicación con Siri debe encontrarse en uno de sus dominios de servicio conocidos de la siguiente manera:

- Llamada de audio o vídeo.
- Reservar un viaje.
- Administrar entrenamientos.
- Correo.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implica a uno de los servicios de una extensión de aplicación, SiriKit envía a la extensión un objeto de **intención** que describe la solicitud del usuario junto con los datos auxiliares. A continuación, la extensión de la aplicación genera el objeto de **respuesta** adecuado para el **objetivo**dado, lo que detalla cómo la extensión puede controlar la solicitud.

## <a name="understanding-sirikit-concepts"></a>[Descripción de los conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

En este artículo se tratan los conceptos clave necesarios para trabajar con SiriKit en una aplicación de Xamarin. iOS. Abarca los nuevos puntos de extensión de la interfaz de usuario de intents y de intents, y cómo funcionan con la aplicación y el vocabulario del usuario para abrir una aplicación en Siri.

## <a name="implementing-sirikit"></a>[Implementación de SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

En este artículo se describen los pasos necesarios para implementar la compatibilidad con SiriKit en las aplicaciones de Xamarin. iOS. El desarrollador debe leer la guía de introducción a los conceptos de SiriKit antes de intentar agregar compatibilidad con SiriKit a una aplicación, ya que se tratan los conceptos clave que se requerirán para una implementación correcta.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](/samples/xamarin/ios-samples/ios10-elizachat)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de trabajo](https://developer.apple.com/reference/intents)
- [Referencia del marco de interfaz de usuario de Intent](https://developer.apple.com/reference/intentsui)