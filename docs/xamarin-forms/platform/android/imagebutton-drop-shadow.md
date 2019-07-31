---
title: Sombras paralelas de colocación en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el específico de la plataforma Android que habilita una sombra paralela en un ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 567216171dd289e849ee0164452e4b876953f2a3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653581"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Sombras paralelas de colocación en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica se usa para habilitar una sombra paralela en un `ImageButton`. Se consume en XAML estableciendo el `ImageButton.IsShadowEnabled` propiedad enlazable a `true`, junto con un número de propiedades enlazables opcionales adicionales que controlan el efecto de sombra:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

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
> Se dibuja una sombra paralela como parte de la `ImageButton` en segundo plano y el fondo se dibuja solo si el `BackgroundColor` se establece la propiedad. Por lo tanto, una sombra paralela no se dibujarán si el `ImageButton.BackgroundColor` no se establece la propiedad.

El `ImageButton.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `ImageButton.SetIsShadowEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si una sombra paralela está habilitada en el `ImageButton`. Además, se pueden invocar los métodos siguientes para controlar el efecto de sombra:

- `SetShadowColor` : establece el color de la sombra paralela. El color predeterminado es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` : establece el desplazamiento de la sombra paralela. El desplazamiento cambia la dirección de la sombra se convierte y se especifica como un [ `Size` ](xref:Xamarin.Forms.Size) valor. El `Size` los valores de la estructura se expresan en unidades independientes del dispositivo, con el primer valor que se va a la distancia a la izquierda (valor negativo) o la derecha (valor positivo) y el segundo valor que se va a la distancia anterior (negativo) o por debajo (valor positivo) . El valor predeterminado de esta propiedad es (0.0, 0.0), que da como resultado la sombra que se va a convertir en torno a cada lado de la `ImageButton`.
- `SetShadowRadius`: establece el radio de desenfoque utilizado para representar la sombra paralela. El valor de radio predeterminado es 10,0.

> [!NOTE]
> Se puede consultar el estado de una sombra paralela mediante una llamada a la `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, y `GetShadowRadius` métodos.

El resultado es que se puede habilitar una sombra paralela en un `ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "ImageButton con sombra paralela")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
