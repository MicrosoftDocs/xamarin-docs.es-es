---
title: Accesibilidad de Xamarin.Forms
description: Crear una aplicación accesible garantiza que la aplicación podrá ser usada por personas que interaccionan con la interfaz de usuario con una variedad de necesidades y experiencias.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 2aaa61400d3775c4d622b805d24ff0b338017de5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "66354042"
---
# <a name="xamarinforms-accessibility"></a>Accesibilidad de Xamarin.Forms

_Crear una aplicación accesible garantiza que la aplicación podrá ser usada por personas que interaccionan con la interfaz de usuario con una variedad de necesidades y experiencias._

Hacer que una aplicación de Xamarin.Forms sea accesible significa pensar en el diseño de muchos elementos de interfaz de usuario. Para obtener información sobre cuestiones que hay que tener en cuenta, vea la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md). Ya se pueden solucionar muchos problemas de accesibilidad como las fuentes grandes y la configuración adecuada de color y contraste mediante las API de Xamarin.Forms.

Las guías [Accesibilidad para Android](~/android/app-fundamentals/accessibility.md) y [Accesibilidad para iOS](~/ios/app-fundamentals/accessibility.md) contienen información detallada de las API nativas expuestas por Xamarin, y la [Guía de accesibilidad UWP en MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica el enfoque nativo en esa plataforma. Estas API se usan para implementar por completo aplicaciones accesibles en cada plataforma.

Actualmente, Xamarin.Forms no tiene compatibilidad *integrada* con todas las API de accesibilidad que hay disponibles en cada una de las plataformas subyacentes. Pero sí admite configurar las propiedades de automatización en los elementos de la interfaz de usuario para admitir las herramientas de ayuda a la navegación y el lector de pantalla, que es uno de los aspectos más importantes al crear aplicaciones accesibles. Para más información, vea [Propiedades de automatización](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

En las aplicaciones de Xamarin.Forms también se puede especificar el orden de tabulación de los controles, con el fin de mejorar la facilidad de uso y la accesibilidad. Para más información, consulte [Accesibilidad del teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Otras API de accesibilidad (como [PostNotification en iOS](~/ios/app-fundamentals/accessibility.md)) pueden ser más adecuadas para una implementación de [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) o [Representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). No se tratarán en esta guía.

## <a name="testing-accessibility"></a>Probar la accesibilidad

Las aplicaciones de Xamarin.Forms normalmente van dirigidas a varias plataformas, lo que significa que las pruebas de las características de accesibilidad se realizan según la plataforma. Siga estos vínculos para saber más sobre cómo probar la accesibilidad en cada plataforma:

- [**Pruebas en iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Pruebas en Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)** ](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Propiedades de automatización](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Accesibilidad de teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
