---
title: Propiedades enlazables de Xamarin. Forms
description: Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 56583aa0df676ae55e1b283f1a8e151b69a13d28
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635755"
---
# <a name="xamarinforms-bindable-properties"></a>Propiedades enlazables de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Las propiedades enlazables extienden la funcionalidad de propiedad de CLR mediante la copia de seguridad de una propiedad con un tipo de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , en lugar de hacer una copia de seguridad de una propiedad con un campo. El propósito de las propiedades enlazables es proporcionar un sistema de propiedades que admite el enlace de datos, estilos, plantillas y valores se establecen a través de relaciones de elementos primarios y secundarios. Además, las propiedades enlazables pueden proporcionar valores predeterminados, validación de los valores de propiedad y las devoluciones de llamada que supervisen los cambios de propiedad.

Las propiedades deben implementarse como propiedades enlazables para admitir una o varias de las siguientes características:

- Actuando como una propiedad de *destino* válida para el enlace de datos.
- Establecer la propiedad a través de un [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Proporcionar un valor de propiedad predeterminado que es diferente del valor predeterminado para el tipo de la propiedad.
- Validar el valor de la propiedad.
- Supervisión de los cambios de propiedad.

Entre los ejemplos de propiedades enlazables de Xamarin. Forms se incluyen [`Label.Text`](xref:Xamarin.Forms.Label.Text), [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)y [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation). Cada propiedad enlazable tiene una propiedad `public static readonly` correspondiente de tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) que se expone en la misma clase y que es el identificador de la propiedad enlazable. Por ejemplo, el identificador de propiedad enlazable correspondiente para la propiedad `Label.Text` es [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty).

## <a name="create-a-bindable-property"></a>Crear una propiedad enlazable

El proceso de creación de una propiedad enlazable es como sigue:

1. Cree una instancia de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) con una de las sobrecargas del método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) .
1. Definir los descriptores de acceso de propiedad para la instancia de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .

Todas las instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) deben crearse en el subproceso de la interfaz de usuario. Esto significa que sólo el código que se ejecuta en el subproceso de interfaz de usuario puede obtener o establecer el valor de una propiedad enlazable. Sin embargo, se puede tener acceso a las instancias de `BindableProperty` desde otros subprocesos si se calculan las referencias al subproceso de interfaz de usuario con el método [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) .

### <a name="create-a-property"></a>Crear una propiedad

Para crear una instancia de `BindableProperty`, la clase contenedora debe derivar de la clase [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Sin embargo, la clase `BindableObject` es alta en la jerarquía de clases, por lo que la mayoría de las clases utilizadas para la funcionalidad de la interfaz de usuario admiten propiedades enlazables.

Se puede crear una propiedad enlazable declarando una propiedad `public static readonly` de tipo [`BindableProperty`](xref:Xamarin.Forms.BindableProperty). La propiedad enlazable debe establecerse en el valor devuelto de una de las sobrecargas del método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La declaración debe estar dentro del cuerpo de [`BindableObject`](xref:Xamarin.Forms.BindableObject) clase derivada, pero fuera de las definiciones de miembro.

Como mínimo, se debe especificar un identificador al crear un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), junto con los parámetros siguientes:

- Nombre del [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).
- Tipo de la propiedad.
- El tipo del objeto propietario.
- El valor predeterminado de la propiedad. Esto garantiza que la propiedad siempre devuelve un valor determinado de forma predeterminada cuando no está establecido, y puede ser diferente del valor predeterminado para el tipo de la propiedad. El valor predeterminado se restaurará cuando se llame al método [`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) en la propiedad enlazable.

El código siguiente muestra un ejemplo de una propiedad enlazable, con un identificador y los valores de los cuatro campos obligatorios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Esto crea una instancia de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) denominada `EventName`, de tipo `string`. La propiedad es propiedad de la clase `EventToCommandBehavior` y tiene un valor predeterminado de `null`. La Convención de nomenclatura para las propiedades enlazables es que el identificador de la propiedad enlazable debe coincidir con el nombre de propiedad especificado en el método `Create`, con la "propiedad" anexada. Por lo tanto, en el ejemplo anterior, el identificador de propiedad enlazable es `EventNameProperty`.

Opcionalmente, al crear una instancia de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , se pueden especificar los parámetros siguientes:

- Modo de enlace. Esto se utiliza para especificar la dirección en la que se propaguen los cambios de valor de propiedad. En el modo de enlace predeterminado, los cambios se propagarán desde el *origen* al *destino*.
- Un delegado de validación que se invocará cuando se establece el valor de propiedad. Para obtener más información, consulte [devoluciones de llamada de validación](#validation-callbacks).
- Un delegado de cambio de propiedad que se invoca cuando ha cambiado el valor de propiedad. Para obtener más información, vea [detectar cambios](#detect-property-changes)en las propiedades.
- Una propiedad de cambio de delegado que se invoca cuando cambia el valor de propiedad. Este delegado tiene la misma firma que el delegado de la propiedad ha cambiado.
- Un delegado del valor de coerción que se invoca cuando ha cambiado el valor de propiedad. Para obtener más información, vea [forzar devoluciones de llamada de valor](#coerce-value-callbacks).
- `Func` que se usa para inicializar un valor de propiedad predeterminado. Para obtener más información, vea [crear un valor predeterminado con una FUNC](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Crear descriptores de acceso

Los descriptores de acceso de propiedad deben usar la sintaxis de la propiedad para tener acceso a una propiedad enlazable. El descriptor de acceso `Get` debe devolver el valor incluido en la propiedad enlazable correspondiente. Esto puede lograrse llamando al método [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , pasando el identificador de la propiedad enlazable en la que se va a obtener el valor y, a continuación, convirtiendo el resultado al tipo requerido. El descriptor de acceso `Set` debe establecer el valor de la propiedad enlazable correspondiente. Esto puede lograrse llamando al método [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) , pasando el identificador de la propiedad enlazable en la que se establece el valor y el valor que se va a establecer.

En el ejemplo de código siguiente se muestran los descriptores de acceso para la `EventName` propiedad enlazable:

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Usar una propiedad enlazable

Una vez creada una propiedad enlazable, se puede consumir desde XAML o código. En XAML, esto se logra mediante la declaración de un espacio de nombres con un prefijo, con la declaración de espacio de nombres que indica el nombre del espacio de nombres CLR y, opcionalmente, un nombre de ensamblado. Para obtener más información, vea [espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad enlazable, que se define dentro del mismo ensamblado que el código de aplicación que se hace referencia al tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La declaración del espacio de nombres se usa al establecer el `EventName` propiedad enlazable, tal y como se muestra en el siguiente ejemplo de código XAML:

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

Al crear una instancia de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios avanzados de propiedades enlazables. Esta sección explora estos escenarios.

### <a name="detect-property-changes"></a>Detección de cambios de propiedades

Un `static` método de devolución de llamada cambiado por propiedad se puede registrar con una propiedad enlazable especificando el parámetro `propertyChanged` para el método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . El método de devolución de llamada especificada se invoca cuando cambia el valor de la propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo el `EventName` propiedad enlazable registra el método `OnEventNameChanged` como un método de devolución de llamada cambiado por propiedad:

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

En el método de devolución de llamada de propiedad cambiada, el parámetro [`BindableObject`](xref:Xamarin.Forms.BindableObject) se usa para indicar qué instancia de la clase propietaria ha comunicado un cambio y los valores de los dos parámetros `object` representan los valores antiguos y nuevos de la propiedad enlazable.

### <a name="validation-callbacks"></a>Devoluciones de llamada de validación

Un método de devolución de llamada de validación de `static` se puede registrar con una propiedad enlazable especificando el parámetro `validateValue` para el método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . Se invocará el método de devolución de llamada especificada cuando se establece el valor de la propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo el `Angle` propiedad enlazable registra el método `IsValidValue` como un método de devolución de llamada de validación:

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

Las devoluciones de llamada de validación se proporcionan con un valor y deben devolver `true` si el valor es válido para la propiedad; de lo contrario `false`. Se producirá una excepción si una devolución de llamada de validación devuelve `false`, que debe ser administrada por el desarrollador. Un uso típico de un método de devolución de llamada de validación es restringir los valores de números enteros o dobles cuando se establece la propiedad enlazable. Por ejemplo, el método `IsValidValue` comprueba que el valor de propiedad es un `double` en el intervalo comprendido entre 0 y 360.

### <a name="coerce-value-callbacks"></a>Devoluciones de llamada de valor de coerción

Un método de devolución de llamada de valor de conversión de `static` se puede registrar con una propiedad enlazable especificando el parámetro `coerceValue` para el método [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . El método de devolución de llamada especificada se invoca cuando cambia el valor de la propiedad enlazable.

> [!IMPORTANT]
> El tipo de `BindableObject` tiene un método `CoerceValue` al que se puede llamar para forzar una reevaluación del valor de su argumento `BindableProperty`, invocando su devolución de llamada de valor de coerción.

Las devoluciones de llamada se usan para forzar una reevaluación de una propiedad enlazable cuando cambia el valor de la propiedad de valor de coerción. Por ejemplo, puede utilizarse una devolución de llamada de valor de coerción para asegurarse de que el valor de una propiedad enlazable no es mayor que el valor de otra propiedad enlazable.

En el ejemplo de código siguiente se muestra cómo el `Angle` propiedad enlazable registra el método `CoerceAngle` como un método de devolución de llamada de valor de coerción:

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

El método `CoerceAngle` comprueba el valor de la propiedad `MaximumAngle` y, si el valor de la propiedad `Angle` es mayor que él, convierte el valor al valor de la propiedad `MaximumAngle`. Además, cuando la propiedad `MaximumAngle` cambia la devolución de llamada de valor de coerción se invoca en la propiedad `Angle` llamando al método `CoerceValue`.

### <a name="create-a-default-value-with-a-func"></a>Crear un valor predeterminado con FUNC

Un `Func` se puede utilizar para inicializar el valor predeterminado de una propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

El parámetro `defaultValueCreator` se establece en un `Func` que invoca el método [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) para devolver un `double` que representa el tamaño con nombre de la fuente que se usa en un [`Label`](xref:Xamarin.Forms.Label) en la plataforma nativa.

## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportamiento de eventos a comandos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Devolución de llamada de validación (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Devolución de llamada de valor de coerción (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API de BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API de BindableObject](xref:Xamarin.Forms.BindableObject)
