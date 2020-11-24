---
title: Introducción a Xamarin.Forms Shell
description: Xamarin.Forms Shell proporciona las características fundamentales que necesitan la mayoría de las aplicaciones, como una experiencia de usuario común de navegación, un esquema de navegación basado en URI y un controlador de búsqueda integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 215bf467c6f00e45d3e00e10438b01d238050504
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590329"
---
# <a name="no-locxamarinforms-shell-introduction"></a>Introducción a Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles, como por ejemplo:

- Un único lugar para describir la jerarquía visual de una aplicación.
- Una interfaz de usuario de navegación común.
- Un esquema de navegación basado en URI que permite la navegación a cualquier página de la aplicación.
- Un controlador de búsqueda integrado.

Además, las aplicaciones de Shell se benefician de una mayor velocidad de representación y un consumo reducido de memoria.

> [!IMPORTANT]
> Las aplicaciones existentes pueden adoptar Shell y aprovechar inmediatamente las mejoras de navegación, rendimiento y extensibilidad.

## <a name="platform-support"></a>Compatibilidad con la plataforma

Xamarin.Forms Shell está totalmente disponible en iOS y Android, pero solo parcialmente disponible en la Plataforma universal de Windows (UWP). Además, Shell es actualmente experimental en UWP y solo se puede usar agregando la siguiente línea de código a la clase `App` en el proyecto de UWP, antes de llamar a `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Para obtener más información sobre el estado de Shell en UWP, vea el [Panel de proyectos de Shell de Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/projects/54) en github.com.

## <a name="shell-navigation-experience"></a>Experiencia de navegación de Shell

Shell ofrece una experiencia de navegación bien fundamentada basada en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante o una barra de pestañas inferior, según los requisitos de navegación de la aplicación. El ejemplo siguiente muestra una aplicación donde el nivel superior de navegación es un control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](introduction-images/flyout.png "Control flotante de Shell")](introduction-images/flyout-large.png#lightbox "Control flotante de Shell")

Al seleccionar un elemento de control flotante, se selecciona y muestra la pestaña inferior que representa el elemento:

[![Captura de pantalla de pestañas inferiores de Shell en iOS y Android](introduction-images/monkeys.png "Pestañas inferiores de Shell")](introduction-images/monkeys-large.png#lightbox "Pestañas inferiores de Shell")

> [!NOTE]
> Cuando el control flotante no está abierto, la barra de pestañas inferior se puede considerar el nivel superior de navegación en la aplicación.

Cada pestaña muestra un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Sin embargo, si una pestaña inferior contiene más de una página, las páginas son navegables mediante la barra de pestañas superior:

[![Captura de pantalla de pestañas superiores de Shell en iOS y Android](introduction-images/cats.png "Pestañas superiores de Shell")](introduction-images/cats-large.png#lightbox "Pestañas superiores de Shell")

Dentro de cada pestaña, se puede navegar a objetos adicionales [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Captura de pantalla de navegación de páginas de Shell en iOS y Android](introduction-images/cat-details.png "Navegación por aplicaciones de Shell")](introduction-images/cat-details-large.png#lightbox "Navegación por aplicaciones de Shell")

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
