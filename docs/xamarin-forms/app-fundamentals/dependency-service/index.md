---
title: Xamarin.Forms DependencyService
description: La clase DependencyService de Xamarin.Forms es un localizador de servicios que habilita las aplicaciones de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma desde código compartido.
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "67650452"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introduction"></a>[Introducción](introduction.md)

La clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) es un localizador de servicios que habilita las aplicaciones de Xamarin.Forms para invocar la funcionalidad nativa de la plataforma desde código compartido.

## <a name="registration-and-resolution"></a>[Registro y resolución](registration-and-resolution.md)

Las implementaciones de la plataforma deben registrarse con [`DependencyService`](xref:Xamarin.Forms.DependencyService) y, después, deben resolverse desde código compartido para poder invocarse.

## <a name="picking-a-photo-from-the-library"></a>[Selección de una foto de la biblioteca](photo-picker.md)

En este artículo, se explica cómo usar la clase [`DependencyService`](xref:Xamarin.Forms.DependencyService) de Xamarin.Forms para seleccionar una foto de la biblioteca de imágenes del teléfono.
