---
title: Ciclo de vida de la aplicación Xamarin.Forms
description: En este artículo se explica cómo responder al ciclo de vida de las aplicaciones, por ejemplo, los métodos de ciclo de vida, los eventos de notificación de página y los eventos de navegación modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a67d0c3adb54332bf30879a5b6f1d086581f0ec
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573344"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida de la aplicación Xamarin.Forms

La clase base [`Application`](xref:Xamarin.Forms.Application) proporciona las características siguientes:

- [Métodos de ciclo de vida](#lifecycle-methods) `OnStart`, `OnSleep` y `OnResume`.
- [Eventos de navegación de página](#page-navigation-events) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) y [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- [Eventos de navegación modal](#modal-navigation-events) `ModalPushing`, `ModalPushed`, `ModalPopping` y `ModalPopped`.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

La clase [`Application`](xref:Xamarin.Forms.Application) contiene tres métodos virtuales que pueden reemplazarse para responder a cambios en el ciclo de vida:

- `OnStart`: se llama cuando se inicia la aplicación.
- `OnSleep`: se llama cada vez que la aplicación pasa a segundo plano.
- `OnResume`: se llama cuando se reanuda la aplicación, después de haberla enviado a segundo plano.

> [!NOTE]
> *No* hay ningún método para la finalización de aplicaciones. En circunstancias normales (es decir, si no se produce un bloqueo), la finalización de la aplicación se producirá desde el estado *OnSleep*, sin que se muestren notificaciones en el código.

Para observar cuándo se llama a estos métodos, implemente una llamada `WriteLine` en cada uno (como se muestra a continuación) y pruébelos en cada plataforma.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> En Android, se llama al método `OnStart` por rotación y también cuando la aplicación se inicia por primera vez si la actividad principal carece de `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` en el atributo `[Activity()]`.

## <a name="page-navigation-events"></a>Eventos de navegación de página

Hay dos eventos en la clase [`Application`](xref:Xamarin.Forms.Application) que proporcionan notificaciones de páginas que aparecen y desaparecen:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing): se genera cuando una página va a aparecer en la pantalla.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing): se genera cuando una página va a desaparecer de la pantalla.

Estos eventos pueden usarse en escenarios en los que quiera realizar un seguimiento de las páginas a medida que aparezcan en la pantalla.

> [!NOTE]
> Los eventos [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) y [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) se generan a partir de la clase base [`Page`](xref:Xamarin.Forms.Page) inmediatamente después de los eventos [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) y [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), respectivamente.

## <a name="modal-navigation-events"></a>Eventos de navegación modal

Hay cuatro eventos en la clase [`Application`](xref:Xamarin.Forms.Application), cada uno con sus propios argumentos de evento, que le permiten responder a páginas modales que empiezan a mostrarse y se descartan:

- `ModalPushing`: se genera cuando se inserta una página de forma modal.
- `ModalPushed`: se genera después de que se ha insertado una página de forma modal.
- `ModalPopping`: se genera cuando se extrae una página de forma modal.
- `ModalPopped`: se genera después de que se ha extraído una página de forma modal.

> [!NOTE]
> Los argumentos de evento `ModalPopping` de tipo `ModalPoppingEventArgs` contienen una propiedad `Cancel`. Cuando `Cancel` se establece en `true`, se cancela la ventana emergente modal.
