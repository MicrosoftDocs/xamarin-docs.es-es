---
title: Clases base y controles nativos del representador
description: Todos los controles de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. En este artículo se enumeran las clases de representador y control nativo que implementan cada página, diseño, vista y celda de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/09/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 612200a23c198cbb1127119548c0a1dcc2928645
ms.sourcegitcommit: cd0c0999b53e825b60471bfbfd4144cfcd783587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225473"
---
# <a name="renderer-base-classes-and-native-controls"></a>Clases base y controles nativos del representador

_Todos los controles de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. En este artículo se enumeran las clases de representador y control nativo que implementan cada página, diseño, vista y celda de Xamarin.Forms._

A excepción de la clase `MapRenderer`, los representadores específicos de la plataforma se pueden encontrar en los espacios de nombres siguientes:

- **iOS**: Xamarin.Forms.Platform.iOS
- **Android**: Xamarin.Forms.Platform.Android
- **Android (AppCompat)** : Xamarin.Forms.Platform.Android.AppCompat
- **Android (FastRenderers)**  - Xamarin.Forms.Platform.Android.FastRenderers
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Platform.UWP

Para obtener más información sobre los representadores rápidos, consulte [Representadores rápidos de Xamarin.Forms](~/xamarin-forms/internals/fast-renderers.md).

La clase `MapRenderer` se puede encontrar en los espacios de nombres siguientes:

- **iOS**: Xamarin.Forms.Maps.iOS
- **Android**: Xamarin.Forms.Maps.Android
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Maps.UWP

> [!NOTE]
> Para obtener información sobre cómo crear representadores personalizados para aplicaciones de Shell, consulte [Representadores personalizados de Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/customrenderers.md).

## <a name="pages"></a>Páginas

En la tabla siguiente se enumeran las clases de representador y control nativo que implementan cada tipo [Page](~/xamarin-forms/user-interface/controls/pages.md) de Xamarin.Forms:

|Página|Representador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS: teléfono), TabletMasterDetailPageRenderer (iOS: tableta), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (teléfono), UISplitViewController (tableta)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Control personalizado)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS y Android), NavigationPageRenderer (AppCompat Android), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (Control personalizado)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS y Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivot)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Diseños

En la tabla siguiente se enumeran las clases de representador y control nativo que implementan cada tipo [Layout](~/xamarin-forms/user-interface/controls/layouts.md) de Xamarin.Forms:

|Diseño|Representador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|CardView|Borde|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Ver|Ver|FrameworkElement|

## <a name="views"></a>Vistas

En la tabla siguiente se enumeran las clases de representador y control nativo que implementan cada tipo [View](~/xamarin-forms/user-interface/controls/views.md) de Xamarin.Forms:

|Vistas|Representador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS y Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Rectángulo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Botón|AppCompatButton|Botón|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`CheckBox`](xref:Xamarin.Forms.CheckBox)|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse)|EllipseRenderer|CALayer|Ver||Ellipse|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Imagen|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Botón|
|[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`Line`](xref:Xamarin.Forms.Shapes.Line)|LineRenderer|CALayer|Ver||Línea|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Path`](xref:Xamarin.Forms.Shapes.Path)|PathRenderer|CALayer|Ver||Ruta de acceso|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`Polygon`](xref:Xamarin.Forms.Shapes.Polygon)|PolygonRenderer|CALayer|Ver||Polígono|
|[`Polyline`](xref:Xamarin.Forms.Shapes.Polyline)|PolylineRenderer|CALayer|Ver||Polilínea|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`RadioButton`](xref:Xamarin.Forms.RadioButton)|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`Rectangle`](xref:Xamarin.Forms.Shapes.Rectangle)|RectangleRenderer|CALayer|Ver||Rectángulo|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`SwipeView`](xref:Xamarin.Forms.SwipeView)|SwipeViewRenderer|UIView||Ver|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Modificador|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android y UWP)|WkWebView|WebView||WebView|

> [!NOTE]
> El control `Expander` se implementa usando un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) con animación. Por lo tanto, no tiene ningún representador de plataforma.

## <a name="cells"></a>Celdas

En la tabla siguiente se enumeran las clases de representador y control nativo que implementan cada tipo [Cell](~/xamarin-forms/user-interface/controls/cells.md) de Xamarin.Forms:

|Celdas|Representador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell con UITextField|LinearLayout con TextView y EditText|DataTemplate con un control TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell con UISwitch|Modificador|DataTemplate con un elemento Grid que contiene controles TextBlock y ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout con dos objetos TextView|DataTemplate con un elemento StackPanel que contiene dos elementos TextBlock|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell con UIImage|LinearLayout con dos objetos TextView y un objeto ImageView|DataTemplate con un elemento Grid que contiene un control Image y dos TextBlock|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Ver|DataTemplate con un elemento ContentPresenter|

## <a name="related-links"></a>Vínculos relacionados

- [Representadores rápidos de Xamarin.Forms](~/xamarin-forms/internals/fast-renderers.md)
- [Representadores personalizados de Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/customrenderers.md)
