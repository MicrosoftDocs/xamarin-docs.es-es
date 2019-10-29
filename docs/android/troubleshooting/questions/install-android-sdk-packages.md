---
title: ¿Qué paquetes de Android SDK debo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: d48767ea29c5f610e02fade20ae92c5e085cc9be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026962"
---
# <a name="which-android-sdk-packages-should-i-install"></a>¿Qué paquetes de Android SDK debo instalar?

La instalación del Android SDK no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque las necesidades individuales de los desarrolladores varían, normalmente se necesitarán los siguientes paquetes para desarrollar con Xamarin. Android:

## <a name="tools"></a>Herramientas

Instale las herramientas más recientes de la carpeta Tools en SDK Manager:

- Herramientas de Android SDK
- Herramientas de Android SDK Platform
- Herramientas de compilación de Android SDK

## <a name="android-platforms"></a>Plataforma (s) Android

Instale la "plataforma de SDK" para las versiones de Android que ha establecido como & destino mínimo. 

Ejemplos:

- API de destino 23
- API 23 como mínimo

Solo es necesario instalar la plataforma SDK para la API 23

- API de destino 23
- API 15 como mínimo

Necesidad de instalar plataformas de SDK para las API 15 y 23. Tenga en cuenta que no es necesario instalar los niveles de API entre el mínimo y el destino (incluso si se traslada a esos niveles de API).

## <a name="system-images"></a>Imágenes del sistema

Solo son necesarios si desea utilizar los emuladores de Android listos para usar de Google. Para obtener más información, consulte [Android Emulator Setup](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Los extras Android SDK no suelen ser necesarios; pero resulta útil ser consciente de ellos, ya que pueden ser necesarios según el caso de uso.

## <a name="further-reading"></a>Información adicional
En la guía siguiente se describen estas opciones y se incluyen más detalles sobre los diferentes paquetes que el administrador de SDK tiene disponible: [Guía de instalación de Android SDK Manager](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)
