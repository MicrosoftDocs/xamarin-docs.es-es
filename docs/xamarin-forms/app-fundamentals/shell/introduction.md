---
title: Introducción a Xamarin.Forms Shell
description: Xamarin.Forms Shell proporciona las características fundamentales que necesitan la mayoría de las aplicaciones, como una experiencia de usuario común de navegación, un esquema de navegación basado en URI y un controlador de búsqueda integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1863481dface7e215764ea4673f2d18ece4e2a87
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757677"
---
# <a name="xamarinforms-shell-introduction"></a>Introducción a Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles, como por ejemplo:

- Un único lugar para describir la jerarquía visual de una aplicación.
- Una interfaz de usuario de navegación común.
- Un esquema de navegación basado en URI que permite la navegación a cualquier página de la aplicación.
- Un controlador de búsqueda integrado.

Además, las aplicaciones de Shell se benefician de una mayor velocidad de representación y un consumo reducido de memoria.

> [!IMPORTANT]
> Las aplicaciones existentes pueden adoptar Shell y aprovechar inmediatamente las mejoras de navegación, rendimiento y extensibilidad.

## <a name="application-visual-hierarchy"></a>Jerarquía visual de la aplicación

En una aplicación de Xamarin.Forms Shell, la jerarquía visual de la aplicación se describe en una clase que genera subclases de la clase [`Shell`](xref:Xamarin.Forms.Shell). Esta clase puede constar de tres objetos jerárquicos principales:

1. [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) o [`TabBar`](xref:Xamarin.Forms.TabBar). Un objeto `FlyoutItem` representa uno o varios elementos en el control flotante y debe usarse cuando el modelo de navegación de la aplicación incluye un control flotante. Un objeto `TabBar` representa la barra de pestañas inferior y debe usarse cuando el modelo de navegación de la aplicación comienza con pestañas en la parte inferior y no requiere control flotante.
1. [`Tab`](xref:Xamarin.Forms.Tab), que representa contenido agrupado, navegable mediante pestañas en la parte inferior.
1. [`ShellContent`](xref:Xamarin.Forms.ShellContent), que representa los objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) de cada pestaña.

Estos objetos no representan ninguna interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación del contenido.

> [!NOTE]
> En una aplicación de Shell, las páginas se crean a petición en respuesta a la navegación.

Para obtener más información, consulte [Creación de una aplicación de Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/create.md).

## <a name="navigation-user-experience"></a>Experiencia del usuario de navegación

La experiencia de navegación proporcionada por Xamarin.Forms Shell se basa en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante o una barra de pestañas inferior, según los requisitos de navegación de la aplicación. El ejemplo siguiente muestra una aplicación donde el nivel superior de navegación es un control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](introduction-images/flyout.png)](introduction-images/flyout-large.png#lightbox)

En este ejemplo, algunos elementos de control flotante se duplican como elementos de barra de pestañas. Aunque también hay elementos a los que solo se puede tener acceso desde el control flotante. Al seleccionar un elemento de control flotante, se selecciona y muestra la pestaña inferior que representa el elemento:

[![Captura de pantalla de pestañas en la parte inferior de Shell en iOS y Android](introduction-images/cats.png)](introduction-images/cats-large.png#lightbox)

> [!NOTE]
> Cuando el control flotante no está abierto, la barra de pestañas inferior se puede considerar el nivel superior de navegación en la aplicación.

Cada pestaña de la barra de pestañas muestra un [`ContentPage`](xref:Xamarin.Forms.ContentPage). Sin embargo, si una pestaña inferior contiene más de una página, las páginas son navegables mediante la barra de pestañas superior:

[![Captura de pantalla de pestañas superiores de Shell en iOS y Android](introduction-images/dogs.png)](introduction-images/dogs-large.png#lightbox)

En cada pestaña, se puede navegar por objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) adicionales que se conocen como páginas de detalles:

[![Captura de pantalla de navegación de páginas de Shell en iOS y Android](introduction-images/dogdetails.png)](introduction-images/dogdetails-large.png#lightbox)

Shell incluye una experiencia de navegación basada en URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida. También ofrece la posibilidad de navegar hacia atrás sin tener que visitar todas las páginas de la pila de navegación. Para obtener más información, consulte [Navegación en Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="search"></a>Search

Xamarin.Forms Shell incluye la funcionalidad de búsqueda integrada que proporciona la clase [`SearchHandler`](xref:Xamarin.Forms.SearchHandler). Se puede agregar funcionalidad de búsqueda a una página agregando un objeto `SearchHandler` con subclases a esta. Esto se traduce en que se agrega un cuadro de búsqueda en la parte superior de la página. Cuando se escriben datos en el cuadro de búsqueda, el área de sugerencias de búsqueda se rellena con datos:

[![Captura de pantalla de la búsqueda de Shell en iOS y Android](introduction-images/search.png)](introduction-images/search-large.png#lightbox)

A continuación, cuando se selecciona un resultado en el área de sugerencias de búsqueda, se puede ejecutar la lógica personalizada; por ejemplo, navegar a una página de detalles.

Para obtener más información, consulte [Búsqueda en Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/search.md).

## <a name="platform-support"></a>Compatibilidad con la plataforma

Xamarin.Forms Shell está totalmente disponible en iOS y Android, pero solo parcialmente disponible en la Plataforma universal de Windows (UWP). Además, Shell es actualmente experimental en UWP y solo se puede usar agregando la siguiente línea de código a la clase `App` en el proyecto de UWP, antes de llamar a `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Para obtener más información sobre el estado de Shell en UWP, vea el [Panel de proyectos de Shell de Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/projects/54) en github.com.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Creación de una aplicación de Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/create.md)
- [Navegación en Shell Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md)
- [Búsqueda enXamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/search.md)
