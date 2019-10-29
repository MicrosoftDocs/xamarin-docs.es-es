---
title: Inserción de dependencias
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers usa la inserción de dependencias para desacoplar tipos concretos del código que depende de estos tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 975b32610b4b496e329c5c5a29b79efd2874d8cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029500"
---
# <a name="dependency-injection"></a>Inserción de dependencias

Normalmente, se invoca un constructor de clase al crear instancias de un objeto y los valores que necesita el objeto se pasan como argumentos al constructor. Este es un ejemplo de inserción de dependencias y, específicamente, se conoce como *inserción de constructores*. Las dependencias que necesita el objeto se insertan en el constructor.

Al especificar las dependencias como tipos de interfaz, la inserción de dependencias permite desacoplar los tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

También hay otros tipos de inserción de dependencias, como la inserción de un *establecedor de propiedad*y la *inserción de llamadas de método*, pero se suelen considerar. Por lo tanto, este capítulo se centrará únicamente en realizar la inserción de constructores con un contenedor de inserción de dependencias.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introducción a la inserción de dependencias

La inserción de dependencias es una versión especializada del patrón de inversión de control (IoC), donde el problema que se invierte es el proceso de obtención de la dependencia necesaria. Con la inserción de dependencias, otra clase es responsable de insertar las dependencias en un objeto en tiempo de ejecución. En el ejemplo de código siguiente se muestra cómo se estructura la clase `ProfileViewModel` cuando se usa la inserción de dependencias:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

El constructor `ProfileViewModel` recibe una instancia de `IOrderService` como argumento, insertada por otra clase. La única dependencia de la clase `ProfileViewModel` está en el tipo de interfaz. Por lo tanto, la clase `ProfileViewModel` no tiene ningún conocimiento de la clase que es responsable de crear instancias del objeto `IOrderService`. La clase que es responsable de crear instancias del objeto `IOrderService` y de insertarlo en la clase `ProfileViewModel` se conoce como *contenedor de inserción de dependencias*.

Los contenedores de inserción de dependencias reducen el acoplamiento entre objetos proporcionando una utilidad para crear instancias de la clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que el objeto requiere en él. Si estas dependencias no se han creado todavía, el contenedor crea y resuelve primero sus dependencias.

> [!NOTE]
> La inserción de dependencias también puede implementarse manualmente mediante generadores. Sin embargo, el uso de un contenedor proporciona funcionalidades adicionales, como la administración de la duración y el registro a través del análisis de ensamblados.

El uso de un contenedor de inserción de dependencias tiene varias ventajas:

- Un contenedor elimina la necesidad de que una clase busque sus dependencias y administre su duración.
- Un contenedor permite la asignación de dependencias implementadas sin afectar a la clase.
- Un contenedor facilita la capacidad de prueba, ya que permite simular las dependencias.
- Un contenedor aumenta la facilidad de mantenimiento al permitir que las nuevas clases se agreguen fácilmente a la aplicación.

En el contexto de una aplicación de Xamarin. Forms que usa MVVM, se suele usar un contenedor de inserción de dependencias para registrar y resolver modelos de vista, así como para registrar servicios e insertarlos en modelos de vista.

Hay muchos contenedores de inserción de dependencias disponibles, con la aplicación móvil eShopOnContainers con Autofac para administrar la creación de instancias del modelo de vista y las clases de servicio en la aplicación. Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se encuentran normalmente en los contenedores de inserción de dependencias, incluidos los métodos para registrar las asignaciones de tipos y las instancias de objeto, resolver objetos, administrar la duración de los objetos e insertar objetos dependientes en constructores de objetos que resuelve. Para obtener más información sobre Autofac, vea [Autofac](https://autofac.readthedocs.io/en/latest/index.html) en readthedocs.IO.

En Autofac, la interfaz de `IContainer` proporciona el contenedor de inserción de dependencias. En la figura 3-1 se muestran las dependencias cuando se usa este contenedor, que crea una instancia de un objeto `IOrderService` y lo inserta en la clase `ProfileViewModel`.

![](dependency-injection-images/dependencyinjection.png "Dependencies example when using dependency injection")

**Figura 3-1:** Dependencias cuando se usa la inserción de dependencias

En tiempo de ejecución, el contenedor debe saber en qué implementación de la interfaz `IOrderService` debe crear una instancia, antes de poder crear una instancia de un objeto `ProfileViewModel`. Esto implica:

- El contenedor que decide cómo crear una instancia de un objeto que implementa la interfaz `IOrderService`. Esto se conoce como *registro*.
- Contenedor que crea una instancia del objeto que implementa la interfaz `IOrderService` y el objeto `ProfileViewModel`. Esto se conoce como *resolución*.

Finalmente, la aplicación terminará de usar el objeto `ProfileViewModel` y estará disponible para la recolección de elementos no utilizados. En este momento, el recolector de elementos no utilizados debe desechar la instancia de `IOrderService` si otras clases no comparten la misma instancia.

> [!TIP]
> Escribir código independiente del contenedor. Intente escribir siempre código independiente del contenedor para desacoplar la aplicación del contenedor de dependencias específico que se está usando.

## <a name="registration"></a>Registro

Antes de que se puedan insertar dependencias en un objeto, los tipos de las dependencias deben registrarse primero con el contenedor. Normalmente, el registro de un tipo implica pasar al contenedor una interfaz y un tipo concreto que implementa la interfaz.

Hay dos formas de registrar tipos y objetos en el contenedor a través del código:

- Registre un tipo o una asignación con el contenedor. Cuando sea necesario, el contenedor creará una instancia del tipo especificado.
- Registra un objeto existente en el contenedor como singleton. Cuando sea necesario, el contenedor devolverá una referencia al objeto existente.

> [!TIP]
> Los contenedores de inserción de dependencias no siempre son adecuados. La inserción de dependencias presenta una complejidad adicional y requisitos que podrían no ser adecuados o útiles para aplicaciones pequeñas. Si una clase no tiene ninguna dependencia, o no es una dependencia para otros tipos, puede que no tenga sentido colocarla en el contenedor. Además, si una clase tiene un único conjunto de dependencias que son integrales para el tipo y nunca cambia, puede que no tenga sentido colocarlo en el contenedor.

El registro de tipos que requieren la inserción de dependencias se debe realizar en un único método en una aplicación, y este método se debe invocar al principio del ciclo de vida de la aplicación para asegurarse de que la aplicación es consciente de las dependencias entre sus clases. En la aplicación móvil eShopOnContainers, se realiza mediante la clase `ViewModelLocator`, que compila el objeto `IContainer` y es la única clase de la aplicación que contiene una referencia a ese objeto. En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers declara el objeto `IContainer` en la clase `ViewModelLocator`:

```csharp
private static IContainer _container;
```

Los tipos e instancias se registran en el método `RegisterDependencies` de la clase `ViewModelLocator`. Esto se consigue creando primero una instancia de `ContainerBuilder`, que se muestra en el ejemplo de código siguiente:

```csharp
var builder = new ContainerBuilder();
```

Los tipos e instancias se registran con el objeto `ContainerBuilder` y en el ejemplo de código siguiente se muestra la forma más común de registro de tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

El método `RegisterType` que se muestra aquí asigna un tipo de interfaz a un tipo concreto. Indica al contenedor que cree una instancia de un objeto `RequestProvider` cuando crea una instancia de un objeto que requiere una inserción de un `IRequestProvider` a través de un constructor.

Los tipos concretos también se pueden registrar directamente sin una asignación de un tipo de interfaz, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Cuando se resuelve el tipo de `ProfileViewModel`, el contenedor insertará sus dependencias necesarias.

Autofac también permite el registro de instancias, donde el contenedor es responsable de mantener una referencia a una instancia singleton de un tipo. Por ejemplo, en el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers registra el tipo concreto que se va a usar cuando una instancia de `ProfileViewModel` requiere una instancia de `IOrderService`:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

El método `RegisterType` que se muestra aquí asigna un tipo de interfaz a un tipo concreto. El método `SingleInstance` configura el registro para que todos los objetos dependientes reciban la misma instancia compartida. Por lo tanto, solo existirá una única instancia de `OrderService` en el contenedor, que es compartida por objetos que requieren una inyección de un `IOrderService` a través de un constructor.

El registro de instancias también se puede realizar con el método `RegisterInstance`, que se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

En el método `RegisterInstance` que se muestra aquí se crea una nueva instancia de `OrderMockService` y se registra con el contenedor. Por lo tanto, solo existe una única instancia de `OrderMockService` en el contenedor, que es compartida por objetos que requieren una inyección de un `IOrderService` a través de un constructor.

A continuación, se debe crear el objeto `IContainer`, que se muestra en el ejemplo de código siguiente:

```csharp
_container = builder.Build();
```

Al invocar el método `Build` en la instancia de `ContainerBuilder`, se crea un nuevo contenedor de inserción de dependencias que contiene los registros que se han realizado.

> [!TIP]
> Considere una `IContainer` como inmutable. Aunque Autofac proporciona un método `Update` para actualizar los registros de un contenedor existente, se debe evitar la llamada a este método siempre que sea posible. Hay riesgos para modificar un contenedor una vez compilado, especialmente si se ha usado el contenedor. Para obtener más información, vea [considerar un contenedor como inmutable](https://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) en readthedocs.IO.

<a name="resolution" />

## <a name="resolution"></a>Resolución

Una vez registrado un tipo, se puede resolver o insertar como una dependencia. Cuando se resuelve un tipo y el contenedor necesita crear una nueva instancia, inserta las dependencias en la instancia.

Normalmente, cuando se resuelve un tipo, se produce una de estas tres cosas:

1. Si el tipo no se ha registrado, el contenedor produce una excepción.
1. Si el tipo se ha registrado como singleton, el contenedor devuelve la instancia singleton. Si es la primera vez que se llama al tipo para, el contenedor lo crea si es necesario y mantiene una referencia a él.
1. Si el tipo no se ha registrado como singleton, el contenedor devuelve una nueva instancia de y no mantiene una referencia a ella.

En el ejemplo de código siguiente se muestra cómo se puede resolver el tipo de `RequestProvider` que se registró anteriormente con Autofac:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

En este ejemplo, se pide a Autofac que resuelva el tipo concreto para el tipo de `IRequestProvider`, junto con las dependencias. Normalmente, se llama al método `Resolve` cuando se requiere una instancia de un tipo específico. Para obtener información sobre cómo controlar la duración de los objetos resueltos, vea [administrar la duración de los objetos resueltos](#managing_the_lifetime_of_resolved_objects).

En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers crea instancias de los tipos de modelo de vista y sus dependencias:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

En este ejemplo, se pide a Autofac que resuelva el tipo de modelo de vista para un modelo de vista solicitado, y el contenedor también resolverá las dependencias. Al resolver el tipo de `ProfileViewModel`, la dependencia que se va a resolver es un objeto `IOrderService`. Por lo tanto, Autofac primero crea un objeto `OrderService` y, a continuación, lo pasa al constructor de la clase `ProfileViewModel`. Para obtener más información sobre cómo la aplicación móvil eShopOnContainers construye modelos de vista y los asocia a las vistas, vea [crear automáticamente un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> El registro y la resolución de tipos con un contenedor supone un costo de rendimiento, que los contenedores usan reflexión para crear todos los tipos, especialmente si se reconstruyen las dependencias para cada navegación de página en la aplicación. Si hay muchas dependencias, o estas son muy amplias, el costo de la creación puede aumentar significativamente.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Administrar la duración de los objetos resueltos

Después de registrar un tipo, el comportamiento predeterminado de Autofac es crear una nueva instancia del tipo registrado cada vez que se resuelva el tipo, o cuando el mecanismo de dependencia Inserte instancias en otras clases. En este escenario, el contenedor no contiene una referencia al objeto resuelto. Sin embargo, al registrar una instancia, el comportamiento predeterminado de Autofac es administrar la duración del objeto como singleton. Por lo tanto, la instancia permanece en el ámbito mientras el contenedor está en el ámbito y se desecha cuando el contenedor sale del ámbito y se recolecta como elemento no utilizado, o cuando el código desecha explícitamente el contenedor.

Un ámbito de instancia de Autofac se puede usar para especificar el comportamiento de singleton para un objeto que Autofac crea a partir de un tipo registrado. Los ámbitos de instancia de Autofac administran la duración de los objetos con instancias creadas por el contenedor. El ámbito de la instancia predeterminada para el método `RegisterType` es el ámbito de `InstancePerDependency`. Sin embargo, el ámbito de `SingleInstance` se puede utilizar con el método `RegisterType`, de modo que el contenedor cree o devuelva una instancia singleton de un tipo cuando se llame al método `Resolve`. En el ejemplo de código siguiente se muestra cómo se indica a Autofac que cree una instancia singleton de la clase `NavigationService`:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La primera vez que se resuelve la interfaz `INavigationService`, el contenedor crea un nuevo objeto `NavigationService` y mantiene una referencia a él. En las resoluciones posteriores de la interfaz `INavigationService`, el contenedor devuelve una referencia al objeto `NavigationService` que se creó anteriormente.

> [!NOTE]
> El ámbito SingleInstance desecha los objetos creados cuando se elimina el contenedor.

Autofac incluye ámbitos de instancia adicionales. Para obtener más información, vea ámbito de la [instancia](https://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) en readthedocs.IO.

## <a name="summary"></a>Resumen

La inserción de dependencias permite desacoplar tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

Autofac facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se encuentran normalmente en los contenedores de inserción de dependencias, incluidos los métodos para registrar las asignaciones de tipos y las instancias de objeto, resolver objetos, administrar la duración de los objetos e insertar objetos dependientes en constructores de objetos que resuelve.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
