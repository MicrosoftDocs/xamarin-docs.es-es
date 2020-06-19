---
title: Crear una Xamarin.Forms aplicación
description: Xamarin.Formslas aplicaciones admiten la creación de un ResourceDictionary para cada tema y, a continuación, la carga de los recursos con la extensión de marcado DynamicResource.
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80660ae7d3af0fe5948a5ae4ffdb35d2f9c2a40f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136141"
---
# <a name="theming-a-xamarinforms-application"></a>Crear una Xamarin.Forms aplicación

## <a name="theme-an-application"></a>[Tema de una aplicación](theming.md)

Los temas se pueden implementar en Xamarin.Forms aplicaciones mediante la creación de un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) para cada tema y, a continuación, cargar los recursos con la `DynamicResource` extensión de marcado.

## <a name="respond-to-system-theme-changes"></a>[Responder a cambios de tema del sistema](system-theme-changes.md)

Los dispositivos suelen incluir temas claros y oscuros, que hacen referencia a un amplio conjunto de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo. Las aplicaciones deben respetar estos temas del sistema y responder inmediatamente cuando cambia el tema del sistema.
