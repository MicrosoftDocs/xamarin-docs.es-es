---
title: Localización de Xamarin.Forms
description: El marco de localización integrado de .NET puede usarse para compilar aplicaciones multilingües multiplataforma con Xamarin.Forms. Se pueden localizar texto e imágenes y las aplicaciones pueden admitir una dirección de flujo de derecha a izquierda.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5dbcc63eb162454845b78fc24a3beb0ca8296453
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558159"
---
# <a name="no-locxamarinforms-localization"></a>Localización de Xamarin.Forms

_El marco de localización integrado de .NET puede usarse para compilar aplicaciones multilingües multiplataforma con Xamarin.Forms._

## <a name="no-locxamarinforms-string-and-image-localization"></a>[Localización de cadenas e imágenes de Xamarin.Forms](text.md)

El mecanismo integrado para la localización de aplicaciones .NET usa [archivos RESX](/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) y las clases de los espacios de nombres `System.Resources` y `System.Globalization`. Los archivos RESX que contienen las cadenas traducidas se insertan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado a las traducciones. Después, se puede recuperar el texto traducido en el código.

## <a name="right-to-left-localization"></a>[Localización de derecha a izquierda](right-to-left.md)

La dirección de flujo es la dirección en la que el ojo humano lee los elementos de la interfaz de usuario en la página. La localización de derecha a izquierda agrega compatibilidad para la dirección de flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms.