---
title: ¿Qué paquetes de Android SDK debo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725065"
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
