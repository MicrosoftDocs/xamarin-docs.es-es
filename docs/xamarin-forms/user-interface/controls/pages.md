---
title: Páginas de Xamarin.Forms
description: Las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma. En este artículo se enumera las páginas que se incluyen en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 77ab879205f0260291f04f94111ea95e0d4e556f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657495"
---
# <a name="xamarinforms-pages"></a>Páginas de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma._

Todos los tipos de página que se describen a continuación se derivan de Xamarin.Forms [ `Page` ](xref:Xamarin.Forms.Page) clase. Estos elementos visuales ocupan todos o la mayoría de la pantalla. Un `Page` objeto representa un `ViewController` en iOS y un `Page` en la plataforma Universal de Windows. En Android, cada página ocupa la pantalla como una `Activity`, pero las páginas de Xamarin.Forms son *no* `Activity` objetos.

[Tipos de páginas de Xamarin. Forms ![(pages-images/pages-sml.png " ")]] (pages-images/pages.png#lightbox "Tipos de páginas de Xamarin. Forms")

## <a name="pages"></a>Páginas

Xamarin.Forms es compatible con los siguientes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) es el tipo más sencillo y común de página. Establecer el [ `Content` ](xref:Xamarin.Forms.ContentPage.Content) propiedad a una sola [ `View` ](views.md) objeto, que es más a menudo un [ `Layout` ](layouts.md) como [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), o [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentación de la API](xref:Xamarin.Forms.ContentPage) | [![Ejemplo de ContentPage](pages-images/ContentPage.png "ContentPage ejemplo")](pages-images/ContentPage-Large.png#lightbox "ContentPage de ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) administra dos paneles de información. Establecer el [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propiedad a una página con carácter general que muestra una lista o un menú. Establecer el [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedad a una página que muestra un elemento seleccionado de la página maestra. El [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propiedad controla si está visible la página maestra o de detalle.<br /><br />[Documentación de API](xref:Xamarin.Forms.MasterDetailPage) / [guía](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Ejemplo de MasterDetailPage](pages-images/MasterDetailPage.png "MasterDetailPage ejemplo")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| El [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) administra la navegación entre otras páginas con una arquitectura basada en la pila. Cuando se utiliza la navegación de páginas en la aplicación, una instancia de la página principal debe pasarse al constructor de un `NavigationPage` objeto.<br /><br />[Documentación de API](xref:Xamarin.Forms.NavigationPage) / [guía](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [1 de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata), y [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Ejemplo de NavigationPage](pages-images/NavigationPage.png "NavigationPage ejemplo")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [código = detrás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se deriva de la clase abstracta [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) clase y permite la navegación entre secundarios páginas con pestañas. Establecer el [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propiedad a una colección de páginas, o un conjunto el [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad a una colección de objetos de datos y el [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que describe cómo cada objeto se para representar visualmente.<br /><br />[Documentación de API](xref:Xamarin.Forms.TabbedPage) / [guía](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [1 de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Ejemplo de TabbedPage](pages-images/TabbedPage.png "TabbedPage ejemplo")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) se deriva de la clase abstracta [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) clase y permite la navegación entre secundarios páginas a través de deslizar el dedo. Establecer el [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propiedad a una colección de [ `ContentPage` ](#contentPage) objetos o conjunto el [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad a una colección de objetos de datos y el [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que describe cómo cada objeto se para representar visualmente.<br /><br />[Documentación de API](xref:Xamarin.Forms.CarouselPage) / [guía](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [1 de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Ejemplo de CarouselPage](pages-images/CarouselPage.png "CarouselPage ejemplo")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Muestra el contenido de pantalla completa con una plantilla de control, y es la clase base para [ `ContentPage` ](#contentPage).<br /><br />[Documentación de API](xref:Xamarin.Forms.TemplatedPage) / [guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Ejemplo de TemplatedPage](pages-images/TemplatedPage.png "TemplatedPage ejemplo")](pages-images/TemplatedPage.png "TemplatedPage ejemplo") |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
