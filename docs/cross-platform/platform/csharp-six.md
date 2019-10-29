---
title: C#6 Introducción a las nuevas características
description: La versión 6 del C# lenguaje sigue evolucionando el lenguaje para tener un código menos reutilizable, una mejor claridad y una mayor coherencia. La sintaxis de inicialización del limpiador, la capacidad de usar Await en bloques Catch/Finally y el condicional null son especialmente útiles.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1db7ee95ec261739463fa2584f4acf493ac71217
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014816"
---
# <a name="c-6-new-features-overview"></a>C#6 Introducción a las nuevas características

_La versión 6 del C# lenguaje sigue evolucionando el lenguaje para tener un código menos reutilizable, una mejor claridad y una mayor coherencia. La sintaxis de inicialización del limpiador, la capacidad de usar Await en bloques Catch/Finally y el condicional null son especialmente útiles._

> [!NOTE]
> Para obtener información acerca de la versión más C# reciente del lenguaje, versión 7, consulte el artículo [novedades de C# 7,0](/dotnet/csharp/whats-new/csharp-7)

En este documento se presentan las nuevas C# características de 6. Es totalmente compatible con el compilador mono y los desarrolladores pueden empezar a usar las nuevas características en todas las plataformas de destino de Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Novedades de C# 6 vídeo**

## <a name="using-c-6"></a>Usando C# 6

El C# compilador 6 se usa en todas las versiones recientes de Visual Studio para Mac.
Los compiladores de línea de comandos deben confirmar que `mcs --version` devuelve 4,0 o superior.
Visual Studio para Mac usuarios pueden comprobar si tienen un mono 4 (o posterior) instalado mediante la referencia a **acerca de Visual Studio para Mac > Visual Studio para Mac > Mostrar detalles**.

## <a name="less-boilerplate"></a>Menos reutilizable
### <a name="using-static"></a>uso de versión estática
Las enumeraciones y ciertas clases, como `System.Math`, son principalmente propietarios de funciones y valores estáticos. En C# 6, puede importar todos los miembros estáticos de un tipo con una sola instrucción`using static`. Comparar una función trigonométrica típica en C# 5 y C# 6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` no hace que los campos de `const` públicos, como `Math.PI` y `Math.E`, sean accesibles directamente:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>usar Static con métodos de extensión

La utilidad `using static` funciona de forma ligeramente diferente con los métodos de extensión. Aunque los métodos de extensión se escriben utilizando `static`, no tienen sentido sin una instancia en la que operar. Por tanto, cuando se utiliza `using static` con un tipo que define métodos de extensión, los métodos de extensión están disponibles en su tipo de destino (el tipo de `this` del método). Por ejemplo, `using static System.Linq.Enumerable` se puede usar para extender la API de `IEnumerable<T>` objetos sin traer todos los tipos de LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

En el ejemplo anterior se muestra la diferencia de comportamiento: el método de extensión `Enumerable.Where` está asociado a la matriz, mientras que se puede llamar al método estático `String.Join` sin hacer referencia al tipo `String`.

### <a name="nameof-expressions"></a>Expresiones Name
A veces, desea hacer referencia al nombre que ha asignado a una variable o campo. En C# 6,`nameof(someVariableOrFieldOrType)`devolverá la cadena`"someVariableOrFieldOrType"`. Por ejemplo, al iniciar una `ArgumentException` es muy probable que desee asignar un nombre a un argumento no válido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

La principal ventaja de las expresiones de `nameof` es que se comprueban de tipos y son compatibles con la refactorización basada en herramientas. La comprobación de tipos de expresiones `nameof` es especialmente útil en situaciones en las que se usa un `string` para asociar tipos de forma dinámica. Por ejemplo, en iOS, se usa un `string` para especificar el tipo que se usa para el prototipo de objetos de `UITableViewCell` en un `UITableView`. `nameof` puede garantizar que no se produzcan errores en la Asociación debido a una refactorización incorrecta o a la refactorización de chapucero:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Aunque puede pasar un nombre completo a `nameof`, solo se devuelve el elemento final (después del último `.`). Por ejemplo, puede Agregar un enlace de datos en Xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Las dos llamadas a `SetBinding` pasan valores idénticos: `nameof(ReactiveType.StringField)` es `"StringField"`, no `"ReactiveType.StringField"` como cabría esperar inicialmente.

## <a name="null-conditional-operator"></a>Operador condicional null
Actualizaciones anteriores para C# introducir los conceptos de tipos que aceptan valores NULL y el operador de uso combinado de NULL`??`para reducir la cantidad de código reutilizable al administrar valores que aceptan valores NULL. C#6 continúa este tema con el`?.`"operador condicional nulo". Cuando se usa en un objeto en el lado derecho de una expresión, el operador condicional null devuelve el valor del miembro si el objeto no es `null` y `null` de lo contrario:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Tanto `length0` como `length1` se deducen para ser de tipo `int?`)

La última línea del ejemplo anterior muestra el `?` operador condicional null en combinación con el `??` operador de uso combinado de NULL. El nuevo C# operador condicional de 6 valores null devuelve`null`en el segundo elemento de la matriz, en cuyo punto se inicia el operador de uso combinado de NULL y proporciona un valor de 0 a la matriz de`lengths`(tanto si es adecuado como si no es, por supuesto, específico del problema).

El operador condicional null debe reducir enormemente la cantidad de comprobación de valores NULL reutilizable necesaria en muchas, muchas aplicaciones.

Hay algunas limitaciones en el operador condicional null debido a ambigüedades. No puede seguir inmediatamente un `?` con una lista de argumentos entre paréntesis, como podría esperar hacer con un delegado:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Sin embargo, `Invoke` se puede usar para separar el `?` de la lista de argumentos y sigue siendo una mejora marcada sobre un bloque de comprobación de `null`de reutilización:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolación de cadenas
La función `String.Format` ha usado tradicionalmente los índices como marcadores de posición en la cadena de formato, por ejemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). Por supuesto, agregar un nuevo valor siempre ha participado en una pequeña tarea de contar argumentos, volver a numerar los marcadores de posición e insertar el nuevo argumento en la secuencia derecha en la lista de argumentos.

C#la nueva característica de interpolación de cadenas de 6 mejora considerablemente en `String.Format`. Ahora, puede asignar nombres directamente a las variables en una cadena con el prefijo `$`. Por ejemplo:

```csharp
$"Expected: {expected} Received: {received}."
```

Por supuesto, las variables se comprueban y una variable mal escrita o no disponible producirá un error del compilador.

No es necesario que los marcadores de posición sean variables simples; pueden ser cualquier expresión. Dentro de estos marcadores de posición, puede usar comillas *sin* escapar a esas comillas. Por ejemplo, tenga en cuenta la `"s"` de la siguiente:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

La interpolación de cadenas admite la alineación y la sintaxis de formato de `String.Format`. Tal y como escribió anteriormente `{index, alignment:format}`, en C# 6 escribirá`{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```

da como resultado:

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

La interpolación de cadenas es el azúcar sintáctico de `String.Format`: no se puede usar con `@""` literales de cadena y no es compatible con `const`, aunque no se use ningún marcador de posición:

```csharp
const string s = $"Foo"; //Error : const requires value
```

En el caso de uso común de la creación de argumentos de función con interpolación de cadenas, todavía debe tener cuidado con los problemas de escape, codificación y referencia cultural. Por supuesto, las consultas SQL y URL son críticas para sanear. Como con `String.Format`, la interpolación de cadenas utiliza el `CultureInfo.CurrentCulture`. El uso de `CultureInfo.InvariantCulture` es un poco más:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inicialización

C#6 proporciona una serie de métodos concisos para especificar propiedades, campos y miembros.

### <a name="auto-property-initialization"></a>Inicialización de propiedad automática

Las propiedades automáticas ahora se pueden inicializar de la misma manera concisa que los campos. Las propiedades automáticas inmutables solo se pueden escribir con un captador:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

En el constructor, puede establecer el valor de una propiedad automática de solo captador:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Esta inicialización de propiedades automáticas es una característica de ahorro de espacio general y una ventaja para los desarrolladores que desean enfatizar la inmutabilidad de sus objetos.

### <a name="index-initializers"></a>Inicializadores de índice

C#6 incluye inicializadores de índice, que permiten establecer la clave y el valor en los tipos que tienen un indexador. Normalmente, esto es para las estructuras de datos de estilo `Dictionary`:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Miembros de función con forma de expresión

Las funciones lambda tienen varias ventajas, una de las cuales simplemente ahorra espacio. Del mismo modo, los miembros de clase con forma de expresión permiten expresar las funciones pequeñas un poco más brevemente de lo que era posible C# en versiones anteriores de 6.

Los miembros de función con forma de expresión usan la sintaxis de flecha lambda en lugar de la sintaxis de bloque tradicional:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Observe que la sintaxis de la flecha lambda no utiliza un `return`explícito. En el caso de las funciones que devuelven `void`, la expresión también debe ser una instrucción:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Los miembros con forma de expresión siguen sujetos a la regla de que `async` es compatible con los métodos pero no con las propiedades:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Excepciones

No hay dos maneras de hacerlo: el control de excepciones es difícil de obtener. Las nuevas características C# de 6 hacen que el control de excepciones sea más flexible y coherente.

### <a name="exception-filters"></a>Filtros de excepciones

Por definición, las excepciones se producen en circunstancias inusuales y puede ser muy difícil de motivar y código sobre *todas* las maneras en que se puede producir una excepción de un tipo determinado. C#6 presenta la capacidad de proteger un controlador de ejecución con un filtro evaluado en tiempo de ejecución. Esto se hace agregando un patrón de `when (bool)` después de la declaración de `catch(ExceptionType)` normal. En el siguiente, un filtro distingue un error de análisis relacionado con el parámetro `date` en lugar de otros errores de análisis.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>Await en Catch... por último...

Las funciones de `async` introducidas en C# 5 han sido un cambio de juego para el lenguaje. En C# 5,`await`no se permitía en los bloques`catch`y`finally`, un molestia dado el valor de la capacidad de`async/await`. C#6 quita esta limitación, lo que permite esperar los resultados asincrónicos de forma coherente a través del programa, tal como se muestra en el siguiente fragmento de código:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Resumen

El C# lenguaje sigue evolucionando para que los desarrolladores sean más productivos, al mismo tiempo que fomentan buenas prácticas y herramientas de soporte técnico. En este documento se proporciona información general sobre las nuevas características del C# lenguaje en 6 y se ha demostrado brevemente cómo se usan.

## <a name="related-links"></a>Vínculos relacionados

- [Nuevas características de lenguaje C# en 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
