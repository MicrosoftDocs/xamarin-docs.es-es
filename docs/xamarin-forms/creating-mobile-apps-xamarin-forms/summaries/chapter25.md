---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 25. Page varieties''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e66fb50b8d537ee0267457d5b0ab0f417813e676
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136622"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumen del capítulo 25. Variedades de páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Hasta ahora se han visto dos clases que se derivan de `Page`: `ContentPage` y `NavigationPage`. En este capítulo se presentan otras dos:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) administra dos páginas, una maestra y una de detalles.
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) administra varias páginas secundarias a las que se tiene acceso a través de pestañas.

Estos tipos de página proporcionan opciones de navegación más sofisticadas que las `NavagationPage` que se describen en [Capítulo 24. Navegación de páginas](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Principal y detalles

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) define dos propiedades de tipo `Page`: [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) y [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail). Por lo general, cada una de estas propiedades se establece en `ContentPage`. `MasterDetailPage` muestra estas dos páginas y cambia entre ellas.

Hay dos maneras fundamentales de cambiar entre estas dos páginas:

- modo de *división*: la página maestra y la de detalles se muestran en paralelo.
- modo de *elemento flotante*: la página de detalles cubre, o cubre parcialmente, la página maestra.

Hay varias variaciones del enfoque *elemento flotante* (*deslizar*, *superponer* e *intercambiar*), pero suelen depender de la plataforma. Puede establecer la propiedad [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) de `MasterDetailPage` en un miembro de la enumeración [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior):

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Sin embargo, esta propiedad no tiene ningún efecto en los teléfonos. Los teléfonos siempre tienen un comportamiento de elemento flotante. Solo las tabletas y las ventanas de escritorio pueden tener un comportamiento de división.

### <a name="exploring-the-behaviors"></a>Exploración de los comportamientos

El ejemplo [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) permite experimentar con el comportamiento predeterminado en distintos dispositivos. El programa contiene dos derivados de `ContentPage` independientes para la página maestra y de detalles (con una propiedad `Title` establecida en ambas) y otra clase que se deriva de `MasterDetailPage` que los combina. La página de detalles se incluye en `NavigationPage` porque el programa de UWP no funcionará sin ella.

Las plataformas Windows 8.1 y Windows Phone  8.1 requieren que un mapa de bits se establezca en la propiedad `Icon` de la página maestra.

### <a name="back-to-school"></a>Vuelta al cole

El ejemplo [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) toma un enfoque algo diferente para construir el programa con el fin de mostrar los alumnos de la biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt).

Las propiedades `Master` y `Detail` se definen con árboles visuales en el archivo [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml), que se deriva de `MasterDetailPage`. Esta disposición permite establecer enlaces de datos entre las páginas maestra y de detalles.

Ese archivo XAML también establece la propiedad [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) de `MasterDetailPage` en `True`. Esto hace que la página maestra se muestre en el inicio; de forma predeterminada, se muestra la página de detalles. El archivo [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) establece `IsPresented` en `false` cuando se selecciona un elemento de `ListView` en la página maestra. A continuación, se muestra la página de detalles:

[![Captura de pantalla triple de la página de la escuela y de detalles](images/ch25fg09-small.png "Página de detalles de MasterDetailPage")](images/ch25fg09-large.png#lightbox "Página de detalles de MasterDetailPage")

### <a name="your-own-user-interface"></a>Su propia interfaz de usuario

Aunque Xamarin.Forms proporciona una interfaz de usuario para cambiar entre las vistas maestra y de detalles, puede proporcionar la suya propia. Para ello:

- Establezca la propiedad [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) en `false` para deshabilitar el deslizamiento rápido.
- Invalide el método [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) y devuelva `false` para ocultar los botones de la barra de herramientas en Windows 8.1 y Windows Phone 8.1.

A continuación, debe proporcionar un medio para cambiar entre las páginas maestra y de detalles, como se muestra en el ejemplo [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails).

En el ejemplo [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) se muestra otro enfoque que usa `TapGestureRecognizer` en las páginas maestra y de detalles.

## <a name="tabbedpage"></a>TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) es una colección de páginas que se pueden cambiar entre las pestañas. Se deriva de `MultiPage<Page>` y no define ninguna propiedad o método público propio. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), sin embargo, define una propiedad:

- Propiedad [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) de tipo `IList<T>`

Esta colección `Children` se rellena con objetos de página.

Otro enfoque permite definir el los elementos secundarios de `TabbedPage` de forma muy similar a `ListView` con estas dos propiedades que generan automáticamente las fichas:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) de tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) de tipo `DataTemplate`

Sin embargo, este enfoque no funciona bien en iOS cuando la colección contiene más de unos pocos elementos.

`MultiPage<T>` define dos propiedades más que permiten realizar un seguimiento de la página que se está viendo actualmente:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) de tipo `T`, que hace referencia a la página.
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) de tipo `Object`, que hace referencia al objeto de la colección `ItemsSource`.

`MultiPage<T>` también define dos eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) cuando cambia la colección `ItemsSource`.
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) cuando cambia la página vista.

### <a name="discrete-tab-pages"></a>Fichas discretas

El ejemplo [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) consta de tres fichas que muestran los colores de tres maneras diferentes. Cada pestaña es un derivado de `ContentPage` y, a continuación, el derivado de `TabbedPage` [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina las tres páginas.

Para cada página que aparece en `TabbedPage`, se requiere la propiedad `Title` para especificar el texto en la pestaña, y Apple Store requiere que se use también un icono, por lo que la propiedad `Icon` se establece para iOS:

[![Captura de pantalla triple de colores con pestañas discretas](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

El ejemplo de [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) consta de una página principal en la que se enumeran todos los alumnos. Cuando se pulsa en un estudiante, se va hasta un derivado de `TabbedPage`, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora tres objetos `ContentPage` en su árbol visual, uno de los cuales permite escribir algunas notas para ese alumno.

### <a name="using-an-itemtemplate"></a>Uso de ItemTemplate

En el ejemplo [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) se usa la clase [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) de [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit). El archivo [MultiTabbedColorsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) establece la propiedad `DataTemplate` de `TabbedPage` en un árbol visual que empieza por `ContentPage` y contiene enlaces a propiedades de `NamedColor` (incluido un enlace a la propiedad `Title`).

Sin embargo, esto es problemático en iOS. Solo se pueden mostrar algunos elementos y no hay ninguna manera adecuada de dotarles de iconos.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 25 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Ejemplos del capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página maestra y de detalles](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Fichas](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
