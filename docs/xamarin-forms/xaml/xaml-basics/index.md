---
title: Conceptos básicos de XAML de Xamarin.Forms
description: Esta guía explica cómo empezar a trabajar con XAML y multiplataforma para dispositivos móviles. XAML permite a los desarrolladores definir interfaces de usuario en aplicaciones de Xamarin.Forms mediante marcado en lugar de código.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 7d46744c20874dd122b63e56ec92c7aa5ae6562e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656990"
---
# <a name="xamarinforms-xaml-basics"></a>Conceptos básicos de XAML de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

XAML (lenguaje de marcado de aplicaciones extensible) permite a los desarrolladores definir interfaces de usuario en Xamarin.Forms utilizando el marcado en lugar de código. XAML nunca es necesario en un programa de Xamarin.Forms, pero suele ser más concisas y visualmente más coherente que el código equivalente y potencialmente dispone de herramientas. XAML es especialmente adecuado para su uso con la popular arquitectura de aplicación MVVM (Model-View-ViewModel): XAML define la vista que está vinculada al código ViewModel a través de enlaces de datos basados en XAML.

## <a name="xaml-basics-contents"></a>Contenido de conceptos básicos XAML

* [Información general](#Overview)
* [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Además de estos artículos aspectos básicos de XAML, puede descargar capítulos del libro [Creating Mobile Apps with Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Cubierta de libro")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

Se tratan los temas XAML con más detalle en muchos de los capítulos del libro, incluidos:

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>Capítulo 7. Frente a XAML. Código</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">Descargar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 8. Código y XAML en armonía</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">Descargar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 10. Extensiones de marcado XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">Descargar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 18. MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">Descargar PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">Resumen</a></td></tr>
</table>

Estos capítulos pueden ser [descargar gratuitamente](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Información general

XAML es un lenguaje basado en XML creado por Microsoft como una alternativa al código de programación para crear instancias e inicializar objetos y organizar los objetos en jerarquías de elementos primarios y secundarios. XAML se haya adaptado a diversas tecnologías en .NET framework, pero su mayor utilidad se encuentra en la definición del diseño de interfaces de usuario dentro de Windows Presentation Foundation (WPF), Silverlight, el tiempo de ejecución de Windows y el Windows Universal Plataforma (UWP).

XAML también forma parte de Xamarin.Forms, el desarrollo multiplataforma basada en forma nativa interfaz de programación para iOS, Android y UWP dispositivos móviles. En el archivo XAML, el desarrollador de Xamarin.Forms puede definir utilizando todas las Xamarin.Forms vistas, diseños y las páginas, como las clases personalizadas así como de las interfaces de usuario. El archivo XAML se puede compilar o incrustado en el archivo ejecutable. En cualquier caso, la información XAML se analiza en tiempo de compilación para localizar objetos con nombre y otra en tiempo de ejecución para crear una instancia e inicializar objetos y establecer vínculos entre estos objetos y el código de programación.

XAML tiene varias ventajas con respecto al código equivalente:

-  XAML suele ser más concisas y legibles que el código equivalente.
-  La jerarquía de elementos primarios y secundarios inherente en XML permite XAML imitar con mayor claridad visual de la jerarquía de elementos primarios y secundarios de objetos de interfaz de usuario.
-  XAML puede ser fácilmente escritos a mano por los programadores, pero también se presta para ser generado por las herramientas de diseño visual y dispone de herramientas.

Por supuesto, también hay inconvenientes, relacionadas principalmente con las limitaciones que son intrínsecas de lenguajes de marcado:

-  XAML no puede contener código. Todos los controladores de eventos deben definirse en un archivo de código.
-  XAML no puede contener bucles para su procesamiento repetitivo. (Sin embargo, varios objetos visuales de Xamarin.Forms, sobre todo [ `ListView` ](xref:Xamarin.Forms.ListView) , puede generar varios elementos secundarios basados en los objetos en su `ItemsSource` colección.)
-  XAML no puede contener un procesamiento condicional (sin embargo, un enlace de datos puede hacer referencia a un conversor de enlaces en código que permite un procesamiento condicional de manera eficaz.)
-  Por lo general XAML no puede crear instancias de clases que no definen un constructor sin parámetros. (Sin embargo, hay algunas veces una manera de evitar esta restricción).
-  Por lo general, XAML no puede llamar a métodos. (De nuevo, esta restricción a veces se puede resolver.)

Aún no tiene un diseñador visual para generar XAML en las aplicaciones de Xamarin.Forms. Todos los XAML debe ser escrito a mano, pero hay un [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). Los programadores nuevos para XAML podría compilar y ejecutar sus aplicaciones, especialmente después de todo lo que puede no ser correcta obviamente con frecuencia. Incluso los desarrolladores con mucha experiencia en XAML sepa que es un regalo para experimentación.

Básicamente, XAML es XML, pero XAML tiene algunas características de sintaxis única. Las más importantes son:

- Elementos de propiedad
- Propiedades adjuntas
- Extensiones de marcado

Estas características son *no* extensiones XML. XAML es XML completamente válido. Pero estas características de sintaxis XAML utilizan XML de manera exclusiva. Se describen en detalle en los artículos siguientes, que concluye con una introducción al uso de XAML para implementar MVVM.

## <a name="requirements"></a>Requisitos

En este artículo se da por supuesto familiarizado con Xamarin.Forms. En este artículo también se da por supuesto un poco familiarizado con XML, incluyendo la descripción del uso de las declaraciones de espacio de nombres XML y los términos *elemento*, *etiqueta*, y *atributo*.

Cuando esté familiarizado con XML y Xamarin.Forms, comience a leer [parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Creación de libro de Mobile Apps](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
