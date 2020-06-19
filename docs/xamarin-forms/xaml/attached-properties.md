---
title: Propiedades adjuntas
description: En este artículo se proporciona una introducción a las propiedades adjuntas y se muestra cómo crearlas y consumirlas.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f26a4415a75b2b02fd7d6893e366ef81156f077
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138195"
---
# <a name="attached-properties"></a>Propiedades adjuntas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


Las propiedades adjuntas permiten a un objeto asignar un valor para una propiedad que su propia clase no define. Por ejemplo, los elementos secundarios pueden utilizar las propiedades adjuntas para informar a su elemento primario de cómo deben presentarse en la interfaz de usuario. El [`Grid`](xref:Xamarin.Forms.Grid) control permite especificar la fila y la columna de un elemento secundario mediante el establecimiento de las `Grid.Row` `Grid.Column` propiedades adjuntas y. `Grid.Row`y `Grid.Column` son propiedades adjuntas porque se establecen en elementos que son elementos secundarios de un `Grid` , en lugar de en el `Grid` propio.

Las propiedades enlazables se deben implementar como propiedades adjuntas en los escenarios siguientes:

- Cuando es necesario tener un mecanismo de configuración de propiedades disponible para las clases que no sean la clase de definición.
- Cuando la clase representa un servicio que debe integrarse fácilmente con otras clases.

Para obtener más información sobre las propiedades enlazables, vea [propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="create-an-attached-property"></a>Crear una propiedad adjunta

El proceso para crear una propiedad adjunta es el siguiente:

1. Cree una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia de con una de las [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) sobrecargas del método.
1. Proporcione `static` `Get` los métodos *PropertyName* y `Set` *PropertyName* como descriptores de acceso para la propiedad adjunta.

### <a name="create-a-property"></a>Crear una propiedad

Al crear una propiedad adjunta para su uso en otros tipos, la clase en la que se crea la propiedad no tiene que derivar de [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Sin embargo, la propiedad de *destino* de los descriptores de acceso debe ser de o derivar de [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

Una propiedad adjunta se puede crear mediante la declaración `public static readonly` de una propiedad de tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.CreateAttached` ] (XREF: Xamarin.Forms . BindableProperty. CreateAttached (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . Sobrecargas del método BindableProperty. CreateDefaultValueDelegate)). La declaración debe estar dentro del cuerpo de la clase propietaria, pero fuera de las definiciones de miembros.

En el código siguiente se muestra un ejemplo de una propiedad adjunta:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Esto crea una propiedad adjunta denominada `HasShadow` , de tipo `bool` . La propiedad es propiedad de la `ShadowEffect` clase y su valor predeterminado es `false` . La Convención de nomenclatura para las propiedades adjuntas es que el identificador de la propiedad adjunta debe coincidir con el nombre de propiedad especificado en el `CreateAttached` método, con la propiedad "Property" anexada. Por lo tanto, en el ejemplo anterior, el identificador de la propiedad adjunta es `HasShadowProperty` .

Para obtener más información sobre cómo crear propiedades enlazables, incluidos los parámetros que se pueden especificar durante la creación, vea [crear una propiedad enlazable](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property).

### <a name="create-accessors"></a>Crear descriptores de acceso

Los métodos estáticos `Get` *PropertyName* y `Set` *PropertyName* son necesarios como descriptores de acceso para la propiedad adjunta; de lo contrario, el sistema de propiedades no podrá utilizar la propiedad adjunta. El `Get` descriptor de acceso *PropertyName* debe cumplir la siguiente firma:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

El `Get` descriptor de acceso *PropertyName* debe devolver el valor incluido en el `BindableProperty` campo correspondiente para la propiedad adjunta. Esto puede lograrse llamando a [ `GetValue` ] (XREF: Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), pasando el identificador de la propiedad enlazable en la que se obtiene el valor y, a continuación, convirtiendo el valor resultante al tipo requerido.

El `Set` descriptor de acceso *PropertyName* debe cumplir la siguiente firma:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

El `Set` descriptor de acceso *PropertyName* debe establecer el valor del `BindableProperty` campo correspondiente para la propiedad adjunta. Esto puede lograrse llamando a [ `SetValue` ] (XREF: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindableProperty, System. Object)), pasando el identificador de la propiedad enlazable en la que se establece el valor y el valor que se va a establecer.

En ambos descriptores de acceso, el objeto de *destino* debe ser o derivar de [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

En el ejemplo de código siguiente se muestran los descriptores de acceso de la `HasShadow` propiedad adjunta:

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consume-an-attached-property"></a>Usar una propiedad adjunta

Una vez que se ha creado una propiedad adjunta, se puede usar desde XAML o desde código. En XAML, esto se logra declarando un espacio de nombres con un prefijo, con la declaración del espacio de nombres que indica el nombre del espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Para obtener más información, vea [espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad adjunta, que se define dentro del mismo ensamblado que el código de aplicación que hace referencia al tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La declaración del espacio de nombres se utiliza a continuación al establecer la propiedad adjunta en un control concreto, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>Usar una propiedad adjunta con un estilo

Las propiedades adjuntas también se pueden agregar a un control mediante un estilo. En el ejemplo de código XAML siguiente se muestra un estilo *explícito* que utiliza la `HasShadow` propiedad adjunta, que se puede aplicar a [`Label`](xref:Xamarin.Forms.Label) los controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) se puede aplicar a un control [`Label`](xref:Xamarin.Forms.Label) si se establece su propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) en la instancia de `Style` mediante la extensión de marcado `StaticResource`, como se muestra en el ejemplo de código siguiente:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Escenarios avanzados

Al crear una propiedad adjunta, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios avanzados de propiedades adjuntas. Esto incluye la detección de cambios de propiedades, la validación de los valores de propiedad y la conversión de valores de propiedad. Para obtener más información, vea [escenarios avanzados](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios).

## <a name="related-links"></a>Vínculos relacionados

- [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md)
- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efecto de sombra (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [API de BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API de BindableObject](xref:Xamarin.Forms.BindableObject)
