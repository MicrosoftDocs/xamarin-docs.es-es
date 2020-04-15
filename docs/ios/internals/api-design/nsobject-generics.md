---
title: Subclases genéricas de NSObject en Xamarin.iOS
description: En este documento se describe cómo crear subclases genéricas de NSObject. Se examina lo que se puede y no se puede hacer, describe el registrador estático y consulta el rendimiento.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022362"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclases genéricas de NSObject en Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Uso de genéricos con NSObjects

Es posible usar genéricos en las subclases de `NSObject`, por ejemplo, [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Dado que los objetos de la subclase `NSObject` se registran con el tiempo de ejecución de Objective-C, existen algunas limitaciones en cuanto a lo que se puede hacer con las subclases genéricas de tipos `NSObject`.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Consideraciones para las subclases genéricas de NSObject

En este documento se detallan las limitaciones de la compatibilidad limitada de las subclases genéricas de `NSObjects`.

### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genérico en signaturas de miembro

Todos los argumentos de tipo genérico de una signatura de miembro expuesta a Objective-C deben tener una restricción `NSObject`.

**Correcto**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: El parámetro de tipo genérico es un objeto `NSObject`, por lo que la signatura de selector para `myMethod:` se puede exponer de forma segura a Objective-C (siempre será `NSObject` o una subclase de ella).

**Incorrecto**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: no se puede crear una signatura de Objective-C para los miembros exportados a los que puede llamar el código Objective-C, ya que la signatura será diferente en función del tipo exacto del tipo genérico `T`.

**Correcto**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**Motivo**: es posible tener argumentos de tipo genérico sin restricciones siempre que no formen parte de la signatura del miembro exportado.

**Correcto**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**Motivo**: el parámetro `T` del método `MyMethod` exportado de Objective-C está restringido a que sea un objeto `NSObject`, el tipo sin restricciones `U` no forma parte de la signatura.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Creaciones de instancias de tipos genéricos de Objective-C

La creación de instancias de tipos genéricos de Objective-C no está permitida. Esto suele ocurrir cuando se usa un tipo administrado en un archivo xib o un guion gráfico.

Considere esta definición de clase, que expone un constructor que toma un elemento `IntPtr` (la forma de Xamarin.iOS de construir un objeto C# a partir de una instancia nativa de Objective-C):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Aunque la construcción anterior es correcta, en tiempo de ejecución, se producirá una excepción si Objective-C intenta crear una instancia.

Esto sucede porque Objective-C no tiene ningún concepto de tipos genéricos y no puede especificar el tipo genérico exacto que se va a crear.

Este problema se puede solucionar creando una subclase especializada del tipo genérico. Por ejemplo:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Ahora, ya no hay ambigüedad, la clase `GenericUIView` se puede usar en archivos xib o guiones gráficos.

## <a name="no-support-for-generic-methods"></a>No hay compatibilidad para métodos genéricos

### <a name="generic-methods-are-not-allowed"></a>No se permiten los métodos genéricos.

El código siguiente no se compilará:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: No se permite porque Xamarin.iOS no sabe qué tipo usar para el argumento de tipo `T` cuando el método se invoca desde Objective-C.

Una alternativa es crear un método especializado y exportarlo en su lugar:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>No se permiten miembros estáticos exportados

No se pueden exponer miembros estáticos a Objective-C si se hospedan en una subclase genérica de `NSObject`.

Ejemplo de un escenario no admitido:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**Motivo:** Al igual que los métodos genéricos, el tiempo de ejecución de Xamarin.iOS debe ser capaz de saber qué tipo usar para el argumento de tipo genérico `T`.

En el caso de los miembros de instancia, se usa la propia instancia (dado que nunca habrá una instancia `Generic<T>`, siempre será `Generic<SomeSpecificClass>`), pero para los miembros estáticos esta información no está presente.

Tenga en cuenta que esto se aplica incluso si el miembro en cuestión no utiliza el argumento de tipo `T` de ninguna manera.

En este caso, la alternativa es crear una subclase especializada:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

## <a name="performance"></a>Rendimiento

El registrador estático no puede resolver un miembro exportado en un tipo genérico en tiempo de compilación como hace normalmente, debe buscarse en tiempo de ejecución. Esto significa que la invocación de este método desde Objective-C es ligeramente más lenta que la invocación de miembros de clases no genéricas.
