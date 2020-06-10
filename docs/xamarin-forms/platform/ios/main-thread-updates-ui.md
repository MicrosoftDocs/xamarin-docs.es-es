---
title: "las actualizaciones principales del control de subprocesos en iOS" Description: "las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir los específicos de la plataforma iOS que permiten realizar las actualizaciones del diseño y la representación del control en el subproceso principal ".
MS. Prod: Xamarin ms. AssetID: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="main-thread-control-updates-on-ios"></a>Actualizaciones principales del control de subprocesos en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS permite realizar actualizaciones del diseño y la representación del control en el subproceso principal, en lugar de realizarse en un subproceso en segundo plano. Rara vez es necesario, pero en algunos casos puede impedir bloqueos. Su consumido en XAML estableciendo la `Application.HandleControlUpdatesOnMainThread` propiedad enlazable en `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

El `Application.On<iOS>` método especifica que este específico de la plataforma solo se ejecutará en iOS. El `Application.SetHandleControlUpdatesOnMainThread` método, en el [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si se realizan actualizaciones de representación y diseño del control en el subproceso principal, en lugar de realizarse en un subproceso en segundo plano. Además, se `Application.GetHandleControlUpdatesOnMainThread` puede usar el método para devolver si se realizan actualizaciones de representación y diseño del control en el subproceso principal.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
