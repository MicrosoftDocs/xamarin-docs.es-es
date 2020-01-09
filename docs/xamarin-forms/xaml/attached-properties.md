---
title: Propiedades adjuntas
description: Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 78dd2d3a63cd0e2b6ab1e6876dd82f49f5580f0b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489978"
---
# <a name="attached-properties"></a>Propiedades adjuntas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


Adjunta propiedades habilitar un objeto para asignar un valor para una propiedad que no define su propia clase. Por ejemplo, secundario, pueden usar los elementos vinculados propiedades para informar a su elemento primario de cómo se presentarán en la interfaz de usuario. El [ `Grid` ](xref:Xamarin.Forms.Grid) control permite la fila y columna de un elemento secundario que se especifica estableciendo el `Grid.Row` y `Grid.Column` propiedades adjuntas. `Grid.Row` y `Grid.Column` son las propiedades adjuntas porque se definen los elementos que son elementos secundarios de un `Grid`, en lugar de en el `Grid` propio.

Propiedades enlazables deben implementarse como propiedades adjuntas en los escenarios siguientes:

- Cuando no hay necesidad de tener un mecanismo de configuración de propiedades disponible para las clases distintas de la definición de clase.
- Cuando la clase representa un servicio que necesita para integrarse fácilmente con otras clases.

Para obtener más información acerca de las propiedades enlazables, vea [propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="create-an-attached-property"></a>Crear una propiedad adjunta

El proceso de creación de una propiedad adjunta es como sigue:

1. Crear un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancia con uno de los [ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*) sobrecargas del método.
1. Proporcionar `static` `Get` *PropertyName* y `Set` *PropertyName* métodos como descriptores de acceso para la propiedad adjunta.

### <a name="create-a-property"></a>Crear una propiedad

Al crear una propiedad adjunta para su uso en otros tipos, la clase donde se crea la propiedad no tiene que derivar [ `BindableObject` ](xref:Xamarin.Forms.BindableObject). Sin embargo, el *destino* debe ser de propiedad para los descriptores de acceso o derivarse de ella [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

Una propiedad adjunta se puede crear mediante la declaración de un `public static readonly` propiedad de tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) sobrecargas del método. La declaración debe estar dentro del cuerpo de la clase propietaria, pero fuera de las definiciones de miembros.

El código siguiente muestra un ejemplo de una propiedad asociada:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Esto crea una propiedad adjunta mencionada `HasShadow`, del tipo `bool`. La propiedad pertenece a la `ShadowEffect` clase y tiene un valor predeterminado de `false`. La convención de nomenclatura para las propiedades adjuntas es que el identificador de propiedad adjunta debe coincidir con el nombre de propiedad especificado en el `CreateAttached` método con "Property" anexado a él. Por lo tanto, en el ejemplo anterior, el identificador de la propiedad adjunta es `HasShadowProperty`.

Para obtener más información sobre cómo crear propiedades enlazables, incluidos los parámetros que se pueden especificar durante la creación, vea [crear una propiedad enlazable](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property).

### <a name="create-accessors"></a>Crear descriptores de acceso

Estática `Get` *PropertyName* y `Set` *PropertyName* métodos son necesarios como descriptores de acceso para la propiedad adjunta, en caso contrario, el sistema de propiedades no se puede usar el propiedad adjunta. El `Get` *PropertyName* descriptor de acceso debe ajustarse a la firma siguiente:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

El `Get` *PropertyName* descriptor de acceso debe devolver el valor que se encuentra en las correspondientes `BindableProperty` campo para la propiedad adjunta. Esto puede lograrse mediante una llamada a la [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método, pasando el identificador de propiedad enlazable en la que se va a obtener el valor y, a continuación, convertir el valor resultante al tipo requerido.

El `Set` *PropertyName* descriptor de acceso debe ajustarse a la firma siguiente:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

El `Set` *PropertyName* descriptor de acceso debe establecer el valor de la correspondiente `BindableProperty` campo para la propiedad adjunta. Esto puede lograrse mediante una llamada a la [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método, pasando el identificador de propiedad enlazable en la que se va a establecer el valor y el valor que se va a establecer.

Para ambos descriptores de acceso, el *destino* debe ser de objeto o derivarse de ella [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

El ejemplo de código siguiente muestra los descriptores de acceso para el `HasShadow` propiedad asociada:

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

Una vez creada una propiedad adjunta, se puede consumir desde XAML o código. En XAML, esto se logra mediante la declaración de un espacio de nombres con un prefijo, con la declaración de espacio de nombres que indica el nombre del espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Para obtener más información, consulte [los espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad adjunta, que se define dentro del mismo ensamblado que el código de aplicación que se hace referencia al tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La declaración de espacio de nombres, a continuación, se utiliza al establecer la propiedad adjunta en un control específico, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>Usar una propiedad adjunta con un estilo

Las propiedades adjuntas también pueden agregarse a un control por un estilo. El ejemplo de código XAML siguiente muestra un *explícita* estilo que usa el `HasShadow` propiedad adjunta, que se puede aplicar a [ `Label` ](xref:Xamarin.Forms.Label) controles:

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

Al crear una propiedad adjunta, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios avanzados de propiedad adjunta. Esto incluye la detección de cambios de propiedad, validar los valores de propiedad y la conversión de valores de propiedad. Para obtener más información, vea [escenarios avanzados](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios).

## <a name="related-links"></a>Vínculos relacionados

- [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md)
- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efecto de sombra (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [API de BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API de BindableObject](xref:Xamarin.Forms.BindableObject)
