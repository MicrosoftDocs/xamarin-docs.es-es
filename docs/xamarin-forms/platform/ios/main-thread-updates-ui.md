---
title: Actualizaciones principales del control de subprocesos en iOS
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo consumir los específicos de la plataforma iOS que permiten realizar las actualizaciones del diseño y la representación del control en el subproceso principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 404e84e9da1df8a44fa176a17f2407e314ae7968
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368321"
---
# <a name="main-thread-control-updates-on-ios"></a>Actualizaciones principales del control de subprocesos en iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)