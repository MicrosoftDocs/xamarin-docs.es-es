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
ms.openlocfilehash: cf03d0c32d80d7a1821e323acdc84a351413efec
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138884"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Pasar parámetros de efecto como propiedades adjuntas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_Las propiedades adjuntas se pueden usar para definir los parámetros de efecto que responden a los cambios de propiedades en tiempo de ejecución. En este artículo se muestra cómo usar las propiedades adjuntas para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución._

El proceso para crear parámetros de efecto que respondan a los cambios de propiedades en tiempo de ejecución es el siguiente:

1. Se crea una clase `static` que contiene una propiedad adjunta para cada parámetro que se va a pasar al efecto.
1. Se agrega una propiedad adjunta adicional a la clase que se va a usar para controlar la adición o eliminación del efecto del control al que se va a conectar la clase. Se asegura que esta propiedad adjunta registra un delegado `propertyChanged` que se ejecutará cuando cambie el valor de la propiedad.
1. Se crean captadores y establecedores `static` para cada propiedad adjunta.
1. Se implementa la lógica en el delegado `propertyChanged` para agregar y quitar el efecto.
1. Se implementa una clase anidada dentro de la clase `static`, con el nombre del efecto, que crea subclases de la clase `RoutingEffect`. Para el constructor, se llama al constructor de clase base, y se pasa una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específica de la plataforma.

Después, se pueden pasar parámetros al efecto mediante la adición de las propiedades adjuntas y los valores de propiedad al control adecuado. Además, los parámetros se pueden cambiar en tiempo de ejecución mediante la especificación de un nuevo valor de propiedad adjunta.

> [!NOTE]
> Una propiedad adjunta es un tipo especial de propiedad enlazable, definida en una clase, pero adjunta a otros objetos, y reconocible en XAML como atributos que contienen una clase y un nombre de propiedad separados por un punto. Para obtener más información, vea [Propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md).

En la aplicación de ejemplo se muestra un elemento `ShadowEffect` que agrega una sombra al texto mostrado por un control [`Label`](xref:Xamarin.Forms.Label). Además, el color de la sombra se puede cambiar en tiempo de ejecución. El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](attached-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

`LabelShadowEffect` personaliza un control [`Label`](xref:Xamarin.Forms.Label) en el elemento `HomePage` en cada proyecto específico de la plataforma. Los parámetros se pasan a cada elemento `LabelShadowEffect` a través de las propiedades adjuntas de la clase `ShadowEffect`. Cada clase `LabelShadowEffect` se deriva de la clase `PlatformEffect` para cada plataforma. Como resultado, se agrega una sombra al texto mostrado por el control `Label`, como se muestra en las capturas de pantalla siguientes:

![](attached-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>Creación de parámetros de efecto

Se debe crear una clase `static` para representar los parámetros efecto, como se muestra en el ejemplo de código siguiente:

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

`ShadowEffect` contiene cinco propiedades adjuntas, con captadores y establecedores `static` para cada propiedad adjunta. Cuatro de estas propiedades representan los parámetros que se van a pasar a cada elemento `LabelShadowEffect` específico de la plataforma. La clase `ShadowEffect` también define una propiedad adjunta `HasShadow` que se usa para controlar la adición o eliminación del efecto del control al que se conecta la clase `ShadowEffect`. Esta propiedad adjunta registra el método `OnHasShadowChanged` que se ejecutará cuando cambie el valor de la propiedad. Este método agrega o quita el efecto en función del valor de la propiedad adjunta `HasShadow`.

La clase `LabelShadowEffect` anidada, que crea subclases de la clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), admite la adición y eliminación de efectos. La clase `RoutingEffect` representa un efecto independiente de la plataforma que encapsula un efecto interno, que suele ser específico de la plataforma. Esto simplifica el proceso de eliminación del efecto, ya que no hay ningún acceso en tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El constructor `LabelShadowEffect` llama al constructor de clase base, y se pasa un parámetro que consiste en la concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específica de la plataforma. Esto habilita la adición y eliminación del efecto en el método `OnHasShadowChanged`, como se indica a continuación:

- **Adición de efectos**: se agrega una nueva instancia de `LabelShadowEffect` a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control. Esto reemplaza al uso del método [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) para agregar el efecto.
- **Eliminación de efectos**: se recupera y se quita la primera instancia de `LabelShadowEffect` en la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control.

## <a name="consuming-the-effect"></a>Consumo del efecto

Cada elemento `LabelShadowEffect` específico de la plataforma se puede usar mediante la adición de las propiedades adjuntas a un control [`Label`](xref:Xamarin.Forms.Label), como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Al establecer la propiedad adjunta `ShadowEffect.HasShadow` en `true` se ejecuta el método `ShadowEffect.OnHasShadowChanged` que agrega o quita `LabelShadowEffect` del control [`Label`](xref:Xamarin.Forms.Label). En ambos ejemplos de código, la propiedad adjunta `ShadowEffect.Color` proporciona valores de color específicos de la plataforma. Para obtener más información, vea [Clase Device](~/xamarin-forms/platform/device.md).

Además, [`Button`](xref:Xamarin.Forms.Button) permite que se pueda cambiar el color de la sombra en tiempo de ejecución. Cuando se hace clic en `Button`, el código siguiente cambia el color de la sombra estableciendo la propiedad adjunta `ShadowEffect.Color`:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consumo del efecto con un estilo

Los efectos que se pueden consumir mediante la adición de propiedades adjuntas a un control también se pueden consumir con un estilo. En el ejemplo de código XAML siguiente se muestra un estilo *explícito* para el efecto de sombra, que se puede aplicar a controles [`Label`](xref:Xamarin.Forms.Label):

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) se puede aplicar a un control [`Label`](xref:Xamarin.Forms.Label) si se establece su propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) en la instancia de `Style` mediante la extensión de marcado `StaticResource`, como se muestra en el ejemplo de código siguiente:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.ShadowRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

El método `OnAttached` llama a métodos que recuperan los valores de propiedad adjunta mediante los captadores `ShadowEffect`, y que establecen propiedades `Control.Layer` en los valores de propiedad para crear la sombra. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

#### <a name="responding-to-property-changes"></a>Respuesta a los cambios de propiedad

Si alguno de los valores de las propiedades adjuntas `ShadowEffect` cambia en tiempo de ejecución, el efecto debe responder mostrando los cambios. Una versión invalidada del método `OnElementPropertyChanged` en la clase de efecto específica de la plataforma es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

El método `OnElementPropertyChanged` actualiza el radio, el color o el desplazamiento de la sombra, siempre que haya cambiado el valor de la propiedad adjunta `ShadowEffect` correspondiente. Siempre se debe realizar una comprobación de la propiedad que ha modificado, ya que esta invalidación se puede llamar varias veces.

### <a name="android-project"></a>Proyecto de Android

En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

El método `OnAttached` llama a métodos que recuperan los valores de propiedad adjunta mediante los captadores `ShadowEffect`, y llama a un método que llama al método [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) para crear una sombra con los valores de propiedad. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

#### <a name="responding-to-property-changes"></a>Respuesta a los cambios de propiedad

Si alguno de los valores de las propiedades adjuntas `ShadowEffect` cambia en tiempo de ejecución, el efecto debe responder mostrando los cambios. Una versión invalidada del método `OnElementPropertyChanged` en la clase de efecto específica de la plataforma es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

El método `OnElementPropertyChanged` actualiza el radio, el color o el desplazamiento de la sombra, siempre que haya cambiado el valor de la propiedad adjunta `ShadowEffect` correspondiente. Siempre se debe realizar una comprobación de la propiedad que ha modificado, ya que esta invalidación se puede llamar varias veces.

### <a name="universal-windows-platform-project"></a>Proyecto de la Plataforma universal de Windows

En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de Plataforma universal de Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

La Plataforma Universal de Windows no proporciona un efecto de sombra, por lo que la implementación de `LabelShadowEffect` en ambas plataformas simula una mediante la adición de un segundo control [`Label`](xref:Xamarin.Forms.Label) de desplazamiento detrás del control `Label` principal. El método `OnAttached` crea el objeto `Label` y establece algunas propiedades de diseño en el objeto `Label`. Después, llama a métodos que recuperan los valores de propiedad adjunta mediante los captadores `ShadowEffect`, y crea la sombra mediante el establecimiento de las propiedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor), [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) para controlar el color y la ubicación de `Label`. Después, el elemento `shadowLabel` se inserta mediante desplazamiento detrás del elemento `Label` principal. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga las propiedades `Control.Layer`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

#### <a name="responding-to-property-changes"></a>Respuesta a los cambios de propiedad

Si alguno de los valores de las propiedades adjuntas `ShadowEffect` cambia en tiempo de ejecución, el efecto debe responder mostrando los cambios. Una versión invalidada del método `OnElementPropertyChanged` en la clase de efecto específica de la plataforma es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

El método `OnElementPropertyChanged` actualiza el color o el desplazamiento de la sombra, siempre que haya cambiado el valor de la propiedad adjunta `ShadowEffect` correspondiente. Siempre se debe realizar una comprobación de la propiedad que ha modificado, ya que esta invalidación se puede llamar varias veces.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo usar propiedades adjuntas para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución. Las propiedades adjuntas se pueden usar para definir los parámetros de efecto que responden a los cambios de propiedades en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efecto de sombra (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)
