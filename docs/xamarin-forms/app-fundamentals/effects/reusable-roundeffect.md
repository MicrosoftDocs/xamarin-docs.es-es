---
title: RoundEffect reutilizable de Xamarin.Forms
description: RoundEffect es un efecto reutilizable que se puede aplicar a cualquier control derivado de VisualElement para representar el control como un círculo.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b459945aec4ef8f1c3fa9729efd82461aec0e0
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561968"
---
# <a name="no-locxamarinforms-reusable-roundeffect"></a>RoundEffect reutilizable de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect simplifica la representación de cualquier control derivado de VisualElement como un círculo. Este efecto se puede usar para crear imágenes circulares, botones u otros controles:

[![Capturas de pantalla de RoundEffect en iOS y Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Creación de un objeto RoutingEffect compartido

Para crear un efecto multiplataforma se debe crear una clase de efecto en el proyecto compartido. La aplicación de ejemplo crea una clase `RoundEffect` vacía que se deriva de la clase `RoutingEffect`:

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Esta clase permite al proyecto compartido resolver las referencias al efecto en código o XAML, pero no proporciona ninguna funcionalidad. El efecto debe tener implementaciones para cada plataforma.

## <a name="implement-the-android-effect"></a>Implementación del efecto de Android

El proyecto de la plataforma Android define una clase `RoundEffect` que se deriva de `PlatformEffect`. Esta clase se etiqueta con atributos `assembly` que permiten que Xamarin.Forms resuelva la clase de efecto:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

En la plataforma Android se usa el concepto de `OutlineProvider` para definir los bordes de un control. En el proyecto de ejemplo se incluye una clase `CornerRadiusProvider` que se deriva de la clase `ViewOutlineProvider`:

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

Esta clase usa las propiedades `Width` y `Height` de la instancia de `Element` de Xamarin.Forms para calcular un radio que es la mitad de la dimensión más corta.

Una vez que se ha definido un proveedor de esquemas, la clase `RoundEffect` puede consumirlo para implementar el efecto:

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

Se llama al método `OnAttached` cuando el efecto se adjunta a un elemento. El objeto `OutlineProvider` existente se guarda para que se pueda restaurar cuando se desasocie el efecto. Se usa una nueva instancia de `CornerRadiusOutlineProvider` como `OutlineProvider` y `ClipToOutline` se establece en true para recortar los elementos que se desbordan en los bordes del contorno.

Se llama al método `OnDetatched` cuando el efecto se quita de un elemento y restaura el valor de `OutlineProvider` original.

> [!NOTE]
> En función del tipo de elemento, la propiedad `Control` puede ser NULL o no. Si la propiedad `Control` no es NULL, las esquinas redondeadas se pueden aplicar directamente al control. Pero si es NULL, las esquinas redondeadas se deben aplicar al objeto `Container`. El campo `effectTarget` permite aplicar el efecto al objeto adecuado.

## <a name="implement-the-ios-effect"></a>Implementación del efecto de iOS

El proyecto de la plataforma iOS define una clase `RoundEffect` que se deriva de `PlatformEffect`. Esta clase se etiqueta con atributos `assembly` que permiten que Xamarin.Forms resuelva la clase de efecto:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

En iOS, los controles tienen una propiedad `Layer`, que tiene una propiedad `CornerRadius`. La implementación de la clase `RoundEffect` en iOS calcula el radio de redondeo adecuado y actualiza la propiedad `CornerRadius` de la capa:

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

El método `CalculateRadius` calcula un radio en función en la dimensión mínima de `Element` de Xamarin.Forms. Se llama al método `OnAttached` cuando el efecto se adjunta a un control y actualiza la propiedad `CornerRadius` de la capa. Establece la propiedad `ClipToBounds` en `true` de modo que el desbordamiento de los elementos se recorta a los bordes del control. Se llama al método `OnDetatched` cuando el efecto se quita de un control e invierte estos cambios, lo que restaura el radio de esquina original.

> [!NOTE]
> En función del tipo de elemento, la propiedad `Control` puede ser NULL o no. Si la propiedad `Control` no es NULL, las esquinas redondeadas se pueden aplicar directamente al control. Pero si es NULL, las esquinas redondeadas se deben aplicar al objeto `Container`. El campo `effectTarget` permite aplicar el efecto al objeto adecuado.

## <a name="consume-the-effect"></a>Consumo del efecto

Una vez que el efecto se ha implementado entre plataformas, los controles de Xamarin.Forms pueden usarlo. Una aplicación común de `RoundEffect` consiste en convertir un objeto `Image` en circular. En el código XAML siguiente se muestra el efecto aplicado a una instancia de `Image`:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

El efecto también se puede aplicar en el código:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

La clase `RoundEffect` se puede aplicar a cualquier control que se derive de `VisualElement`.

> [!NOTE]
> Para que el efecto calcule el radio correcto, el control al que se aplica debe tener un tamaño explícito. Por tanto, se deben definir las propiedades `HeightRequest` y `WidthRequest`. Si el control afectado aparece en un elemento `StackLayout`, su propiedad `HorizontalOptions` no debe usar uno de los valores **Expand** como `LayoutOptions.CenterAndExpand`, o no tendrá dimensiones precisas.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de RoundEffect](/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introducción a los efectos](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md)