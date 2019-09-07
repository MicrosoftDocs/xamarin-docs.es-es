---
title: Resumen del capítulo 27. Representadores personalizados
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 27. Representadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760505"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumen del capítulo 27. Representadores personalizados

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Un elemento de Xamarin.Forms como `Button` se representa con un botón específico de la plataforma encapsulado en una clase denominada `ButtonRenderer`.  Este es el [versión de iOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), el [versión Android de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)y el [versión de UWP de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Este capítulo describe cómo puede escribir sus propios representadores para crear vistas personalizadas que se asignan a objetos específicos de la plataforma.

## <a name="the-complete-class-hierarchy"></a>La jerarquía de clases completa

Hay cuatro ensamblados que contienen el código específico de la plataforma de Xamarin.Forms.
Puede ver el código fuente en GitHub con estos vínculos:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muy pequeño)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> El `WinRT` ensamblados mencionados en el libro ya no forman parte de esta solución. 

El [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) ejemplo muestra una jerarquía de clases para los ensamblados que son válidos para la plataforma que se está ejecuta.

Observará una importante clase denominada `ViewRenderer`. Se trata de la clase que se deriva de, al crear a un representador específica de la plataforma. Existe en tres versiones diferentes, ya que está enlazado al sistema de la vista de la plataforma de destino:

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) tiene argumentos genéricos:

- `TView` restringido a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restringido a [`UIKit.UIView`](xref:UIKit.UIView)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) tiene argumentos genéricos:

- `TView` restringido a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restringido a [`Android.Views.View`](xref:Android.Views.View)

UWP [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) diferente ha nombrado a argumentos genéricos:

- `TElement` restringido a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` restringido a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Al escribir un representador, se derive una clase de `View`y, a continuación, escribir varias `ViewRenderer` clases, uno para cada plataforma compatible. Cada implementación específica de la plataforma hará referencia a una clase nativa que se deriva el tipo que se especifica como el `TNativeView` o `TNativeElement` parámetro.

## <a name="hello-custom-renderers"></a>Hola, los representadores personalizados

El [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programa hace referencia a una vista personalizada denominada `HelloView` en su [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) clase.

El [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) clase se incluye en el **HelloRenderers** del proyecto y se deriva simplemente `View`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) clase en el **HelloRenderers.iOS** proyecto se deriva de `ViewRenderer<HelloView, UILabel>`. En el `OnElementChanged` invalidación, crea una nativas para iOS `UILabel` y llama a `SetNativeControl`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) clase en el **HelloRenderers.Droid** proyecto se deriva de `ViewRenderer<HelloView, TextView>`. En el `OnElementChanged` invalidación, crea un Android `TextView` y llama a `SetNativeControl`.

El [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) clase en el **HelloRenderers.UWP** y otros proyectos de Windows se deriva de `ViewRenderer<HelloView, TextBlock>`. En el `OnElementChanged` invalidación, crea un Windows `TextBlock` y llama a `SetNativeControl`.

Todos los `ViewRenderer` derivados contienen un `ExportRenderer` atributo en el nivel de ensamblado que se asocia el `HelloView` clase con la instancia concreta `HelloViewRenderer` clase. Se trata cómo Xamarin.Forms ubica a los representadores en los proyectos de plataforma individuales:

[![Captura de pantalla de vista Hello triple](images/ch27fg02-small.png "representadores personalizados")](images/ch27fg02-large.png#lightbox "representadores personalizados")

## <a name="renderers-and-properties"></a>Los representadores y propiedades

El siguiente conjunto de representadores implementa el dibujo de la elipse y se encuentra en los distintos proyectos de la [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución.

El [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) clase está en el **Xamarin.FormsBook.Platform** plataforma. Es similar a la clase `BoxView` y define una propiedad de solo: `Color` de tipo `Color`.

Los representadores pueden transferir valores de propiedad establecidos en un `View` al objeto nativo invalidando el `OnElementPropertyChanged` método en el representador. Dentro de este método (y en la mayoría del procesador), existen dos propiedades:

- `Element`, el elemento de Xamarin.Forms
- `Control`, la vista nativo o el objeto de widget o de control

Los tipos de estas propiedades dependen de los parámetros genéricos a `ViewRenderer`. En este ejemplo, `Element` es de tipo `EllipseView`.

El `OnElementPropertyChanged` invalidación, por tanto, puede transferir la `Color` valor de la `Element` nativo `Control` objeto, probablemente con algún tipo de conversión. Los representadores de tres son:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que usa un [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) clase de la elipse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que usa un [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) clase de la elipse.
- UWP: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que puede usar el Windows nativo [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) clase.

El [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) clase muestra unas cuantas `EllipseView` objetos:

[![Captura de pantalla triple de demostración de elipse](images/ch27fg03-small.png "representadores personalizados de EllipseView")](images/ch27fg03-large.png#lightbox "representadores personalizados de EllipseView")

El [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebota un `EllipseView` desactivado los lados de la pantalla.

## <a name="renderers-and-events"></a>Los representadores y eventos

También es posible que los representadores indirectamente generar eventos. El [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) clase es similar a la normal Xamarin.Forms `Slider` pero permite especificar un número de pasos discretos entre el `Minimum` y `Maximum` valores.

Los representadores de tres son:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Los representadores de detectar cambios en el control nativo y, a continuación, llame a `SetValueFromRenderer`, que hace referencia a una propiedad enlazable definida en el `StepSlider`, un cambio en lo que hace que el `StepSlider` para desencadenar un `ValueChanged` eventos.

El [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) muestra este control deslizante de nuevo.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 27 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capítulo 27 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
