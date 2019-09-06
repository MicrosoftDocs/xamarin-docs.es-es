---
title: Introducción con el SDK de Xamarin Workbooks
description: En este documento se describe cómo empezar a trabajar con el SDK de Xamarin Workbooks, que se puede usar para desarrollar integraciones para Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 8e3dc65f9f615ff893f3526d53d99da25045c794
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283964"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introducción con el SDK de Xamarin Workbooks

En este documento se proporciona una guía rápida para empezar a desarrollar integraciones para Xamarin Workbooks. Gran parte de esto funcionará con el Xamarin Workbooks estable, pero la **carga de integraciones a través de paquetes NuGet solo se admite en los libros 1,3**, en el canal alfa, en el momento de la escritura.

## <a name="general-overview"></a>Información general

Xamarin Workbooks integraciones son bibliotecas pequeñas que usan el [ `Xamarin.Workbooks.Integrations` ][nuget] SDK de NuGet para integrarse con los agentes Xamarin Workbooks y inspector para proporcionar experiencias mejoradas.

Hay 3 pasos principales para empezar a desarrollar una integración, aquí se describen.

## <a name="creating-the-integration-project"></a>Crear el proyecto de integración

Las bibliotecas de integración se desarrollan mejor como bibliotecas multiplataforma. Dado que desea proporcionar la mejor integración en todos los agentes disponibles, más allá y en el futuro, querrá elegir un conjunto de bibliotecas ampliamente admitido. Se recomienda usar la plantilla "biblioteca portable" para obtener la compatibilidad más amplia:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Visual Studio para Mac de plantilla de biblioteca portable](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Plantilla de biblioteca portable de Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

En Visual Studio, querrá asegurarse de que selecciona las siguientes plataformas de destino para la biblioteca portable:

[![Plataformas de biblioteca portable Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Una vez creado el proyecto de biblioteca, agregue una referencia a `Xamarin.Workbooks.Integration` nuestra biblioteca de Nuget a través del administrador de paquetes Nuget.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Visual Studio para Mac NuGet](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet en Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Querrá eliminar la clase vacía que se crea para usted como parte del proyecto; no la necesitará para esto. Una vez realizados estos pasos, está listo para empezar a crear la integración.

## <a name="building-an-integration"></a>Creación de una integración

Crearemos una integración simple. Realmente nos encanta el color verde, por lo que agregaremos el color verde como una representación de cada objeto. En primer lugar, cree una nueva `SampleIntegration`clase denominada y haga que implemente nuestra `IAgentIntegration` interfaz:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Lo que queremos hacer es agregar una [representación](~/tools/workbooks/sdk/representations.md) para cada objeto que sea un color verde. Haremos esto mediante un proveedor de representación. Los proveedores heredan `RepresentationProvider` de la clase, por lo que solo necesitamos invalidar: `ProvideRepresentations`

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Devolvemos un `Color`, un tipo de representación pregenerada en nuestro SDK.
Observará que el tipo de valor devuelto aquí `IEnumerable<object>`es un &mdash;proveedor de representación puede devolver muchas representaciones para un objeto. Se llama a todos los proveedores de representación para cada objeto, por lo que es importante no realizar ninguna suposición sobre los objetos que se le pasan.

El último paso es registrar realmente nuestro proveedor con el agente e indicar a los libros dónde encontrar el tipo de integración. Para registrar el proveedor, agregue este código al `IntegrateWith` método de la `SampleIntegration` clase que hemos creado anteriormente:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

La configuración del tipo de integración se realiza a través de un atributo de todo el ensamblado. Puede colocarlo en el AssemblyInfo.cs o en la misma clase que el tipo de integración para mayor comodidad:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante el desarrollo, es posible que le resulte más `AddProvider` cómodo usar sobrecargas en `RepresentationManager` que le permitan registrar una devolución de llamada simple para proporcionar representaciones dentro de un libro y, a `RepresentationProvider` continuación, trasladar el código a la implementación una vez. ya ha terminado. Un ejemplo de representación [`OxyPlot`][oxyplot] `PlotModel` de podría ser similar al siguiente:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Estas API proporcionan una forma rápida de empezar a trabajar, pero no recomendamos que se envíe una integración completa solo con ellas&mdash;, ya que proporcionan un control muy bajo de cómo el cliente procesa los tipos.

Con la representación registrada, la integración está lista para su envío.

## <a name="shipping-your-integration"></a>Envío de la integración

Para enviar la integración, debe agregarla a un paquete NuGet.
Puede enviarlo con el NuGet de la biblioteca existente o, si está creando un nuevo paquete, puede usar este archivo. nuspec de plantilla como punto de partida.
Tendrá que rellenar las secciones relevantes para su integración. La parte más importante es que todos los archivos de la integración deben estar en un `xamarin.interactive` directorio en la raíz del paquete. Esto nos permite encontrar fácilmente todos los archivos relevantes para la integración, independientemente de si usa un paquete existente o crea uno nuevo.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

Una vez que haya creado el archivo. nuspec, puede empaquetar el NuGet de la manera siguiente:

```csharp
nuget pack MyIntegration.nuspec
```

y, a continuación, publíquelo en [NuGet][nugetorg]. Una vez allí, podrá hacer referencia a él desde cualquier libro y verlo en acción. En la captura de pantalla siguiente, hemos empaquetado la integración de ejemplo que hemos creado en este documento e instalado el paquete de NuGet en un libro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Libro con integración](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Libro con integración](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Tenga en cuenta que no ve `#r` ninguna directiva ni nada para inicializar la integración: los libros se han ocupado de todo en segundo plano.

## <a name="next-steps"></a>Pasos siguientes

Consulte nuestra otra documentación para obtener más información sobre las piezas móviles que componen el SDK, así como nuestras [integraciones de ejemplo](~/tools/workbooks/samples/index.md) para las acciones adicionales que puede realizar desde la integración, como proporcionar JavaScript personalizado que se ejecuta en el cliente de los libros.

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
