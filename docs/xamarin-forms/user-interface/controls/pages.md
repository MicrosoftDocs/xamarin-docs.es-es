---
title: Páginas de Xamarin. Forms
description: Las páginas de Xamarin. Forms representan pantallas de aplicaciones móviles multiplataforma. En este artículo se enumeran las páginas que se incluyen en Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 278256f75d94fe47510ae4d15f12a3ff3a6a2b19
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976448"
---
# <a name="xamarinforms-pages"></a>Páginas de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las páginas de Xamarin. Forms representan pantallas de aplicaciones móviles multiplataforma._

Todos los tipos de página que se describen a continuación derivan de la clase de [`Page`](xref:Xamarin.Forms.Page) de Xamarin. Forms. Estos elementos visuales ocupan toda la pantalla o la mayoría de ellas. Un objeto `Page` representa un `ViewController` en iOS y un `Page` en el Plataforma universal de Windows. En Android, cada página ocupa la pantalla como un `Activity`, pero las páginas de Xamarin. Forms *no* son objetos `Activity`.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Páginas

Xamarin. Forms admite los siguientes tipos de páginas:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) es el tipo de página más sencillo y común. Establezca la propiedad [`Content`](xref:Xamarin.Forms.ContentPage.Content) en un solo [`View`](views.md) objeto, que suele ser una [`Layout`](layouts.md) como [`StackLayout`](layouts.md#stackLayout), [`Grid`](layouts.md#grid)o [1](layouts.md#scrollView).<br /><br />[Documentación de la API](xref:Xamarin.Forms.ContentPage) | [![Ejemplo de ContentPage](pages-images/ContentPage.png "Ejemplo de ContentPage")](pages-images/ContentPage-Large.png#lightbox "Ejemplo de ContentPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) administra dos paneles de información. Establezca la propiedad [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) en una página que normalmente muestra una lista o un menú. Establezca la propiedad [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) en una página que muestre un elemento seleccionado en la página maestra. La propiedad [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) rige si está visible la página maestra o de detalle.<br /><br />[Documentación de API](xref:Xamarin.Forms.MasterDetailPage)  / [Guía](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Ejemplo de MasterDetailPage](pages-images/MasterDetailPage.png "Ejemplo de MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Ejemplo de MasterDetailPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| El [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) administra la navegación entre otras páginas mediante una arquitectura basada en la pila. Al usar la navegación de páginas en la aplicación, se debe pasar una instancia de la Página principal al constructor de un objeto `NavigationPage`.<br /><br />[Documentación de API](xref:Xamarin.Forms.NavigationPage)  / [Guía](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)y [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Ejemplo de NavigationPage](pages-images/NavigationPage.png "Ejemplo de NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Ejemplo de NavigationPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [código = Behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva de la clase [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) abstracta y permite la navegación entre las páginas secundarias mediante tabulaciones. Establezca la propiedad [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) en una colección de páginas o establezca la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) en una colección de objetos de datos y la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que describa cómo se va a representar visualmente cada objeto.<br /><br />[Documentación de API](xref:Xamarin.Forms.TabbedPage)  / [Guía](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Ejemplo de TabbedPage](pages-images/TabbedPage.png "Ejemplo de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Ejemplo de TabbedPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) se deriva de la clase [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) abstracta y permite la navegación entre las páginas secundarias a través del dedo deslizante. Establezca la propiedad [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) en una colección de objetos de [`ContentPage`](#contentPage) o establezca la propiedad [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) en una colección de objetos de datos y la propiedad [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) en una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que describa cómo cada objeto va a ser visualmente. representado.<br /><br />[Documentación de API](xref:Xamarin.Forms.CarouselPage)  / [Guía](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Ejemplo de CarouselPage](pages-images/CarouselPage.png "Ejemplo de CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Ejemplo de CarouselPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) muestra el contenido de pantalla completa con una plantilla de control y es la clase base de [`ContentPage`](#contentPage).<br /><br />[Guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentación de API](xref:Xamarin.Forms.TemplatedPage) | [![Ejemplo de TemplatedPage](pages-images/TemplatedPage.png "Ejemplo de TemplatedPage")](pages-images/TemplatedPage.png "Ejemplo de TemplatedPage") |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
