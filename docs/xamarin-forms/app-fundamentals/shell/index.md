---
title: Xamarin.Forms Shell
description: En esta guía se explica cómo usar Xamarin.Forms Shell, que reduce la complejidad de las aplicaciones de Xamarin.Forms al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0acf00d85c2bfb823ec1cfba099179bb6743117c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138754"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introduction"></a>[Introducción](introduction.md)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones móviles. Esto incluye una experiencia de usuario de navegación común, un esquema de navegación basado en URI y un controlador de búsqueda integrada.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Creación de una aplicación de Xamarin.Forms Shell](create.md)

El proceso para crear una aplicación de Xamarin.Forms Shell consiste en crear un archivo XAML que sirva de subclase de la clase `Shell`, establecer la propiedad `MainPage` de la clase `App` de la aplicación en el objeto `Shell` con subclases y, después, describir la jerarquía visual de la aplicación en la clase `Shell` con subclases.

## <a name="flyout"></a>[Control flotante](flyout.md)

El control flotante es el menú raíz de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lado de la pantalla. El control flotante consta de un encabezado opcional, elementos de control flotante y elementos de menú opcionales.

## <a name="tabs"></a>[Pestañas](tabs.md)

Después de un control flotante, el siguiente nivel de navegación en una aplicación de Shell es la barra de pestañas de la parte inferior. Como alternativa, el modelo de navegación para una aplicación puede comenzar con pestañas en la parte inferior y no usar un control flotante. En ambos casos, cuando una pestaña inferior contiene más de una página, las páginas son navegables mediante las pestañas principales.

## <a name="page-configuration"></a>[Configuración de la página](configuration.md)

La clase `Shell` define las propiedades adjuntas que se pueden usar para configurar la apariencia de las páginas en las aplicaciones de Xamarin.Forms Shell. Esto incluye establecer los colores de la página, deshabilitar la barra de navegación y la barra de pestañas y mostrar las vistas en la barra de navegación.

## <a name="navigation"></a>[Navegación](navigation.md)

Las aplicaciones de Shell pueden usar un esquema de navegación basado en URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida.

## <a name="search"></a>[Búsqueda](search.md)

Las aplicaciones de Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar a la parte superior de cada página.

## <a name="lifecycle"></a>[Ciclo de vida](lifecycle.md)

Las aplicaciones del shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento `Appearing` si una página está a punto de aparecer en la pantalla y se genera un evento `Disappearing` si una página está a punto de desaparecer de la pantalla.

## <a name="custom-renderers"></a>[Representadores personalizados](customrenderers.md)

Las aplicaciones de Shell son muy personalizables mediante las propiedades y los métodos que exponen las distintas clases de Shell. Sin embargo, también es posible crear a un representador personalizado de Shell cuando se requieren personalizaciones más sofisticadas específicas de la plataforma.
