---
title: Visual Basic y .NET Standard
description: En esta guía se explica cómo se pueden usar Visual Basic para escribir proyectos de .NET Standard que se pueden usar en soluciones destinadas a Xamarin. iOS y Xamarin. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 8ccf96e266f9d5eae69a178cfcad3d1e48fb6962
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457540"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic y .NET Standard

Los proyectos de Xamarin Android e iOS no admiten Visual Basic de forma nativa; sin embargo, los desarrolladores pueden usar bibliotecas de [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) para migrar código de Visual Basic existente a Android e iOS, o para escribir una parte significativa de la lógica de la aplicación en Visual Basic. Las aplicaciones de Xamarin. Forms se pueden crear completamente en Visual Basic (excluidos los representadores personalizados, los servicios de dependencia y el código XAML subyacente).

## <a name="requirements"></a>Requisitos

Para crear y compilar Visual Basic bibliotecas de .NET Standard debe usar Visual Studio en Windows (Visual Studio 2017 o posterior).

> [!NOTE]
> Las bibliotecas de Visual Basic solo se pueden crear y compilar con Visual Studio. Xamarin. Android y Xamarin. iOS no admiten el lenguaje Visual Basic.
>
> Si trabaja únicamente en Visual Studio, puede hacer referencia al proyecto de Visual Basic de proyectos de Xamarin. Android y Xamarin. iOS.
>
> Si los proyectos de Android e iOS también se deben cargar en Visual Studio para Mac debe hacer referencia al ensamblado de salida del ensamblado de Visual Basic.

## <a name="creating-a-visual-basicnet-net-standard-library"></a>Creación de una biblioteca de .NET Standard de Visual Basic.NET

En esta sección se explica cómo crear una biblioteca de .NET Standard de Visual Basic con Visual Studio 2019.
A continuación, se puede hacer referencia a la biblioteca en otros proyectos, incluidas las aplicaciones Xamarin. Android, Xamarin. iOS y Xamarin. Forms.

Al agregar una biblioteca de .NET Standard de Visual Basic en Visual Studio, debe tener cuidado de elegir el tipo de proyecto correcto:

1. En Visual Studio 2019, elija **crear un nuevo proyecto**.

2. Escriba **Visual Basic biblioteca** para filtrar las opciones del proyecto y elija la opción **biblioteca de clases (.net Standard)** con el icono de Visual Basic:

    [![Filtrar por biblioteca de Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. En la siguiente pantalla, escriba un nombre para el proyecto y presione **crear**.

4. El proyecto Visual Basic aparecerá como se muestra en la  **Explorador de soluciones** de la siguiente manera:

    [![Proyecto de Visual Basic vacío](images/new-library-sml.png)](images/new-library.png#lightbox)

El proyecto ya está listo para agregar Visual Basic código. Otros proyectos (proyectos de aplicación o proyectos de biblioteca) pueden hacer referencia a proyectos de .NET Standard.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar el código Visual Basic en aplicaciones de Xamarin con Visual Studio. Aunque Xamarin no admite Visual Basic directamente, compilar Visual Basic en una biblioteca de .NET Standard permite incluir código escrito con Visual Basic en aplicaciones Android e iOS.

En las páginas siguientes se describe cómo usar las bibliotecas de .NET Standard de Visual Basic.NET en aplicaciones nativas o de Xamarin. Forms:

- [Compilación de aplicaciones de Xamarin. iOS y Xamarin. Android nativas que usan VB](native-apps.md)
- [Compilar aplicaciones de Xamarin. Forms con VB](xamarin-forms.md)

## <a name="related-links"></a>Vínculos relacionados

- [TaskyVB (ejemplo)](/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (ejemplo)](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard y Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)