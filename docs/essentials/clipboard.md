---
title: 'Xamarin.Essentials: Portapapeles'
description: En este documento se describe la clase Clipboard de Xamarin.Essentials, que permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
ms.openlocfilehash: 0b5eaf3feb608a352f8f9c97bdddac55c89d4f94
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545181"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Portapapeles

La clase **Clipboard** permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Uso de Clipboard

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para comprobar si actualmente el **Portapapeles** tiene texto listo para pegar:

```csharp
var hasText = Clipboard.HasText;
```

Para establecer texto en el **Portapapeles**:

```csharp
await Clipboard.SetTextAsync("Hello World");
```

Para leer texto desde el **Portapapeles**:

```csharp
var text = await Clipboard.GetTextAsync();
```

Siempre que cambia el contenido del Portapapeles, se desencadena un evento:

```csharp
public class ClipboardTest
{
    public ClipboardTest()
    {
        // Register for clipboard changes, be sure to unsubscribe when needed
        Clipboard.ClipboardContentChanged += OnClipboardContentChanged;
    }

    void OnClipboardContentChanged(object sender, EventArgs    e)
    {
        Console.WriteLine($"Last clipboard change at {DateTime.UtcNow:T}";);
    }
}
```

> [!TIP]
> El acceso al Portapapeles debe realizarse en el subproceso de la interfaz de usuario principal. Consulte la API de [MainThread](~/essentials/main-thread.md) para ver cómo invocar métodos en el subproceso de la interfaz de usuario principal.

## <a name="api"></a>API

- [Código fuente de Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de API de Clipboard](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
