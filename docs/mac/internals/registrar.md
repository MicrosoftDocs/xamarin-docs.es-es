---
title: Registrador de Xamarin. Mac
description: En este documento se describe el propósito del registrador de Xamarin. Mac y sus configuraciones de uso estático, estático y dinámico (híbrido).
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: d44f445b0c3bcc6fd498372f6cdf3e20be39d5b5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290098"
---
# <a name="xamarinmac-registrar"></a>Registrador de Xamarin. Mac

_En este documento se describe el propósito del registrador de Xamarin. Mac y sus diferentes configuraciones de uso._

## <a name="overview"></a>Información general

Xamarin. Mac se une a la brecha entre el mundo administrado (.NET) y el tiempo de ejecución de cacao, lo que permite que las clases administradas llamen a clases de Objective-C no administradas y se vuelvan a llamar cuando se produzcan eventos. El trabajo necesario para realizar este "mágico" lo controla el registrador y, en general, está oculto en la vista.

Hay implicaciones de rendimiento en este registro, en concreto en el tiempo de inicio de la aplicación, y comprender un poco de lo que está ocurriendo "bajo el capó" puede ser útil a veces.

## <a name="configurations"></a>Configuraciones

Fundamentalmente, el trabajo del registrador en el inicio puede dividirse en dos categorías:

- Examine todas las clases administradas de las que se derivan de NSObject y recopile una lista de los elementos que se van a exponer en el tiempo de ejecución de Objective-C.
- Registre esta información con el tiempo de ejecución de Objective-C.

Con el tiempo, se han creado tres configuraciones de registrador diferentes para cubrir diferentes casos de uso. Cada una de ellas tiene diferentes consecuencias en el tiempo de ejecución y de compilación:

- **Registrador dinámico** : durante el inicio, use la reflexión de .net para examinar todos los tipos cargados, determinar la lista de elementos relevantes e informar al Runtime nativo. Esta opción agrega tiempo a la compilación pero es muy caro de calcular durante el inicio (hasta varios segundos).
- **Registrador estático** : durante la compilación, calcula el conjunto de elementos que se van a registrar y genera código de Objective-C para controlar el registro. Este código se invoca durante el inicio para registrar rápidamente todos los elementos. Agrega una pausa significativa para compilar, pero puede reducir una cantidad significativa de tiempo desde el inicio de la aplicación.
- **"Parcial" estático** : un enfoque "híbrido" más reciente que ofrece la mayoría de las ventajas de ambos. Dado que las exportaciones de **Xamarin. Mac. dll** son constantes, guarde una biblioteca precalculada para controlar su registro y vincúlelo en. Use la reflexión para controlar las bibliotecas de usuario, pero a medida que las bibliotecas de usuario exportan muchos menos tipos que la plataforma enlaza, esta suele ser más rápida. Un impacto en el tiempo de compilación desatendida y reduce una gran mayoría del "costo" de dinámica.

Today static es el valor predeterminado para la configuración Debug y Static es el valor predeterminado para las configuraciones de lanzamiento.

Hay algunos escenarios:

- Complementos cargados después del inicio con clases que derivan de NSObject
- Instancias de clase creadas dinámicamente que se derivan de NSObject

en el que el registrador no puede saber que necesita registrar algún tipo en el inicio. El `ObjCRuntime.Runtime.RegisterAssembly` método se proporciona para informar al registrador de que tiene tipos adicionales que se deben tener en cuenta.
