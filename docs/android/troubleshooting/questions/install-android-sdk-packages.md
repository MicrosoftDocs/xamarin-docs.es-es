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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725065"
---
# <a name="which-android-sdk-packages-should-i-install"></a>¿Qué paquetes de Android SDK debo instalar?

La instalación de Android SDK no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque las necesidades individuales de los desarrolladores varían, normalmente se necesitarán los siguientes paquetes para desarrollar con Xamarin.Android:

## <a name="tools"></a>Herramientas

Instale las herramientas más recientes de la carpeta Tools en SDK Manager:

- Herramientas de Android SDK
- Herramientas de la plataforma de Android SDK
- Herramientas de compilación de Android SDK

## <a name="android-platforms"></a>Plataformas Android

Instale la plataforma del SDK correspondiente a las versiones de Android que ha establecido como mínima y de destino.

Ejemplos:

- API 23 de destino
- API 23 mínima

Solo es necesario instalar la plataforma del SDK para la API 23

- API 23 de destino
- API 15 mínima

Necesidad de instalar las plataformas del SDK de la API 15 y 23. Tenga en cuenta que no es necesario instalar los niveles de API situados entre mínimo y de destino (incluso si se va a pasar a esos niveles de API).

## <a name="system-images"></a>Imágenes del sistema

Solo son necesarias si quiere utilizar los emuladores Android listos para usar de Google. Para más información, consulte [Configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md).

## <a name="extras"></a>Extras
Los extras de Android SDK no suelen ser necesarios, pero resulta útil conocerlos, ya que pueden ser necesarios según el caso de uso.
