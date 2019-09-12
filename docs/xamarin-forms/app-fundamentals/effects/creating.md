---
title: Creación de un efecto
description: Los efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control Entry cuando recibe el foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: c07848b808d023439c88117924e69c336984630b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771507"
---
# <a name="creating-an-effect"></a>Creación de un efecto

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Los efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control Entry cuando recibe el foco._

El proceso para crear un efecto de cada proyecto específico de la plataforma es el siguiente:

1. Se crea una subclase de la clase `PlatformEffect`.
1. Se invalida el método `OnAttached` y se escribe lógica para personalizar el control.
1. Se invalida el método `OnDetached` y se escribe lógica para limpiar la personalización del control, si es necesario.
1. Se agrega un atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) a la clase de efecto. Este atributo establece un espacio de nombres para los efectos para toda la empresa, lo que evita conflictos con otros efectos con el mismo nombre. Tenga en cuenta que este atributo solo se puede aplicar una vez por proyecto.
1. Agregue un atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) a la clase de efecto. Este atributo registra el efecto con un identificador único que Xamarin.Forms usa junto con el nombre del grupo para buscar el efecto antes de aplicarlo a un control. El atributo toma dos parámetros: el nombre de tipo del efecto y una cadena única que se usará para buscar el efecto antes de aplicarlo a un control.

Después, el efecto se puede consumir si se adjunta al control adecuado.

> [!NOTE]
> Proporcionar un efecto en cada proyecto de la plataforma es un paso opcional. Al intentar usar un efecto cuando no se ha registrado uno, se devolverá un valor distinto de NULL que no hace nada.

En la aplicación de ejemplo se muestra un elemento `FocusEffect` que cambia el color de fondo de un control cuando recibe el foco. En el diagrama siguiente se ilustran las responsabilidades de cada proyecto en la aplicación de ejemplo, junto con las relaciones entre ellos:

![](creating-images/focus-effect.png "Responsabilidades del proyecto de efecto de foco")

La clase `FocusEffect` personaliza un control [`Entry`](xref:Xamarin.Forms.Entry) en el elemento `HomePage` en cada proyecto específico de la plataforma. Cada clase `FocusEffect` se deriva de la clase `PlatformEffect` para cada plataforma. Como resultado, se representa el control `Entry` con un color de fondo específico de la plataforma, que cambia cuando el control recibe el foco, como se muestra en las capturas de pantalla siguientes:

![](creating-images/screenshots-1.png "Efecto de foco en cada plataforma")
![](creating-images/screenshots-2.png "Focus Effect on each Platform")

## <a name="creating-the-effect-on-each-platform"></a>Creación del efecto en cada plataforma

En las secciones siguientes se describe la implementación específica de la plataforma de la clase `FocusEffect`.

## <a name="ios-project"></a>Proyecto de iOS

En el ejemplo de código siguiente se muestra la implementación `FocusEffect` para el proyecto de iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

El método `OnAttached` establece la propiedad `BackgroundColor` del control en color morado claro con el método `UIColor.FromRGB` y también almacena este color en un campo. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga una propiedad `BackgroundColor`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

La invalidación de `OnElementPropertyChanged` responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando cambia la propiedad [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused), la propiedad `BackgroundColor` del control se cambia a color blanco si el control tiene el foco; en caso contrario, se cambia a color morado claro. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga una propiedad `BackgroundColor`.

## <a name="android-project"></a>Proyecto de Android

En el ejemplo de código siguiente se muestra la implementación `FocusEffect` para el proyecto de Android:

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

El método `OnAttached` llama al método `SetBackgroundColor` para establecer el color de fondo del control en verde claro y también almacena este color en un campo. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga una propiedad `SetBackgroundColor`. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

La invalidación de `OnElementPropertyChanged` responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando cambia la propiedad [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused), el color de fondo del control se cambia a color blanco si el control tiene el foco; en caso contrario, se cambia a color verde claro. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto no tenga una propiedad `BackgroundColor`.

## <a name="universal-windows-platform-projects"></a>Proyectos de la Plataforma universal de Windows

En el ejemplo de código siguiente se muestra la implementación `FocusEffect` para los proyectos de la Plataforma universal de Windows (UWP):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

El método `OnAttached` establece la propiedad `Background` del control en cian y la propiedad `BackgroundFocusBrush` en blanco. Esta funcionalidad se encapsula en un bloque `try`/`catch` en caso de que el control al que está asociado el efecto carezca de estas propiedades. El método `OnDetached` no proporciona ninguna implementación porque no se necesita limpieza.

## <a name="consuming-the-effect"></a>Consumo del efecto

El proceso para consumir un efecto desde un proyecto de biblioteca de .NET Standard o de biblioteca compartida de Xamarin.Forms es el siguiente:

1. Se declara un control que el efecto va a personalizar.
1. Se adjunta el efecto al control agregándolo a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control.

> [!NOTE]
> Una instancia de efecto solo se puede adjuntar a un único control. Por tanto, un efecto se debe resolver dos veces para usarlo en dos controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumo del efecto en XAML

En el ejemplo de código XAML siguiente se muestra un control [`Entry`](xref:Xamarin.Forms.Entry) al que se adjunta el elemento `FocusEffect`:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La clase `FocusEffect` de la biblioteca de .NET Standard admite el consumo de efectos en XAML, y se muestra en el ejemplo de código siguiente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

La clase `FocusEffect` crea subclases de la clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), que representa un efecto independiente de la plataforma que encapsula un efecto interno que suele ser específico de la plataforma. La clase `FocusEffect` llama al constructor de clase base, y se pasa un parámetro que consiste en la concatenación del nombre del grupo de resolución (que se especifica con el atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) en la clase de efecto), y el identificador único que se ha especificado con el atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) en la clase de efecto. Por tanto, cuando se inicializa [`Entry`](xref:Xamarin.Forms.Entry) en tiempo de ejecución, se agrega una nueva instancia de `MyCompany.FocusEffect` a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control.

Los efectos también se pueden adjuntar a los controles mediante un comportamiento, o bien mediante propiedades adjuntas. Para obtener más información sobre cómo adjuntar un efecto a un control mediante un comportamiento, vea [EffectBehavior reutilizable](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obtener más información sobre cómo adjuntar un efecto a un control mediante propiedades adjuntas, vea [Pasar parámetros a un efecto](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumo del efecto en C&num;

El control [`Entry`](xref:Xamarin.Forms.Entry) equivalente en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect` se adjunta a la instancia de `Entry` mediante la adición del efecto a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control, como se muestra en el ejemplo de código siguiente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) devuelve un elemento [`Effect`](xref:Xamarin.Forms.Effect) para el nombre especificado, que es una concatenación del nombre del grupo de resolución (que se especifica con el atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) en la clase de efecto), y el identificador único que se ha especificado con el atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) en la clase de efecto. Si una plataforma no proporciona el efecto, el método `Effect.Resolve` devolverá un valor que no es `null`.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear un efecto que cambia el color de fondo del control [`Entry`](xref:Xamarin.Forms.Entry) cuando el control recibe el foco.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Efecto de color de fondo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect)
- [Efecto de enfoque (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)
