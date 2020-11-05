---
title: Controls Reference (Referencia de controles)
description: Una descripción de todos los elementos de la interfaz de usuario que se usan para crear una Xamarin.Forms aplicación. En este artículo se enumeran los grupos de controles que componen la interfaz de usuario de una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 90d97e690c4d1fcdb22f17bbc856f9f9b2d0900c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374659"
---
# <a name="controls-reference"></a>Controls Reference (Referencia de controles)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/formsgallery/)

La interfaz de usuario de una Xamarin.Forms aplicación se construye de objetos que se asignan a los controles nativos de cada plataforma de destino. Esto permite a las aplicaciones específicas de la plataforma iOS, Android y el Plataforma universal de Windows usar el Xamarin.Forms código contenido en una [biblioteca de .net Standard](~/cross-platform/app-fundamentals/net-standard.md).

Los cuatro grupos de control principales que se usan para crear la interfaz de usuario de una Xamarin.Forms aplicación son los siguientes:

- [**Páginas**](pages.md)
- [**Diseños**](layouts.md)
- [**Vistas**](views.md)
- [**Celdas**](cells.md)

Una Xamarin.Forms Página suele ocupar toda la pantalla. La página contiene normalmente un diseño, que contiene vistas y posiblemente otros diseños. Las celdas son componentes especializados que se usan en la conexión con [`TableView`](xref:Xamarin.Forms.TableView) y [`ListView`](xref:Xamarin.Forms.ListView) . En la Xamarin.Forms [ Xamarin.Forms jerarquía de clases de controles](~/xamarin-forms/internals/class-hierarchy.md)se puede encontrar un diagrama de clases que muestre la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en.

En los cuatro artículos de [**páginas**](pages.md), [**diseños**](layouts.md), [**vistas**](views.md)y [**celdas**](cells.md), cada tipo de control se describe con vínculos a la documentación de la API, un artículo que describe su uso (si existe) y uno o varios programas de ejemplo (si existen). Cada tipo de control también está acompañado por una captura de pantalla que muestra una página del ejemplo [**FormsGallery**](/samples/xamarin/xamarin-forms-samples/formsgallery) que se ejecuta en dispositivos iOS y Android. Debajo de cada captura de pantalla hay vínculos al código fuente de la página de C#, la página XAML equivalente y, si procede, el archivo de código subyacente de C# para la página XAML.

> [!NOTE]
> Las páginas, los diseños y las vistas derivan de la `VisualElement` clase. La `VisualElement` clase proporciona diversas propiedades, métodos y eventos que son útiles para derivar clases. Para obtener más información, vea [propiedades, métodos y eventos de VisualElement](common-properties.md).

Además de los controles proporcionados con Xamarin.Forms , hay disponibles controles de terceros. Para obtener más información, vea [controles de terceros](thirdparty.md).

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms Ejemplo de FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Jerarquía de clases de controles](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentación de la API](/dotnet/api/xamarin.forms?view=xamarin-forms)