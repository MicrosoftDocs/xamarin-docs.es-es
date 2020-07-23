---
title: Codificaciones de internacionalización en Xamarin. iOS
description: En este documento se describen las codificaciones de internacionalización en Xamarin. iOS, que describen las codificaciones disponibles y cómo agregarlas a una aplicación.
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 88f95f797d1230c818a635376f7b17a2264afadf
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930343"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Codificaciones de internacionalización en Xamarin. iOS

No todas las codificaciones se incluyen de forma predeterminada en la biblioteca de clases de Xamarin. iOS.

Para reducir el tamaño de la aplicación, Xamarin. iOS no incluye ninguna codificación específica y tiene que indicar a Mtouch que incluya los ensamblados que contienen la compatibilidad con la codificación que necesita.

Para ello, seleccione las codificaciones adicionales en el panel de compilación/avanzado de iOS en Visual Studio para Mac o Visual Studio:

 [![Selección de las codificaciones adicionales](encodings-images/00.png)](encodings-images/00.png#lightbox)

 [![Selección de las codificaciones adicionales](encodings-images/00a.png)](encodings-images/00a.png#lightbox)

Puede seleccionar una de estas opciones:

- CJK: para chineese, Japonés y Coreano
- Oriente: Árabe, hebreo, Turco y Latin5.
- otros: cirílico, Báltico, vietnamita, ucraniano y tailandés
- poco frecuente: codificaciones de EBCDIC y otras páginas de códigos poco frecuentes
- oeste: lenguas latinas, Pascua y Europa occidental
- todo

 <a name="cjk"></a>

## <a name="cjk"></a>compa

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast"></a>

## <a name="mideast"></a>Oriente de

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other"></a>

## <a name="other"></a>Otros

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare"></a>

## <a name="rare"></a>menos

- CP1026
- CP1047
- CP1140
- CP1141
- CP1142
- CP1143
- CP1144
- CP1145
- CP1146
- CP1147
- CP1148
- CP1149
- CP20273
- CP20277
- CP20278
- CP20280
- CP20284
- CP20285
- CP20290
- CP20297
- CP20420
- CP20424
- CP20871
- CP21025
- CP37
- CP500
- CP708
- CP852
- CP855
- CP857
- CP858
- CP862
- CP864
- CP866
- CP869
- CP870
- CP875

 <a name="west"></a>

## <a name="west"></a>west (oeste)

- CP10000
- CP10079
- CP1250
- CP1252
- CP1253
- CP28592
- CP28593
- CP28597
- CP28605
- CP437
- CP850
- CP860
- CP861
- CP863
- CP865
