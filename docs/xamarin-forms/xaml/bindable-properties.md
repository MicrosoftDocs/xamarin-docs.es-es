---
title: Propiedades enlazables
description: Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 59ec82c222208ff0e73b0b7b5226d416d3a53398
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656598"
---
# <a name="bindable-properties"></a>Propiedades enlazables

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_En Xamarin.Forms, la funcionalidad de las propiedades de common language runtime (CLR) se extiende por las propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde el valor de propiedad se realiza el seguimiento por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos._

## <a name="overview"></a>Información general

Propiedades enlazables amplían la funcionalidad de propiedad CLR de seguridad de una propiedad con un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) tipo, en lugar de una propiedad con un campo de respaldo. El propósito de las propiedades enlazables es proporcionar un sistema de propiedades que admite el enlace de datos, estilos, plantillas y valores se establecen a través de relaciones de elementos primarios y secundarios. Además, las propiedades enlazables pueden proporcionar valores predeterminados, validación de los valores de propiedad y las devoluciones de llamada que supervisen los cambios de propiedad.

Las propiedades deben implementarse como propiedades enlazables para admitir una o varias de las siguientes características:

- Que actúa como válido *destino* propiedad para el enlace de datos.
- Establecer la propiedad a través de un [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Proporcionar un valor de propiedad predeterminado que es diferente del valor predeterminado para el tipo de la propiedad.
- Validar el valor de la propiedad.
- Supervisión de los cambios de propiedad.

Ejemplos de Xamarin.Forms que las propiedades enlazables [ `Label.Text` ](xref:Xamarin.Forms.Label.Text), [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius), y [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation). Cada propiedad enlazable le corresponde una `public static readonly` propiedad de tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) que se expone en la misma clase y que es el identificador de la propiedad enlazable. Por ejemplo, el identificador de propiedad enlazable correspondiente para el `Label.Text` propiedad es [ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Creación y consumo de una propiedad enlazable

El proceso de creación de una propiedad enlazable es como sigue:

1. Crear un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancia con uno de los [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*) sobrecargas del método.
1. Definir descriptores de acceso de propiedad para el [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancia.

Tenga en cuenta que todos los [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancias deben crearse en el subproceso de interfaz de usuario. Esto significa que sólo el código que se ejecuta en el subproceso de interfaz de usuario puede obtener o establecer el valor de una propiedad enlazable. Sin embargo, `BindableProperty` instancias pueden tener acceso desde otros subprocesos por el cálculo de referencias para el subproceso de interfaz de usuario con el [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

### <a name="creating-a-property"></a>Creación de una propiedad

Para crear un `BindableProperty` instancia, debe derivar la clase contenedora de la [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) clase. Sin embargo, la `BindableObject` clase es alta en la jerarquía de clases, por lo que la mayoría de las clases que se usa para las propiedades enlazables para compatibilidad con la funcionalidad de interfaz de usuario.

Se puede crear una propiedad enlazable declarando un `public static readonly` propiedad de tipo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) sobrecargas del método. La declaración debe estar dentro del cuerpo de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) clase derivada, pero fuera de las definiciones de miembros.

Como mínimo, debe especificarse un identificador al crear un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), junto con los parámetros siguientes:

- El nombre de la [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty).
- Tipo de la propiedad.
- El tipo del objeto propietario.
- Valor predeterminado de la propiedad. Esto garantiza que la propiedad siempre devuelve un valor determinado de forma predeterminada cuando no está establecido, y puede ser diferente del valor predeterminado para el tipo de la propiedad. El valor predeterminado será cuando restaura la [ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) se llama al método en la propiedad enlazable.

El código siguiente muestra un ejemplo de una propiedad enlazable, con un identificador y los valores de los cuatro campos obligatorios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Esto crea un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancia denominada `EventName`, del tipo `string`. La propiedad pertenece a la `EventToCommandBehavior` clase y tiene un valor predeterminado de `null`. La convención de nomenclatura para las propiedades enlazables es que el identificador de propiedad enlazable debe coincidir con el nombre de propiedad especificado en el `Create` método con "Property" anexado a él. Por lo tanto, en el ejemplo anterior, el identificador de propiedad enlazable es `EventNameProperty`.

Si lo desea, al crear un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) de instancia, los siguientes parámetros se pueden especificar:

- Modo de enlace. Esto se utiliza para especificar la dirección en la que se propaguen los cambios de valor de propiedad. En el modo de enlace predeterminada, los cambios se propaguen desde el *origen* a la *destino*.
- Un delegado de validación que se invocará cuando se establece el valor de propiedad. Para obtener más información, consulte [las devoluciones de llamada de validación](#validation).
- Un delegado de cambio de propiedad que se invoca cuando ha cambiado el valor de propiedad. Para obtener más información, consulte [detectar los cambios de propiedad](#propertychanges).
- Una propiedad de cambio de delegado que se invoca cuando cambia el valor de propiedad. Este delegado tiene la misma firma que el delegado de la propiedad ha cambiado.
- Un delegado del valor de coerción que se invoca cuando ha cambiado el valor de propiedad. Para obtener más información, consulte [devoluciones de llamada de valor de coerción](#coerce).
- Un `Func` que se usa para inicializar un valor de propiedad predeterminado. Para obtener más información, consulte [creación de un valor predeterminado con un elemento Func](#defaultfunc).

### <a name="creating-accessors"></a>Creación de los descriptores de acceso

Los descriptores de acceso de propiedad deben usar la sintaxis de la propiedad para tener acceso a una propiedad enlazable. El `Get` descriptor de acceso debe devolver el valor que se encuentra en la propiedad enlazable correspondiente. Esto puede lograrse mediante una llamada a la [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método, pasando el identificador de propiedad enlazable en la que se va a obtener el valor y, a continuación, convertir el resultado al tipo requerido. El `Set` descriptor de acceso debe establecer el valor de la propiedad enlazable correspondiente. Esto puede lograrse mediante una llamada a la [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) método, pasando el identificador de propiedad enlazable en la que se va a establecer el valor y el valor que se va a establecer.

El ejemplo de código siguiente muestra los descriptores de acceso para el `EventName` propiedad enlazable:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Consumo de una propiedad enlazable

Una vez creada una propiedad enlazable, se puede consumir desde XAML o código. En XAML, esto se logra mediante la declaración de un espacio de nombres con un prefijo, con la declaración de espacio de nombres que indica el nombre del espacio de nombres CLR y, opcionalmente, un nombre de ensamblado. Para obtener más información, consulte [los espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad enlazable, que se define dentro del mismo ensamblado que el código de aplicación que se hace referencia al tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La declaración de espacio de nombres se utiliza al establecer el `EventName` propiedad enlazable, como se muestra en el ejemplo de código XAML siguiente:

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
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Escenarios avanzados

Al crear un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) de la instancia, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios avanzados de propiedad enlazable. Esta sección explora estos escenarios.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Detección de cambios de propiedad

Un `static` método de devolución de llamada de cambio de propiedad se puede registrar con una propiedad enlazable especificando el `propertyChanged` parámetro para el [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. El método de devolución de llamada especificada se invoca cuando cambia el valor de la propiedad enlazable.

El siguiente ejemplo de código muestra cómo el `EventName` propiedad enlazable registra el `OnEventNameChanged` método como un método de devolución de llamada de cambio de propiedad:

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

En el método de devolución de llamada de cambio de propiedad, el [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) parámetro se usa para indicar qué instancia de la clase propietaria informó de un cambio y los valores de los dos `object` parámetros representan los valores antiguos y nuevos de la propiedad enlazable.

<a name="validation" />

### <a name="validation-callbacks"></a>Devoluciones de llamada de validación

Un `static` método de devolución de llamada de validación se puede registrar con una propiedad enlazable especificando el `validateValue` parámetro para el [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. Se invocará el método de devolución de llamada especificada cuando se establece el valor de la propiedad enlazable.

El siguiente ejemplo de código muestra cómo el `Angle` propiedad enlazable registra el `IsValidValue` método como método de devolución de llamada de validación:

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

Las devoluciones de llamada de validación se proporcionan con un valor y debe devolver `true` si el valor es válido para la propiedad, de lo contrario `false`. Se producirá una excepción si una devolución de llamada de validación devuelve `false`, que debe controlarse por el desarrollador. Un uso típico de un método de devolución de llamada de validación es restringir los valores de números enteros o dobles cuando se establece la propiedad enlazable. Por ejemplo, el `IsValidValue` método comprueba que el valor de propiedad es un `double` dentro del intervalo de 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>CoerceValue Callbacks

Un `static` convertir el valor del método de devolución de llamada se puede registrar con una propiedad enlazable especificando el `coerceValue` parámetro para el [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método. El método de devolución de llamada especificada se invoca cuando cambia el valor de la propiedad enlazable.

Las devoluciones de llamada se usan para forzar una reevaluación de una propiedad enlazable cuando cambia el valor de la propiedad de valor de coerción. Por ejemplo, puede utilizarse una devolución de llamada de valor de coerción para asegurarse de que el valor de una propiedad enlazable no es mayor que el valor de otra propiedad enlazable.

El siguiente ejemplo de código muestra cómo el `Angle` propiedad enlazable registra el `CoerceAngle` método como un método de devolución de llamada de valor de coerción:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

El `CoerceAngle` método comprueba el valor de la `MaximumAngle` propiedad y si el `Angle` es mayor el valor de propiedad, convierte el valor en el `MaximumAngle` valor de propiedad.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Creación de un valor predeterminado con un elemento Func

Un `Func` puede usarse para inicializar el valor predeterminado de una propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

El `defaultValueCreator` parámetro se establece en un `Func` que invoca la [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) método devuelva un `double` que representa el nombre de tamaño para la fuente que se usa en un [ `Label` ](xref:Xamarin.Forms.Label) en la plataforma nativa.

## <a name="summary"></a>Resumen

En este artículo proporciona una introducción a las propiedades enlazables y se muestra cómo crear y consumirlos. Una propiedad enlazable es un tipo especial de propiedad, donde el valor de propiedad se realiza el seguimiento por el sistema de propiedades de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Evento para el comportamiento de comandos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Devolución de llamada de validación (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Convertir el valor de devolución de llamada (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
