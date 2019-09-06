---
title: En el capó de Xamarin. Mac
description: Este documento contiene vínculos a varias guías que describen el funcionamiento interno de Xamarin. Mac. Los documentos vinculados describen la compilación por encima de la hora, la arquitectura de Xamarin. Mac y el registrador de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 51cf479ba07a769f5d7a875bb3f1203caef2ad0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290107"
---
# <a name="under-the-hood-in-xamarinmac"></a>En el capó de Xamarin. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Compilación por encima de tiempo (AOT)](aot.md)

La compilación de antemano de tiempo (AOT) es una técnica eficaz de optimización para mejorar el rendimiento de inicio. Sin embargo, también afecta al tiempo de compilación, al tamaño de la aplicación y a la ejecución del programa de maneras profundas, por lo que merece la pena entender cómo funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitectura de Mac](architecture.md)

Relación de Xamarin. Mac con Objective-C, incluidos conceptos como la compilación, los selectores, los registradores, el inicio de la aplicación y el generador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador de Xamarin. Mac](registrar.md)

Xamarin. Mac se une a la brecha entre el entorno administrado y el tiempo de ejecución de cacao, lo que permite que las clases administradas llamen a clases de Objective-C no administradas y se vuelvan a llamar cuando se produzcan eventos. El trabajo necesario para realizar esta "magia" es controlado por el registrador, pero entender lo que está ocurriendo "bajo el capó" puede ser útil a veces.
