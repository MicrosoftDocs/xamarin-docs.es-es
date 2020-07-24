---
title: Sombras paralelas de colocación en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir el específico de la plataforma Android que habilita una sombra paralela en un ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6f3304c9eeb87405ab303a80450a1cd8b2af267
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938065"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Sombras paralelas de colocación en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para habilitar una sombra paralela en un `ImageButton` . Se consume en XAML estableciendo la `ImageButton.IsShadowEnabled` propiedad enlazable en `true` , junto con una serie de propiedades adicionales enlazables opcionales que controlan la sombra paralela:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Se dibuja una sombra paralela como parte del `ImageButton` fondo y el fondo solo se dibuja si `BackgroundColor` se establece la propiedad. Por lo tanto, no se dibujará una sombra paralela si `ImageButton.BackgroundColor` no se establece la propiedad.

El `ImageButton.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. El `ImageButton.SetIsShadowEnabled` método, en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si una sombra paralela está habilitada en `ImageButton` . Además, se pueden invocar los métodos siguientes para controlar la sombra paralela:

- `SetShadowColor`: establece el color de la sombra paralela. El color predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- `SetShadowOffset`: establece el desplazamiento de la sombra paralela. El desplazamiento cambia la dirección en la que se proyecta la sombra y se especifica como un [`Size`](xref:Xamarin.Forms.Size) valor. Los `Size` valores de la estructura se expresan en unidades independientes del dispositivo, donde el primer valor es la distancia a la izquierda (valor negativo) o a la derecha (valor positivo) y el segundo valor es la distancia por encima (valor negativo) o inferior (valor positivo). El valor predeterminado de esta propiedad es (0,0, 0,0), lo que hace que se convierta la sombra en torno a cada lado de `ImageButton` .
- `SetShadowRadius`: establece el radio de desenfoque que se usa para representar la sombra paralela. El valor de radio predeterminado es 10,0.

> [!NOTE]
> El estado de una sombra paralela se puede consultar mediante una llamada a los `GetIsShadowEnabled` `GetShadowColor` métodos,, `GetShadowOffset` y `GetShadowRadius` .

El resultado es que se puede habilitar una sombra paralela en un `ImageButton` :

![ImageButton con sombra paralela](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
