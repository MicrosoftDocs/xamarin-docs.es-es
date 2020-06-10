---
title: Registrar el tipo de Xamarin. iOS
description: En este documento se describe el registrador de tipos de Xamarin. iOS, que hace que las clases de C# estén disponibles para el tiempo de ejecución de Objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 1a664b197b9e1a28f19ef9fd90a6f1dd26c159b4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565062"
---
# <a name="type-registrar-for-xamarinios"></a>Registrar el tipo de Xamarin. iOS

En este documento se describe el sistema de registro de tipos usado por Xamarin. iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de clases y métodos administrados

Durante el inicio, se registrará Xamarin. iOS:

- Clases con un atributo [[Register]](xref:Foundation.RegisterAttribute) como clases de Objective-C.
- Clases con un atributo [[Category]](xref:ObjCRuntime.CategoryAttribute) como categorías Objective-C.
- Interfaces con un atributo [[Protocol]](xref:Foundation.ProtocolAttribute) como protocolos Objective-C.
- Miembros con [[Export]](xref:Foundation.ExportAttribute), lo que permite a Objective-C tener acceso a ellos.

Por ejemplo, considere el `Main` método administrado común en aplicaciones de Xamarin. iOS:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Este código indica al tiempo de ejecución de Objective-C que use el tipo denominado `AppDelegate` como la clase delegada de la aplicación. Para que el tiempo de ejecución de Objective-C pueda crear una instancia de la `AppDelegate` clase de C#, se debe registrar esa clase.

Xamarin. iOS realiza el registro automáticamente, ya sea en tiempo de ejecución (registro dinámico) o en tiempo de compilación (registro estático).

El registro dinámico usa la reflexión en el inicio para buscar todas las clases y los métodos que se van a registrar y pasarlos al tiempo de ejecución de Objective-C. El registro dinámico se utiliza de forma predeterminada para las compilaciones del simulador.

El registro estático inspecciona, en tiempo de compilación, los ensamblados utilizados por la aplicación. Determina las clases y los métodos que se van a registrar con Objective-C y genera una asignación, que se incrusta en el binario.
Después, en el inicio, registra el mapa con el tiempo de ejecución de Objective-C. El registro estático se usa para las compilaciones de dispositivos.

### <a name="categories"></a>Categorías

A partir de Xamarin. iOS 8,10, es posible crear categorías de Objective-C con la sintaxis de C#.

Para crear una categoría, utilice el `[Category]` atributo y especifique el tipo que se va a extender. Por ejemplo, el código siguiente extiende `NSString` :

```csharp
[Category (typeof (NSString))]
```

Cada uno de los métodos de una categoría tiene un `[Export]` atributo, lo que lo pone a disposición del tiempo de ejecución de Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia de Objective-C con la sintaxis estándar de C# para los métodos de extensión:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

El primer argumento del método de extensión es la instancia en la que se invocó el método:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

En este ejemplo se agregará un `toUpper` método de instancia nativo a la `NSString` clase. Se puede llamar a este método desde Objective-C:

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>Protocolos

A partir de Xamarin. iOS 8,10, las interfaces con el `[Protocol]` atributo se exportarán a Objective-C como protocolos:

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

Este código exporta `IMyProtocol` a Objective-C como un protocolo denominado `MyProtocol` y una clase denominada `MyClass` que implementa el protocolo.

## <a name="new-registration-system"></a>Nuevo sistema de registro

A partir de la versión de 6.2.6 estable y la versión beta de 6.3.4, se ha agregado un nuevo registrador estático. En la versión 7.2.1, hicimos que el nuevo registrador sea el predeterminado.

Este nuevo sistema de registro ofrece las siguientes características nuevas:

- Detección en tiempo de compilación de errores de programador:
  - Dos clases que se registran con el mismo nombre.
  - Se exportó más de un método para responder al mismo selector
- Eliminación de código nativo sin usar:
  - El nuevo sistema de registro agregará referencias fuertes al código que se usa en bibliotecas estáticas, lo que permite que el vinculador nativo destaque el código nativo sin usar del binario resultante. En los enlaces de ejemplo de Xamarin, la mayoría de las aplicaciones se 300 000 más pequeñas.

- Compatibilidad con subclases genéricas de `NSObject` ; vea los [genéricos de NSObject](~/ios/internals/api-design/nsobject-generics.md) para obtener más información. Además, el nuevo sistema de registro detectará construcciones genéricas no admitidas que anteriormente habrían causado un comportamiento aleatorio en tiempo de ejecución.

### <a name="errors-caught-by-the-new-registrar"></a>Errores detectados por el nuevo registrador

A continuación se muestran algunos ejemplos de los errores detectados por el nuevo registrador.

- Exportar el mismo selector más de una vez en la misma clase:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo:")]
      void Foo (NSString str);
      [Export ("foo:")]
      void Foo (string str)
  }
  ```

- Exportar más de una clase administrada con el mismo nombre de Objective-C:

  ```csharp
  [Register ("Class")]
  class MyClass : NSObject {}

  [Register ("Class")]
  class YourClass : NSObject {}
  ```

- Exportar métodos genéricos:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo")]
      void Foo<T> () {}
  }
  ```

### <a name="limitations-of-the-new-registrar"></a>Limitaciones del nuevo registrador

Algunos aspectos que hay que tener en cuenta sobre el nuevo registrador:

- Algunas bibliotecas de terceros deben actualizarse para que funcionen con el nuevo sistema de registro. Consulte las [modificaciones necesarias](#required-modifications) a continuación para obtener más detalles.

- Una desventaja a corto plazo es también que se debe usar Clang si se usa el marco de cuentas (esto se debe a que el encabezado **accounts. h** de Apple solo lo puede compilar Clang). Agregue `--compiler:clang` los argumentos de Mtouch adicionales para usar Clang si usa Xcode 4,6 o una versión anterior (Xamarin. iOS seleccionará automáticamente Clang en xcode 5,0 o posterior).

- Si se usa Xcode 4,6 (o anterior), se debe seleccionar GCC/G + + si los nombres de tipo exportados contienen caracteres que no son ASCII (esto se debe a que la versión de Clang que se incluye con Xcode 4,6 no admite caracteres no ASCII dentro de identificadores en código de Objective-C). Agregue `--compiler:gcc` los argumentos Mtouch adicionales para usar gcc.

## <a name="selecting-a-registrar"></a>Selección de un registrador

Puede seleccionar un registrador diferente agregando una de las siguientes opciones a los argumentos Mtouch adicionales en la configuración de **compilación de iOS** del proyecto:

- `--registrar:static`: valor predeterminado para las compilaciones de dispositivos
- `--registrar:dynamic`: valor predeterminado para las compilaciones del simulador

> [!NOTE]
> Los Classic API de Xamarin admiten otras opciones, como `--registrar:legacystatic` y `--registrar:legacydynamic` . Sin embargo, estas opciones no son compatibles con el Unified API.

## <a name="shortcomings-in-the-old-registration-system"></a>Deficiencias en el sistema de registro anterior

El sistema de registro anterior tiene las siguientes desventajas:

- No había ninguna referencia estática (nativa) a las clases y métodos de Objective-C en bibliotecas nativas de terceros, lo que significaba que no pudimos solicitar al enlazador nativo que quitara código nativo de terceros que no se usaba realmente (porque se quitaría todo). Este es el motivo por el `-force_load libNative.a` que cada enlace de terceros tenía que hacer (o el equivalente `ForceLoad=true` en el `[LinkWith]` atributo).
- Podría exportar dos tipos administrados con el mismo nombre de Objective-C sin ninguna advertencia. Un escenario poco frecuente era acabar con dos `AppDelegate` clases en diferentes espacios de nombres. En el tiempo de ejecución, sería completamente aleatorio cuál se eligió (de hecho, se modificó entre las ejecuciones de una aplicación que no se han vuelto a generar, lo que se hizo para una experiencia de depuración muy desconcertantes y frustrante).
- Podría exportar dos métodos con la misma firma de Objective-C. De nuevo, el motivo por el que se llamaría desde Objective-C era aleatorio (pero este problema no era tan común como el anterior, por lo que la única manera de experimentar este error era invalidar el método administrado de la suerte).
- El conjunto de métodos que se exportó era ligeramente diferente entre las compilaciones estáticas y dinámicas.
- No funciona correctamente cuando se exportan clases genéricas (que la implementación genérica exacta ejecutada en tiempo de ejecución sería aleatoria, lo que da lugar a un comportamiento indeterminado).

<a name="required-modifications"></a>

## <a name="new-registrar-required-changes-to-bindings"></a>Nuevo registrador: cambios necesarios en los enlaces

En esta sección se describen los enlaces que se deben realizar para trabajar con el nuevo registrador.

### <a name="protocols-must-have-the-protocol-attribute"></a>Los protocolos deben tener el atributo [Protocol]

Los protocolos deben tener ahora el `[Protocol]` atributo. Si no lo hace, se producirá un error del vinculador nativo como:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Los selectores deben tener un número válido de parámetros

Todos los selectores deben indicar el número de parámetros correctamente. Anteriormente, estos errores se omitieron y podrían causar problemas de tiempo de ejecución.

En Resumen, el número de caracteres de dos puntos debe coincidir con el número de parámetros:

- Sin parámetros:`foo`
- Un parámetro:`foo:`
- Dos parámetros:`foo:parameterName2:`

Los siguientes son usos incorrectos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Usar el parámetro IsVariadic en la exportación

Las funciones variádicas deben usar el `IsVariadic` argumento para el `[Export]` atributo:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Debe vincularse a símbolos existentes

No es posible enlazar clases que no existen en la biblioteca nativa.
Si una clase se ha quitado o cambiado de nombre en la biblioteca nativa, asegúrese de actualizar los enlaces para que coincidan.
