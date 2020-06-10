---
title: " Xamarin.Forms pages" Description: " Xamarin.Forms las páginas representan pantallas de aplicaciones móviles multiplataforma. En este artículo se enumeran las páginas que se incluyen en Xamarin.Forms ".
MS. Prod: Xamarin ms. AssetID: 9C8C710F-E312-420B-9324-A7A20CEDB7EC ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/12/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las páginas de Xamarin. Forms representan pantallas de aplicaciones móviles multiplataforma._

Todos los tipos de página que se describen a continuación derivan de la Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) clase. Estos elementos visuales ocupan toda la pantalla o la mayoría de ellas. Un `Page` objeto representa un `ViewController` en iOS y un `Page` en el plataforma universal de Windows. En Android, cada página ocupa la pantalla como `Activity` , pero Xamarin.Forms las páginas no son *not* `Activity` objetos.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Páginas

Xamarin.Formsadmite los siguientes tipos de páginas:

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)es el tipo de página más sencillo y más común. Establezca la [`Content`](xref:Xamarin.Forms.ContentPage.Content) propiedad en un solo [`View`](views.md) objeto, que suele ser, como [`Layout`](layouts.md) [`StackLayout`](layouts.md#stacklayout) , [`Grid`](layouts.md#grid) o [`ScrollView`](layouts.md#scrollview) .<br /><br />[Documentación de la API](xref:Xamarin.Forms.ContentPage) | [![Ejemplo de ContentPage](pages-images/ContentPage.png "Ejemplo de ContentPage")](pages-images/ContentPage-Large.png#lightbox "Ejemplo de ContentPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) administra dos paneles de información. Establezca la [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propiedad en una página que normalmente muestra una lista o un menú. Establezca la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedad en una página que muestre un elemento seleccionado en la página maestra. La [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propiedad rige si está visible la página maestra o de detalle.<br /><br />[Documentación](xref:Xamarin.Forms.MasterDetailPage)  /  de API [Guía](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) de | [![Ejemplo de MasterDetailPage](pages-images/MasterDetailPage.png "Ejemplo de MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Ejemplo de MasterDetailPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)Administra la navegación entre otras páginas mediante una arquitectura basada en la pila. Cuando se usa la navegación de páginas en la aplicación, se debe pasar una instancia de la Página principal al constructor de un `NavigationPage` objeto.<br /><br />[Documentación](xref:Xamarin.Forms.NavigationPage)  /  de API [Guía](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  de [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)y [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Ejemplo de NavigationPage](pages-images/NavigationPage.png "Ejemplo de NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Ejemplo de NavigationPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [código = Behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)deriva de la clase abstracta [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) y permite la navegación entre las páginas secundarias mediante tabulaciones. Establezca la [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propiedad en una colección de páginas o establezca la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad en una colección de objetos de datos y la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propiedad en, que [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) describe cómo se representará visualmente cada objeto.<br /><br />[Documentación](xref:Xamarin.Forms.TabbedPage)  /  de API [Guía](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  de [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Ejemplo de TabbedPage](pages-images/TabbedPage.png "Ejemplo de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Ejemplo de TabbedPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)deriva de la clase abstracta [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) y permite la navegación entre las páginas secundarias a través del dedo deslizante. Establezca la [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propiedad en una colección de [`ContentPage`](#contentpage) objetos o establezca la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad en una colección de objetos de datos y la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propiedad en, que [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) describe cómo se representará visualmente cada objeto.<br /><br />[Documentación](xref:Xamarin.Forms.CarouselPage)  /  de API [Guía](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  de [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Ejemplo de CarouselPage](pages-images/CarouselPage.png "Ejemplo de CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Ejemplo de CarouselPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)muestra el contenido de pantalla completa con una plantilla de control y es la clase base para [`ContentPage`](#contentpage) .<br /><br />[Documentación](xref:Xamarin.Forms.TemplatedPage)  /  de API [Guía](~/xamarin-forms/app-fundamentals/templates/control-template.md) de | [![Ejemplo de TemplatedPage](pages-images/TemplatedPage.png "Ejemplo de TemplatedPage")](pages-images/TemplatedPage.png "Ejemplo de TemplatedPage") |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsEjemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
