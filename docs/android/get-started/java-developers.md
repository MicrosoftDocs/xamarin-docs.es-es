---
title: Xamarin para desarrolladores de Java
description: Si es usted es desarrollador de Java, ya tiene la posibilidad de empezar a aprovechar sus habilidades y el código existente de Xamarin en la plataforma Xamarin, al tiempo que disfruta de los beneficios de reutilizar código de C#. Observará que la sintaxis de C# es muy similar a la sintaxis de Java y que ambos lenguajes ofrecen características muy similares. Además, encontrará características exclusivas de C# que facilitarán el trabajo a los desarrolladores.
ms.prod: xamarin
ms.assetid: A3B6C041-4052-4E7D-999C-C4FA10BE3D67
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: b9c6694ea49607b839a3658e5cc8bac5fb529c85
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728062"
---
# <a name="xamarin-for-java-developers"></a>Xamarin para desarrolladores de Java

_Si es usted es desarrollador de Java, ya tiene la posibilidad de empezar a aprovechar sus habilidades y el código existente de Xamarin en la plataforma Xamarin, al tiempo que disfruta de los beneficios de reutilizar código de C#. Observará que la sintaxis de C# es muy similar a la sintaxis de Java y que ambos lenguajes ofrecen características muy similares. Además, encontrará características exclusivas de C# que facilitarán el trabajo a los desarrolladores._

## <a name="overview"></a>Información general

En este artículo se proporciona una introducción a la programación en C# para los desarrolladores de Java y se centra principalmente en las características del lenguaje C# con las que se encontrará al desarrollar aplicaciones Xamarin.Android. Además, en este artículo se explican las diferencias de estas características con respecto a las de Java y, además, se presentan características importantes de C# (relevantes para Xamarin.Android) que no se encuentran disponibles en Java. Se incluyen vínculos a material de referencia adicional, para que pueda usar este artículo como un punto de partida para obtener más información sobre C# y .NET.

Si está familiarizado con Java, no tardará en sentirse cómodo con la sintaxis de C#. La sintaxis de C# es muy similar a la de Java &ndash; C# es un lenguaje "de apertura" como Java, C y C++. En muchos sentidos, la sintaxis de C# se lee como un superconjunto de la sintaxis de Java, pero con algunas palabras clave agregadas y con el nombre cambiado.

Muchas características clave de Java pueden encontrarse en C#:

- Programación orientada a objetos basados en clases

- Tipado fuerte

- Compatibilidad con interfaces

- Genéricos

- Recolección de elementos no utilizados

- Compilación en tiempo de ejecución

Java y C# se compilan en un lenguaje intermedio que se ejecuta en un entorno de ejecución administrado. C# y Java son tipos estáticos, y ambos lenguajes tratan las cadenas como tipos inmutables.
Ambos lenguajes utilizan una jerarquía de clases de raíz única. Al igual que Java, C# solo admite una herencia única y no acepta los métodos globales.
En ambos lenguajes, los objetos se crean en el montón con la palabra clave `new` y se recoleccionan los objetos no utilizados cuando ya no se utilizan. Ambos lenguajes ofrecen una compatibilidad formal con el control de excepciones mediante la semántica `try`/`catch`. Los dos ofrecen también compatibilidad con la sincronización y administración de subprocesos.

No obstante, existen muchas diferencias entre Java y C#. Por ejemplo:

- Java (tal como se usa en Android) no admite las variables locales con tipo implícito (C# admite la palabra clave `var`).

- En Java, puede pasar parámetros solo por valor, mientras que en C# puede pasarlos tanto por referencia como por valor. (C# ofrece las palabras clave `ref` y `out` para pasar los parámetros por referencia; no hay ningún equivalente en Java).

- Java no admite las directivas de preprocesador como `#define`.

- Java no admite los tipos de enteros sin signo, mientras que C# sí que proporciona tipos de enteros sin signo como `ulong`, `uint`, `ushort` y `byte`.

- Java no admite la sobrecarga de operador; en C# se pueden sobrecargar operadores y conversiones.

- En una instrucción Java `switch`, el código puede pasar a la siguiente sección del modificador; en cambio, en C#, el final de cada sección `switch` debe terminar el modificador (el final de cada sección debe cerrarse con una instrucción `break`).

- En Java, puede especificar las excepciones generadas por un método con la palabra clave `throws`, pero C# no tiene ningún concepto de excepciones comprobadas; la palabra clave `throws` no se admite en C#.

- C# admite Language Integrated Query (LINQ), que permite usar las palabras reservadas `from`, `select` y `where` para escribir consultas de colecciones de forma similar a las consultas de bases de datos.

Por supuesto, hay muchas más diferencias entre C# y Java que pueden tratarse en este artículo. Además, Java y C# continúan evolucionando (por ejemplo, Java 8, que aún no se encuentra en la cadena de herramientas de Android, admite expresiones lambda del estilo de C#), por lo que estas diferencias seguirán evolucionando. Aquí solo se destacan las diferencias más importantes que han detectado los desarrolladores de Java que son nuevos en Xamarin.Android.

- En [Pasar del desarrollo en Java al desarrollo en C#](#fundamentals) se ofrece una introducción a las diferencias fundamentales entre C# y Java.

- En [Características de la programación orientada a objetos](#oopfeatures) se explican las diferencias más importantes de la característica orientada a objetos entre ambos lenguajes.

- En [Diferencias de palabras claves](#keywords) se ofrece una lista de equivalentes de palabras claves útiles, solo palabras claves de C# y vínculos a las definiciones de las palabras claves de C#.

C# proporciona muchas características claves de Xamarin.Android que actualmente no están disponibles para los desarrolladores de Java en Android. Estas características pueden ayudarle a escribir código mejor y en menos tiempo:

- [Propiedades](#properties) &ndash; Con el sistema de propiedades de C#, puede acceder a las variables de miembros de forma segura y directa sin tener que escribir métodos de establecedor y captador.

- [Expresiones lambda](#lambdas) &ndash; En C# puede usar métodos anónimos (también denominados *lambdas*) para expresar la funcionalidad de forma más sucinta y eficaz. Puede evitar la sobrecarga de tener que escribir objetos de un solo uso, y puede pasar el estado local a un método sin tener que agregar parámetros.

- [Control de eventos](#events) &ndash; C# ofrece compatibilidad a nivel de lenguaje para la *programación orientada a eventos*, donde se puede registrar un objeto para recibir una notificación cuando se produzca algún evento de interés. La palabra clave `event` define un mecanismo de multidifusión que una clase de publicador puede usar para informar a los suscriptores de eventos.

- [Programación asincrónica](#async) &ndash; Las características de la programación asincrónica de C# (`async`/`await`) mantienen la capacidad de respuesta de las aplicaciones.
    La compatibilidad a nivel de lenguaje de esta característica permite que la programación asincrónica resulte fácil de implementar y que sea menos propensa a errores.

Además, Xamarin le permite [aprovechar los recursos de Java existentes](#interop) mediante una tecnología que se conoce como *enlaces*. Puede llamar al código, los marcos de trabajo y las bibliotecas de Java existentes desde C# con el uso de generadores de enlaces automáticos de Xamarin. Para ello, basta con crear una biblioteca estática en Java y exponerla en C# mediante un enlace.

> [!NOTE]
> La programación de Android usa una versión específica del lenguaje Java que admite todas las características de Java 7 [y un subconjunto de Java 8](https://developer.android.com/studio/write/java8-support.html).
>
> Algunas características mencionadas en esta página (como la palabra clave `var` en C#) están disponibles en versiones más recientes de Java (p. ej., [`var` en Java 10](https://developer.oracle.com/java/jdk-10-local-variable-type-inference.html)), pero siguen si estar a disposición de los desarrolladores de Android.

<a name="fundamentals" />

## <a name="going-from-java-to-c-development"></a>Pasar del desarrollo en Java al desarrollo en C#

En las secciones siguientes se describen las diferencias básicas de "introducción" entre C# y Java; en una sección posterior se describen las diferencias orientadas a objetos entre estos lenguajes.

### <a name="libraries-vs-assemblies"></a>Bibliotecas frente a ensamblados

Java suele empaquetar clases relacionadas en archivos **.jar**. Sin embargo, en C# and .NET, los bits reutilizables de código precompilado se empaquetan en *ensamblados*, que suelen empaquetarse como archivos *.dll*. Un ensamblado es una unidad de implementación de código de C#/.NET, y cada ensamblado suele asociarse con un proyecto de C#. Los ensamblados contienen código intermedio que se compilan Just-In-Time en tiempo de ejecución.

Para obtener más información sobre los ensamblados, vea el tema [Ensamblados y caché global de ensamblados](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/assemblies-gac/).

### <a name="packages-vs-namespaces"></a>Paquetes frente a espacios de nombres

C# usa la palabra clave `namespace` para agrupar tipos relacionados; es similar a la palabra clave `package` de Java. Normalmente, una aplicación de Xamarin.Android residirá en un espacio de nombres creado para esa aplicación. Por ejemplo, el siguiente código de C# declara el contenedor del espacio de nombres `WeatherApp` para un aplicación de información meteorológica:

```csharp
namespace WeatherApp
{
    ...
```

### <a name="importing-types"></a>Importación de tipos

Al usar tipos definidos en los espacios de nombres externos, importe estos tipos con una instrucción `using` (que es muy similar a la instrucción `import` de Java). En Java, puede importar un solo tipo con una instrucción similar a la siguiente:

```java
import javax.swing.JButton
```

Puede importar un paquete de Java completo con una instrucción como esta:

```java
import javax.swing.*
```

La instrucción `using` de C# funciona de forma muy similar, pero permite importar un paquete completo sin especificar un carácter comodín. Por ejemplo, a menudo verá una serie de instrucciones `using` al principio de los archivos de origen de Xamarin.Android, como se observa en este ejemplo:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Net;
using System.IO;
using System.Json;
using System.Threading.Tasks;
```

Estas instrucciones importan la funcionalidad desde los espacios de nombres `System`, `Android.App`, `Android.Content`, etc.

### <a name="generics"></a>Genéricos

Tanto Java como C# admiten *genéricos*, que son marcadores de posición que permiten conectar diferentes tipos en tiempo de compilación. Sin embargo, los genéricos funcionan de forma algo diferente en C#. En Java, el [borrado de tipos](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html) habilita la información sobre tipos solo en tiempo de compilación, pero no en tiempo de ejecución. Por el contrario, .NET Common Language Runtime (CLR) proporciona compatibilidad explícita para los tipos genéricos, lo que significa que C# tiene acceso a información sobre tipos en tiempo de ejecución. En el desarrollo diario de Xamarin.Android, la importancia de esta distinción no suele ser evidente, pero si usa la [reflexión](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/reflection), dependerá de esta característica para acceder a la información sobre tipos en tiempo de ejecución.

En Xamarin.Android, verá con frecuencia el método genérico `FindViewById` utilizado para obtener una referencia a un control de diseño. Este método acepta un parámetro de tipo genérico que especifica el tipo de control que se buscará. Por ejemplo:

```csharp
TextView label = FindViewById<TextView> (Resource.Id.Label);
```

En este ejemplo de código, `FindViewById` obtiene una referencia al control `TextView` que se define en el diseño como **etiqueta**; a continuación, se devuelve como un tipo `TextView`.

Para obtener más información sobre los genéricos, vea el tema [Genéricos](https://docs.microsoft.com/dotnet/csharp/programming-guide/generics/index).
Tenga en cuenta que existen algunas limitaciones en la compatibilidad de Xamarin.Android con las clases genéricas de C#; para más información, vea [Limitaciones](~/android/internals/limitations.md).

<a name="oopfeatures" />

## <a name="object-oriented-programming-features"></a>Características de la programación orientada a objetos

Java y C# utilizan expresiones de programación orientada a objetos muy similares:

- Todas las clases derivan en última instancia de un único objeto raíz; todos los objetos de Java derivan de `java.lang.Object`, mientras que los de C# derivan de `System.Object`.

- Las instancias de clases son tipos de referencia.

- Cuando acceda a las propiedades y los métodos de una instancia, use el operador "`.`".

- Todas las instancias de clases se crean en el montón mediante el operador `new`.

- Debido a que ambos lenguajes usan la recolección de elementos no utilizados, no hay ningún método para liberar de forma explícita los objetos no utilizados (es decir, no hay ninguna palabra clave `delete` como hay en C++).

- Puede extender las clases mediante la herencia, y ambos lenguajes solo permiten una única clase base por tipo.

- Puede definir interfaces y una clase puede heredar de varias definiciones de interfaz (es decir, implementar).

Sin embargo, también hay algunas diferencias importantes:

- Java tiene dos características eficaces que C# no admite: las clases anónimas y las clases internas. (Sin embargo, C# permite el anidamiento de las definiciones de clases; las clases anidadas de C# son similares a las clases anidadas estáticas de Java).

- C# admite tipos de estructuras del estilo de C (`struct`), pero Java no.

- En C#, puede implementar una definición de clase en los archivos de código fuente independientes mediante la palabra clave `partial`.

- Las interfaces de C# no pueden declarar campos.

- C# utiliza la sintaxis de destructores de estilo de C++ para expresar los finalizadores. La sintaxis es diferente del método `finalize` de Java, pero la semántica es prácticamente la misma. (Tenga en cuenta que, en C#, los destructores llaman automáticamente al destructor de clase base &ndash;, a diferencia de Java, donde se usa una llamada explícita a `super.finalize`).

### <a name="class-inheritance"></a>Herencia de clases

Para extender una clase de Java, utilice la palabra clave `extends`. Para extender una clase de C#, utilice dos puntos (`:`) para indicar la derivación. Por ejemplo, en aplicaciones de Xamarin.Android, a menudo verá derivaciones de clases que se parecen al fragmento de código siguiente:

```csharp
public class MainActivity : Activity
{
    ...
```

En este ejemplo, `MainActivity` hereda de la clase `Activity`.

Para declarar la compatibilidad con una interfaz en Java, use la palabra clave `implements`. Sin embargo, en C#, solo tiene que agregar nombres de interfaz a la lista de clases de la que se hereda, como se muestra en este fragmento de código:

```csharp
public class SensorsActivity : Activity, ISensorEventListener
{
    ...
```

En este ejemplo, `SensorsActivity` hereda de `Activity` e implementa la funcionalidad declarada en la interfaz `ISensorEventListener`. Tenga en cuenta que la lista de interfaces debe aparecer después de la clase base o, de lo contrario, se producirá un error en tiempo de compilación. Por convención, los nombres de interfaz de C# van precedidos de una letra mayúscula "I"; esto permite determinar qué clases son interfaces sin requerir una palabra clave `implements`.

Si desea impedir que una clase derive en subclases en C#, coloque `sealed` &ndash; delante del nombre de clase; en Java, coloque `final` delante del nombre de clase.

Para obtener más información sobre las definiciones de clases de C#, vea los temas [Clases](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/classes) y [Herencia](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/inheritance).

### <a name="properties"></a>Propiedades

En Java, los métodos mutadores (establecedores) y los métodos de inspector (captadores) a menudo se utilizan para controlar cómo se realizan los cambios en los miembros de clase al ocultarlos y protegerlos del código externo. Por ejemplo, la clase `TextView` de Android proporciona los métodos `getText` y `setText`. C# proporciona un mecanismo similar pero más directo conocido como *propiedades*.
Los usuarios de una clase de C# pueden acceder a una propiedad de la misma manera en que accederían a un campo, pero cada acceso realmente produce una llamada de método que es transparente para el autor de la llamada. Este método "pormenorizado" puede implementar los efectos secundarios, como establecer otros valores, realizar conversiones o cambiar el estado del objeto.

Las propiedades suelen utilizarse para acceder a los miembros de objetos de la interfaz de usuario y para modificarlos. Por ejemplo:

```csharp
int width = rulerView.MeasuredWidth;
int height = rulerView.MeasuredHeight;
...
rulerView.DrawingCacheEnabled = true;
```

En este ejemplo, se leen los valores de anchura y altura del objeto `rulerView` mediante el acceso a sus propiedades `MeasuredWidth` y `MeasuredHeight`. Cuando se leen estas propiedades, los valores de sus valores de campos asociados, pero ocultos, se capturan en segundo plano y se devuelven al autor de la llamada. El objeto `rulerView` puede almacenar los valores de anchura y altura en una unidad de medida (es decir, píxeles) y convertirlos sobre la marcha en una unidad de medida distinta (por ejemplo, milímetros) al acceder a las propiedades `MeasuredWidth` y `MeasuredHeight`.

El objeto `rulerView` también tiene una propiedad denominada `DrawingCacheEnabled` &ndash; el código de ejemplo establece esta propiedad en `true` para habilitar la caché de dibujo en `rulerView`. En segundo plano, se actualiza un campo oculto asociado con el nuevo valor y es posible que se modifiquen otros aspectos del estado `rulerView`. Por ejemplo, cuando `DrawingCacheEnabled` está establecido en `false`, `rulerView` también puede borrar cualquier información de la caché de dibujo ya acumulada en el objeto.

El acceso a las propiedades puede ser de lectura/escritura, solo lectura o solo escritura. Además, puede usar distintos modificadores de acceso para leer y escribir. Por ejemplo, puede definir una propiedad que tenga acceso de lectura público, pero acceso de escritura privado.

Para obtener más información sobre las propiedades de C#, vea el tema [Propiedades](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/properties).

### <a name="calling-base-class-methods"></a>Llamar a métodos de clase base

Para llamar a un constructor de clase base en C#, utilice dos puntos (`:`) seguidos de la palabra clave `base` y una lista de inicializadores; esta llamada al constructor `base` se coloca inmediatamente después de la lista de parámetros del constructor derivada. Se llama al constructor de clase base al introducir el constructor derivado; el compilador inserta la llamada en el constructor base al inicio del cuerpo del método. El siguiente fragmento de código muestra un constructor base llamado desde un constructor derivado en una aplicación de Xamarin.Android:

```csharp
public class PictureLayout : ViewGroup
{
    ...
    public PictureLayout (Context context)
           : base (context)
    {
        ...
    }
    ...
}

```

En este ejemplo, la clase `PictureLayout` se deriva de la clase `ViewGroup`. El constructor `PictureLayout` que se muestra en este ejemplo acepta un argumento `context` y lo pasa al constructor `ViewGroup` mediante la llamada `base(context)`.

Para llamar a un método de clase base en C#, use la palabra clave `base`. Por ejemplo, las aplicaciones de Xamarin.Android suelen realizar llamadas a los métodos base, como se muestra aquí:

```csharp
public class MainActivity : Activity
{
    ...
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
```

En este caso, el método `OnCreate` definido por la clase derivada (`MainActivity`) llama al método `OnCreate` de la clase base (`Activity`).

### <a name="access-modifiers"></a>Modificadores de acceso

Java y C# admiten los modificadores de acceso `public`, `private` y `protected`. Sin embargo, C# admite dos modificadores de acceso adicionales:

- **`internal`** &ndash; Al miembro de clase se puede acceder únicamente dentro del ensamblado actual.

- **`protected internal`** &ndash; Al miembro de clase se puede acceder dentro del ensamblado de definición, de la clase de definición y de las clases derivadas (tienen acceso las clases derivadas dentro y fuera del ensamblado).

Para obtener más información sobre los modificadores de acceso de C#, vea el tema [Modificadores de acceso](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers).

### <a name="virtual-and-override-methods"></a>Métodos virtuales y de invalidación

Java y C# admiten el *polimorfismo*, que es la capacidad de tratar objetos relacionados de la misma manera. En ambos lenguajes, se puede usar una referencia de clase base para hacer referencia a un objeto de clases derivada, y los métodos de una clase derivada pueden invalidar los métodos de sus clases base. Ambos lenguajes tienen el concepto de un método *virtual*, que es un método de una clase base diseñado para sustituirse por un método de una clase derivada.
Al igual que Java, C# admite clases y métodos `abstract`.

Sin embargo, hay algunas diferencias entre Java y C# en cómo declarar métodos virtuales e invalidarlos:

- En C#, los métodos son no virtuales de forma predeterminada. Las clases principales deben etiquetar de forma explícita qué métodos deben invalidarse con el uso de la palabra clave `virtual`. Por el contrario, todos los métodos de Java son virtuales de forma predeterminada.

- Para evitar la invalidación de un método en C#, solo tiene que excluir la palabra clave `virtual`. Por el contrario, Java usa la palabra clave `final` para marcar un método con "override is not allowed" (No se permite la invalidación).

- Las clases derivadas de C# deben usar la palabra clave `override` para indicar de forma explícita que se va a anular un método de clase base virtual.

Para obtener más información sobre la compatibilidad de C# con el polimorfismo, vea el tema [Polimorfismo](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/polymorphism).

<a name="lambdas" />

## <a name="lambda-expressions"></a>Expresiones lambda

C# permite crear *clausuras*: métodos anónimos insertados que pueden acceder al estado del método en que están incluidos.
Con el uso de expresiones lambda, puede escribir menos líneas de código para implementar la misma funcionalidad que hubiera implementado en Java con muchas más líneas de código.

Las expresiones lambda permiten omitir los pasos adicionales necesarios para crear una clase de un solo uso o una clase anónima que debería aplicar en Java; en su lugar, solo puede escribir la lógica de negocios del código del método insertado. Además, como las expresiones lambda tienen acceso a las variables del método adyacente, no tiene que crear una lista larga de parámetros para pasar el estado al código del método.

En C#, las expresiones lambda se crean con el operador `=>`, como se muestra a continuación:

```csharp
(arg1, arg2, ...) => {
    // implementation code
};
```

En Xamarin.Android, a menudo se usan expresiones lambda para definir controladores de eventos. Por ejemplo:

```csharp
button.Click += (sender, args) => {
    clickCount += 1;    // access variable in surrounding code
    button.Text = string.Format ("Clicked {0} times.", clickCount);
};
```

En este ejemplo, el código de la expresión lambda (el código encerrado entre llaves) incrementa un recuento de clics y actualiza el texto `button` para mostrar el recuento de clics. Esta expresión lambda se registra con el objeto `button` como un controlador de eventos de clics al que se llama cada vez que se pulsa un botón. (Los controladores de eventos se explican con más detalle a continuación). En este ejemplo, el código de la expresión lambda no usa los parámetros `sender` y `args`, pero sí son necesarios en la expresión lambda para satisfacer los requisitos de firma del método para el registro del evento. En segundo plano, el compilador de C# convierte la expresión lambda en un método anónimo al que se llama cada vez que se hace clic en un botón.

Para obtener más información sobre C# y las expresiones lambda, vea el tema [Expresiones lambda](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).

<a name="events" />

## <a name="event-handling"></a>Control de eventos

Un *eventos* es la forma en que un objeto notifica a los suscriptores registrados cuándo sucede algo interesante a dicho objeto. A diferencia de Java, donde un suscriptor que suele implementar una interfaz `Listener` que contiene un método de devolución de llamada, C# ofrece compatibilidad a nivel de lenguaje para el control de eventos mediante *delegados*. Un *delegado* es similar a un puntero de función con seguridad de tipos orientado a objetos; encapsula una referencia de objeto y un token de método. Si un objeto de cliente desea suscribirse a un evento, crea un delegado y pasa el delegado al objeto notificador.
Cuando se produce el evento, el objeto notificador invoca el método representado por el objeto delegado, para notificar al objeto de cliente de suscripción del evento. En C#, los controladores de eventos básicamente no son más que métodos que se invocan mediante delegados.

Para obtener más información sobre los delegados, vea el tema [Delegados](https://docs.microsoft.com/dotnet/csharp/programming-guide/delegates/index).

En C#, los eventos son *multidifusión*; esto quiere decir que se puede notificar a más de un agente de escucha cuando se produce un evento. Esta diferencia se observa al tener en cuenta las diferencias sintácticas entre el registro de eventos de Java y C#. En Java, se llama a `SetXXXListener` para registrar notificaciones de eventos; en C#, se usa el operador `+=` para registrar notificaciones de eventos al "agregar" el delegado a la lista de agentes de escucha de eventos.
En Java, se llama a `SetXXXListener` para anular el registro, mientras que en C# se utiliza `-=` para "restar" el delegado de la lista de agentes de escucha.

En Xamarin.Android, los eventos se usan con frecuencia para informar a los objetos cuando un usuario realiza una acción en un control de interfaz de usuario. Normalmente, un control de interfaz de usuario tendrá miembros que se definen con la palabra clave `event`; asocie los delegados con estos miembros para suscribirse a eventos de ese control de interfaz de usuario.

Para suscribirse a un evento:

1. Cree un objeto delegado que haga referencia al método que quiere invocar cuando se produce el evento.

2. Use el operador `+=` para asociar el delegado al evento al que se va a suscribir.

En el ejemplo siguiente se define un delegado (con el uso explícito de la palabra clave `delegate`) para suscribirse a los clics de botón.
Este controlador de clics de botón inicia una nueva actividad:

```csharp
startActivityButton.Click += delegate {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

Sin embargo, también puede utilizar una expresión lambda para registrar eventos, omitiendo por completo la palabra clave `delegate`. Por ejemplo:

```csharp
startActivityButton.Click += (sender, e) => {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

En este ejemplo, el objeto `startActivityButton` tiene un evento que espera un delegado con una firma de método determinada: uno que acepta argumentos de remitente y evento y devuelve un valor nulo. No obstante, como no queremos ocasionar la molestia de definir de forma explícita dicho delegado o su método, declaramos la firma del método con `(sender, e)` y usamos una expresión lambda para implementar el cuerpo del controlador de eventos.
Tenga en cuenta que tenemos que declarar esta lista de parámetros aunque usemos los parámetros `sender` y `e`.

Es importante recordar que se puede anular la suscripción de un delegado (con el operador `-=`), pero no se puede anular la suscripción de una expresión lambda; tratar de hacerlo puede causar fugas de memoria. Use la forma de expresión lambda del registro de eventos solo si no se anula la suscripción del controlador al evento.

Normalmente, las expresiones lambda se usan para declarar controladores de eventos en el código de Xamarin.Android. Esta forma abreviada de declarar controladores de eventos puede parecer críptica al principio, pero ahorra una gran cantidad de tiempo al leer y escribir código. Cuando se familiarice más, se habituará a reconocer este patrón, que ocurre con frecuencia en el código de Xamarin.Android, y podrá dedicar más tiempo a pensar en la lógica de negocios de su aplicación y menos tiempo a repasar la sobrecarga sintáctica.

<a name="async" />

## <a name="asynchronous-programming"></a>Programación asincrónica

La *programación asincrónica* es una manera de mejorar la capacidad de respuesta general de la aplicación. Las características de la programación asincrónica permiten que el resto del código de la aplicación continúe ejecutándose mientras que una operación larga bloquea alguna parte de la aplicación.
El acceso a la Web, el procesamiento de imágenes y la lectura y escritura de archivos son ejemplos de operaciones que pueden causar que una aplicación parezca inmovilizada si no se escribe de forma asincrónica.

C# incluye compatibilidad a nivel de lenguaje con la programación asincrónica mediante las palabras clave `async` y `await`. Estas características del lenguaje facilitan bastante la escritura de código que realiza tareas de ejecución prolongada sin bloquear el subproceso principal de la aplicación. En resumen, use la palabra clave `async` en un método para indicar que el código del método debe ejecutarse de forma asincrónica y no bloquear el subproceso del autor de la llamada. Use la palabra clave `await` para llamar a métodos marcados como `async`. El compilador interpreta `await` como el punto al que se va a mover la ejecución del método a un subproceso en segundo plano (se devuelve una tarea al autor de la llamada). Una vez completada esta tarea, la ejecución del código se reanuda en el subproceso del autor de la llamada en el punto `await` del punto, devolviendo los resultados de la llamada `async`. Por convención, los métodos que se ejecutan de forma asincrónica llevan el sufijo `Async` en sus nombres.

En las aplicaciones de Xamarin.Android, `async` y `await` suelen usarse para liberar el subproceso de la interfaz de usuario, para que pueda responder a la entrada del usuario (como pulsar un botón **Cancelar**) mientras tiene lugar una operación de ejecución prolongada en una tarea en segundo plano.

En el ejemplo siguiente, un controlador de eventos de clics de botón resulta en una operación asincrónica para descargar una imagen de la Web:

```csharp
downloadButton.Click += downloadAsync;
...
async void downloadAsync(object sender, System.EventArgs e)
{
    webClient = new WebClient ();
    var url = new Uri ("http://photojournal.jpl.nasa.gov/jpeg/PIA15416.jpg");
    byte[] bytes = null;

    bytes = await webClient.DownloadDataTaskAsync(url);

    // display the downloaded image ...
```

En este ejemplo, cuando el usuario hace clic en el control `downloadButton`, el controlador de eventos `downloadAsync` crea un objeto `WebClient` y un objeto `Uri` para capturar una imagen de la dirección URL especificada. A continuación, llama al método `DownloadDataTaskAsync` del objeto `WebClient` con esta dirección URL para recuperar la imagen.

Tenga en cuenta que la declaración del método `downloadAsync` va precedida de la palabra clave `async` para indicar que se ejecutará de forma asincrónica y que devolverá una tarea. Tenga en cuenta también que la llamada a `DownloadDataTaskAsync` va precedida de la palabra clave `await`. La aplicación mueve la ejecución del controlador de eventos (a partir del punto en que `await` aparece) a un subproceso en segundo plano hasta que `DownloadDataTaskAsync` se completa y realiza la devolución.
Mientras tanto, el subproceso de la interfaz de usuario de la aplicación puede responder aún a la entrada del usuario y activar los controladores de eventos para los otros controles. Cuando `DownloadDataTaskAsync` se completa (que puede tardar varios segundos), la ejecución se reanuda donde la variable `bytes` está establecida en el resultado de la llamada a `DownloadDataTaskAsync`, y el resto del código del controlador de eventos muestra la imagen descargada en el subproceso del autor de la llamada (interfaz de usuario).

Para obtener una introducción a `async`/`await` en C#, vea el tema [Programación asincrónica con Async y Await](https://docs.microsoft.com/dotnet/csharp/async).
Para más información sobre la compatibilidad de Xamarin con las características de la programación asincrónica, vea [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

<a name="keywords" />

## <a name="keyword-differences"></a>Diferencias de palabras claves

Muchas de las palabras claves del lenguaje de Java también se usan en C#. También hay una serie de palabras clave de Java que tienen un equivalente en C# pero con un nombre distinto, como se indica en esta tabla:

|Java|C#|Descripción|
|---|---|---|
|`boolean`|[bool](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/bool)|Se utiliza para declarar los valores booleanos true y false.|
|`extends`|`:`|Precede a la clase e interfaces de las que se hereda.|
|`implements`|`:`|Precede a la clase e interfaces de las que se hereda.|
|`import`|[using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using)|Importa tipos de un espacio de nombres, que también se usa para crear un alias de espacio de nombres.|
|`final`|[sealed](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/sealed)|Evita la derivación de clase; impide que se invaliden los métodos y las propiedades de la clase derivada.|
|`instanceof`|[is](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/is)|Evalúa si un objeto es compatible con un tipo determinado.|
|`native`|[extern](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/extern)|Declara un método que se implementa externamente.|
|`package`|[namespace](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/namespace)|Declara un ámbito para un conjunto de objetos relacionados.|
|`T...`|[params T](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/params)|Especifica un parámetro de método que toma un número variable de argumentos.|
|`super`|[base](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/base)|Se usa para acceder a los miembros de la clase principal desde una clase derivada.|
|`synchronized`|[lock](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/lock-statement)|Ajusta una sección crítica del código con lanzamiento y adquisición de bloqueo.|

Además, hay muchas palabras clave que son exclusivas de C# y que no tienen homólogo en el Java usado en Android. El código de Xamarin.Android suele usar las siguientes palabras clave de C# (es útil hacer referencia a esta tabla al leer [código de ejemplo](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android) de Xamarin.Android):

|C#|Descripción|
|---|---|
|[as](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/as)|Realiza conversiones entre tipos de referencia compatibles o tipos que aceptan valores NULL.|
|[async](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async)|Especifica que un método o una expresión lambda son asincrónicos.|
|[await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await)|Suspende la ejecución de un método hasta que se completa una tarea.|
|[byte](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/byte)|Tipo entero de 8 bits sin signo.|
|[delegate](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/delegate)|Se utiliza para encapsular un método o un método anónimo.|
|[enum](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/enum)|Declara una enumeración, un conjunto de constantes con nombre.|
|[event](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/event)|Declara un evento en una clase de publicador.|
|[fixed](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/fixed-statement)|Impide la reubicación de una variable.|
|`get`|Define un método de descriptor de acceso que recupera el valor de una propiedad.|
|[in](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/in-generic-modifier)|Permite que un parámetro acepte un tipo menos derivado en una interfaz genérica.|
|[object](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/object)|Un alias para el tipo Object en .NET Framework.|
|[out](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/out)|Modificador de parámetros o declaración de parámetros de tipo genérico.|
|[override](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/override)|Amplía o modifica la implementación de un miembro heredado.|
|[partial](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/partial-method)|Declara que una definición se va a dividir en varios archivos o que se va a separar una definición de método de su implementación.|
|[readonly](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/readonly)|Declara que un miembro de clase puede asignarse solo en el momento de la declaración o mediante el constructor de clase.|
|[ref](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/ref)|Hace que un argumento se pase por referencia en lugar de por valor.|
|[set](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/set)|Define un método de descriptor de acceso que establece el valor de una propiedad.|
|[string](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string)|Alias para el tipo String en .NET Framework.|
|[struct](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/struct)|Un tipo de valor que encapsula un grupo de variables relacionadas.|
|[typeof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/typeof)|Obtiene el tipo de un objeto.|
|[var](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/var)|Declara una variable local con tipo implícito.|
|[value](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value)|Hace referencia al valor que el código de cliente desea asignar a una propiedad.|
|[virtual](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/virtual)|Permite la invalidación de un método en una clase derivada.|

<a name="interop" />

## <a name="interoperating-with-existing-java-code"></a>Interoperación con código de Java existente

Si dispone de una funcionalidad de Java existente que no desea convertir a C#, puede volver a usar las bibliotecas de Java existentes en las aplicaciones de Xamarin.Android con estas dos técnicas:

- **Crear una biblioteca de enlaces de Java** &ndash; Con este enfoque, se utilizan las herramientas de Xamarin para generar contenedores de C# que incluyen tipos de Java. Estos contenedores se denominan *enlaces*. Como resultado, la aplicación de Xamarin.Android puede usar el archivo *.jar* con una llamada a estos contenedores.

- **Java Native Interface** &ndash; *Java Native Interface* (JNI) es un marco que permite a las aplicaciones de C# llamar al código de Java o recibir llamadas de este código.

Para obtener más información sobre estas técnicas, vea [Java Integration Overview](~/android/platform/java-integration/index.md) (Información general sobre la integración de Java).

## <a name="further-reading"></a>Información adicional

La [guía de programación de C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/) es un recurso muy útil para iniciarse en el aprendizaje del lenguaje de programación C#, y puede usar la [referencia de C#](https://docs.microsoft.com/dotnet/csharp/language-reference/) para buscar características particulares del lenguaje C#.

De la misma forma que el conocimiento de Java tiene al menos tanto que ver con la familiaridad con las bibliotecas de clases de Java como con el conocimiento del lenguaje Java, el conocimiento práctico de C# requiere alguna familiaridad con .NET Framework. El paquete de aprendizaje [Pasar a C# y .NET Framework, para desarrolladores de Java](https://www.microsoft.com/download/details.aspx?id=6073) de Microsoft es un buen recurso para aprender más sobre .NET Framework desde la perspectiva de Java (al mismo tiempo que se adquiere un conocimiento más profundo de C#).

Cuando esté listo para abordar el primer proyecto de Xamarin.Android en C#, nuestra serie [Hello, Android](~/android/get-started/hello-android/index.md) puede facilitar la compilación de la primera aplicación de Xamarin.Android y ayudar a profundizar los conocimientos de los aspectos fundamentales del desarrollo de aplicaciones de Android con Xamarin.

## <a name="summary"></a>Resumen

Este artículo ofrece una introducción al entorno de programación de C# en Xamarin.Android desde la perspectiva de los desarrolladores de Java. Se indican las similitudes entre C# y Java, además de explicar sus diferencias prácticas. Aborda los ensamblados y los espacios de nombres, se explica cómo importar tipos externos y se ofrece información general sobre las diferencias de los modificadores de acceso, los genéricos, la derivación de clases, la llamada a métodos de clases base, la invalidación de métodos y el control de eventos. Se presentan características de C# que no están disponibles en Java, como las propiedades, la programación asincrónica de `async`/`await`, las expresiones lambda, los delegados de C# y el sistema de control de eventos de C#. Incluye tablas de palabras claves importantes de C#, se explica cómo interoperar con bibliotecas de Java existentes y se ofrecen vínculos a documentación relacionada realizar un estudio adicional.

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la integración de Java](~/android/platform/java-integration/index.md)
- [Guía de programación de C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/)
- [Referencia de C#](https://docs.microsoft.com/dotnet/csharp/language-reference/index)
- [Pasar a C# y .NET Framework, para desarrolladores de Java](https://www.microsoft.com/download/details.aspx?id=6073)
