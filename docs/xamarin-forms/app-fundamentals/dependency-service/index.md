---
title: DependencyService de Xamarin.Forms
description: La clase DependencyService de Xamarin.Forms es un localizador de servicios que habilita las aplicaciones de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma desde código compartido.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 126e2d2373bad923fe1d66fe355ad811c15fbe4f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138377"
---
# <a name="xamarinforms-dependencyservice"></a>DependencyService de Xamarin.Forms

## <a name="introduction"></a>[Introducción](introduction.md)

La clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) es un localizador de servicios que habilita las aplicaciones de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma desde código compartido.

## <a name="registration-and-resolution"></a>[Registro y resolución](registration-and-resolution.md)

Las implementaciones de la plataforma deben registrarse con [`DependencyService`](xref:Xamarin.Forms.DependencyService) y, después, deben resolverse desde código compartido para poder invocarse.

## <a name="picking-a-photo-from-the-library"></a>[Selección de una foto de la biblioteca](photo-picker.md)

En este artículo, se explica cómo usar la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) de Xamarin.Forms para seleccionar una foto de la biblioteca de imágenes del teléfono.
