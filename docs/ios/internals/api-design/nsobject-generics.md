---
title: Subclases genéricas de NSObject en Xamarin. iOS
description: En este documento se describe cómo crear subclases genéricas de NSObject. Examina lo que se puede y no se puede hacer, describe el registrador estático y consulta el rendimiento.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 531d0b2b6141cc0e1f4014f1d3422af3c6f8643a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291978"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclases genéricas de NSObject en Xamarin. iOS

## <a name="using-generics-with-nsobjects"></a>Usar genéricos con NSObjects

Es posible usar genéricos en las subclases de `NSObject`, por ejemplo [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Dado que los objetos que `NSObject` se registran con el tiempo de ejecución de Objective-C existen algunas limitaciones en cuanto a lo que se puede hacer `NSObject` con las subclases genéricas de los tipos.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Consideraciones sobre las subclases genéricas de NSObject

En este documento se detallan las limitaciones de la compatibilidad limitada con las `NSObjects`subclases genéricas de.

### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genérico en firmas de miembro

Todos los argumentos de tipo genérico de una firma de miembro expuesta a Objective-C `NSObject` deben tener una restricción.

**Bueno**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: El parámetro de tipo genérico es `NSObject`, por lo que la firma `myMethod:` de selector de se puede exponer de forma segura a Objective-C `NSObject` (siempre será o una subclase de ella).

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

**Motivo**: no es posible crear una firma de Objective-c para los miembros exportados a los que puede llamar el código de Objective-c, ya que la signatura diferirá en función del tipo `T`exacto del tipo genérico.

**Bueno**:

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

**Motivo**: es posible tener argumentos de tipo genérico sin restricciones siempre que no formen parte de la firma del miembro exportado.

**Bueno**:

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

**Motivo**: el `T` parámetro en el elemento exportado `MyMethod` de Objective-C `NSObject`está restringido a, el tipo `U` sin restricciones no forma parte de la firma.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Instancias de tipos genéricos de Objective-C

No se permite la creación de instancias de tipos genéricos desde Objective-C. Esto suele ocurrir cuando se usa un tipo administrado en un Xib o un guion gráfico.

Considere esta definición de clase, que expone un constructor que toma un `IntPtr` (la forma de Xamarin. iOS de construir un C# objeto a partir de una instancia de Objective-C nativa):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Aunque la construcción anterior es correcta, en tiempo de ejecución, se producirá una excepción en si Objective-C intenta crear una instancia del mismo.

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

Ahora, ya no hay ambigüedad, la clase `GenericUIView` se puede usar en Xib o guiones gráficos.

## <a name="no-support-for-generic-methods"></a>No se admiten métodos genéricos

### <a name="generic-methods-are-not-allowed"></a>No se permiten métodos genéricos.

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

**Motivo**: Esto no se permite porque Xamarin. iOS no sabe qué tipo usar para el argumento `T` de tipo cuando se invoca el método desde Objective-C.

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

No se puede exponer un miembro estático a Objective-C si está hospedado dentro de una subclase genérica `NSObject`de.

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

**Debido** Al igual que los métodos genéricos, el tiempo de ejecución de Xamarin. iOS debe ser capaz de saber qué tipo usar para `T`el argumento de tipo genérico.

En el caso de los miembros de instancia, se usa la propia instancia (dado `Generic<T>`que nunca habrá una instancia `Generic<SomeSpecificClass>`, siempre será), pero para los miembros estáticos esta información no está presente.

Tenga en cuenta que esto se aplica incluso si el miembro en cuestión no utiliza el `T` argumento de tipo de ninguna manera.

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

El registrador estático no puede resolver un miembro exportado en un tipo genérico en tiempo de compilación, ya que normalmente lo hace, debe buscarse en tiempo de ejecución. Esto significa que la invocación de este método desde Objective-C es ligeramente más lenta que la invocación de miembros de clases no genéricas.

