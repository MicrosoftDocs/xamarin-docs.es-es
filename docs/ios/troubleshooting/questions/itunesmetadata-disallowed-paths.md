---
title: '¿Por qué se produce un error en el envío de la aplicación: rutas de acceso no permitidas (iTunesMetadata. plist) en...?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0255c918f7d6e984c9af2b396d9a2a00286286e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030998"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>¿Por qué se produce un error en el envío de la aplicación: rutas de acceso no permitidas (iTunesMetadata. plist) en...?

> ERROR: ERROR ITMS-90047: "se encontraron rutas de acceso no permitidas (" iTunesMetadata. plist ") en: payload/iPhoneApp1. app"

Este error se debe a un cambio en el proceso de comprobación de App Store de Apple para evitar que los usuarios golpeen problemas como el [error 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Este error específico _no_ está relacionado con la versión concreta de Xamarin que ha instalado, por lo que la degradación _no_ servirá de ayuda.

Vea la discusión del foro [aquí](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) para obtener información sobre la solución y las actualizaciones más recientes para este problema.
