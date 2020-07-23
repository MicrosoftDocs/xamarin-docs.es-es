---
title: Pasar parámetros de efecto como propiedades de Common Language Runtime
description: Las propiedades de Common Language Runtime (CLR) se pueden usar para definir parámetros de efecto que no responden a los cambios de propiedades en tiempo de ejecución. En este artículo se muestra cómo usar propiedades CLR para pasar parámetros a un efecto.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24468e88d39283d2b8282fe0cb9b59f33fcef2f0
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936700"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Pasar parámetros de efecto como propiedades de Common Language Runtime

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_Las propiedades de Common Language Runtime (CLR) se pueden usar para definir parámetros de efecto que no responden a los cambios de propiedades en tiempo de ejecución. En este artículo se muestra cómo usar propiedades CLR para pasar parámetros a un efecto._

El proceso para crear parámetros de efecto que no respondan a los cambios de propiedades en tiempo de ejecución es el siguiente:

1. Cree una clase `public` que genere subclases de la clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect). La clase `RoutingEffect` representa un efecto independiente de la plataforma que encapsula un efecto interno, que suele ser específico de la plataforma.
1. Cree un constructor que llame al constructor de clase base, y pase una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específica de la plataforma.
1. Agregue propiedades a la clase para cada parámetro que se va a pasar al efecto.

Después, se pueden pasar parámetros al efecto mediante la especificación de valores para cada propiedad al crear una instancia del efecto.

En la aplicación de ejemplo se muestra un elemento `ShadowEffect` que agrega una sombra al texto mostrado por un control [`Label`](xref:Xamarin.Forms.Label). El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![Responsabilidades del proyecto de efecto de sombra](clr-properties-images/shadow-effect.png)

`LabelShadowEffect` personaliza un control [`Label`](xref:Xamarin.Forms.Label) en el elemento `HomePage` en cada proyecto específico de la plataforma. Los parámetros se pasan a cada elemento `LabelShadowEffect` a través de las propiedades de la clase `ShadowEffect`. Cada clase `LabelShadowEffect` se deriva de la clase `PlatformEffect` para cada plataforma. Como resultado, se agrega una sombra al texto mostrado por el control `Label`, como se muestra en las capturas de pantalla siguientes:

![Efecto de sombra en cada plataforma](clr-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>Creación de parámetros de efecto

Se debe crear una clase `public` que genere subclases de la clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) para representar los parámetros efecto, como se muestra en el ejemplo de código siguiente:

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

El elemento `ShadowEffect` contiene cuatro propiedades que representan los parámetros que se van a pasar a cada elemento `LabelShadowEffect` específico de la plataforma. El constructor de la clase llama al constructor de clase base, y se pasa un parámetro que consiste en la concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específica de la plataforma. Por tanto, cuando se crea una instancia de `ShadowEffect`, se agrega una nueva instancia de `MyCompany.LabelShadowEffect` a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) de un control.

## <a name="consuming-the-effect"></a>Consumo del efecto

En el ejemplo de código XAML siguiente se muestra un control [`Label`](xref:Xamarin.Forms.Label) al que se adjunta el elemento `ShadowEffect`:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

En el siguiente ejemplo de código, se muestra el control [`Label`](xref:Xamarin.Forms.Label) equivalente en C#:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

En ambos ejemplos de código, se crea una instancia de la clase `ShadowEffect` con valores especificados para cada propiedad, antes de agregarse a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control. Observe que la propiedad `ShadowEffect.Color` usa valores de color específicos de la plataforma. Para obtener más información, vea [Clase Device](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Creación del efecto en cada plataforma

En las secciones siguientes se describe la implementación específica de la plataforma de la clase `LabelShadowEffect`.

### <a name="ios-project"></a>Proyecto de iOS

En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de iOS:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.ShadowRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

El método `OnAttached` recupera la instancia de `ShadowEffect` y, después, establece las propiedades `Control.Layer` en los valores de propiedad especificados para crear la sombra. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

### <a name="android-project"></a>Proyecto de Android

En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

El método `OnAttached` recupera la instancia de `ShadowEffect` y llama al método [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) para crear una sombra con los valores de propiedad especificados. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

### <a name="universal-windows-platform-project"></a>Proyecto de la Plataforma universal de Windows

En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de Plataforma universal de Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La Plataforma Universal de Windows no proporciona un efecto de sombra, por lo que la implementación de `LabelShadowEffect` en ambas plataformas simula una mediante la adición de un segundo control [`Label`](xref:Xamarin.Forms.Label) de desplazamiento detrás del control `Label` principal. El método `OnAttached` recupera la instancia de `ShadowEffect`, crea el objeto `Label` y establece algunas propiedades de diseño en el objeto `Label`. Después, para crear la sombra, se establecen las propiedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor), [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) para controlar el color y la ubicación del objeto `Label`. Después, el elemento `shadowLabel` se inserta mediante desplazamiento detrás del elemento `Label` principal. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo usar propiedades CLR para pasar parámetros a un efecto. Las propiedades CLR se pueden usar para definir parámetros de efecto que no responden a los cambios de propiedades en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efecto de sombra (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
