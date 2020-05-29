---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8bd539c2bb0e8963afae3392b6f8e99d79af9af
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136974"
---
# <a name="visualelement-first-responder-on-ios"></a>VisualElement primer respondedor en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este tipo específico de la plataforma iOS permite [`VisualElement`](xref:Xamarin.Forms.VisualElement) que un objeto se convierta en el primer respondedor para tocar eventos, en lugar de la página que contiene el elemento. Se consume en XAML estableciendo la `VisualElement.CanBecomeFirstResponder` propiedad Bindable en `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

El `VisualElement.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `VisualElement.SetCanBecomeFirstResponder` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer `VisualElement` para que se convierta en el primer respondedor de los eventos Touch. Además, se `VisualElement.CanBecomeFirstResponder` puede usar el método para devolver si `VisualElement` es el primer respondedor a los eventos de toque.

El resultado es que un [`VisualElement`](xref:Xamarin.Forms.VisualElement) puede convertirse en el primer respondedor para los eventos Touch, en lugar de la página que contiene el elemento. Esto permite escenarios como aplicaciones de chat que no descartan un teclado cuando [`Button`](xref:Xamarin.Forms.Button) se puntea.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
