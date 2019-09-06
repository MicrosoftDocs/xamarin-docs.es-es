---
title: ¿Por qué se produce un error No se permiten las rutas de acceso (iTunesMetadata. plist) en...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291033"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>¿Por qué se produce un error No se permiten las rutas de acceso (iTunesMetadata. plist) en...

> ERROR ERROR ITMS-90047: "No se permiten rutas de acceso (" iTunesMetadata. plist ") en: Payload/iPhoneApp1. app "

Este error se debe a un cambio en el proceso de comprobación de App Store de Apple para evitar que los usuarios golpeen problemas como el [error 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Este error específico _no_ está relacionado con la versión concreta de Xamarin que ha instalado, por lo que la degradación _no_ servirá de ayuda.

Vea la discusión del foro [aquí](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) para obtener información sobre la solución y las actualizaciones más recientes para este problema.
