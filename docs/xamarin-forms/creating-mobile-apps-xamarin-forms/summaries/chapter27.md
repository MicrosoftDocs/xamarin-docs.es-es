---
title: Resumen del capítulo 27. Representadores personalizados
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 27. Representadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760505"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumen del capítulo 27. Representadores personalizados

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

Un elemento de Xamarin.Forms como `Button` se representa con un botón específico de la plataforma encapsulado en una clase denominada `ButtonRenderer`.  Esta es la [versión de iOS de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la [versión de Android de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs) y la [versión de UWP de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

En este capítulo se describe cómo puede escribir sus propios representadores para crear vistas personalizadas que se asignan a objetos específicos de la plataforma.

## <a name="the-complete-class-hierarchy"></a>Jerarquía de clases completa

Hay cuatro ensamblados que contienen el código específico de la plataforma de Xamarin.Forms.
Puede ver el código fuente en GitHub mediante estos vínculos:

- [**Xamarin.Forms.Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muy pequeño)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Los ensamblados de `WinRT` mencionados en el libro ya no forman parte de esta solución. 

El ejemplo [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) muestra una jerarquía de clases para los ensamblados que son válidos para la plataforma en ejecución.

Observará una clase importante denominada `ViewRenderer`. Esta es la clase de la que se deriva al crear un representador específico de la plataforma. Existe en tres versiones diferentes, ya que está ligada al sistema de vista de la plataforma de destino:

El elemento [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) de iOS tiene argumentos genéricos:

- `TView`, con la restricción [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`, con la restricción [`UIKit.UIView`](xref:UIKit.UIView)

El elemento [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) de Android tiene argumentos genéricos:

- `TView`, con la restricción [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`, con la restricción [`Android.Views.View`](xref:Android.Views.View)

El elemento [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) de UWP tiene argumentos genéricos con nombres diferentes:

- `TElement`, con la restricción [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`, con la restricción [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Al escribir un representador, se derivará una clase de `View` y, a continuación, se escriben varias clases de `ViewRenderer`, una para cada plataforma compatible. Cada implementación específica de la plataforma hará referencia a una clase nativa que se deriva del tipo que se especifica como parámetro `TNativeView` o `TNativeElement`.

## <a name="hello-custom-renderers"></a>¡Hola, representadores personalizados!

El programa [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) hace referencia a una vista personalizada denominada `HelloView` en su clase [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs).

La clase [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) se incluye en el proyecto **HelloRenderers** y simplemente se deriva de `View`.

La clase [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) del proyecto **HelloRenderers.iOS** se deriva de `ViewRenderer<HelloView, UILabel>`. En la invalidación de `OnElementChanged`, crea un elemento `UILabel` de iOS nativo y llama a `SetNativeControl`.

La clase [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) del proyecto **HelloRenderers.Droid** se deriva de `ViewRenderer<HelloView, TextView>`. En la invalidación de `OnElementChanged`, crea un elemento `TextView` de Android y llama a `SetNativeControl`.

La clase [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) de **HelloRenderers.UWP** y otros proyectos de Windows se derivan de `ViewRenderer<HelloView, TextBlock>`. En la invalidación de `OnElementChanged`, crea un elemento `TextBlock` de Windows y llama a `SetNativeControl`.

Todos los derivados de `ViewRenderer` contienen un atributo `ExportRenderer` en el nivel de ensamblado que asocia la clase `HelloView` a la clase `HelloViewRenderer` determinada. Este es el modo en que Xamarin.Forms localiza los representadores en los proyectos de plataforma individuales:

[![Captura de pantalla triple de la vista Hello](images/ch27fg02-small.png "Representadores personalizados")](images/ch27fg02-large.png#lightbox "Representadores personalizados")

## <a name="renderers-and-properties"></a>Representadores y propiedades

El siguiente conjunto de representadores implementa el dibujo de elipse, y se encuentra en los distintos proyectos de la solución [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform).

La clase [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) se encuentra en la plataforma **Xamarin.FormsBook.Platform**. La clase es similar a `BoxView` y define solo una propiedad única: `Color` de tipo `Color`.

Los representadores pueden transferir los valores de propiedad establecidos en `View` al objeto nativo invalidando el método `OnElementPropertyChanged` en el representador. Dentro de este método (y dentro de la mayor parte del representador), hay dos propiedades disponibles:

- `Element`, el elemento de Xamarin.Forms
- `Control`, la vista nativa o el objeto del widget o el control

Los tipos de estas propiedades vienen determinados por los parámetros genéricos de `ViewRenderer`. En este ejemplo, `Element` es de tipo `EllipseView`.

Por consiguiente, la invalidación de `OnElementPropertyChanged` puede transferir el valor `Color` de `Element` al objeto `Control` nativo, probablemente con algún tipo de conversión. Los tres representadores son:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que utiliza una clase [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) para la elipse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que utiliza una clase [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) para la elipse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que puede usar la clase nativa [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) de Windows.

La clase [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) muestra algunos de estos objetos `EllipseView`:

[![Captura de pantalla triple de la demostración de elipse](images/ch27fg03-small.png "Representadores personalizados de EllipseView")](images/ch27fg03-large.png#lightbox "Representadores personalizados de EllipseView")

[**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebota un elemento `EllipseView` en los lados de la pantalla.

## <a name="renderers-and-events"></a>Representadores y eventos

También es posible que los representadores generen eventos indirectamente. La clase [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) es similar a `Slider` de Xamarin.Forms normal, pero permite especificar una serie de pasos discretos entre los valores `Minimum` y `Maximum`.

Los tres representadores son:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Los representadores detectan los cambios en el control nativo y, a continuación, llaman a `SetValueFromRenderer`, que hace referencia a una propiedad enlazable definida en `StepSlider`, un cambio en el cual hace que `StepSlider` active un evento `ValueChanged`.

En el ejemplo [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) se muestra este nuevo control deslizante.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 27 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Ejemplos del capítulo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
