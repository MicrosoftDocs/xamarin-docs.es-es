---
title: 'Xamarin.Essentials: Captura de pantalla'
description: En este documento se describe la clase Screenshot en Xamarin.Essentials, que permite realizar una captura de la pantalla de la aplicación que se muestra actualmente.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e907cf06814a5b14678e4584f9aabc56f39bb164
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972271"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials: Captura de pantalla

La clase **Screenshot** permite realizar una captura de la pantalla de la aplicación que se muestra actualmente.

## <a name="get-started"></a>Introducción

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenshot"></a>Uso de la captura de pantalla

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Después, llame a `CaptureAsync` para realizar una captura de la pantalla actual de la aplicación en ejecución. Esto devolverá un elemento `ScreenshotResult` que se puede usar para obtener `Width`, `Height` y un elemento `Stream` de la captura de pantalla tomada.


```csharp
async Task CaptureScreenshot()
{
    var screenshot = await Screenshot.CaptureAsync();
    var stream = await screenshot.OpenReadAsync();

    Image = ImageSource.FromStream(() => stream);
}
```


## <a name="api"></a>API

- [Código fuente de la captura de pantalla](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Screenshot)
- [Documentación de Screenshot API](xref:Xamarin.Essentials.Screenshot)
