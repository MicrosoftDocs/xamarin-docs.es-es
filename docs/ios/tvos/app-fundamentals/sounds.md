---
title: Reproducir sonido en tvOS con AVAudioPlayer en Xamarin
description: En este artículo se muestra cómo usar una clase auxiliar para controlar la reproducción de sonido mediante un AVAudioPlayer en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: b34c769eaa3aef5bf47a9bfa891859289b195f03
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283784"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Reproducir sonido en tvOS con AVAudioPlayer en Xamarin

## <a name="about-the-avaudioplayer"></a>Acerca de AVAudioPlayer

`AVAudioPlayer` Se utiliza para reproducir datos de audio de cualquier memoria o de un archivo. Apple recomienda el uso de esta clase para reproducir audio en la aplicación a menos que esté realizando streaming de red o requiera e/s de audio de baja latencia.

Puede usar el `AVAudioPlayer` para hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucles opcionales.
- Reproducir varios sonidos al mismo tiempo con la sincronización opcional.
- Controle el volumen, la velocidad de reproducción y la posición estéreo de cada sonido que se reproduce.
- Compatibilidad con características como el avance rápido o el rebobinado.
- Obtener datos de medición de nivel de reproducción.

`AVAudioPlayer`admite sonidos en cualquier formato de audio proporcionado por iOS, tvOS y OS X como `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Reproducir sonidos en tvOS

Dado que tvOS admite las mismas clases de cuadro de herramientas de audio que iOS, consulte nuestra documentación sobre la reproducción de iOS [con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) para ver los detalles completos de la reproducción de audio en una aplicación Xamarin. tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
