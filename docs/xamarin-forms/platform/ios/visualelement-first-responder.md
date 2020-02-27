---
title: VisualElement primer respondedor en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma iOS que permite que un objeto VisualElement se convierta en el primer respondedor para tocar eventos.
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646717"
---
# <a name="visualelement-first-responder-on-ios"></a>VisualElement primer respondedor en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este tipo específico de la plataforma iOS permite a un objeto [`VisualElement`](xref:Xamarin.Forms.VisualElement) convertirse en el primer respondedor para tocar eventos, en lugar de la página que contiene el elemento. Se consume en XAML estableciendo la propiedad `VisualElement.CanBecomeFirstResponder` enlazable en `true`:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

El método `VisualElement.On<iOS>` especifica que este específico de la plataforma solo se ejecutará en iOS. El método `VisualElement.SetCanBecomeFirstResponder`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , se usa para establecer el `VisualElement` de modo que se convierta en el primer respondedor de los eventos Touch. Además, se puede usar el método `VisualElement.CanBecomeFirstResponder` para devolver si el `VisualElement` es el primer respondedor para los eventos de toque.

El resultado es que un [`VisualElement`](xref:Xamarin.Forms.VisualElement) puede convertirse en el primer respondedor para los eventos Touch, en lugar de la página que contiene el elemento. Esto permite escenarios como aplicaciones de chat que no descartan un teclado cuando se puntea un [`Button`](xref:Xamarin.Forms.Button) .

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
