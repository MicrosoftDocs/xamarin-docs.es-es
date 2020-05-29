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
ms.openlocfilehash: 8478db85bd9904ee6c5cfeab9b2af390e7d3096d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139508"
---
# <a name="platform-specifics"></a>Características específicas de las plataformas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados._

El proceso de consumo de un específico de la plataforma a través de XAML, o a través de la API de código Fluent es el siguiente:

1. Agregue una `xmlns` declaración o una `using` Directiva para el [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) espacio de nombres.
1. Agregue una `xmlns` declaración o una `using` Directiva para el espacio de nombres que contiene la funcionalidad específica de la plataforma:
    1. En iOS, es el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres.
    1. En Android, este es el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres. Para el AppCompat de Android, este es el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres.
    1. En el Plataforma universal de Windows, es el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres.
1. Aplique la plataforma específica de la plataforma desde XAML o desde el código con la `On<T>` API fluida. El valor de `T` puede ser los [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) tipos, o [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) del espacio de [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) nombres.

> [!NOTE]
> Tenga en cuenta que si se intenta consumir una plataforma específica en una plataforma en la que no está disponible, no se producirá un error. En su lugar, el código se ejecutará sin la aplicación específica de la plataforma.

Los específicos de la plataforma utilizados a través de la `On<T>` API de código Fluent devuelven [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos. Esto permite invocar varias características específicas de la plataforma en el mismo objeto con el método en cascada.

Para obtener más información sobre las características específicas de la plataforma que proporciona, consulte características específicas de la plataforma Xamarin.Forms [iOS](~/xamarin-forms/platform/ios/index.md), [características específicas](~/xamarin-forms/platform/android/index.md)de la plataforma Android y [características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creación de características específicas de la plataforma

Los proveedores pueden crear sus propias características específicas de la plataforma con efectos. Un efecto proporciona la funcionalidad específica, que se expone a través de un específico de la plataforma. El resultado es un efecto que se puede consumir más fácilmente a través de XAML y a través de una API de código Fluent.

El proceso para crear un específico de plataforma es el siguiente:

1. Implemente la funcionalidad específica como efecto. Para obtener más información, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Cree una clase específica de la plataforma que expondrá el efecto. Para obtener más información, vea [crear una clase específica de la plataforma](#creating-a-platform-specific-class).
1. En la clase específica de la plataforma, implemente una propiedad adjunta para permitir el consumo de la plataforma específica a través de XAML. Para obtener más información, vea [Agregar una propiedad adjunta](#adding-an-attached-property).
1. En la clase específica de la plataforma, implemente métodos de extensión para permitir el consumo de la plataforma específica a través de una API de código Fluent. Para obtener más información, vea [Agregar métodos de extensión](#adding-extension-methods).
1. Modifique la implementación del efecto para que el efecto se aplique solo si se ha invocado la plataforma específica en la misma plataforma que el efecto. Para obtener más información, vea [crear el efecto](#creating-the-effect).

El resultado de exponer un efecto como específico de la plataforma es que el efecto se puede consumir más fácilmente a través de XAML y a través de una API de código Fluent.

> [!NOTE]
> Está previsto que los proveedores utilicen esta técnica para crear sus propias características específicas de la plataforma, para facilitar el consumo de los usuarios. Aunque los usuarios pueden optar por crear sus propias características específicas de la plataforma, se debe tener en cuenta que requiere más código que la creación y el consumo de un efecto.

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) muestra un `Shadow` específico de la plataforma que agrega una sombra al texto mostrado por un [`Label`](xref:Xamarin.Forms.Label) control:

![](images/screenshots.png "Shadow Platform-Specific")

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa la `Shadow` plataforma específica en cada plataforma, para facilitar la comprensión. Sin embargo, aparte de cada implementación de efecto específico de la plataforma, la implementación de la clase Shadow es en gran medida idéntica para cada plataforma. Por lo tanto, esta guía se centra en la implementación de la clase Shadow y en el efecto asociado en una sola plataforma.

Para obtener más información sobre los efectos, vea [Personalizar controles con efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Crear una clase específica de la plataforma

Una clase específica de la plataforma se crea como una `public static` clase:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

En las secciones siguientes se describe la implementación de los `Shadow` efectos asociados y específicos de la plataforma.

#### <a name="adding-an-attached-property"></a>Agregar una propiedad adjunta

Una propiedad adjunta se debe agregar a la `Shadow` plataforma específica para permitir el consumo a través de XAML:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

La `IsShadowed` propiedad adjunta se usa para agregar el `MyCompany.LabelShadowEffect` efecto a y quitarlo de, el control `Shadow` al que está asociada la clase. Esta propiedad adjunta registra el método `OnIsShadowedPropertyChanged` que se ejecutará cuando cambie el valor de la propiedad. A su vez, este método llama `AttachEffect` al `DetachEffect` método o para agregar o quitar el efecto basado en el valor de la `IsShadowed` propiedad adjunta. El efecto se agrega o se quita del control modificando la colección del control [`Effects`](xref:Xamarin.Forms.Element.Effects) .

> [!NOTE]
> Tenga en cuenta que el efecto se resuelve especificando un valor que es una concatenación del nombre del grupo de resolución y el identificador único que se especifica en la implementación del efecto. Para obtener más información, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para más información sobre las propiedades adjuntas, consulte [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Agregar métodos de extensión

Los métodos de extensión se deben agregar a la `Shadow` plataforma específica para permitir el consumo a través de una API de código Fluent:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

Los `IsShadowed` `SetIsShadowed` métodos de extensión y invocan los descriptores de acceso get y set para la `IsShadowed` propiedad adjunta, respectivamente. Cada método de extensión funciona en el `IPlatformElementConfiguration<iOS, FormsElement>` tipo, que especifica que el específico de la plataforma se puede invocar en [`Label`](xref:Xamarin.Forms.Label) instancias de iOS.

#### <a name="creating-the-effect"></a>Crear el efecto

El `Shadow` específico de la plataforma agrega el `MyCompany.LabelShadowEffect` a un [`Label`](xref:Xamarin.Forms.Label) y lo quita. En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de iOS:

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
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

El `UpdateShadow` método establece `Control.Layer` las propiedades para crear la sombra, siempre que la `IsShadowed` propiedad adjunta esté establecida en `true` y siempre que se haya `Shadow` invocado la plataforma específica en la misma plataforma para la que se implementa el efecto. Esta comprobación se realiza con el `OnThisPlatform` método.

Si el `Shadow.IsShadowed` valor de la propiedad adjunta cambia en tiempo de ejecución, el efecto debe responder quitando la sombra. Por lo tanto, se usa una versión invalidada del `OnElementPropertyChanged` método para responder al cambio de la propiedad enlazable llamando al `UpdateShadow` método.

Para obtener más información sobre la creación de un efecto, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md) y [pasar parámetros de efecto como propiedades adjuntas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Consumo de las características específicas de la plataforma

La `Shadow` plataforma específica de la plataforma se consume en XAML estableciendo la `Shadow.IsShadowed` propiedad adjunta en un `boolean` valor:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Características específicas de la plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Características específicas de la plataforma Android](~/xamarin-forms/platform/android/index.md)
- [Características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizar controles con efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)
- [API de PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
