---
title: Cargar XAML en el tiempo de ejecución en Xamarin. Forms
description: XAML se puede cargar y analizar en tiempo de ejecución con los métodos de extensión LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045068"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Cargar XAML en el tiempo de ejecución en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

El espacio de nombres [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) incluye dos métodos de extensión [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) que se pueden usar para cargar y analizar XAML en tiempo de ejecución.

## <a name="background"></a>Información previa

Cuando se construye una clase XAML de Xamarin. Forms, se llama indirectamente al método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) . Esto se debe a que el archivo de código subyacente de una clase XAML llama al método `InitializeComponent` desde su constructor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Cuando Visual Studio compila un proyecto que contiene un archivo XAML, analiza el archivo XAML para generar un C# archivo de código (por ejemplo, **mainpage.Xaml.g.CS**) que contiene la definición del método `InitializeComponent`:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

El método `InitializeComponent` llama al método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) para extraer el archivo XAML (o su binario compilado) de la biblioteca de .net Standard. Después de la extracción, inicializa todos los objetos definidos en el archivo XAML, los conecta todos juntos en las relaciones de elementos primarios y secundarios, asocia los controladores de eventos definidos en el código a los eventos establecidos en el archivo XAML y establece el árbol resultante de objetos como el contenido de la del.

## <a name="loading-xaml-at-runtime"></a>Cargar XAML en tiempo de ejecución

Los métodos de [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) son `public`y, por tanto, se puede llamar a desde aplicaciones de Xamarin. Forms para cargar y analizar XAML en tiempo de ejecución. Esto permite escenarios como, por ejemplo, una aplicación que descarga XAML de un servicio Web, crea la vista necesaria desde el código XAML y la muestra en la aplicación.

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

En este ejemplo, se crea una instancia de [`Button`](xref:Xamarin.Forms.Button) , con su valor de propiedad [`Text`](xref:Xamarin.Forms.Button.Text) establecido en el código XAML definido en el `string`. A continuación, se agrega el `Button` a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) que se ha definido en el XAML de la página.

> [!NOTE]
> Los métodos de extensión [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) permiten especificar un argumento de tipo genérico. Sin embargo, rara vez es necesario especificar el argumento de tipo, ya que se deduce del tipo de la instancia en la que se está trabajando.

El método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) se puede usar para inflar cualquier código XAML, con el siguiente ejemplo de inflar una [`ContentPage`](xref:Xamarin.Forms.ContentPage) y, a continuación, navegar hasta ella:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Obtener acceso a elementos

Cargar XAML en tiempo de ejecución con el método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) no permite el acceso fuertemente tipado a los elementos XAML que tienen nombres de objeto en tiempo de ejecución especificados (mediante `x:Name`). Sin embargo, estos elementos XAML se pueden recuperar mediante el método [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) y, a continuación, se puede acceder a ellos según sea necesario:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

En este ejemplo, el XAML de un [`ContentPage`](xref:Xamarin.Forms.ContentPage) se infla. Este código XAML incluye una [`Label`](xref:Xamarin.Forms.Label) denominada `monkeyName`, que se recupera mediante el método [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) , antes de que se establezca su propiedad [`Text`](xref:Xamarin.Forms.Label.Text) .

## <a name="related-links"></a>Vínculos relacionados

- [LoadRuntimeXAML (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
