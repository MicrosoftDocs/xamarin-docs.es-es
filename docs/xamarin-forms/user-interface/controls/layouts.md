---
Título: " Xamarin.Forms diseños" Descripción: " Xamarin.Forms los diseños se usan para crear controles de interfaz de usuario en estructuras visuales. En este artículo se enumeran los diseños incluidos en Xamarin.Forms ".
MS. Prod: Xamarin ms. AssetID: F4180997-BA21-453A-9958-D1E2940DF050 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/21/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-layouts"></a>Xamarin.FormsDiseños

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Los diseños de Xamarin. Forms se usan para crear controles de interfaz de usuario en estructuras visuales._

Las [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clases y de Xamarin.Forms son subtipos especializados de vistas que actúan como contenedores para vistas y otros diseños. La `Layout` propia clase deriva de [`View`](views.md) . `Layout`Normalmente, un derivado contiene lógica para establecer la posición y el tamaño de los elementos secundarios en Xamarin.Forms las aplicaciones.

[![Xamarin.FormsTipos de diseño](layouts-images/layouts-sml.png "[! Operador. Tipos de diseño NO-LOC (Xamarin. Forms)]")](layouts-images/layouts.png#lightbox "[! Operador. Tipos de diseño NO-LOC (Xamarin. Forms)]")

Las clases que derivan de `Layout` pueden dividirse en dos categorías:

## <a name="layouts-with-single-content"></a>Diseños con un solo contenido

Estas clases derivan de [`Layout`](xref:Xamarin.Forms.Layout) , que [`Padding`](xref:Xamarin.Forms.Layout.Padding) define [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) las propiedades y.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView)contiene un único elemento secundario que se establece con la [`Content`](xref:Xamarin.Forms.ContentView.Content) propiedad. La `Content` propiedad se puede establecer en cualquier `View` derivado, incluidos otros `Layout` derivados. `ContentView`se utiliza principalmente como elemento estructural y sirve como clase base para [`Frame`](#frame) .<br /><br />[Documentación](xref:Xamarin.Forms.ContentView)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/contentview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) de | [![Ejemplo de ContentView](layouts-images/ContentView.png "Ejemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Ejemplo de ContentView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Fotograma

|     |     |
| --- | --- |
| La [`Frame`](xref:Xamarin.Forms.Frame) clase se deriva de [`ContentView`](#contentView) y muestra un borde, o marco, alrededor de su elemento secundario. La `Frame` clase tiene un [`Padding`](xref:Xamarin.Forms.Layout.Padding) valor predeterminado de 20 y también define [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) las propiedades, y [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentación](xref:Xamarin.Forms.Frame)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/frame.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) de | [![Ejemplo de marco](layouts-images/Frame.png "Ejemplo de marco")](layouts-images/Frame-Large.png#lightbox "Ejemplo de marco")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView)es capaz de desplazar su contenido. Establezca la [`Content`](xref:Xamarin.Forms.ScrollView.Content) propiedad en una vista o diseño demasiado grande para caber en la pantalla. (El contenido de una `ScrollView` es muy frecuente [`StackLayout`](#stackLayout) ). Establezca la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propiedad para indicar si el desplazamiento debe ser vertical, horizontal o ambos.<br /><br />[Documentación](xref:Xamarin.Forms.ScrollView)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/scrollview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Ejemplo de ScrollView](layouts-images/ScrollView.png "Ejemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Ejemplo de ScrollView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)muestra el contenido con una plantilla de control y es la clase base para [`ContentView`](#contentView) .<br /><br />[Documentación](xref:Xamarin.Forms.TemplatedView)  /  de API [Guía](~/xamarin-forms/app-fundamentals/templates/control-template.md) de | [![Ejemplo de TemplatedView](layouts-images/TemplatedView.png "Ejemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Ejemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)es un administrador de diseño para vistas con plantilla, que se utiliza dentro de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para marcar dónde aparece el contenido que se va a presentar.<br /><br />[Documentación](xref:Xamarin.Forms.ContentPresenter)  /  de API [Guía](~/xamarin-forms/app-fundamentals/templates/control-template.md) de | [![Ejemplo de ContentPresenter](layouts-images/ContentPresenter.png "Ejemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Ejemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Diseños con varios elementos secundarios

Estas clases se derivan de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) .

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout)coloca los elementos secundarios en una pila, horizontal o verticalmente, en función de la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propiedad. La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propiedad rige el espaciado entre los elementos secundarios y tiene un valor predeterminado de 6.<br /><br />[Documentación](xref:Xamarin.Forms.StackLayout)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/stacklayout.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de| [![Ejemplo de StackLayout](layouts-images/StackLayout.png "Ejemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Ejemplo de StackLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Cuadrícula

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid)coloca los elementos secundarios en una cuadrícula de filas y columnas. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty) ,, [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) y [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .<br /><br />[Documentación](xref:Xamarin.Forms.Grid)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/grid.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Ejemplo de cuadrícula](layouts-images/Grid.png "Ejemplo de cuadrícula")](layouts-images/Grid-Large.png#lightbox "Ejemplo de cuadrícula")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)coloca los elementos secundarios en ubicaciones específicas en relación con su elemento primario. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) y [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) . `AbsoluteLayout`Es útil para animar las posiciones de las vistas.<br /><br />[Documentación](xref:Xamarin.Forms.AbsoluteLayout)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/absolute-layout.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Ejemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Ejemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Ejemplo de AbsoluteLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)coloca los elementos secundarios en relación con el `RelativeLayout` propio o con sus elementos del mismo nivel. La posición de un elemento secundario se indica mediante las [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) que se establecen en objetos de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) y [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) .<br /><br />[Documentación](xref:Xamarin.Forms.RelativeLayout)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/relative-layout.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Ejemplo de RelativeLayout](layouts-images/RelativeLayout.png "Ejemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Ejemplo de RelativeLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)se basa en el [módulo de diseño de caja flexible](https://www.w3.org/TR/css-flexbox-1/)de CSS, conocido normalmente como _diseño_ flexible o _caja flexible_. `FlexLayout`define seis propiedades enlazables y cinco propiedades enlazables asociadas que permiten apilar o ajustar los elementos secundarios con muchas opciones de alineación y orientación.<br /><br />[Documentación](xref:Xamarin.Forms.FlexLayout)  /  de API [Guía](~/xamarin-forms/user-interface/layouts/flex-layout.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) de | [![Ejemplo de FlexLayout](layouts-images/FlexLayout.png "Ejemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Ejemplo de FlexLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsEjemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAssembl](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
