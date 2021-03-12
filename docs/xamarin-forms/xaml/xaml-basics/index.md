---
title: Xamarin.Forms Conceptos básicos de XAML
description: En esta guía se explica cómo empezar a trabajar con XAML multiplataforma para dispositivos móviles. XAML permite a los desarrolladores definir interfaces de usuario en Xamarin.Forms aplicaciones con marcado en lugar de código.
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e269a56daa7517b2b3deb3808d7eb73da825ffac
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603066"
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.Forms Conceptos básicos de XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/xamlsamples)

El lenguaje de marcado de aplicaciones extensible (XAML) es un lenguaje basado en XML creado por Microsoft como una alternativa a código de programación para la creación de instancias e inicialización de objetos, y la organización de esos objetos en jerarquías de elementos primarios y secundarios. XAML se ha adaptado a varias tecnologías de .NET Framework, pero ha encontrado su utilidad más importante para definir el diseño de las interfaces de usuario en el Windows Presentation Foundation (WPF), Silverlight, el Windows Runtime y el Plataforma universal de Windows (UWP).

XAML permite a los desarrolladores definir interfaces de usuario en Xamarin.Forms aplicaciones con marcado en lugar de código. XAML no se requiere nunca en un Xamarin.Forms programa, pero suele ser más conciso y visualmente coherente que el código equivalente, y que puede ser de utilidad. XAML está bien adaptado para su uso con la popular arquitectura de aplicación MVVM (modelo-vista-ViewModel): XAML define la vista que está vinculada al código ViewModel a través de enlaces de datos basados en XAML.

Dentro de un archivo XAML, el Xamarin.Forms desarrollador puede definir interfaces de usuario mediante todas las Xamarin.Forms vistas, diseños y páginas, así como clases personalizadas. El archivo XAML se puede compilar o incrustar en el ejecutable. En cualquier caso, la información XAML se analiza en tiempo de compilación para buscar objetos con nombre y de nuevo en tiempo de ejecución para crear instancias e inicializar objetos, y para establecer vínculos entre estos objetos y el código de programación.

XAML tiene varias ventajas con respecto al código equivalente:

- XAML suele ser más conciso y legible que el código equivalente.
- La jerarquía de elementos primarios y secundarios inherente en XML permite que XAML imite con mayor claridad visual de la jerarquía de elementos primarios y secundarios de los objetos de la interfaz de usuario.
- El código XAML puede escribirse fácilmente a los programadores, pero también se presta a ser herramienta y generarse mediante herramientas de diseño visual.

También hay desventajas, principalmente relacionadas con las limitaciones que son intrínsecas a los lenguajes de marcado:

- XAML no puede contener código. Todos los controladores de eventos se deben definir en un archivo de código.
- XAML no puede contener bucles para el procesamiento repetitivo. (Sin embargo, varios Xamarin.Forms objetos visuales, en concreto,  [`ListView`](xref:Xamarin.Forms.ListView) pueden generar varios elementos secundarios basados en los objetos de su `ItemsSource` colección).
- XAML no puede contener procesamiento condicional (sin embargo, un enlace de datos puede hacer referencia a un convertidor de enlace basado en código que permita de forma eficaz algún procesamiento condicional).
- Por lo general, XAML no puede crear instancias de clases que no definen un constructor sin parámetros. (Sin embargo, a veces hay una forma de evitar esta restricción).
- Normalmente, XAML no puede llamar a métodos. (De nuevo, esta restricción a veces se puede solucionar).

Todavía no hay un diseñador visual para generar XAML en Xamarin.Forms aplicaciones. Todo el código XAML debe estar escrito a mano, pero puede usar [recarga en caliente (~/Xamarin-Forms/XAML/Hot-Reload/index.MD) de XAML en Visual Studio 2019 o Visual Studio para Mac para ver los diseños de pantalla a medida que los edita. Incluso los desarrolladores con mucha experiencia en XAML saben que la experimentación es gratificante.

XAML es básicamente XML, pero XAML tiene algunas características de sintaxis únicas. Las más importantes son las siguientes:

- Elementos de propiedad
- Propiedades asociadas
- Extensiones de marcado

Estas características *no* son extensiones XML. XAML es completamente XML legal. Pero estas características de sintaxis XAML usan XML de maneras únicas. Se describen en detalle en los artículos siguientes, que concluyen con una introducción al uso de XAML para implementar MVVM.

## <a name="requirements"></a>Requisitos

En este artículo se supone que está familiarizado con Xamarin.Forms . En este artículo también se da por hecho que está familiarizado con XML, incluida la descripción del uso de las declaraciones de espacios de nombres XML y el *elemento* de términos, la *etiqueta* y el *atributo*.

Cuando esté familiarizado con Xamarin.Forms y XML, empiece a leer la [parte 1. Introducción con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Crear Mobile Apps libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Ejemplos de Xamarin.Forms](/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]