---
title: Representaciones en Xamarin Workbooks
description: En este documento se describe la canalización de representación Xamarin Workbooks, que habilita la representación de resultados enriquecidos para cualquier código que devuelva un valor.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: dde4e6b9c4903ccb0f23d8df82f39ff68030850e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292824"
---
# <a name="representations-in-xamarin-workbooks"></a>Representaciones en Xamarin Workbooks

## <a name="representations"></a>Representan

Dentro de una sesión de libro o inspector, el código que se ejecuta y produce un resultado (por ejemplo, un método que devuelve un valor o el resultado de una expresión) se procesa a través de la canalización de representación del agente. Todos los objetos, a excepción de los tipos primitivos, como los enteros, se reflejarán para generar gráficos de miembros interactivos y pasarán por un proceso para proporcionar representaciones alternativas que el cliente pueda representar de forma más enriquecida. Los objetos de cualquier tamaño y profundidad se admiten de forma segura (incluidos los ciclos y los enumerables infinitos) debido a la reflexión diferida e interactiva y la comunicación remota.

Xamarin Workbooks proporciona algunos tipos comunes a todos los agentes y clientes que permiten una representación enriquecida de los resultados. `Color`es un ejemplo de este tipo, donde, por ejemplo, en iOS, el agente es responsable de `CGColor` la `UIColor` conversión de objetos `Xamarin.Interactive.Representations.Color` o en un objeto.

Además de las representaciones comunes, el SDK de integración proporciona las API para serializar representaciones personalizadas en el agente y representar representaciones en el cliente.

## <a name="external-representations"></a>Representaciones externas

`Xamarin.Interactive.IAgent.RepresentationManager`proporciona la capacidad de registrar un `RepresentationProvider`, que una integración debe implementar para convertir de un objeto arbitrario a un formato independiente que se va a representar. Estos formularios independientes deben implementar la `ISerializableObject` interfaz.

Al implementar `ISerializableObject` la interfaz, se agrega un método Serialize que controla precisamente cómo se serializan los objetos. El `Serialize` método espera que un desarrollador especifique exactamente qué propiedades se van a serializar y cuál será el nombre final. Al mirar el `Person` objeto en nuestro [`KitchenSink` ejemplo] [ejemplo], podemos ver cómo funciona esto:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Si deseamos proporcionar un superconjunto o subconjunto de propiedades del objeto original, podemos hacerlo con `Serialize`. Por ejemplo, podríamos hacer algo parecido a esto para proporcionar una `Age` propiedad calculada previamente en: `Person`

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> Las API que `ISerializableObject` generan objetos directamente no necesitan ser controladas `RepresentationProvider`por. Si el objeto que desea mostrar **no** es un `ISerializableObject`, querrá controlarlo en su. `RepresentationProvider`

### <a name="rendering-a-representation"></a>Representar una representación

Los representadores se implementan en JavaScript y tendrán acceso a una versión de JavaScript del objeto `ISerializableObject`representado a través de. La copia de JavaScript también tendrá una `$type` propiedad de cadena que indica el nombre de tipo .net.

Se recomienda usar TypeScript para el código de integración de cliente, que, por supuesto, se compila en script de vainilla de JavaScript. En cualquier caso, el SDK proporciona [tipos][typings] a los que se puede hacer referencia directamente a TypeScript o simplemente se hace referencia a ellos manualmente si se prefiere escribir JavaScript de vainilla.

El punto de integración principal para la representación `xamarin.interactive.RendererRegistry`es:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Aquí implementa la `Renderer`interfaz. `PersonRenderer` Vea los [tipos][typings] para obtener más detalles.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
