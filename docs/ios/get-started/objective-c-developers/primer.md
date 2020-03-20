---
title: Manual de C# para desarrolladores de Objective-C
description: En este documento se describe C# para desarrolladores de Objective-C. Se comparan y se contrastan los dos lenguajes, para lo que se examinan los protocolos y las interfaces, las categorías y los métodos de extensión, los marcos y los ensamblados, los selectores y los parámetros con nombre, entre otras cosas.
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 6b2753b0f1a7389d62866e5c8003b439a283e5b5
ms.sourcegitcommit: 1c87135a47780f34102952d4b140850b4f08b075
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536508"
---
# <a name="c-primer-for-objective-c-developers"></a>Manual de C# para desarrolladores de Objective-C

_Xamarin.iOS permite el uso compartido entre plataformas del código independiente de plataformas escrito en C#. Sin embargo, es posible que las aplicaciones de iOS existentes quieran aprovechar el código de Objective-C que ya se ha creado. Este artículo sirve como un breve manual para los desarrolladores de Objective-C que desean pasarse a Xamarin y el lenguaje C#._

Las aplicaciones iOS y macOS desarrolladas en Objective-C se pueden beneficiar de Xamarin al aprovechar C# en lugares donde no se requiere código específico de la plataforma, lo que permite que este código se pueda usar en dispositivos que no son de Apple. Así pues, aspectos como los servicios web, el análisis de JSON y XML y los algoritmos personalizados podrán utilizarse entre plataformas.

Para aprovechar las ventajas de Xamarin manteniendo los activos existentes de Objective-C, aquel se puede exponer a C# en una tecnología de Xamarin conocida como "enlaces", que hace emerger el código de Objective-C para el entorno administrado de C#. Además, si lo desea, el código también se puede migrar línea por línea a C#. Sin embargo, con independencia del enfoque escogido (enlace o migración), se requieren algunos conocimientos de Objective-C y C# para aprovechar de manera efectiva el código de Objective-C existente con Xamarin.iOS.

## <a name="objective-c-interop"></a>Interoperabilidad de Objective-C

Actualmente no existe ningún mecanismo compatible para crear una biblioteca en C# con Xamarin.iOS que se pueda llamar desde Objective-C. La principal razón para ello es que se requiere el entorno de ejecución Mono además del enlace. Sin embargo, podrá seguir creando la mayoría de la lógica en Objective-C, incluidas las interfaces de usuario. Para ello, encapsule el código de Objective-C en una biblioteca y cree un enlace a él. Se necesita Xamarin.iOS para arrancar la aplicación (lo que significa que debe crear el punto de entrada `Main`). Después de eso, cualquier otra lógica puede estar en Objective-C, expuesta a C# a través del enlace (o mediante P/Invoke). De esta manera, puede mantener la lógica específica de la plataforma en Objective-C y desarrollar las partes independientes de la plataforma en C#.

En este artículo se resaltan algunas de las principales similitudes, y se contrastan también varias diferencias de ambos lenguajes, para que tenga unas nociones generales al respecto al trasladarse a C# con Xamarin.iOS, ya sea con un enlace al código existente de Objective-C o migrándolo a C#.

Para obtener más información acerca de cómo crear enlaces, vea el resto de los documentos en [Binding Objective-C](~/ios/platform/binding-objective-c/index.md) (Enlace de Objective-C).

## <a name="language-comparison"></a>Comparación de lenguajes

Objective-C y C# son lenguajes muy diferentes tanto sintácticamente como desde la perspectiva del entorno de ejecución. Objective-C es un lenguaje dinámico y utiliza un esquema de paso de mensajes, mientras que C# presenta tipos estáticos. Con respecto a la sintaxis, Objective-C es como Smalltalk, mientras que C# deriva gran parte de su sintaxis fundamental de Java, aunque en los últimos años ha madurado para incluir muchas funciones más allá de Java.

Dicho esto, hay varias características de lenguaje de Objective-C y C# cuya función es similar. Si va a crear un enlace a código de Objective-C desde C# o al migrar Objective-C a C#, es importante que entienda estas similitudes.

### <a name="protocols-vs-interfaces"></a>Protocolos frente a Interfaces

Objective-C y C# son lenguajes de herencia única. Sin embargo, ambos lenguajes ofrecen la posibilidad de implementar varias interfaces en una clase determinada. En Objective-C, estas interfaces lógicas se llaman *protocolos*, mientras que en C# se denominan *interfaces*. Capaces de asumir implementaciones, la diferencia principal entre una interfaz de C# y un protocolo de Objective-C es que el último puede tener métodos opcionales. Para obtener más información, consulte el artículo [Events, Delegates and Protocols](~/ios/app-fundamentals/delegates-protocols-and-events.md) (Eventos, delegados y protocolos).

### <a name="categories-vs-extension-methods"></a>Categorías frente a Métodos de extensión

Objective-C permite la adición de métodos a una clase para la que quizá no tenga el código de implementación por medio de *Categorías*. En C# está disponible un concepto similar a través de lo que se conoce como *métodos de extensión*.

Los métodos de extensión permiten agregar métodos estáticos a una clase, donde los métodos estáticos de C# son similares a los métodos de clase en Objective-C. Por ejemplo, el código siguiente agrega un método denominado `ScrollToBottom` a la clase `UITextView`, que a su vez es una clase administrada que se enlaza con la clase `UITextView` de Objective-C desde UIKit:

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

A continuación, cuando una se crea una instancia de `UITextView` en el código, el método estará disponible en la lista de autocompletar tal y como se muestra a continuación:

 ![](primer-images/01-extensionmethodintellisense.png "The method available in the autocomplete")

Cuando se llama al método de extensión, se pasa la instancia al argumento, como `textView` en este ejemplo.

### <a name="frameworks-vs-assemblies"></a>Marcos de trabajo frente a Ensamblados

Los paquetes de Objective-C relacionaban clases en directorios especiales conocidos como marcos de trabajo. Sin embargo, en C# y .NET los ensamblados se utilizan para proporcionar fragmentos de código precompilado reutilizables. En entornos fuera de iOS, los ensamblados contienen código de lenguaje intermedio (IL) que se compila en el entorno de ejecución según el método Just-In-Time (JIT). Pero Apple no permite la ejecución de código compilado JIT en aplicaciones iOS publicadas en la App Store. Por lo tanto, el código de C# destinado a iOS con Xamarin se compila antes de tiempo, produciendo un ejecutable de Unix junto con archivos de metadatos que se incluyen en el conjunto de la aplicación.

### <a name="selectors-vs-named-parameters"></a>Selectores frente a parámetros con nombre

Los métodos de Objective-C incluyen inherentemente nombres de parámetros en los selectores por su propia naturaleza. Por ejemplo, un selector como `AddCrayon:WithColor:` deja claro lo que significa cada parámetro cuando se usa en el código. C# admite opcionalmente también argumentos con nombre.

Por ejemplo, un código similar en C# utilizando argumentos con nombre sería este:

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

Aunque C# admite esta función en la versión 4.0 del lenguaje, en la práctica no se usa con frecuencia. Sin embargo, si desea ser explícito en el código, la compatibilidad efectivamente existe.

### <a name="headers-and-namespaces"></a>Encabezados y espacios de nombres

Al ser un superconjunto de C, Objective-C usa encabezados para las declaraciones públicas que son independientes del archivo de implementación. C# no utiliza archivos de encabezado. A diferencia de Objective-C, el código de C# se encuentra en los espacios de nombres. Si desea incluir código disponible en algún espacio de nombres, agregue una directiva using a la parte superior del archivo de implementación o califique el tipo con el espacio de nombres completo.

Por ejemplo, el código siguiente incluye el espacio de nombres `UIKit`, que permite que todas las clases de ese espacio de nombres estén disponibles para la implementación:

```csharp
using UIKit;
namespace MyAppNamespace
{
    // implementation of classes
}
```

Además, la palabra clave del espacio de nombres en el código anterior establece el espacio de nombres utilizado para el propio archivo de implementación. Si varios archivos de implementación comparten el mismo espacio de nombres, no es necesario incluir también el espacio de nombres en una directiva using, porque está implícito.

### <a name="properties"></a>Propiedades

Objective-C y C# tienen el concepto de propiedades para proporcionar una abstracción de alto nivel alrededor de los métodos de descriptor de acceso. En Objective C, se usa la directiva de compilador de @property para generar de forma eficaz los métodos de descriptor de acceso. En cambio, C# incluye compatibilidad con las propiedades en el propio lenguaje. Una propiedad de C# se puede implementar con un estilo más largo que tiene acceso a un campo de respaldo, o mediante una propiedad automática más corta, como se muestra en los ejemplos siguientes:

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Static (palabra clave)

La palabra clave *static* tiene un significado muy diferente en Objective-C y C#. En Objective-C, las funciones estáticas se usan para limitar el ámbito de una función al archivo actual. En C#, no obstante, el ámbito se mantiene a través de las palabras clave *public*, *private* e *internal*.

Cuando la palabra clave static se aplica a una variable en Objective-C, la variable mantiene su valor en las llamadas de las funciones.

C# también tiene una palabra clave static. Cuando se aplica a un método, hace efectivamente lo mismo que el modificador `+` hace en Objective-C. Es decir, crea un método de clase. De forma similar, cuando se aplica a otras construcciones, como campos, propiedades y eventos, hace que esas partes del tipo se declaren dentro de la instancia en lugar de con cualquier instancia de ese tipo. También puede crear una clase estática, en la que todos los métodos definidos en la clase también deben ser estáticos.

### <a name="nsarray-vs-list-initialization"></a>NSArray frente a Inicialización de lista

Objective-C ahora incluye la sintaxis literal para su uso con `NSArray`, con lo que el inicio es más sencillo. Sin embargo, C# tiene un tipo enriquecido denominado `List`, que es *genérico*, lo que significa que el tipo que contiene la lista puede proporcionarse por el código que crea la lista (por ejemplo, plantillas de C++). Además, las listas admiten la sintaxis de inicialización automática, tal y como se muestra a continuación:

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>Bloques frente a Expresiones lambda

Objective-C usa  *bloques* para crear cierres, donde puede crear una función en línea que puede hacer uso del estado en el que está incluida. C# tiene un concepto similar que se aplica mediante el uso de expresiones lambda. En C#, las expresiones lambda se crean con el operador `=>`, como se muestra a continuación:

```csharp
(args) => {
    //  implementation code
};
```

Para obtener más información sobre las expresiones lambda, vea la [guía de programación de C#](https://msdn.microsoft.com/library/vstudio/bb397687.aspx) de Microsoft.

## <a name="summary"></a>Resumen

En este artículo se contrastan diferentes características del lenguaje de Objective-C y C#. En algunos casos, se señalan características similares que existen en ambos lenguajes, como los bloques y las expresiones lambda o las categorías y los métodos de extensión. Además, contrasta aquellos aspectos en que los lenguajes se diferencian, como los espacios de nombres en C# y el significado de la palabra clave static.
