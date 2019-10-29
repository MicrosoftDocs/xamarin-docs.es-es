---
title: Temas de integración avanzada
description: En este documento se describen los temas avanzados relacionados con las integraciones de Xamarin Workbooks. Describe el paquete NuGet de Xamarin. Workbook. Integrations y la exposición de API en un libro de Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 159fecbe1385c091c56a6ececb61bf7d020dfc1b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029607"
---
# <a name="advanced-integration-topics"></a>Temas de integración avanzada

Los ensamblados de integración deben hacer referencia al [`Xamarin.Workbooks.Integrations` NuGet][nuget]. Consulte nuestra [documentación de inicio rápido](~/tools/workbooks/sdk/index.md) para obtener más información sobre cómo empezar a usar el paquete NuGet.

También se admiten las integraciones de cliente y se inician colocando archivos JavaScript o CSS con el mismo nombre que el ensamblado de integración del agente en el mismo directorio. Por ejemplo, si el ensamblado de integración del agente (que hace referencia a NuGet) se denomina `SampleExternalIntegration.dll`, `SampleExternalIntegration.js` y `SampleExternalIntegration.css` se integrarán también en el cliente, si existen. Las integraciones de cliente son opcionales.

La propia integración externa se puede empaquetar como un NuGet, se proporciona y se hace referencia a ella directamente dentro de la aplicación que hospeda el agente, o simplemente se coloca junto a un `.workbook` archivo que desea consumirlo.

Las integraciones externas (agente y cliente) en los paquetes NuGet se cargarán automáticamente cuando se haga referencia al paquete, según la documentación de inicio rápido, mientras que los ensamblados de integración que se envíen junto al libro deberán hacer referencia a él de la manera siguiente:

```csharp
#r "SampleExternalIntegration.dll"
```

Al hacer referencia a una integración de este modo, el cliente no lo cargará de inmediato&mdash;deberá llamar a algún código desde la integración para que se cargue. Abordaremos este error en el futuro.

La `Xamarin.Interactive` PCL proporciona algunas API de integración importantes. Cada integración debe proporcionar al menos un punto de entrada de integración:

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

En este punto, una vez que se hace referencia al ensamblado de integración, el cliente cargará implícitamente los archivos de integración de JavaScript y CSS.

## <a name="apis"></a>API

Como con cualquier ensamblado al que haga referencia un libro o una sesión de inspección en directo, cualquiera de sus API públicas es accesible para la sesión. Por lo tanto, es importante tener una superficie de API segura y sensata para que los usuarios exploren.

El ensamblado de integración es realmente un puente entre una aplicación o un SDK de interés y la sesión. Puede proporcionar nuevas API que tengan sentido concretamente en el contexto de un libro o una sesión de inspección en directo, o bien no proporcionar API públicas y simplemente realizar tareas "en segundo plano" como la producción de [representaciones](~/tools/workbooks/sdk/representations.md)de objetos.

> [!NOTE]
> Las API que deben ser públicas, pero que no deben aparecer a través de IntelliSense, se pueden marcar con el atributo de `[EditorBrowsable (EditorBrowsableState.Never)]` habitual.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
