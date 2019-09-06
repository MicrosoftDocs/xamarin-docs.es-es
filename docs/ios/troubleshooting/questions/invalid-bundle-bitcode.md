---
title: 'Error al enviar a la tienda de aplicaciones: "No se permite la inserción de opciones de agrupación no válidas en Bitcode en el envío".'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 84244e0c4c24a8ca6ac71a79de963bedf5c1ee68
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292533"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Error al enviar a la tienda de aplicaciones: "No se permite la inserción de opciones de agrupación no válidas en Bitcode en el envío".

las aplicaciones de watchos y tvOS _requieren_ Bitcode cuando se envían a la tienda de aplicaciones. Al compilar y enviar aplicaciones watchos y tvOS con Xcode 8,3 o una versión anterior, se puede producir el siguiente error (a través de una notificación por correo electrónico) al intentar cargar en la tienda de aplicaciones:

>Agrupación no válida: no se puede procesar la aplicación porque se han detectado opciones que no se pueden insertar en Bitcode en el envío. Es probable que no esté compilando la aplicación con la cadena de herramientas proporcionada en Xcode.

La solución a este problema consiste en compilar las aplicaciones con Xcode 9 y la versión más reciente de Xamarin. iOS.
