---
title: Temas para una aplicación de Xamarin.Forms
description: Las aplicaciones de Xamarin. Forms admiten la creación de un ResourceDictionary para cada tema y, a continuación, la carga de los recursos con la extensión de marcado DynamicResource.
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: 5988437b40ac875b8b59f9af0f25d4b5c60ded97
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517129"
---
# <a name="theming-a-xamarinforms-application"></a>Crear una aplicación de Xamarin. Forms

## <a name="theme-an-application"></a>[Tema de una aplicación](theming.md)

Los temas se pueden implementar en aplicaciones de Xamarin. Forms creando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) una para cada tema y, a continuación, cargando los `DynamicResource` recursos con la extensión de marcado.

## <a name="respond-to-system-theme-changes"></a>[Responder a los cambios de tema del sistema](system-theme-changes.md)

Los dispositivos suelen incluir temas claros y oscuros, que hacen referencia a un amplio conjunto de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo. Las aplicaciones deben respetar estos temas del sistema y responder inmediatamente cuando cambia el tema del sistema.
