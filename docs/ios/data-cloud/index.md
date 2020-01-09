---
title: Datos y Cloud Services en aplicaciones de Xamarin. iOS
description: En este documento se proporcionan vínculos a guías que describen cómo trabajar con datos locales, iCloud y CloudKit en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: e9f910804f3da9d173206d51c59e1d7ddd9b90a6
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663517"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Datos y Cloud Services en aplicaciones de Xamarin. iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Acceso a datos](~/ios/data-cloud/data/index.md)

La mayoría de las aplicaciones tienen algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos sea trivialmente pequeña, normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso a la base de datos. iOS tiene el motor de base de datos de SQLite "integrado" y el acceso a los datos se simplifica mediante la plataforma de Xamarin que se incluye con el proveedor de datos de SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

La API de almacenamiento de iCloud permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación en una ubicación central y acceder a esos elementos desde todos los dispositivos del usuario.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

El marco CloudKit agiliza el desarrollo de aplicaciones que tienen acceso a iCloud. Esto incluye la recuperación de los datos de la aplicación y los derechos de los recursos, así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit proporciona a los usuarios una capa de anonimato al permitir el acceso a las aplicaciones con sus identificadores de iCloud sin compartir información personal.
