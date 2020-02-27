---
title: Directorio de imágenes predeterminado en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir la plataforma específica de Windows que define el directorio del proyecto desde el que se cargarán los recursos de imagen.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 52197b980726936f4368ef1e4507ea671c9e70b1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646663"
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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

El método `Application.On<Windows>` especifica que este específico de la plataforma solo se ejecutará en el Plataforma universal de Windows. El método `Application.SetImageDirectory`, en el espacio de nombres [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , se usa para especificar el directorio del proyecto desde el que se cargarán las imágenes. Además, el método `GetImageDirectory` se puede utilizar para devolver un `string` que representa el directorio del proyecto que contiene los recursos de la imagen de aplicación.

El resultado es que todas las imágenes utilizadas en una aplicación se cargarán desde el directorio del proyecto especificado.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
