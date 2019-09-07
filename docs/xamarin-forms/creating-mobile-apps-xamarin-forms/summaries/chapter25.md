---
title: Resumen del capítulo 25. Variedades de página
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 25. Variedades de página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760543"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumen del capítulo 25. Variedades de página

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Hasta ahora ha visto dos clases que derivan de `Page`: `ContentPage` y `NavigationPage`. Este capítulo presenta otras dos:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) administra dos páginas, un servidor maestro y un detalle
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) administra varias páginas secundarias tiene acceso a través de pestañas

Estos tipos de página ofrecen opciones de exploración más sofisticadas que el `NavagationPage` descritos en [capítulo 24. Navegación por páginas](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Maestro y detalles

El [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) define dos propiedades de tipo `Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) y [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail). Por lo general cada una de estas propiedades para establecer un `ContentPage`. La `MasterDetailPage` muestra y cambia entre estas dos páginas.

Hay dos maneras fundamentales de cambiar entre estas dos páginas:

- *dividir* donde están en paralelo el maestro y detalles
- *elemento flotante* donde la página de detalles se trata o cubre parcialmente el maestro de página

Hay diversas variaciones de la *elemento flotante* enfoque (*diapositiva*, *se superponen*, y *intercambio*), pero estos suelen ser plataforma dependientes. Puede establecer el [ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propiedad de `MasterDetailPage` a un miembro de la [ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior) enumeración:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Sin embargo, esta propiedad tiene ningún efecto en los teléfonos. Los teléfonos siempre tienen un comportamiento del elemento flotante. Solo las tabletas y escritorio windows pueden tener un comportamiento de división.

### <a name="exploring-the-behaviors"></a>Explorar los comportamientos

El [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) ejemplo le permite experimentar con el comportamiento predeterminado en diferentes dispositivos. El programa contiene dos `ContentPage` derivados para el maestro y detalles (con un `Title` propiedad establecida en ambos) y otra clase que derive de `MasterDetailPage` que combina. La página de detalles está incluida en un `NavigationPage` porque el programa UWP no funcionaría sin él.

Las plataformas de Windows 8.1 y Windows Phone 8.1 requieren que un mapa de bits se establezca en el `Icon` propiedad de la página maestra.

### <a name="back-to-school"></a>Vuelta al colegio

El [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) ejemplo adopta un enfoque ligeramente distinto para construir el programa para mostrar los estudiantes de la [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca.

El `Master` y `Detail` las propiedades se definen con árboles visuales en el [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) archivo, que se deriva de `MasterDetailPage`. Esta disposición permite que los enlaces de datos debe establecerse entre las páginas maestro y detalles.

Que el archivo XAML también establece la [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propiedad de `MasterDetailPage` a `True`. Esto hace que la página maestra que se mostrará en el inicio; de forma predeterminada, se muestra la página de detalles. El [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) archivo establece `IsPresented` a `false` cuando se selecciona un elemento de la `ListView` en la página maestra. A continuación, se muestra la página de detalles:

[![Captura de pantalla de detalle de School y triple](images/ch25fg09-small.png "página de detalles de un MasterDetailPage")](images/ch25fg09-large.png#lightbox "página de detalles de un MasterDetailPage")

### <a name="your-own-user-interface"></a>Su propia interfaz de usuario

Aunque Xamarin.Forms proporciona una interfaz de usuario para cambiar entre las vistas de maestro y detalles, puede proporcionar sus propios. Para ello:

- Establecer el [ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) propiedad `false` para deshabilitar el gesto de deslizar rápidamente
- Invalidar el [ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) método y devuelva `false` para ocultar los botones de barra de herramientas de Windows 8.1 y Windows Phone 8.1.

A continuación, debe proporcionar un medio para cambiar entre las páginas maestro y detalles, como se muestra en el [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) ejemplo.

El [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) muestra otro enfoque utilizando un `TapGestureRecognizer` en las páginas maestro y detalles.

## <a name="tabbedpage"></a>TabbedPage

El [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) es una colección de páginas que se puede cambiar entre pestañas. Se deriva de `MultiPage<Page>` y define no hay propiedades o métodos públicos de su propio. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), sin embargo, definir una propiedad:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propiedad de tipo `IList<T>`

Rellene esto `Children` recopilación con objetos de la página.

Otro enfoque le permite definir la `TabbedPage` elementos secundarios de forma similar a un `ListView` utilizando estas dos propiedades que generan las páginas con pestañas automáticamente:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) de tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) de tipo `DataTemplate`

Sin embargo, este enfoque no funciona bien en iOS, cuando la colección contiene más de unos pocos elementos.

`MultiPage<T>` define dos propiedades más que le permiten realizar un seguimiento de la página que está visible actualmente:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) de tipo `T`, que hace referencia a la página
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) de tipo `Object`, que hace referencia al objeto en el `ItemsSource` colección

`MultiPage<T>` También define dos eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Cuando el `ItemsSource` los cambios de colección
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) Cuando se cambia la página vista

### <a name="discrete-tab-pages"></a>Páginas de fichas discretos

El [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) ejemplo consta de tres páginas con pestañas que muestran los colores de tres maneras diferentes. Cada pestaña sirve un `ContentPage` derivado y, a continuación, el `TabbedPage` derivado [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina las tres páginas.

Para cada página que aparece en un `TabbedPage`, el `Title` propiedad es necesaria para especificar el texto en la ficha y el Store de Apple requiere que un icono de usarse, por lo que el `Icon` propiedad se establece para iOS:

[![Triple captura de pantalla de colores diferenciados por pestañas](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

El [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) ejemplo tiene una página principal que se enumeran todos los alumnos. Cuando se pulsa un alumno, navega a una `TabbedPage` derivado, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora tres `ContentPage` objetos de su árbol visual, uno de los cuales permite introducir algunas notas para ese estudiante.

### <a name="using-an-itemtemplate"></a>Uso de una plantilla ItemTemplate

El [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) ejemplo se utiliza el [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. El [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) archivo establece la `DataTemplate` propiedad de `TabbedPage` a un árbol visual a partir `ContentPage` que contiene enlaces a las propiedades de `NamedColor` (incluido un enlace a la `Title` propiedad).

Sin embargo, esto es problemático en iOS. Solo algunos de los elementos se pueden mostrar y hay ninguna manera buena para concederles los iconos.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 25 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Capítulo 25 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página Master-Detail](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página con pestañas](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
