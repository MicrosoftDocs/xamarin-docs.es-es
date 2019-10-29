---
title: Diseños de Xamarin. Forms
description: Los diseños de Xamarin. Forms se usan para crear controles de interfaz de usuario en estructuras visuales. En este artículo se enumeran los diseños incluidos en Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 39ea210670476604b18c9f1aac760cc130b93c3c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005401"
---
# <a name="xamarinforms-layouts"></a>Diseños de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Los diseños de Xamarin. Forms se usan para crear controles de interfaz de usuario en estructuras visuales._

Las clases [`Layout`](xref:Xamarin.Forms.Layout) y [`Layout<T>`](xref:Xamarin.Forms.Layout`1) de Xamarin. Forms son subtipos especializados de vistas que actúan como contenedores para vistas y otros diseños. La propia clase `Layout` deriva de [`View`](views.md). Una `Layout` derivada normalmente contiene lógica para establecer la posición y el tamaño de los elementos secundarios en las aplicaciones de Xamarin. Forms.

[![Tipos de diseño de Xamarin. Forms](layouts-images/layouts-sml.png "Tipos de diseño de Xamarin. Forms")](layouts-images/layouts.png#lightbox "Tipos de diseño de Xamarin. Forms")

Las clases que derivan de `Layout` pueden dividirse en dos categorías:

## <a name="layouts-with-single-content"></a>Diseños con un solo contenido

Estas clases se derivan de [`Layout`](xref:Xamarin.Forms.Layout), que define las propiedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) y [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) .

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contiene un único elemento secundario que se establece con la propiedad [`Content`](xref:Xamarin.Forms.ContentView.Content) . La propiedad `Content` se puede establecer en cualquier `View` derivado, incluidos otros derivados de `Layout`. `ContentView` se usa principalmente como elemento estructural y sirve como clase base para [`Frame`](#frame).<br /><br />[Documentación de API](xref:Xamarin.Forms.ContentView)  / [Guía](~/xamarin-forms/user-interface/layouts/contentview.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-cardview/) | [![Ejemplo de ContentView](layouts-images/ContentView.png "Ejemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Ejemplo de ContentView")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Fotograma

|     |     |
| --- | --- |
| La clase [`Frame`](xref:Xamarin.Forms.Frame) se deriva de [`ContentView`](#contentView) y muestra un borde, o marco, alrededor de su elemento secundario. La clase `Frame` tiene un valor predeterminado de [`Padding`](xref:Xamarin.Forms.Layout.Padding) de 20 y también define las propiedades [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)y [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentación de API](xref:Xamarin.Forms.Frame)  / [Guía](~/xamarin-forms/user-interface/layouts/frame.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Ejemplo de marco](layouts-images/Frame.png "Ejemplo de marco")](layouts-images/Frame-Large.png#lightbox "Ejemplo de marco")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) es capaz de desplazar su contenido. Establezca la propiedad [`Content`](xref:Xamarin.Forms.ScrollView.Content) en una vista o diseño demasiado grande para caber en la pantalla. (El contenido de una `ScrollView` suele ser un [`StackLayout`](#stackLayout)). Establezca la propiedad [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) para indicar si el desplazamiento debe ser vertical, horizontal o ambos.<br /><br />[Documentación de API](xref:Xamarin.Forms.ScrollView)  / [Guía](~/xamarin-forms/user-interface/layouts/scroll-view.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de ScrollView](layouts-images/ScrollView.png "Ejemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Ejemplo de ScrollView")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) muestra el contenido con una plantilla de control y es la clase base para [`ContentView`](#contentView).<br /><br />[Guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentación de API](xref:Xamarin.Forms.TemplatedView) | [![Ejemplo de TemplatedView](layouts-images/TemplatedView.png "Ejemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Ejemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) es un administrador de diseño para vistas con plantilla, que se utiliza dentro de un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para marcar dónde aparece el contenido que se va a presentar.<br /><br />[Guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentación de API](xref:Xamarin.Forms.ContentPresenter) | [![Ejemplo de ContentPresenter](layouts-images/ContentPresenter.png "Ejemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Ejemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Diseños con varios elementos secundarios

Estas clases se derivan de [`Layout<View>`](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) coloca los elementos secundarios en una pila, horizontal o verticalmente, en función de la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . La propiedad [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) rige el espaciado entre los elementos secundarios y tiene un valor predeterminado de 6.<br /><br />[Documentación de API](xref:Xamarin.Forms.StackLayout)  / [Guía](~/xamarin-forms/user-interface/layouts/stack-layout.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Ejemplo de StackLayout](layouts-images/StackLayout.png "Ejemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Ejemplo de StackLayout")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Cuadrícula

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) coloca sus elementos secundarios en una cuadrícula de filas y columnas. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty), [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)y [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentación de API](xref:Xamarin.Forms.Grid)  / [Guía](~/xamarin-forms/user-interface/layouts/grid.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de cuadrícula](layouts-images/Grid.png "Ejemplo de cuadrícula")](layouts-images/Grid-Large.png#lightbox "Ejemplo de cuadrícula")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) coloca los elementos secundarios en ubicaciones específicas en relación con su elemento primario. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) y [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Una `AbsoluteLayout` es útil para animar las posiciones de las vistas.<br /><br />[Documentación de API](xref:Xamarin.Forms.AbsoluteLayout)  / [Guía](~/xamarin-forms/user-interface/layouts/absolute-layout.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Ejemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Ejemplo de AbsoluteLayout")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) coloca los elementos secundarios en relación con el `RelativeLayout` mismo o con sus elementos relacionados. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) que se establecen en objetos de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) y [`BoundsConstraint`](xref:Xamarin.Forms.Constraint).<br /><br />[Documentación de API](xref:Xamarin.Forms.RelativeLayout)  / [Guía](~/xamarin-forms/user-interface/layouts/relative-layout.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Ejemplo de RelativeLayout](layouts-images/RelativeLayout.png "Ejemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Ejemplo de RelativeLayout")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) se basa en el [módulo de diseño de caja flexible](https://www.w3.org/TR/css-flexbox-1/)de CSS, conocido normalmente como _diseño_ flexible o _flexible_. `FlexLayout` define seis propiedades enlazables y cinco propiedades enlazables asociadas que permiten apilar o ajustar los elementos secundarios con muchas opciones de alineación y orientación.<br /><br />[Documentación de API](xref:Xamarin.Forms.FlexLayout)  / [Guía](~/xamarin-forms/user-interface/layouts/flex-layout.md) de  /  de[ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Ejemplo de FlexLayout](layouts-images/FlexLayout.png "Ejemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Ejemplo de FlexLayout")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
