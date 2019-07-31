---
title: Actualizaciones principales del control de subprocesos en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir los específicos de la plataforma iOS que permiten realizar las actualizaciones del diseño y la representación del control en el subproceso principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 9603cccc1f08be057bc66012cdde75e1b7391f1a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655272"
---
# <a name="main-thread-control-updates-on-ios"></a>Actualizaciones principales del control de subprocesos en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este específico de la plataforma iOS permite realizar actualizaciones del diseño y la representación del control en el subproceso principal, en lugar de realizarse en un subproceso en segundo plano. Debería ser necesario con poca frecuencia, pero en algunos casos puede evitar que se bloquee. Su XAML consumido en estableciendo el `Application.HandleControlUpdatesOnMainThread` propiedad enlazable a `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Application.SetHandleControlUpdatesOnMainThread` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si el diseño del control y la representación de las actualizaciones se realizan en el subproceso principal, en lugar de que se va a realizar en un subproceso en segundo plano. Además, el `Application.GetHandleControlUpdatesOnMainThread` método puede utilizarse para devolver si el diseño de controles y la representación de las actualizaciones se realizan en el subproceso principal.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
