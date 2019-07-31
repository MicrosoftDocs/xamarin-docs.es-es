---
title: Funcionalidades específicas de plataforma
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir y crear características específicas de la plataforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646837"
---
# <a name="platform-specifics"></a>Características específicas de las plataformas

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos._

El proceso para consumir una plataforma específica a través de XAML, o a través de la API fluida de código es como sigue:

1. Agregar un `xmlns` declaración o `using` la directiva para el [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) espacio de nombres.
1. Agregar un `xmlns` declaración o `using` la directiva para el espacio de nombres que contiene la funcionalidad específica de la plataforma:
    1. En iOS, se trata la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres.
    1. En Android, se trata la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres. Para Android AppCompat, este es el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres.
    1. En la plataforma Universal de Windows, se trata la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres.
1. Aplicar el específico de la plataforma de XAML, o de código con el `On<T>` API fluida. El valor de `T` puede ser el [ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS), [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android), o [ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows) tipos a partir de la [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) espacio de nombres.

> [!NOTE]
> Tenga en cuenta que intenta consumir un específico de la plataforma en una plataforma que no está disponible no dará como resultado un error. En su lugar, el código se ejecutará sin el específico de la plataforma que se va a aplicar.

Funcionalidades específicas de plataforma consumen a través de la `On<T>` devueltos de la API de código fluent [ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos. Esto permite que varias funcionalidades específicas de plataforma que se invocará en el mismo objeto con el método en cascada.

Para obtener más información sobre las características específicas de la plataforma que proporciona Xamarin. Forms, consulte [características](~/xamarin-forms/platform/ios/index.md)específicas de la plataforma iOS, [características específicas](~/xamarin-forms/platform/android/index.md)de la plataforma Android y [características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creación de características específicas de la plataforma

Los proveedores pueden crear sus propias características específicas de la plataforma con efectos. Un efecto proporciona la funcionalidad específica, que, a continuación, se expone a través de una plataforma específica. El resultado es un efecto que puede consumir más fácilmente a través de XAML y una API fluida de código.

El proceso de creación de una plataforma específica es como sigue:

1. Implementar la funcionalidad específica como un efecto. Para obtener más información, consulte [crea un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Cree una clase específica de la plataforma que se va a exponer el efecto. Para obtener más información, consulte [creación de una clase específica de la plataforma](#creating-a-platform-specific-class).
1. En la clase específica de la plataforma, implemente una propiedad adjunta para permitir el específico de la plataforma para consumirse a través de XAML. Para obtener más información, consulte [agregar una propiedad adjunta](#adding-an-attached-property).
1. En la clase específica de la plataforma, implemente los métodos de extensión para permitir el específico de la plataforma para consumirse a través de una API fluida de código. Para obtener más información, consulte [agregar métodos de extensión](#adding-extension-methods).
1. Modifique la implementación de efecto para que el efecto se aplica solo si se ha invocado el específico de la plataforma en la misma plataforma que el efecto. Para obtener más información, consulte [crea el efecto de](#creating-the-effect).

El resultado de exponer un efecto como una plataforma específica es que el efecto se puede consumir más fácilmente a través de XAML y una API fluida de código.

> [!NOTE]
> Se prevé que los proveedores usará esta técnica para crear sus propias funcionalidades específicas de plataforma, para facilitar su consumo por usuarios. Mientras que los usuarios pueden elegir crear sus propias funcionalidades específicas de plataforma, se debe tener en cuenta que requiere más código que la creación y consumo de un efecto.

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) muestra `Shadow` un específico de la plataforma que agrega una sombra al texto mostrado por [`Label`](xref:Xamarin.Forms.Label) un control:

![](images/screenshots.png "Sombra específicos de la plataforma")

La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa la `Shadow` plataforma específica en cada plataforma, para facilitar la comprensión. Sin embargo, aparte de cada implementación de efecto específico de la plataforma, la implementación de la clase de la sombra es idéntica en gran medida para cada plataforma. Por lo tanto, esta guía se centra en la implementación de la clase de instantáneas y el efecto asociado en una sola plataforma.

Para obtener más información acerca de los efectos, vea [personalización de controles con efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Crear una clase específica de la plataforma

Se crea una plataforma específica como un `public static` clase:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Las secciones siguientes describen la implementación de la `Shadow` efecto específico de la plataforma y asociado.

#### <a name="adding-an-attached-property"></a>Agregar una propiedad adjunta

Una propiedad adjunta debe agregarse a la `Shadow` específicos de la plataforma para permitir el consumo a través de XAML:

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

El `IsShadowed` propiedad adjunta se utiliza para agregar el `MyCompany.LabelShadowEffect` efecto y lo eliminará del control que el `Shadow` clase se adjunta a. Esto adjunta la propiedad registra el `OnIsShadowedPropertyChanged` método que se ejecutará cuando cambia el valor de la propiedad. A su vez, llama este método la `AttachEffect` o `DetachEffect` método para agregar o quitar el efecto en función del valor de la `IsShadowed` propiedad adjunta. El efecto se agrega o quita el control modificando el control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección.

> [!NOTE]
> Tenga en cuenta que el efecto se resuelve mediante la especificación de un valor que es una concatenación del nombre del grupo de resolución y el identificador único que se especifica en la implementación del efecto. Para obtener más información, consulte [crea un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para obtener más información sobre las propiedades adjuntas, consulte [Attached Properties](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Adición de métodos de extensión

Métodos de extensión deben agregarse a la `Shadow` específicos de la plataforma para permitir el consumo a través de una API fluida de código:

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

El `IsShadowed` y `SetIsShadowed` invocar la operación get de métodos de extensión y establecer los descriptores de acceso para el `IsShadowed` propiedad adjunta, respectivamente. Cada método de extensión funciona en el `IPlatformElementConfiguration<iOS, FormsElement>` type que especifica que se puede invocar el específico de la plataforma en [ `Label` ](xref:Xamarin.Forms.Label) instancias de iOS.

#### <a name="creating-the-effect"></a>Crear el efecto

El `Shadow` específicos de la plataforma agrega el `MyCompany.LabelShadowEffect` a un [ `Label` ](xref:Xamarin.Forms.Label)y lo quita. El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de iOS:

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

El `UpdateShadow` método establece `Control.Layer` propiedades para crear la sombra, siempre que el `IsShadowed` se establece la propiedad adjunta en `true`y siempre que el `Shadow` específico de la plataforma que se haya invocado en la misma plataforma que el Efecto se implementa. Esta comprobación se realiza con el `OnThisPlatform` método.

Si el `Shadow.IsShadowed` conectados cambia de valor de propiedad en tiempo de ejecución, las necesidades de efecto para responder mediante la eliminación de la sombra. Por lo tanto, una versión reemplazada de la `OnElementPropertyChanged` método se usa para responder al cambio de propiedad enlazable mediante una llamada a la `UpdateShadow` método.

Para obtener más información acerca de cómo crear un efecto, consulte [crea un efecto](~/xamarin-forms/app-fundamentals/effects/creating.md) y [pasar parámetros de efecto como adjunta propiedades](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Consumo de las características específicas de la plataforma

El `Shadow` específicos de la plataforma se consumen en XAML estableciendo el `Shadow.IsShadowed` propiedad adjunta un `boolean` valor:

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
- [Personalización de controles con efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propiedades asociadas](~/xamarin-forms/xaml/attached-properties.md)
- [API de PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
