---
title: Cargar XAML en tiempo de ejecución enXamarin.Forms
description: XAML se puede cargar y analizar en tiempo de ejecución con los métodos de extensión LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d750aa84a48ad4c8015a619d819134cefc63c3d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139378"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Cargar XAML en tiempo de ejecución enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

El [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) espacio de nombres incluye dos [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión que se pueden usar para cargar y analizar XAML en tiempo de ejecución.

## <a name="background"></a>Fondo

Cuando Xamarin.Forms se construye una clase XAML, [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) se llama indirectamente al método. Esto se debe a que el archivo de código subyacente de una clase XAML llama al `InitializeComponent` método desde su constructor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Cuando Visual Studio compila un proyecto que contiene un archivo XAML, analiza el archivo XAML para generar un archivo de código de C# (por ejemplo, **mainpage.Xaml.g.CS**) que contiene la definición del `InitializeComponent` método:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

El `InitializeComponent` método llama al [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método para extraer el archivo XAML (o su binario compilado) de la biblioteca de .net Standard. Después de la extracción, inicializa todos los objetos definidos en el archivo XAML, los conecta todos juntos en las relaciones de elementos primarios y secundarios, asocia los controladores de eventos definidos en el código a los eventos establecidos en el archivo XAML y establece el árbol resultante de objetos como el contenido de la página.

## <a name="loading-xaml-at-runtime"></a>Cargar XAML en tiempo de ejecución

Los [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos son `public` y, por tanto, se puede llamar desde Xamarin.Forms las aplicaciones para cargar y analizar XAML en tiempo de ejecución. Esto permite escenarios como, por ejemplo, una aplicación que descarga XAML de un servicio Web, crea la vista necesaria desde el código XAML y la muestra en la aplicación.

> [!WARNING]
> La carga de XAML en tiempo de ejecución tiene un costo de rendimiento considerable y, por lo general, debe evitarse.

En el ejemplo de código siguiente se muestra un uso sencillo:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

En este ejemplo, [`Button`](xref:Xamarin.Forms.Button) se crea una instancia de, con [`Text`](xref:Xamarin.Forms.Button.Text) el valor de propiedad establecido en el código XAML definido en `string` . `Button`A continuación, se agrega a un que se ha [`StackLayout`](xref:Xamarin.Forms.StackLayout) definido en el XAML de la página.

> [!NOTE]
> Los [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión permiten especificar un argumento de tipo genérico. Sin embargo, rara vez es necesario especificar el argumento de tipo, ya que se deduce del tipo de la instancia en la que se está trabajando.

El [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método se puede utilizar para aumentar cualquier código XAML, con el siguiente ejemplo de la inflación de un [`ContentPage`](xref:Xamarin.Forms.ContentPage) y, a continuación, desplazarse hasta él:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Obtener acceso a elementos

Cargar XAML en tiempo de ejecución con el [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método no permite el acceso fuertemente tipado a los elementos XAML que tienen nombres de objeto en tiempo de ejecución especificados (mediante `x:Name` ). Sin embargo, estos elementos XAML se pueden recuperar utilizando el [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método y, a continuación, se puede obtener acceso a ellos según sea necesario:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

En este ejemplo, el XAML de un [`ContentPage`](xref:Xamarin.Forms.ContentPage) se infla. Este código XAML incluye un [`Label`](xref:Xamarin.Forms.Label) denominado `monkeyName` , que se recupera mediante el [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método, antes de que [`Text`](xref:Xamarin.Forms.Label.Text) se establezca su propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [LoadRuntimeXAML (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
