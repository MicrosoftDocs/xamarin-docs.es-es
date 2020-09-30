---
title: InputView orden de lectura en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que permite detectar de forma dinámica el orden de lectura del texto bidireccional.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0fd754b2d41de61238cb7b0b2f34c1035d8dc2bf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557769"
---
# <a name="inputview-reading-order-on-windows"></a>InputView orden de lectura en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta Plataforma universal de Windows específica de la plataforma habilita el orden de lectura (de izquierda a derecha o de derecha a izquierda) del texto bidireccional en [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) las instancias de, y [`Label`](xref:Xamarin.Forms.Label) que se detectan de forma dinámica. Se consume en XAML estableciendo [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para instancias de `Entry` y `Editor` ) o [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

El `Editor.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. [ `InputView.SetDetectReadingOrderFromContent` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView}, System. Boolean)), en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si se detecta el orden de lectura del contenido de [`InputView`](xref:Xamarin.Forms.InputView) . Además, se `InputView.SetDetectReadingOrderFromContent` puede usar el método para alternar si se detecta el orden de lectura del contenido mediante una llamada a [ `InputView.GetDetectReadingOrderFromContent` ] (XREF: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView})) para devolver el valor actual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

El resultado es que [`Entry`](xref:Xamarin.Forms.Entry) las [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) instancias de, y pueden tener el orden de lectura de su contenido detectado dinámicamente:

[![InputView detectar el orden de lectura de contenido específico de la plataforma](inputview-reading-order-images/editor-readingorder.png "InputView detectar el orden de lectura de contenido específico de la plataforma")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView detectar el orden de lectura de contenido específico de la plataforma")

> [!NOTE]
> A diferencia de la configuración de la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad, la lógica de las vistas que detectan el orden de lectura de su contenido de texto no afectará a la alineación del texto dentro de la vista. En su lugar, ajusta el orden en el que se colocan los bloques de texto bidireccional.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)