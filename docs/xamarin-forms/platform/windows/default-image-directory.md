---
title: "directorio de imagen predeterminado en Windows" Descripción: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir la plataforma específica de Windows que define el directorio del proyecto desde el que se cargarán los recursos de imagen.
MS. Prod: Xamarin ms. AssetID: 537A032B-74DD-4D43-864E-7D7113286D0D ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/16/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
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
