---
title: Imágenes e iconos en Xamarin. iOS
description: En esta sección se incluye una variedad de artículos que cubren el trabajo con imágenes en una aplicación de Xamarin. iOS, como su uso como iconos, iniciar pantallas o incluirlas en controles y proporcionar iconos para tipos de documentos personalizados.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 5ef7d08242702143de667803b76de7325fca1b82
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602338"
---
# <a name="images-and-icons-in-xamarinios"></a>Imágenes e iconos en Xamarin. iOS

_En esta sección se incluye una variedad de artículos que cubren el trabajo con imágenes en una aplicación de Xamarin. iOS, como su uso como iconos, iniciar pantallas o incluirlas en controles y proporcionar iconos para tipos de documentos personalizados._

Hay varias maneras de usar recursos de imagen dentro de una aplicación iOS. Desde simplemente mostrar una imagen como parte de la interfaz de usuario de una aplicación a, asignarla a un control de IU como `UIButton` o `UIImageView` , para proporcionar iconos y pantallas de inicio, Xamarin. iOS facilita la tarea de agregar material gráfico de gran calidad a una aplicación de iOS de las siguientes maneras: 

- **Imágenes independientes** de la resolución: Use la compatibilidad integrada de iOS para trabajar con imágenes en diferentes tipos y resoluciones de dispositivos (iPhone, iPad, etc.).
- **Conjuntos de imágenes del catálogo de activos** : use **conjuntos de imágenes del catálogo de recursos** para administrar y agrupar toda la versión de un recurso de imagen determinado que necesita una aplicación.
- **Imágenes en el código** : Use los `UIImage` métodos de la clase para cargar y trabajar con recursos de imagen y asignarlos a controles de interfaz de usuario en el código de C#.
- **Icono de aplicación** : defina el icono de la aplicación que requiere cada aplicación de iOS. Este es el icono que el usuario va a pulsar en la pantalla principal de iOS para iniciar la aplicación. Además, Game Center usa este icono, si procede.
- **Icono de Spotlight** : defina el icono de noticias destacadas de la aplicación. Siempre que el usuario escriba el nombre de una aplicación en una búsqueda de Spotlight, se mostrará este icono.
- **Icono de configuración** : defina el icono de **configuración** de la aplicación. Si el usuario escribe la aplicación de **configuración** en su dispositivo iOS, este icono se mostrará al final de la lista de configuración de la aplicación. 
- **Pantallas de inicio** : defina la pantalla de inicio de la aplicación. Después de que el usuario pulse el icono de la aplicación y antes de que aparezca la primera vista, se mostrará una pantalla en blanco. Afortunadamente, iOS incluye compatibilidad para mostrar una imagen en lugar de la pantalla en blanco mediante un guion gráfico. 
- **icono de iTunes** : proporcione un icono de iTunes. Si usa el método ad hoc de la entrega de una aplicación (ya sea para usuarios corporativos o para pruebas beta en dispositivos reales), el desarrollador también debe incluir una 512 x 512 y una imagen de 1024x1024 que se usará para representar la aplicación en iTunes.
- **Iconos de documento** : Use una imagen como icono para cualquier tipo de documento específico que admita o cree una aplicación de Xamarin. iOS.

Hay varias consideraciones que deben tenerse en cuenta al crear recursos de imagen para una aplicación de iOS, así como varios lugares donde se usarán esos recursos. Cada uno de ellos tiene un efecto sobre el número de recursos de imagen que serán necesarios, pero el modo en que se crean. En los temas siguientes se tratan los tipos de recursos de imagen que serán necesarios, cómo se incluyen esos recursos en el paquete de la aplicación y cómo se consumen los recursos de imagen para proporcionar la funcionalidad necesaria:

## <a name="displaying-an-image"></a>[Visualización de una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

En este artículo se describe cómo incluir un recurso de imagen en una aplicación de Xamarin. iOS y cómo mostrar esa imagen mediante código de C# o mediante su asignación a un control en el diseñador de iOS.

## <a name="application-icons"></a>[Iconos de aplicación](~/ios/app-fundamentals/images-icons/app-icons.md)

En este artículo se explica cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se usará como icono de aplicación.

## <a name="alternate-app-icons"></a>[Iconos de aplicación alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha agregado varias mejoras a iOS 10,3 que permiten a una aplicación administrar su icono:

- `ApplicationIconBadgeNumber` : Obtiene o establece el distintivo del icono de la aplicación en el Springboard.
- `SupportsAlternateIcons` : Si `true` la aplicación tiene un conjunto alternativo de iconos.
- `AlternateIconName` : Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si se usa el icono principal.
- `SetAlternameIconName` : Use este método para cambiar el icono de la aplicación al icono alternativo determinado.

## <a name="launch-screens"></a>[Pantallas de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md)

En este artículo se describe el uso de un tipo especial de guion gráfico para proporcionar una pantalla de inicio universal para cada tamaño y resolución de dispositivos iOS.

## <a name="custom-document-types"></a>[Tipos de documentos personalizados](~/ios/app-fundamentals/images-icons/custom-document-types.md)

En este artículo se describe cómo incluir y administrar un recurso de imagen en una aplicación Xamarin. iOS que se va a usar como un icono de tipo de documento personalizado.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instrucciones de creación de iconos e imágenes personalizadas](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)