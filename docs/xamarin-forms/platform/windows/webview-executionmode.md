---
title: Modo de ejecución de WebView en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que establece el subproceso en el que un WebView hospeda su contenido.
ms.prod: xamarin
ms.assetid: 4D3C4112-0FF6-47F8-BC22-579D92032807
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 314851c4697e95ef8662710c986d0b175249fad6
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940858"
---
# <a name="webview-execution-mode-on-windows"></a>Modo de ejecución de WebView en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este conjunto específico de la plataforma establece el subproceso en el que un [`WebView`](xref:Xamarin.Forms.WebView) hospeda su contenido. Se consume en XAML estableciendo la `WebView.ExecutionMode` propiedad Bindable en un `WebViewExecutionMode` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.ExecutionMode="SeparateThread" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

WebView webView = new Xamarin.Forms.WebView();
webView.On<Windows>().SetExecutionMode(WebViewExecutionMode.SeparateThread);
```

El `WebView.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. El `WebView.SetExecutionMode` método, en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer el subproceso en el que un `WebView` host hospeda su contenido, con la `WebViewExecutionMode` enumeración que proporciona tres valores posibles:

- `SameThread` indica que el contenido se hospeda en el subproceso de la interfaz de usuario. Este es el valor predeterminado de `WebView` en Windows.
- `SeparateThread` indica que el contenido está hospedado en un subproceso en segundo plano.
- `SeparateProcess` indica que el contenido está hospedado en un proceso independiente del proceso de la aplicación. No hay un proceso independiente por instancia de WebView, por lo que todas las instancias de WebView de una aplicación comparten el mismo proceso independiente.

Además, el `GetExecutionMode` método se puede utilizar para devolver el actual `WebViewExecutionMode` para `WebView` .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
