---
title: Inserción de dependencias
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers usa la inserción de dependencias para desacoplar tipos concretos del código que depende de estos tipos.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3facf6e1e5796d8e17488f3c018cba23e5f99b7f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375361"
---
# <a name="dependency-injection"></a>Inserción de dependencias

> [!NOTE]
> Este libro electrónico se publicó en el muelle de 2017 y no se ha actualizado desde entonces. Hay mucho en el libro que sigue siendo útil, pero parte del material es obsoleto.

Normalmente, se invoca un constructor de clase al crear instancias de un objeto y los valores que necesita el objeto se pasan como argumentos al constructor. Este es un ejemplo de inserción de dependencias y, específicamente, se conoce como *inserción de constructores*. Las dependencias que necesita el objeto se insertan en el constructor.

Al especificar las dependencias como tipos de interfaz, la inserción de dependencias permite desacoplar los tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

También hay otros tipos de inserción de dependencias, como la inserción de un *establecedor de propiedad* y la *inserción de llamadas de método* , pero se suelen considerar. Por lo tanto, este capítulo se centrará únicamente en realizar la inserción de constructores con un contenedor de inserción de dependencias.

## <a name="introduction-to-dependency-injection"></a>Introducción a la inserción de dependencias

La inserción de dependencias es una versión especializada del patrón de inversión de control (IoC), donde el problema que se invierte es el proceso de obtención de la dependencia necesaria. Con la inserción de dependencias, otra clase es responsable de insertar las dependencias en un objeto en tiempo de ejecución. En el ejemplo de código siguiente se muestra cómo `ProfileViewModel` se estructura la clase cuando se usa la inserción de dependencias:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

El `ProfileViewModel` constructor recibe una `IOrderService` instancia de como argumento, insertada por otra clase. La única dependencia de la `ProfileViewModel` clase se encuentra en el tipo de interfaz. Por lo tanto, la `ProfileViewModel` clase no tiene ningún conocimiento de la clase que es responsable de crear instancias del `IOrderService` objeto. La clase que es responsable de crear instancias del `IOrderService` objeto e insertarlo en la `ProfileViewModel` clase se conoce como contenedor de inserción de *dependencias*.

Los contenedores de inserción de dependencias reducen el acoplamiento entre objetos proporcionando una utilidad para crear instancias de la clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que el objeto requiere en él. Si estas dependencias no se han creado todavía, el contenedor crea y resuelve primero sus dependencias.

> [!NOTE]
> La inserción de dependencias también puede implementarse manualmente mediante generadores. Sin embargo, el uso de un contenedor proporciona funcionalidades adicionales, como la administración de la duración y el registro a través del análisis de ensamblados.

El uso de un contenedor de inserción de dependencias tiene varias ventajas:

- Un contenedor elimina la necesidad de que una clase busque sus dependencias y administre su duración.
- Un contenedor permite la asignación de dependencias implementadas sin afectar a la clase.
- Un contenedor facilita la capacidad de prueba, ya que permite simular las dependencias.
- Un contenedor aumenta la facilidad de mantenimiento al permitir que las nuevas clases se agreguen fácilmente a la aplicación.

En el contexto de una Xamarin.Forms aplicación que usa MVVM, se utilizará un contenedor de inserción de dependencias para registrar y resolver modelos de vista, así como para registrar servicios e insertarlos en modelos de vista.

Hay muchos contenedores de inserción de dependencias disponibles, con la aplicación móvil eShopOnContainers con TinyIoC para administrar la creación de instancias del modelo de vista y las clases de servicio en la aplicación. TinyIoC se eligió después de evaluar varios contenedores diferentes y ofrece un rendimiento superior en las plataformas móviles en comparación con la mayoría de los contenedores conocidos. Facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se encuentran normalmente en los contenedores de inserción de dependencias, incluidos los métodos para registrar asignaciones de tipos, resolver objetos, administrar la duración de los objetos e insertar objetos dependientes en constructores de objetos que resuelve. Para obtener más información sobre TinyIoC, vea [TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) en github.com.

En TinyIoC, el `TinyIoCContainer` tipo proporciona el contenedor de inserción de dependencias. En la figura 3-1 se muestran las dependencias al usar este contenedor, que crea una instancia de un `IOrderService` objeto y lo inserta en la `ProfileViewModel` clase.

![Ejemplo de dependencias al usar la inserción de dependencias](dependency-injection-images/dependencyinjection.png)

**Figura 3-1:** Dependencias cuando se usa la inserción de dependencias

En tiempo de ejecución, el contenedor debe saber en qué implementación de la `IOrderService` interfaz debe crear una instancia, antes de poder crear una instancia de un `ProfileViewModel` objeto. Esto implica lo siguiente:

- El contenedor que decide cómo crear una instancia de un objeto que implementa la `IOrderService` interfaz. Esto se conoce como *registro*.
- Contenedor que crea una instancia del objeto que implementa la `IOrderService` interfaz y el `ProfileViewModel` objeto. Esto se conoce como *resolución*.

Finalmente, la aplicación terminará de usar el `ProfileViewModel` objeto y estará disponible para la recolección de elementos no utilizados. En este momento, el recolector de elementos no utilizados debe desechar la `IOrderService` instancia si otras clases no comparten la misma instancia.

> [!TIP]
> Escribir código independiente del contenedor. Intente escribir siempre código independiente del contenedor para desacoplar la aplicación del contenedor de dependencias específico que se está usando.

## <a name="registration"></a>Registro

Antes de que se puedan insertar dependencias en un objeto, los tipos de las dependencias deben registrarse primero con el contenedor. Normalmente, el registro de un tipo implica pasar al contenedor una interfaz y un tipo concreto que implementa la interfaz.

Hay dos formas de registrar tipos y objetos en el contenedor a través del código:

- Registre un tipo o una asignación con el contenedor. Cuando sea necesario, el contenedor creará una instancia del tipo especificado.
- Registra un objeto existente en el contenedor como singleton. Cuando sea necesario, el contenedor devolverá una referencia al objeto existente.

> [!TIP]
> Los contenedores de inserción de dependencias no siempre son adecuados. La inserción de dependencias presenta una complejidad adicional y requisitos que podrían no ser adecuados o útiles para aplicaciones pequeñas. Si una clase no tiene ninguna dependencia, o no es una dependencia para otros tipos, puede que no tenga sentido colocarla en el contenedor. Además, si una clase tiene un único conjunto de dependencias que son integrales para el tipo y nunca cambia, puede que no tenga sentido colocarlo en el contenedor.

El registro de tipos que requieren la inserción de dependencias se debe realizar en un único método en una aplicación, y este método se debe invocar al principio del ciclo de vida de la aplicación para asegurarse de que la aplicación es consciente de las dependencias entre sus clases. En la aplicación móvil eShopOnContainers, se realiza mediante la `ViewModelLocator` clase, que compila el `TinyIoCContainer` objeto y es la única clase de la aplicación que contiene una referencia a ese objeto. En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers declara el `TinyIoCContainer` objeto en la `ViewModelLocator` clase:

```csharp
private static TinyIoCContainer _container;
```

Los tipos se registran en el `ViewModelLocator` constructor. Esto se consigue creando primero una `TinyIoCContainer` instancia de, que se muestra en el ejemplo de código siguiente:

```csharp
_container = new TinyIoCContainer();
```

Después, los tipos se registran con el `TinyIoCContainer` objeto y en el ejemplo de código siguiente se muestra la forma más común de registro de tipo:

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

El `Register` método que se muestra aquí asigna un tipo de interfaz a un tipo concreto. De forma predeterminada, cada registro de interfaz se configura como singleton para que todos los objetos dependientes reciban la misma instancia compartida. Por lo tanto, solo `RequestProvider` existirá una única instancia en el contenedor, que es compartida por objetos que requieren una inyección de `IRequestProvider` a través de un constructor.

Los tipos concretos también se pueden registrar directamente sin una asignación de un tipo de interfaz, como se muestra en el ejemplo de código siguiente:

```csharp
_container.Register<ProfileViewModel>();
```

De forma predeterminada, cada registro de clase concreta se configura como una instancia múltiple para que todos los objetos dependientes reciban una nueva instancia. Por lo tanto, cuando `ProfileViewModel` se resuelva el, se creará una nueva instancia de y el contenedor insertará sus dependencias necesarias.

## <a name="resolution"></a>Solución

Una vez registrado un tipo, se puede resolver o insertar como una dependencia. Cuando se resuelve un tipo y el contenedor necesita crear una nueva instancia, inserta las dependencias en la instancia.

Normalmente, cuando se resuelve un tipo, se produce una de estas tres cosas:

1. Si el tipo no se ha registrado, el contenedor produce una excepción.
1. Si el tipo se ha registrado como singleton, el contenedor devuelve la instancia singleton. Si es la primera vez que se llama al tipo para, el contenedor lo crea si es necesario y mantiene una referencia a él.
1. Si el tipo no se ha registrado como singleton, el contenedor devuelve una nueva instancia de y no mantiene una referencia a ella.

En el ejemplo de código siguiente se muestra cómo `RequestProvider` se puede resolver el tipo que se registró anteriormente con TinyIoC:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

En este ejemplo, se pide a TinyIoC que resuelva el tipo concreto para el `IRequestProvider` tipo, junto con las dependencias. Normalmente, `Resolve` se llama al método cuando se requiere una instancia de un tipo específico. Para obtener información sobre cómo controlar la duración de los objetos resueltos, vea [administrar la duración de los objetos resueltos](#managing-the-lifetime-of-resolved-objects).

En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers crea instancias de los tipos de modelo de vista y sus dependencias:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

En este ejemplo, se pide a TinyIoC que resuelva el tipo de modelo de vista para un modelo de vista solicitado, y el contenedor también resolverá las dependencias. Al resolver el `ProfileViewModel` tipo, las dependencias que se van a resolver son un `ISettingsService` objeto y un `IOrderService` objeto. Dado que los registros de interfaz se usaron al registrar las `SettingsService` `OrderService` clases y, TinyIoC devuelve instancias singleton para las `SettingsService` `OrderService` clases y y, a continuación, las pasa al constructor de la `ProfileViewModel` clase. Para obtener más información sobre cómo la aplicación móvil eShopOnContainers construye modelos de vista y los asocia a las vistas, vea [crear automáticamente un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

> [!NOTE]
> El registro y la resolución de tipos con un contenedor supone un costo de rendimiento, que los contenedores usan reflexión para crear todos los tipos, especialmente si se reconstruyen las dependencias para cada navegación de página en la aplicación. Si hay muchas dependencias, o estas son muy amplias, el costo de la creación puede aumentar significativamente.

## <a name="managing-the-lifetime-of-resolved-objects"></a>Administrar la duración de los objetos resueltos

Después de registrar un tipo mediante un registro de clase concreto, el comportamiento predeterminado de TinyIoC es crear una nueva instancia del tipo registrado cada vez que se resuelva el tipo, o cuando el mecanismo de dependencia Inserte instancias en otras clases. En este escenario, el contenedor no contiene una referencia al objeto resuelto. Sin embargo, al registrar un tipo mediante el registro de la interfaz, el comportamiento predeterminado de TinyIoC es administrar la duración del objeto como singleton. Por lo tanto, la instancia permanece en el ámbito mientras el contenedor está en el ámbito y se desecha cuando el contenedor sale del ámbito y se recolecta como elemento no utilizado, o cuando el código desecha explícitamente el contenedor.

El comportamiento predeterminado de registro de TinyIoC se puede invalidar mediante `AsSingleton` los `AsMultiInstance` métodos de API y FLUENT. Por ejemplo, el `AsSingleton` método se puede usar con el `Register` método, por lo que el contenedor crea o devuelve una instancia singleton de un tipo al llamar al `Resolve` método. En el ejemplo de código siguiente se muestra cómo se indica a TinyIoC que cree una instancia singleton de la `LoginViewModel` clase:

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

La primera vez que `LoginViewModel` se resuelve el tipo, el contenedor crea un nuevo `LoginViewModel` objeto y mantiene una referencia a él. En las resoluciones posteriores de `LoginViewModel` , el contenedor devuelve una referencia al `LoginViewModel` objeto que se ha creado previamente.

> [!NOTE]
> Los tipos que se registran como singletons se eliminan cuando se elimina el contenedor.

## <a name="summary"></a>Resumen

La inserción de dependencias permite desacoplar tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

TinyIoC es un contenedor ligero que ofrece un rendimiento superior en plataformas móviles en comparación con la mayoría de los contenedores conocidos. Facilita la creación de aplicaciones de acoplamiento flexible y proporciona todas las características que se encuentran normalmente en los contenedores de inserción de dependencias, incluidos los métodos para registrar asignaciones de tipos, resolver objetos, administrar la duración de los objetos e insertar objetos dependientes en constructores de objetos que resuelve.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
