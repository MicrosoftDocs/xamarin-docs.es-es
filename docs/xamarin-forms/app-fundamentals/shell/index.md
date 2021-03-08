---
title: Xamarin.Forms Shell
description: En esta guía se explica cómo usar Xamarin.Forms Shell, que reduce la complejidad de las aplicaciones de Xamarin.Forms al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76598f50199eb88b809b1d63e49885b750cf3e07
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757574"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introduction"></a>[Introducción](introduction.md)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones móviles. Esto incluye una experiencia de usuario de navegación común, un esquema de navegación basado en URI y un controlador de búsqueda integrada.

## <a name="create-a-xamarinforms-shell-application"></a>[Creación de una aplicación de Xamarin.Forms Shell](create.md)

El proceso para crear una aplicación de Xamarin.Forms Shell consiste en crear un archivo XAML que sirva de subclase de la clase [`Shell`](xref:Xamarin.Forms.Shell), establecer la propiedad `MainPage` de la clase `App` de la aplicación en el objeto `Shell` con subclases y, después, describir la jerarquía visual de la aplicación en la clase `Shell` con subclases.

## <a name="flyout"></a>[control flotante](flyout.md)

Un control flotante es el menú raíz opcional de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lateral de la pantalla. El control flotante consta de un encabezado opcional, elementos de control flotante, elementos de menú opcionales y un pie de página opcional.

## <a name="tabs"></a>[Pestañas](tabs.md)

Después de un control flotante, el siguiente nivel de navegación en una aplicación de Shell es la barra de pestañas de la parte inferior. Como alternativa, el modelo de navegación para una aplicación puede comenzar con pestañas en la parte inferior y no usar un control flotante. En ambos casos, cuando una pestaña inferior contiene más de una página, las páginas son navegables mediante las pestañas principales.

## <a name="pages"></a>[Páginas](pages.md)

Un objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) representa el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) correspondiente a cada [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o [`Tab`](xref:Xamarin.Forms.Tab).

## <a name="navigation"></a>[Navegación](navigation.md)

Las aplicaciones de Shell pueden usar un esquema de navegación basado en URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida.

## <a name="search"></a>[Búsqueda](search.md)

Las aplicaciones de Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar a la parte superior de cada página.

## <a name="lifecycle"></a>[Ciclo de vida](lifecycle.md)

Las aplicaciones de Shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento `Appearing` si una página está a punto de aparecer en la pantalla y un evento `Disappearing` si una página está a punto de desaparecer de la pantalla.

## <a name="custom-renderers"></a>[Representadores personalizados](customrenderers.md)

Las aplicaciones de Shell son muy personalizables mediante las propiedades y los métodos que exponen las distintas clases de Shell. Aun así, también es posible crear un representador personalizado de Shell cuando se requieren personalizaciones más sofisticadas específicas de la plataforma.
