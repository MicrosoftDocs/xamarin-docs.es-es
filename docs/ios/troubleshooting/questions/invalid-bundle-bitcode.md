---
title: 'Error al enviar a la tienda de aplicaciones: "no se permiten las opciones de agrupación no válidas que se van a insertar en Bitcode en el envío".'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: c6a03fa3327e81f577f85b05a033d9c97495eb2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031074"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Error al enviar a la tienda de aplicaciones: "no se permiten las opciones de agrupación no válidas que se van a insertar en Bitcode en el envío".

las aplicaciones de watchos y tvOS _requieren_ Bitcode cuando se envían a la tienda de aplicaciones. Al compilar y enviar aplicaciones watchos y tvOS con Xcode 8,3 o una versión anterior, se puede producir el siguiente error (a través de una notificación por correo electrónico) al intentar cargar en la tienda de aplicaciones:

>Agrupación no válida: no se puede procesar la aplicación porque se han detectado opciones que no se pueden insertar en Bitcode en el envío. Es probable que no esté compilando la aplicación con la cadena de herramientas proporcionada en Xcode.

La solución a este problema consiste en compilar las aplicaciones con Xcode 9 y la versión más reciente de Xamarin. iOS.
