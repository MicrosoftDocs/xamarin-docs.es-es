---
title: Creación de un representador visual de Xamarin. Forms
description: Cree objetos visuales de Xamarin. Forms que se apliquen de forma selectiva a objetos VisualElement, sin tener que crear subclases de las vistas de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 71836021b29094911f9d1ad43639ed103fa18b73
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652815"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Creación de un representador visual de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

El objeto visual de Xamarin. Forms permite crear representadores y aplicarlos de forma selectiva a [`VisualElement`](xref:Xamarin.Forms.VisualElement) los objetos, sin tener que crear subclases de las vistas de Xamarin. Forms. Se usará un representador `IVisual` que especifique un tipo, como `ExportRendererAttribute`parte de su, para representar en las vistas, en lugar del representador predeterminado. En tiempo de selección del representador, el `Visual` propiedad de la vista es inspeccionar y se incluye en el proceso de selección del representador.

> [!IMPORTANT]
> Actualmente no [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) se puede cambiar la propiedad una vez representada la vista, pero esto cambiará en una versión futura.

El proceso para crear y utilizar un representador visual de Xamarin. Forms es:

1. Cree representadores de plataforma para la vista necesaria. Para obtener más información, vea [crear](#create-platform-renderers)representadores.
1. Cree un tipo que se derive de `IVisual`. Para obtener más información, vea [crear un tipo IVisual](#create-an-ivisual-type).
1. Registre el `IVisual` tipo como parte `ExportRendererAttribute` del que decora los representadores. Para obtener más información, consulte [registrar el tipo IVisual](#register-the-ivisual-type).
1. Para usar el representador visual, [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) establezca la propiedad de la vista `IVisual` en el nombre. Para obtener más información, vea [usar el representador visual](#consume-the-visual-renderer).
1. opta Registre un nombre para el `IVisual` tipo. Para obtener más información, vea [registrar un nombre para el tipo IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Crear representadores de plataforma

Para obtener información sobre cómo crear una clase de representador, vea representadores [personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Sin embargo, tenga en cuenta que un representador visual de Xamarin. Forms se aplica a una vista sin tener que subclaser la vista.

Las clases de representador que se describen aquí implementan un personalizado [`Button`](xref:Xamarin.Forms.Button) que muestra el texto con una sombra.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra el representador de botón para iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra el representador de botón para Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Creación de un tipo de IVisual

En la biblioteca multiplataforma, cree un tipo que derive de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

Después `CustomVisual` , el tipo se puede registrar en las clases de representador, [`Button`](xref:Xamarin.Forms.Button) lo que permite a los objetos participar en el uso de los representadores.

## <a name="register-the-ivisual-type"></a>Registrar el tipo de IVisual

En los proyectos de la plataforma, Decore las clases de `ExportRendererAttribute`representador con:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

`ExportRendererAttribute` En este ejemplo, especifica que la `CustomButtonRenderer` clase se [`Button`](xref:Xamarin.Forms.Button) usará para representar objetos de consumo, con el `IVisual` tipo registrado como tercer argumento. Se usará un representador `IVisual` que especifique un tipo, como `ExportRendererAttribute`parte de su, para representar en las vistas, en lugar del representador predeterminado.

## <a name="consume-the-visual-renderer"></a>Usar el representador visual

Un [`Button`](xref:Xamarin.Forms.Button) objeto puede optar por usar las clases de representador [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) estableciendo su `Custom`propiedad en:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> En XAML, un convertidor de tipos quita la necesidad de incluir el sufijo "visual" en [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) el valor de propiedad. Sin embargo, también se puede especificar el nombre de tipo completo.

El código de C# equivalente es:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

En el momento [`Button`](xref:Xamarin.Forms.Button) de la selección del [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) representador, la propiedad de se inspecciona y se incluye en el proceso de selección del representador. Si no se encuentra un representador, se usará el representador predeterminado de Xamarin. Forms.

Las capturas de pantallas siguientes muestran [`Button`](xref:Xamarin.Forms.Button)el representado, que muestra su texto con una sombra:

[![Captura de pantalla del botón personalizado con texto de sombra, en iOS y Android](material-visual-images/custom-button.png "Botón con texto de sombra")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registro de un nombre para el tipo IVisual

Se puede utilizar para registrar opcionalmente un nombre diferente para el `IVisual` tipo. [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) Este enfoque se puede usar para resolver conflictos de nomenclatura entre diferentes bibliotecas visuales o en situaciones en las que solo desea hacer referencia a un visual con un nombre diferente al nombre de su tipo.

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) Debe definirse en el nivel de ensamblado en la biblioteca multiplataforma o en el proyecto de plataforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

A `IVisual` continuación, el tipo se puede consumir a través de su nombre registrado:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Al consumir un visual a través de su nombre registrado, se debe incluir cualquier sufijo "visual".

## <a name="related-links"></a>Vínculos relacionados

- [Material visual (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Material visual de Xamarin. Forms](material-visual.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
