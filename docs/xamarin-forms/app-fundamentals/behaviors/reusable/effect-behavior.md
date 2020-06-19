---
title: EffectBehavior reutilizable
description: Los comportamientos son una manera útil de agregar un efecto a un control, quitando el código de control de efecto reutilizable de los archivos de código subyacente. En este artículo se explica cómo crear y consumir un comportamiento de Xamarin.Forms para agregar un efecto a un control.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0a105548f869eb448a990a1cc12e6feff4197d48
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135790"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizable

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Los comportamientos son una manera útil de agregar un efecto a un control, quitando el código de control de efecto reutilizable de los archivos de código subyacente. En este artículo se explica cómo crear y consumir un comportamiento de Xamarin.Forms para agregar un efecto a un control._

## <a name="overview"></a>Información general

La clase `EffectBehavior` es un comportamiento personalizado de Xamarin.Forms reutilizable que agrega una instancia de [`Effect`](xref:Xamarin.Forms.Effect) a un control cuando el comportamiento se asocia al control, y quita la instancia de `Effect` cuando el comportamiento se desasocia del control.

Para usar el comportamiento, se deben establecer las propiedades de comportamiento siguientes:

- **Group**: el valor del atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para la clase del efecto.
- **Name**: el valor del atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para la clase del efecto.

Para obtener más información sobre los efectos, vea [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior` es una clase personalizada que se puede encontrar en el [ejemplo Comportamiento de un efecto](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior) y que no forma parte de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creación del comportamiento

La clase `EffectBehavior` se deriva de la clase [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), donde `T` es un objeto [`View`](xref:Xamarin.Forms.View). Esto significa que la clase `EffectBehavior` se puede asociar a cualquier control de Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementación de propiedades enlazables

La clase `EffectBehavior` define dos instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), que se usan para agregar un elemento [`Effect`](xref:Xamarin.Forms.Effect) a un control cuando el comportamiento está asociado al control. Estas propiedades se muestran en el ejemplo de código siguiente:

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Cuando se consume `EffectBehavior`, la propiedad `Group` se debe establecer en el valor del atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) para el efecto. Además, la propiedad `Name` se debe establecer en el valor del atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para la clase de efecto.

### <a name="implementing-the-overrides"></a>Implementación de las invalidaciones

La clase `EffectBehavior` invalida los métodos [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) de la clase [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), como se muestra en el ejemplo de código siguiente:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

El método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the `AddEffect` method, passing in the attached control as a parameter. The [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) realiza una limpieza mediante una llamada al método `RemoveEffect`, para lo que pasa el control asociado como parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementación de la funcionalidad del comportamiento

El propósito del comportamiento es agregar el elemento [`Effect`](xref:Xamarin.Forms.Effect) definido en las propiedades `Group` y `Name` a un control cuando el comportamiento está asociado al control, y quitar el elemento `Effect` cuando es el comportamiento se desasocia del control. La funcionalidad básica del comportamiento se muestra en el ejemplo de código siguiente:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

El método `AddEffect` se ejecuta en respuesta a la asociación de `EffectBehavior` a un control y recibe el control adjunto como un parámetro. Después, el método agrega el efecto recuperado a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control. El método `RemoveEffect` se ejecuta en respuesta a la desasociación de `EffectBehavior` de un control y recibe el control adjunto como un parámetro. Después, el método quita el efecto de la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control.

El método`GetEffect` usa el método [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) para recuperar el elemento [`Effect`](xref:Xamarin.Forms.Effect). El efecto se localiza a través de una concatenación de los valores de propiedad `Group` y `Name`. Si una plataforma no proporciona el efecto, el método `Effect.Resolve` devolverá un valor que no es `null`.

## <a name="consuming-the-behavior"></a>Consumo del comportamiento

La clase `EffectBehavior` se puede adjuntar a la colección [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de un control, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

Las propiedades `Group` y `Name` del comportamiento se establecen en los valores de los atributos [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) y [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) para la clase de efecto de cada proyecto específico de la plataforma.

En tiempo de ejecución, cuando el comportamiento se asocia al control [`Label`](xref:Xamarin.Forms.Label), se agregará `Xamarin.LabelShadowEffect` a la colección [`Effects`](xref:Xamarin.Forms.Element.Effects) del control. Como resultado, se agrega una sombra al texto mostrado por el control `Label`, como se muestra en las capturas de pantalla siguientes:

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

La ventaja de usar este comportamiento para agregar y quitar efectos de los controles es que se puede quitar el código de control de efecto reutilizable de los archivos de código subyacente.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado el uso de un comportamiento para agregar un efecto a un control. La clase `EffectBehavior` es un comportamiento personalizado de Xamarin.Forms reutilizable que agrega una instancia de [`Effect`](xref:Xamarin.Forms.Effect) a un control cuando el comportamiento se asocia al control, y quita la instancia de `Effect` cuando el comportamiento se desasocia del control.

## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamiento de un efecto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
