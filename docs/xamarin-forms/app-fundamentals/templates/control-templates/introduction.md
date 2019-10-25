---
title: Introducción a las plantillas de control de Xamarin.Forms
description: Las plantillas de control de Xamarin.Forms permiten crear y cambiar fácilmente el tema de las páginas de la aplicación en tiempo de ejecución. En este artículo se proporciona una introducción a las plantillas de control.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 70646999154297592137c6966626b318fb73897c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70771266"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introducción a las plantillas de control de Xamarin.Forms

_Las plantillas de control de Xamarin.Forms permiten crear y cambiar fácilmente el tema de las páginas de la aplicación en tiempo de ejecución. En este artículo se proporciona una introducción a las plantillas de control._

Los controles tienen propiedades diferentes, como `BackgroundColor` y `TextColor`, que pueden definir los aspectos de la apariencia del control. Estas propiedades se pueden establecer mediante [estilos](~/xamarin-forms/user-interface/styles/index.md), que se pueden cambiar en tiempo de ejecución para implementar la creación de temas básicos. Pero los estilos no mantienen una separación clara entre la apariencia de una página y su contenido, y los cambios que se pueden realizar mediante el establecimiento de estas propiedades son limitados.

Las plantillas de control proporcionan una separación clara entre el aspecto de una página y su contenido, lo que permite crear páginas a las que se les puede aplicar temas fácilmente. Por ejemplo, una aplicación puede contener plantillas de control de nivel de aplicación que proporcionan un tema oscuro y un tema claro. Se pueden crear temas de todos los elementos [`ContentPage`](xref:Xamarin.Forms.ContentPage) de la aplicación si se aplica una de las plantillas de control sin cambiar el contenido mostrado por cada página. Además, los temas que proporcionan las plantillas de control no están limitados al cambio de las propiedades de los controles. También pueden cambiar los controles que se usan para implementar el tema.

## <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

Un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) especifica la apariencia de una página o vista, y contiene un diseño raíz que incluye los controles que implementan la plantilla. Normalmente, un elemento `ControlTemplate` usará un elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) para marcar dónde va a aparecer el contenido que la página o vista va a mostrar. Después, la página o vista que consume el elemento `ControlTemplate` definirá el contenido que `ContentPresenter` va a mostrar. En el diagrama siguiente se ilustra un elemento `ControlTemplate` para una página que contiene una serie controles, incluido un control `ContentPresenter` marcado por un rectángulo de color azul:

![](introduction-images/control-template.png "Control Template for a Page")

Se puede aplicar [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a los tipos siguientes si se establecen sus propiedades `ControlTemplate`:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Cuando se crea un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) y se asigna a estos tipos, cualquier apariencia existente se reemplaza con la que se define en el elemento `ControlTemplate`. Además de establecer la apariencia con la propiedad `ControlTemplate`, también se pueden aplicar plantillas de control mediante estilos para ampliar más las funciones del tema.

> [!NOTE]
> *¿Qué son los tipos `TemplatedPage` y `TemplatedView`?* `TemplatedPage` es la clase base para `ContentPage` y es el tipo de página más básico proporcionado por Xamarin.Forms. A diferencia de `ContentPage`, `TemplatedPage` no tiene una propiedad `Content`. Por tanto, no se puede agregar contenido directamente a una instancia de `TemplatedPage`. En su lugar, el contenido se agrega mediante el establecimiento de la plantilla de control para la instancia de `TemplatedPage`. De forma similar, `TemplatedView` es la clase base para `ContentView`. A diferencia de `ContentView`, `TemplatedView` no tiene una propiedad `Content`. Por tanto, no se puede agregar contenido directamente a una instancia de `TemplatedView`. En su lugar, el contenido se agrega mediante el establecimiento de la plantilla de control para la instancia de `TemplatedView`.

Las plantillas de control se pueden crear en XAML y en C#:

- Las plantillas de control creadas en XAML se definen en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que se asigna a la colección [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de una página, o bien generalmente a la colección [`Resources`](xref:Xamarin.Forms.Application.Resources) de la aplicación.
- Las plantillas de control creadas en C# se suelen definir en la clase de página, o bien en una clase a la que se puede acceder de forma global.

La elección de dónde se puede definir una instancia de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) afecta a dónde se puede usar:

- Las instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definidas en el nivel de página solo se pueden aplicar a la página.
- Las instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) definidas en el nivel de aplicación se pueden aplicar a las páginas de toda la aplicación.

Las plantillas de control situadas más abajo en la jerarquía de vistas tienen prioridad sobre las definidas más arriba. Por ejemplo, un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominado `DarkTheme` que se define en el nivel de página tendrá prioridad sobre una plantilla con el mismo nombre definida en el nivel de aplicación. Por tanto, una plantilla de control que defina un tema que se va a aplicar a cada página de una aplicación se debe definir en el nivel de aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
