---
title: Diseños de Xamarin. Forms
description: Los diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras visuales. En este artículo se enumera los diseños que se incluye en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 4747ce6555a6440c687dc3d239d75307f68683ca
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724487"
---
# <a name="xamarinforms-layouts"></a>Diseños de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Los diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras visuales._

El [ `Layout` ](xref:Xamarin.Forms.Layout) y [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) clases de Xamarin.Forms son subtipos especializadas de las vistas que actúan como contenedores para otros diseños y vistas. El `Layout` propia clase se deriva de [ `View` ](views.md). Un `Layout` derivado normalmente contiene lógica para establecer la posición y tamaño de los elementos secundarios en las aplicaciones de Xamarin.Forms.

[![Tipos de diseño de Xamarin. Forms](layouts-images/layouts-sml.png "Tipos de diseño de Xamarin. Forms")](layouts-images/layouts.png#lightbox "Tipos de diseño de Xamarin. Forms")

Las clases que derivan de `Layout` pueden dividirse en dos categorías:

## <a name="layouts-with-single-content"></a>Diseños con contenido único

Estas clases derivan de [ `Layout` ](xref:Xamarin.Forms.Layout), que define [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) y [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) propiedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contiene un único elemento secundario que se establece con el [ `Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad. El `Content` propiedad puede establecerse en cualquier `View` derivados, incluidos otros `Layout` derivados. `ContentView` se usa principalmente como un elemento estructural y actúa como clase base para [ `Frame` ](#frame).<br /><br />[Documentación de API](xref:Xamarin.Forms.ContentView) / [guía](~/xamarin-forms/user-interface/layouts/contentview.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![Ejemplo de ContentView](layouts-images/ContentView.png "Ejemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Ejemplo de ContentView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Trama

|     |     |
| --- | --- |
| La clase [`Frame`](xref:Xamarin.Forms.Frame) se deriva de [`ContentView`](#contentView) y muestra un borde, o marco, alrededor de su elemento secundario. La clase `Frame` tiene un valor predeterminado de [`Padding`](xref:Xamarin.Forms.Layout.Padding) de 20 y también define las propiedades [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)y [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentación de API](xref:Xamarin.Forms.Frame) / [guía](~/xamarin-forms/user-interface/layouts/frame.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Ejemplo de marco](layouts-images/Frame.png "Ejemplo de marco")](layouts-images/Frame-Large.png#lightbox "Ejemplo de marco")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) es capaz de desplazar su contenido. Establecer el [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) propiedad a una vista o diseño demasiado grande para caber en la pantalla. (El contenido de una `ScrollView` suele ser un [`StackLayout`](#stackLayout)). Establezca la propiedad [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) para indicar si el desplazamiento debe ser vertical, horizontal o ambos.<br /><br />[Documentación de API](xref:Xamarin.Forms.ScrollView) / [guía](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de ScrollView](layouts-images/ScrollView.png "Ejemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Ejemplo de ScrollView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Muestra el contenido con una plantilla de control, y es la clase base para [ `ContentView` ](#contentView).<br /><br />[Documentación de API](xref:Xamarin.Forms.TemplatedView) / [guía](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Ejemplo de TemplatedView](layouts-images/TemplatedView.png "Ejemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Ejemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) es un administrador de diseño para las vistas con plantilla, se utiliza dentro de un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) para marcar dónde aparece el contenido que debe presentarse.<br /><br />[Documentación de API](xref:Xamarin.Forms.ContentPresenter) / [guía](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Ejemplo de ContentPresenter](layouts-images/ContentPresenter.png "Ejemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Ejemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Diseños con varios elementos secundarios

Estas clases derivan de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) coloca los elementos secundarios en una pila horizontal o verticalmente se basa en el [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propiedad. El [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) propiedad controla el espaciado entre los elementos secundarios y tiene un valor predeterminado de 6.<br /><br />[Documentación de API](xref:Xamarin.Forms.StackLayout) / [guía](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Ejemplo de StackLayout](layouts-images/StackLayout.png "Ejemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Ejemplo de StackLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) coloca sus elementos secundarios en una cuadrícula de filas y columnas. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), y [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentación de API](xref:Xamarin.Forms.Grid) / [guía](~/xamarin-forms/user-interface/layouts/grid.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de cuadrícula](layouts-images/Grid.png "Ejemplo de cuadrícula")](layouts-images/Grid-Large.png#lightbox "Ejemplo de cuadrícula")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) coloca los elementos secundarios en ubicaciones específicas en relación con su elemento primario. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) y [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Un `AbsoluteLayout` es útil para animar las posiciones de las vistas.<br /><br />[Documentación de API](xref:Xamarin.Forms.AbsoluteLayout) / [guía](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Ejemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Ejemplo de AbsoluteLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) coloca los elementos secundarios respecto a la `RelativeLayout` propio o a sus elementos relacionados. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) que se establecen en objetos de tipo [ `Constraint` ](xref:Xamarin.Forms.Constraint) y [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Documentación de API](xref:Xamarin.Forms.RelativeLayout) / [guía](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de RelativeLayout](layouts-images/RelativeLayout.png "Ejemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Ejemplo de RelativeLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) se basa en la hoja CSS [Flexible diseño módulo](https://www.w3.org/TR/css-flexbox-1/), normalmente conocido como _flex diseño_ o _flex cuadro_. `FlexLayout` define propiedades enlazables seis y cinco propiedades enlazables adjuntas que permiten a los elementos secundarios se pueden superponer ni ajusta con muchas opciones de alineación y orientación.<br /><br />[Documentación de API](xref:Xamarin.Forms.FlexLayout) / [guía](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Ejemplo de FlexLayout](layouts-images/FlexLayout.png "Ejemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Ejemplo de FlexLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
