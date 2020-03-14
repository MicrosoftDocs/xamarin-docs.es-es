---
title: Características específicas de las plataformas
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir y crear características específicas de la plataforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305736"
---
# <a name="platform-specifics"></a>Características específicas de las plataformas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados._

El proceso para consumir una plataforma específica a través de XAML, o a través de la API fluida de código es como sigue:

1. Agregue una declaración de `xmlns` o una directiva de `using` para el espacio de nombres [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .
1. Agregue una declaración de `xmlns` o una directiva de `using` para el espacio de nombres que contiene la funcionalidad específica de la plataforma:
    1. En iOS, es el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) .
    1. En Android, es el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) . Para Android AppCompat, es el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) .
    1. En el Plataforma universal de Windows, es el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) .
1. Aplique la plataforma específica de la plataforma desde XAML o desde el código con la API fluida de `On<T>`. El valor de `T` puede ser los tipos [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS), [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android)o [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) del espacio de nombres [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .

> [!NOTE]
> Tenga en cuenta que intenta consumir un específico de la plataforma en una plataforma que no está disponible no dará como resultado un error. En su lugar, el código se ejecutará sin el específico de la plataforma que se va a aplicar.

Las características específicas de la plataforma que se usan a través de la API de código Fluent de `On<T>` devuelven [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos. Esto permite que varias funcionalidades específicas de plataforma que se invocará en el mismo objeto con el método en cascada.

Para obtener más información sobre las características específicas de la plataforma que proporciona Xamarin. Forms, consulte [características](~/xamarin-forms/platform/ios/index.md)específicas de la plataforma iOS, [características específicas](~/xamarin-forms/platform/android/index.md)de la plataforma Android y [características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creación de características específicas de la plataforma

Los proveedores pueden crear sus propias características específicas de la plataforma con efectos. Un efecto proporciona la funcionalidad específica, que, a continuación, se expone a través de una plataforma específica. El resultado es un efecto que puede consumir más fácilmente a través de XAML y una API fluida de código.

El proceso de creación de una plataforma específica es como sigue:

1. Implementar la funcionalidad específica como un efecto. Para obtener más información, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Cree una clase específica de la plataforma que se va a exponer el efecto. Para obtener más información, vea [crear una clase específica de la plataforma](#creating-a-platform-specific-class).
1. En la clase específica de la plataforma, implemente una propiedad adjunta para permitir el específico de la plataforma para consumirse a través de XAML. Para obtener más información, vea [Agregar una propiedad adjunta](#adding-an-attached-property).
1. En la clase específica de la plataforma, implemente los métodos de extensión para permitir el específico de la plataforma para consumirse a través de una API fluida de código. Para obtener más información, vea [Agregar métodos de extensión](#adding-extension-methods).
1. Modifique la implementación de efecto para que el efecto se aplica solo si se ha invocado el específico de la plataforma en la misma plataforma que el efecto. Para obtener más información, vea [crear el efecto](#creating-the-effect).

El resultado de exponer un efecto como una plataforma específica es que el efecto se puede consumir más fácilmente a través de XAML y una API fluida de código.

> [!NOTE]
> Se prevé que los proveedores usará esta técnica para crear sus propias funcionalidades específicas de plataforma, para facilitar su consumo por usuarios. Mientras que los usuarios pueden elegir crear sus propias funcionalidades específicas de plataforma, se debe tener en cuenta que requiere más código que la creación y consumo de un efecto.

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) muestra una `Shadow` específica de la plataforma que agrega una sombra al texto que muestra un control [`Label`](xref:Xamarin.Forms.Label) :

![](images/screenshots.png "Shadow Platform-Specific")

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa la `Shadow` específica de la plataforma en cada plataforma, para facilitar la comprensión. Sin embargo, aparte de cada implementación de efecto específico de la plataforma, la implementación de la clase de la sombra es idéntica en gran medida para cada plataforma. Por lo tanto, esta guía se centra en la implementación de la clase de instantáneas y el efecto asociado en una sola plataforma.

Para obtener más información sobre los efectos, vea [Personalizar controles con efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Crear una clase específica de la plataforma

Se crea un específico de la plataforma como una clase `public static`:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

En las secciones siguientes se describe la implementación del `Shadow` efecto específico de la plataforma y asociado.

#### <a name="adding-an-attached-property"></a>Agregar una propiedad adjunta

Se debe agregar una propiedad adjunta al `Shadow` específico de la plataforma para permitir el consumo a través de XAML:

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

La propiedad adjunta `IsShadowed` se utiliza para agregar el efecto `MyCompany.LabelShadowEffect` a y quitarlo de, el control al que está asociada la clase `Shadow`. Esta propiedad adjunta registra el método `OnIsShadowedPropertyChanged` que se ejecutará cuando cambie el valor de la propiedad. A su vez, este método llama al método `AttachEffect` o `DetachEffect` para agregar o quitar el efecto en función del valor de la propiedad adjunta de `IsShadowed`. El efecto se agrega o se quita del control modificando la colección de [`Effects`](xref:Xamarin.Forms.Element.Effects) del control.

> [!NOTE]
> Tenga en cuenta que el efecto se resuelve mediante la especificación de un valor que es una concatenación del nombre del grupo de resolución y el identificador único que se especifica en la implementación del efecto. Para obtener más información, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para más información sobre las propiedades adjuntas, consulte [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Adición de métodos de extensión

Los métodos de extensión se deben agregar al `Shadow` específico de la plataforma para permitir el consumo a través de una API de código Fluent:

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

Los métodos de extensión `IsShadowed` y `SetIsShadowed` invocan los descriptores de acceso get y set para la propiedad adjunta de `IsShadowed`, respectivamente. Cada método de extensión funciona en el tipo de `IPlatformElementConfiguration<iOS, FormsElement>`, que especifica que el específico de la plataforma se puede invocar en [`Label`](xref:Xamarin.Forms.Label) instancias de iOS.

#### <a name="creating-the-effect"></a>Crear el efecto

El `Shadow` específico de la plataforma agrega el `MyCompany.LabelShadowEffect` a un [`Label`](xref:Xamarin.Forms.Label)y lo quita. En el ejemplo de código siguiente se muestra la implementación `LabelShadowEffect` para el proyecto de iOS:

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

El método `UpdateShadow` establece `Control.Layer` propiedades para crear la sombra, siempre que el `IsShadowed` propiedad adjunta se establezca en `true`y siempre que se haya invocado el `Shadow` específico de la plataforma en la misma plataforma para la que se implementa el efecto. Esta comprobación se realiza con el método `OnThisPlatform`.

Si el `Shadow.IsShadowed` valor de propiedad adjunta cambia en tiempo de ejecución, el efecto debe responder quitando la sombra. Por lo tanto, se usa una versión invalidada del método `OnElementPropertyChanged` para responder al cambio de la propiedad enlazable llamando al método `UpdateShadow`.

Para obtener más información sobre la creación de un efecto, vea [crear un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md) y [pasar parámetros de efecto como propiedades adjuntas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Consumo de las características específicas de la plataforma

El `Shadow` específico de la plataforma se consume en XAML estableciendo la propiedad adjunta `Shadow.IsShadowed` en un valor `boolean`:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

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
- [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md)
- [API de PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
