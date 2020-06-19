---
title: Representar controles personalizados en el previsor de XAML
description: En este artículo se describe cómo mostrar los controles personalizados en la vista previa de XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f20a0586aee998c10372c60c96577321e697aad
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137272"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Representar controles personalizados en el previsor de XAML

_A veces, los controles personalizados no funcionan como se esperaba en la vista previa de XAML. Siga las instrucciones de este artículo para conocer las limitaciones de la vista previa de los controles personalizados._

## <a name="basic-preview-mode"></a>Modo de vista previa básica

Incluso si no ha compilado el proyecto, el previsor de XAML representará las páginas. Hasta que se compile, cualquier control que se base en el código subyacente mostrará su Xamarin.Forms tipo base. Cuando se compila el proyecto, el previsor de XAML intentará mostrar los controles personalizados con la representación en tiempo de diseño habilitada. Si se produce un error en el procesamiento, se mostrará el Xamarin.Forms tipo base.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar la representación en tiempo de diseño para controles personalizados

Si crea sus propios controles personalizados o usa controles de una biblioteca de terceros, el previsor podría mostrarlos incorrectamente. Los controles personalizados deben optar por la representación en tiempo de diseño para que aparezcan en el controlador de vista previa, tanto si escribió el control como si lo importó desde una biblioteca. Con los controles que ha creado, agregue [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) a la clase del control para mostrarlo en el controlador de vista previa:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Use [la clase base ImageCirclePlugin's de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) como ejemplo.

## <a name="skiasharp-controls"></a>Controles SkiaSharp

Actualmente, los controles de SkiaSharp solo se admiten cuando se realiza una vista previa en iOS. No se representarán en la versión preliminar de Android.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="check-your-xamarinforms-version"></a>Comprobar la Xamarin.Forms versión
Asegúrese de que tiene instalado al menos Xamarin.Forms 3,6. Puede actualizar su Xamarin.Forms versión en NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Incluso con `[DesignTimeVisible(true)]` , el control personalizado no se representa correctamente.
Los controles personalizados que se basan en gran medida en los datos de código subyacente o de back-end no funcionan siempre en el visor de vista previa de XAML. Pruebe lo siguiente:

* Mover el control para que no se inicialice si [está habilitado el modo de diseño](index.md#detect-design-mode)
* Configurar los [datos de tiempo de diseño](design-time-data.md) para Mostrar datos falsos del back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>El previsor de XAML muestra el error "los controles personalizados no se representan correctamente"
Intente limpiar y volver a generar el proyecto, o bien cierre y vuelva a abrir el archivo XAML.
