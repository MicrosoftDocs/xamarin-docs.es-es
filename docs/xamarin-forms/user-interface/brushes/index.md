---
title: Xamarin.FormsBrush
description: La Xamarin.Forms clase Brush es una clase abstracta que pinta un área con su salida.
ms.prod: xamarin
ms.assetid: 44420FC2-304C-4175-8654-76769F79A813
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f1f56103b20eac84ce6106c0955acebf974cfe3
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919146"
---
# <a name="no-locxamarinforms-brushes"></a>Xamarin.FormsBrush

![API de vista previa](~/media/shared/preview.png "Esta API se encuentra en versión preliminar.")

Un pincel permite pintar un área, como el fondo de un control, mediante distintos enfoques. La compatibilidad con Xamarin.Forms el pincel en está disponible en el `Xamarin.Forms` espacio de nombres de iOS, Android, MacOS, el plataforma universal de Windows (UWP) y el Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> La compatibilidad con Xamarin.Forms el pincel en es actualmente experimental y solo se puede usar si se establece la `Brush_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

La `Brush` clase es una clase abstracta que pinta un área con su salida. Las clases que derivan de `Brush` describen diferentes formas de dibujar un área. En la lista siguiente se describen los diferentes tipos de pincel disponibles en Xamarin.Forms :

- `SolidColorBrush`, que pinta un área con un color sólido. Para obtener más información, vea [ Xamarin.Forms pinceles: colores sólidos](solidcolor.md).
- `LinearGradientBrush`, que pinta un área con un degradado lineal. Para obtener más información, vea [ Xamarin.Forms pinceles: degradados lineales](lineargradient.md).
- `RadialGradientBrush`, que pinta un área con un degradado radial. Para obtener más información, vea [ Xamarin.Forms pinceles: degradados radiales](radialgradient.md).

Las instancias de estos tipos de pincel pueden asignarse a las `Stroke` `Fill` propiedades y de un `Shape` , y a la `Background` propiedad de [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!NOTE]
> La `VisualElement.Background` propiedad permite usar pinceles como fondo en cualquier control.

La `Brush` clase también tiene un `IsNullOrEmpty` método que devuelve un `bool` que representa si el pincel está definido o no.
