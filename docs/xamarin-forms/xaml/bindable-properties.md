---
title: Xamarin.Forms Propiedades enlazables
description: En este artículo se proporciona una introducción a las propiedades enlazables y se muestra cómo crearlas y consumirlas.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f50dde4bb96f43c6487cb40889746514f23d9d44
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374087"
---
# <a name="no-locxamarinforms-bindable-properties"></a>Xamarin.Forms Propiedades enlazables

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Las propiedades enlazables extienden la funcionalidad de propiedad de CLR mediante la copia de seguridad de una propiedad con un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) tipo, en lugar de hacer una copia de seguridad de una propiedad con un campo. El propósito de las propiedades enlazables es proporcionar un sistema de propiedades que admita el enlace de datos, los estilos, las plantillas y los valores establecidos mediante relaciones de elementos primarios y secundarios. Además, las propiedades enlazables pueden proporcionar valores predeterminados, la validación de los valores de propiedad y las devoluciones de llamada que supervisan los cambios de propiedad.

Las propiedades deben implementarse como propiedades enlazables para admitir una o varias de las características siguientes:

- Actuando como una propiedad de *destino* válida para el enlace de datos.
- Establecer la propiedad a través de un [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Proporcionar un valor de propiedad predeterminado diferente del predeterminado para el tipo de la propiedad.
- Validando el valor de la propiedad.
- Supervisión de los cambios de propiedad.

Entre los ejemplos de Xamarin.Forms propiedades enlazables se incluyen [`Label.Text`](xref:Xamarin.Forms.Label.Text) , [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) y [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . Cada propiedad enlazable tiene un `public static readonly` campo correspondiente de tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) que se expone en la misma clase y que es el identificador de la propiedad enlazable. Por ejemplo, el identificador de propiedad enlazable correspondiente para la `Label.Text` propiedad es [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty) .

## <a name="create-a-bindable-property"></a>Crear una propiedad enlazable

El proceso para crear una propiedad enlazable es el siguiente:

1. Cree una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia de con una de las [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) sobrecargas del método.
1. Definir los descriptores de acceso de las propiedades de la [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia.

Todas [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) las instancias deben crearse en el subproceso de la interfaz de usuario. Esto significa que solo el código que se ejecuta en el subproceso de la interfaz de usuario puede obtener o establecer el valor de una propiedad enlazable. Sin embargo, `BindableProperty` se puede tener acceso a las instancias desde otros subprocesos si se calculan las referencias al subproceso de interfaz de usuario con el [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

### <a name="create-a-property"></a>Crear una propiedad

Para crear una `BindableProperty` instancia de, la clase contenedora debe derivar de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) clase. Sin embargo, la `BindableObject` clase es alta en la jerarquía de clases, por lo que la mayoría de las clases utilizadas para la funcionalidad de la interfaz de usuario admiten propiedades enlazables.

Se puede crear una propiedad enlazable mediante la declaración `public static readonly` de una propiedad de tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.Create` ] (XREF: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . Sobrecargas del método BindableProperty. CreateDefaultValueDelegate)). La declaración debe estar dentro del cuerpo de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) clase derivada, pero fuera de las definiciones de miembro.

Como mínimo, se debe especificar un identificador al crear [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , junto con los parámetros siguientes:

- Nombre del [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .
- Tipo de la propiedad.
- Tipo del objeto propietario.
- Valor predeterminado de la propiedad. Esto garantiza que la propiedad siempre devuelve un valor predeterminado determinado cuando no está establecido y puede ser diferente del valor predeterminado para el tipo de la propiedad. El valor predeterminado se restaurará cuando [ `ClearValue` ] (XREF: Xamarin.Forms . BindableObject. ClearValue ( Xamarin.Forms . BindableProperty)) en la propiedad enlazable.

> [!IMPORTANT]
> La Convención de nomenclatura para las propiedades enlazables es que el identificador de la propiedad enlazable debe coincidir con el nombre de propiedad especificado en el `Create` método, con la propiedad "Property" anexada. 

En el código siguiente se muestra un ejemplo de una propiedad enlazable, con un identificador y valores para los cuatro parámetros necesarios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Esto crea una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia denominada `EventNameProperty` , de tipo `string` . La propiedad es propiedad de la `EventToCommandBehavior` clase y su valor predeterminado es `null` .

Opcionalmente, al crear una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia de, se pueden especificar los parámetros siguientes:

- Modo de enlace. Se utiliza para especificar la dirección en la que se propagarán los cambios de valores de propiedad. En el modo de enlace predeterminado, los cambios se propagarán desde el *origen* al *destino*.
- Delegado de validación que se invocará cuando se establezca el valor de propiedad. Para obtener más información, consulte [devoluciones de llamada de validación](#validation-callbacks).
- Un delegado de propiedad cambiado que se invocará cuando el valor de propiedad haya cambiado. Para obtener más información, vea [detectar cambios](#detect-property-changes)en las propiedades.
- Un delegado de cambio de propiedad que se invocará cuando se cambie el valor de propiedad. Este delegado tiene la misma firma que el delegado de propiedad cambiada.
- Delegado de valor de conversión que se invocará cuando el valor de propiedad haya cambiado. Para obtener más información, vea [forzar devoluciones de llamada de valor](#coerce-value-callbacks).
- `Func`Que se usa para inicializar un valor de propiedad predeterminado. Para obtener más información, vea [crear un valor predeterminado con una FUNC](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Crear descriptores de acceso

Los descriptores de acceso de propiedad son necesarios para utilizar la sintaxis de propiedad para tener acceso a una propiedad enlazable. El `Get` descriptor de acceso debe devolver el valor incluido en la propiedad enlazable correspondiente. Esto puede lograrse llamando a [ `GetValue` ] (XREF: Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), pasando el identificador de la propiedad enlazable en la que se obtiene el valor y, a continuación, convirtiendo el resultado al tipo requerido. El `Set` descriptor de acceso debe establecer el valor de la propiedad enlazable correspondiente. Esto puede lograrse llamando a [ `SetValue` ] (XREF: Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindableProperty, System. Object)), pasando el identificador de la propiedad enlazable en la que se establece el valor y el valor que se va a establecer.

En el ejemplo de código siguiente se muestran los descriptores de acceso de la `EventName` propiedad enlazable:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Usar una propiedad enlazable

Una vez que se ha creado una propiedad enlazable, se puede usar desde XAML o código. En XAML, esto se logra declarando un espacio de nombres con un prefijo, con la declaración del espacio de nombres que indica el nombre del espacio de nombres CLR y, opcionalmente, un nombre de ensamblado. Para obtener más información, vea [espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad enlazable, que se define dentro del mismo ensamblado que el código de aplicación que hace referencia al tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La declaración del espacio de nombres se usa al establecer la `EventName` propiedad enlazable, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>Escenarios avanzados

Al crear una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instancia, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios de propiedades enlazables avanzadas. En esta sección se analizan estos escenarios.

### <a name="detect-property-changes"></a>Detección de cambios de propiedades

Un `static` método de devolución de llamada de cambio de propiedad se puede registrar con una propiedad enlazable especificando el `propertyChanged` parámetro para [ `BindableProperty.Create` ] (XREF: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)). El método de devolución de llamada especificado se invocará cuando cambie el valor de la propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo la `EventName` propiedad enlazable registra el `OnEventNameChanged` método como un método de devolución de llamada cambiado por propiedad:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

En el método de devolución de llamada de propiedad cambiada, el [`BindableObject`](xref:Xamarin.Forms.BindableObject) parámetro se usa para indicar qué instancia de la clase propietaria ha detectado un cambio y los valores de los dos `object` parámetros representan los valores antiguos y nuevos de la propiedad enlazable.

### <a name="validation-callbacks"></a>Devoluciones de llamada de validación

Un `static` método de devolución de llamada de validación se puede registrar con una propiedad enlazable especificando el `validateValue` parámetro para [ `BindableProperty.Create` ] (XREF: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)). El método de devolución de llamada especificado se invocará cuando se establezca el valor de la propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo la `Angle` propiedad enlazable registra el `IsValidValue` método como un método de devolución de llamada de validación:

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

Las devoluciones de llamada de validación se proporcionan con un valor y deben devolver `true` si el valor es válido para la propiedad; en caso contrario, `false` . Se producirá una excepción si se devuelve una devolución de llamada de validación `false` , que debe ser administrada por el desarrollador. Un uso típico de un método de devolución de llamada de validación es restringir los valores de enteros o dobles cuando se establece la propiedad enlazable. Por ejemplo, el `IsValidValue` método comprueba que el valor de la propiedad es un `double` comprendido entre 0 y 360.

### <a name="coerce-value-callbacks"></a>Devoluciones de llamada de valor de coerción

Un `static` método de devolución de llamada de valor de coerción se puede registrar con una propiedad enlazable especificando el `coerceValue` parámetro para [ `BindableProperty.Create` ] (XREF: Xamarin.Forms . BindableProperty. Create (System. String, System. Type, System. Type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)). El método de devolución de llamada especificado se invocará cuando cambie el valor de la propiedad enlazable.

> [!IMPORTANT]
> El `BindableObject` tipo tiene un `CoerceValue` método al que se puede llamar para forzar una reevaluación del valor de su `BindableProperty` argumento, invocando su devolución de llamada de valor de coerción.

Las devoluciones de llamada de valor de coerción se usan para forzar una reevaluación de una propiedad enlazable cuando el valor de la propiedad cambia. Por ejemplo, se puede usar una devolución de llamada de valor de coerción para asegurarse de que el valor de una propiedad enlazable no sea mayor que el valor de otra propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo la `Angle` propiedad enlazable registra el `CoerceAngle` método como un método de devolución de llamada de valor de conversión:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

El `CoerceAngle` método comprueba el valor de la `MaximumAngle` propiedad y, si el `Angle` valor de la propiedad es mayor que, convierte el valor al valor de la `MaximumAngle` propiedad. Además, cuando la `MaximumAngle` propiedad cambia la devolución de llamada de valor de coerción se invoca en la `Angle` propiedad llamando al `CoerceValue` método.

### <a name="create-a-default-value-with-a-func"></a>Crear un valor predeterminado con FUNC

`Func`Se puede utilizar para inicializar el valor predeterminado de una propiedad enlazable, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

El `defaultValueCreator` parámetro se establece en `Func` que invoca [ `Device.GetNamedSize` ] (XREF: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . NamedSize, System. Type)) para devolver un `double` que representa el tamaño con nombre de la fuente que se usa en [`Label`](xref:Xamarin.Forms.Label) en la plataforma nativa.

## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamiento de eventos a comandos (ejemplo)](/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Devolución de llamada de validación (ejemplo)](/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Devolución de llamada de valor de coerción (ejemplo)](/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API de BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API de BindableObject](xref:Xamarin.Forms.BindableObject)