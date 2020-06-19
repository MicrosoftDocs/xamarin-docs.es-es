---
title: Directorio de imágenes predeterminado en Windows
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que define el directorio del proyecto desde el que se cargarán los recursos de imagen.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5c5e6db8ddcf3cef32bde5c387adc378afd0058
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135621"
---
# <a name="default-image-directory-on-windows"></a>Directorio de imágenes predeterminado en Windows

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este Plataforma universal de Windows específico de la plataforma define el directorio del proyecto desde el que se cargarán los recursos de imagen. Se consume en XAML estableciendo el `Application.ImageDirectory` en un `string` que representa el directorio del proyecto que contiene los recursos de imagen:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

El `Application.On<Windows>` método especifica que este específico de la plataforma solo se ejecutará en el plataforma universal de Windows. El `Application.SetImageDirectory` método, en el [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para especificar el directorio del proyecto desde el que se cargarán las imágenes. Además, `GetImageDirectory` se puede usar el método para devolver un `string` que representa el directorio del proyecto que contiene los recursos de la imagen de aplicación.

El resultado es que todas las imágenes utilizadas en una aplicación se cargarán desde el directorio del proyecto especificado.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
