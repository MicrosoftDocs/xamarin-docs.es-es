---
title: Cargar XAML en el tiempo de ejecución en Xamarin. Forms
description: XAML se puede cargar y analizar en tiempo de ejecución con los métodos de extensión LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: e253d2ba949a94637d7773fdc50b479679fd3f41
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657250"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Cargar XAML en el tiempo de ejecución en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

El [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) espacio de nombres [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) incluye dos métodos de extensión que se pueden usar para cargar y analizar XAML en tiempo de ejecución.

## <a name="background"></a>Fondo

Cuando se construye una clase XAML de Xamarin. Forms [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) , se llama indirectamente al método. Esto se debe a que el archivo de código subyacente de una clase XAML `InitializeComponent` llama al método desde su constructor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Cuando Visual Studio compila un proyecto que contiene un archivo XAML, analiza el archivo XAML para generar un C# archivo de código (por ejemplo, **mainpage.Xaml.g.CS**) que contiene la `InitializeComponent` definición del método:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

El `InitializeComponent` [método`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) llama al método para extraer el archivo XAML (o su binario compilado) de la biblioteca de .net Standard. Después de la extracción, inicializa todos los objetos definidos en el archivo XAML, los conecta todos juntos en las relaciones de elementos primarios y secundarios, asocia los controladores de eventos definidos en el código a los eventos establecidos en el archivo XAML y establece el árbol resultante de objetos como el contenido de la del.

## <a name="loading-xaml-at-runtime"></a>Cargar XAML en tiempo de ejecución

Los [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos son `public`y, por lo tanto, se puede llamar desde aplicaciones de Xamarin. Forms para cargar y analizar XAML en tiempo de ejecución. Esto permite escenarios como, por ejemplo, una aplicación que descarga XAML de un servicio Web, crea la vista necesaria desde el código XAML y la muestra en la aplicación.

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

En este ejemplo, se [`Button`](xref:Xamarin.Forms.Button) crea una instancia de, con [`Text`](xref:Xamarin.Forms.Button.Text) `string`el valor de propiedad que se establece desde el código XAML definido en. A continuación, [`StackLayout`](xref:Xamarin.Forms.StackLayout) `Button` se agrega a un que se ha definido en el XAML de la página.

> [!NOTE]
> Los [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión permiten especificar un argumento de tipo genérico. Sin embargo, rara vez es necesario especificar el argumento de tipo, ya que se deduce del tipo de la instancia en la que se está trabajando.

El [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método se puede utilizar para aumentar cualquier código XAML, con el siguiente ejemplo de la inflación de [`ContentPage`](xref:Xamarin.Forms.ContentPage) un y, a continuación, desplazarse hasta él:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Obtener acceso a elementos

Cargar XAML en tiempo de ejecución [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) con el método no permite el acceso fuertemente tipado a los elementos XAML que tienen nombres de objeto en tiempo `x:Name`de ejecución especificados (mediante). Sin embargo, estos elementos XAML se pueden recuperar utilizando [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) el método y, a continuación, se puede obtener acceso a ellos según sea necesario:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

En este ejemplo, el XAML de un [`ContentPage`](xref:Xamarin.Forms.ContentPage) se infla. Este código XAML incluye [`Label`](xref:Xamarin.Forms.Label) un `monkeyName`denominado, que se recupera mediante [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) el método, antes de [`Text`](xref:Xamarin.Forms.Label.Text) que se establezca la propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [LoadRuntimeXAML (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
