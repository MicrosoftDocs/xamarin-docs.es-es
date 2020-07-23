---
title: Personalización de una entrada
description: El control Entry de Xamarin.Forms permite que se edite una sola línea de texto. En este artículo se muestra cómo crear un representador personalizado para el control Entry, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5a5dc7de2835038079a1bdf8af5be44a173f86e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939833"
---
# <a name="customizing-an-entry"></a>Personalización de una entrada

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

_El control Entry de Xamarin.Forms permite que se edite una sola línea de texto. En este artículo se muestra cómo crear un representador personalizado para el control Entry, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma._

Todos los controles de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa un control [`Entry`](xref:Xamarin.Forms.Entry), se crea en iOS una instancia de la clase `EntryRenderer` que, a su vez, crea una instancia de un control `UITextField` nativo. En la plataforma Android, la clase `EntryRenderer` crea una instancia de un control `EditText`. En la Plataforma universal de Windows (UWP), la clase `EntryRenderer` crea una instancia de un control `TextBox`. Para obtener más información sobre el representador y las clases de control nativo a las que se asignan los controles de Xamarin.Forms, vea [Clases base y controles nativos del representador](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama muestra la relación entre el control [`Entry`](xref:Xamarin.Forms.Entry) y los controles nativos correspondientes que lo implementan:

![Relación entre el control Entry y los controles nativos de implementación](entry-images/entry-classes.png)

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma creando un representador personalizado para el control [`Entry`](xref:Xamarin.Forms.Entry) en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#creating-the-custom-entry-control) un control personalizado de Xamarin.Forms.
1. [Consuma](#consuming-the-custom-control) el control personalizado de Xamarin.Forms.
1. [Cree](#creating-the-custom-renderer-on-each-platform) el representador personalizado para el control en cada plataforma.

Ahora se analizará en detalle cada elemento, para implementar un control [`Entry`](xref:Xamarin.Forms.Entry) que tiene un color de fondo diferente en cada plataforma.

> [!IMPORTANT]
> En este artículo se explica cómo crear un representador personalizado simple. Empero, no es necesario crear un representador personalizado para implementar un `Entry` que tiene un color de fondo diferente en cada plataforma. Esto puede realizarse más fácilmente usando la clase [`Device`](xref:Xamarin.Forms.Device) o la extensión de marcado `OnPlatform`, para proporcionar los valores específicos de la plataforma. Para obtener más información, vea [Providing Platform-Specific Values](~/xamarin-forms/platform/device.md#provide-platform-specific-values) (Proporcionar valores específicos de la plataforma) y [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension) (Extensión de marcado OnPlatform).

## <a name="creating-the-custom-entry-control"></a>Creación de un control Entry personalizado

Se puede crear un control [`Entry`](xref:Xamarin.Forms.Entry) personalizado mediante la creación de subclases del control `Entry`, como se muestra en el siguiente ejemplo de código:

```csharp
public class MyEntry : Entry
{
}
```

El control `MyEntry` se crea en el proyecto de biblioteca de .NET Standard y es simplemente un control [`Entry`](xref:Xamarin.Forms.Entry). La personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en el control `MyEntry`.

## <a name="consuming-the-custom-control"></a>Uso del control personalizado

En XAML puede hacerse referencia al control `MyEntry` en el proyecto de biblioteca de .NET Standard declarando un espacio de nombres para su ubicación y usando el prefijo del espacio de nombres en el elemento de control. El siguiente ejemplo de código muestra cómo el control personalizado `MyEntry` puede utilizarse en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

El prefijo de espacio de nombres `local` puede tener cualquier nombre. Pero los valores `clr-namespace` y `assembly` deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se usa para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo el control personalizado `MyEntry` puede utilizarse en una página C#:

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Este código crea una instancia de un nuevo objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) que mostrará un [`Label`](xref:Xamarin.Forms.Label) y un control `MyEntry` centrado tanto vertical como horizontalmente en la página.

Ahora se puede agregar un representador personalizado a cada proyecto de aplicación para personalizar la apariencia del control en cada plataforma.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creación del representador personalizado en cada plataforma

El proceso para crear la clase del representador personalizado es el siguiente:

1. Cree una subclase de la clase `EntryRenderer` que representa el control nativo.
1. Invalide el método `OnElementChanged` que representa el control nativo y escriba lógica para personalizar el control. Se llama a este método cuando se crea el correspondiente control de Xamarin.Forms.
1. Agregue un atributo `ExportRenderer` a la clase de representador personalizada para especificar que se usará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no se registra un representador personalizado, se usará el representador predeterminado de la clase base del control.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![Responsabilidades del proyecto de representador personalizado MyEntry](entry-images/solution-structure.png)

Las clases `MyEntryRenderer` del representador específico de la plataforma, que se derivan de la clase `EntryRenderer` para cada plataforma, representan el control `MyEntry`. Esto da como resultado que cada control `MyEntry` se represente con un color de fondo específico de la plataforma, como se muestra en las siguientes capturas de pantalla:

![Control MyEntry en cada plataforma](entry-images/screenshots.png)

La clase `EntryRenderer` expone el método `OnElementChanged`, al que se llama cuando se crea el control de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un parámetro `ElementChangedEventArgs` que contiene propiedades `OldElement` y `NewElement`. Estas propiedades representan al elemento de Xamarin.Forms al que *estaba* asociado el representador y al elemento de Xamarin.Forms al que *está* asociado el representador, respectivamente. En la aplicación de ejemplo, la propiedad `OldElement` es `null` y la propiedad `NewElement` contiene una referencia al control de `MyEntry`.

El lugar para realizar la personalización del control nativo es una versión reemplazada del método `OnElementChanged` en la clase `MyEntryRenderer`. Una referencia con tipo para el control nativo que se usa en la plataforma puede obtenerse a través de la propiedad `Control`. Además, se puede obtener una referencia al control de Xamarin.Forms que se representa mediante la propiedad `Element`, aunque no se usa en la aplicación de ejemplo.

Cada clase de representador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control de Xamarin.Forms que se representa y el nombre de tipo del representador personalizado. El prefijo `assembly` para el atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se describe la implementación de cada clase de representador personalizado `MyEntryRenderer` específico de plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

El siguiente ejemplo de código muestra el representador personalizado para la plataforma iOS:

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

La llamada al método `OnElementChanged` de la clase base crea una instancia de un control `UITextField` de iOS, con una referencia al control que se asigna en la propiedad `Control` del representador. Después se establece el color de fondo en púrpura claro con el método `UIColor.FromRGB`.

### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

En el ejemplo de código siguiente se muestra el representador personalizado para la plataforma Android:

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

La llamada al método `OnElementChanged` de la clase base crea una instancia de un control `EditText` de Android, con una referencia al control que se asigna en la propiedad `Control` del representador. Después se establece el color de fondo en verde claro con el método `Control.SetBackgroundColor`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creación del representador personalizado en UWP

En el siguiente ejemplo de código se muestra el representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

La llamada al método `OnElementChanged` de la clase base crea una instancia de un control `TextBox`, con una referencia al control que se asigna en la propiedad `Control` del representador. Después se establece el color de fondo en cian mediante la creación de una instancia de `SolidColorBrush`.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear un representador de control personalizado para el control [`Entry`](xref:Xamarin.Forms.Entry) de Xamarin.Forms, lo que permite que los desarrolladores reemplacen la representación nativa predeterminada por su propia representación específica de la plataforma. Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia de los controles de Xamarin.Forms. Se pueden usar para realizar pequeños cambios de estilo o para una personalización sofisticada del diseño y el comportamiento específicos de una plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererEntry (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry) (CustomRendererEntry [ejemplo])
