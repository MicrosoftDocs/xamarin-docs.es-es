---
title: Servicios de licencia de Google
ms.prod: xamarin
ms.assetid: E96BDCC3-454A-A797-5819-905E2BB1AC41
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/20/2017
ms.openlocfilehash: 4cc7f3b36c43d9c9bb611b7d669f8304aab05820
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021263"
---
# <a name="google-licensing-services"></a>Servicios de licencia de Google

Antes de Google Play, las aplicaciones de Android se basaban en la protección contra copia heredada proporcionada por Google Market para garantizar que solo los usuarios autorizados pudieran ejecutar aplicaciones en sus dispositivos. Las limitaciones del mecanismo de protección contra copia la convertían en una solución que no era óptima para la protección de aplicaciones.

Licencias de Google es un sustituto de este mecanismo de protección contra copia heredado.
Licencias de Google es un servicio basado en la red, seguro y flexible que las aplicaciones de Android pueden consultar para determinar si una aplicación tiene licencia para ejecutarse en un dispositivo determinado.

Licencias de Google es flexible en el sentido de que las aplicaciones de Android tienen el control total sobre el momento de comprobar la licencia, la frecuencia de comprobación de la licencia y la manera de controlar la respuesta del servidor de licencias.

Licencias de Google es seguro en que cada respuesta se firma con un par de claves RSA que se comparte de manera exclusiva entre el servidor de Google Play y la aplicación. Google Play proporciona una clave pública para desarrolladores que está insertada dentro de la aplicación de Android y que se usa para autenticar las respuestas. El servidor de Google Play mantiene la clave privada de manera interna.

Una aplicación que tiene Licencias de Google implementadas realiza una solicitud a un servicio hospedado mediante la aplicación de Google Play en el dispositivo. Después, Google Play envía esta solicitud al servidor de Licencias de Google, que responde con el estado de la licencia: 

[![Diagrama de flujo de trabajo del servidor de licencias](google-licensing-services-images/gp-licensing-service-overview.png)](google-licensing-services-images/gp-licensing-service-overview.png#lightbox)

En el diagrama anterior se muestra este flujo de trabajo: 

- La aplicación proporciona el nombre del paquete, un *nonce* (un autenticador criptográfico) que se usa para validar la respuesta del servidor y una devolución de llamada que puede controlar la respuesta de forma asincrónica. 

- Google Play proporciona información como la cuenta de Google y el propio dispositivo, como el número IMSI. 

El servicio de Licencias de Google también es un componente clave de los archivos de expansión APK (que se tratarán posteriormente en este documento). Los archivos de expansión APK usan los servicios de Licencias de Google para obtener las direcciones URL de los archivos de expansión que se descargarán.

## <a name="requirements"></a>Requisitos

Las aplicaciones que se han adquirido a través de Google Play no recibirán ningún beneficio de los servicios de Licencias de Google. Si Google Play no está instalado en un dispositivo, entonces las aplicaciones que usen los Servicios de licencias seguirán funcionando normalmente en ese dispositivo.

Google Play necesita acceso a Internet para realizar esta función. Una aplicación puede almacenar en caché la licencia para incluir escenarios en los que el dispositivo no tenga acceso a los servidores de licencias de Google Play.

Las aplicaciones gratuitas solo necesitan Licencias de Google cuando la aplicación usa archivos de expansión APK.
