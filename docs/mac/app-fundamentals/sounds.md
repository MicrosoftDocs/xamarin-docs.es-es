---
title: Reproducir sonido con AVAudioPlayer en Xamarin. Mac
description: En este documento se describe cómo reproducir sonido con AVAudioPlayer en una aplicación de Xamarin. Mac. Describe AVAudioPlayer en un nivel alto y vínculos a otra documentación que lo explora más completamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: b4a5ead3e3c02fbdd2ae5486a6ac637defeb5abd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283303"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reproducir sonido con AVAudioPlayer en Xamarin. Mac

## <a name="about-the-avaudioplayer"></a>Acerca de AVAudioPlayer

La `AVAudioPlayer` clase se usa para reproducir datos de audio de cualquier memoria o de un archivo. Apple recomienda el uso de esta clase para reproducir audio en la aplicación a menos que esté realizando streaming de red o requiera e/s de audio de baja latencia.

Puede utilizar la `AVAudioPlayer` clase para hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucles opcionales.
- Reproducir varios sonidos al mismo tiempo con la sincronización opcional.
- Controle el volumen, la velocidad de reproducción y la posición estéreo de cada sonido que se reproduce.
- Compatibilidad con características como el avance rápido o el rebobinado.
- Obtener datos de medición de nivel de reproducción.

`AVAudioPlayer`admite sonidos en cualquier formato de audio proporcionado por iOS, tvOS y macOS, como. AIF,. WAV o. mp3.

## <a name="playing-sounds-in-macos"></a>Reproducir sonidos en macOS

Dado que macOS es compatible con las mismas clases de cuadro de herramientas de audio que iOS, consulte nuestra documentación sobre el sonido de reproducción de iOS [con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) para ver los detalles completos de la reproducción de audio en una aplicación de Xamarin. Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
