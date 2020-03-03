---
title: Extensiones de plataforma de Xamarin.Essentials
description: Xamarin.Essentials ofrece varios métodos de extensión de la plataforma al trabajar con tipos de plataforma como Rect, Size y Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545151"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: extensiones de plataforma

Xamarin.Essentials ofrece varios métodos de extensión de plataforma al trabajar con tipos de plataforma como Rect, Size y Point. Esto significa que puede convertir la versión `System` de estos tipos a tipos específicos de iOS, Android y UWP. 

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Uso de las extensiones de plataforma

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Todas las extensiones de plataforma solo se pueden llamar desde proyectos UWP, iOS o Android.

## <a name="android-extensions"></a>Extensiones de Android

Solo se puede acceder a estas extensiones desde un proyecto de Android.

### <a name="application-context--activity"></a>Actividad y contexto de la aplicación

Con las extensiones de plataforma de la clase `Platform`, puede acceder a los elementos `Context` o `Activity` para la aplicación en ejecución.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Si hay una situación en la que se necesita el elemento `Activity`, pero la aplicación no se ha iniciado totalmente, se debe usar el método `WaitForActivityAsync`.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

Además de obtener la actividad actual, también puede registrarse en los eventos de ciclo de vida.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

Los estados de actividad son los siguientes:

* Creado
* Reanudado
* En pausa
* Destruido
* SaveInstanceState
* Comenzado
* Detenido

Lea la documentación [Ciclo de vida de la actividad](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) para obtener más información.

## <a name="ios-extensions"></a>Extensiones de iOS

Solo se puede acceder a estas extensiones desde un proyecto de iOS.

### <a name="current-uiviewcontroller"></a>UIViewController actual

Acceder al elemento `UIViewController`actualmente visible:

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Este método devolverá `null` si no puede detectar un elemento `UIViewController`.

## <a name="cross-platform-extensions"></a>Extensiones multiplataforma

Estas extensiones existen en todas las plataformas.

### <a name="point"></a>Punto

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>Tamaño

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Rectángulo

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [Código fuente de los convertidores](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentación sobre la API de los convertidores de puntos](xref:Xamarin.Essentials.PointExtensions)
- [Documentación sobre la API de los convertidores de rectángulos](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentación sobre la API de los convertidores de tamaño](xref:Xamarin.Essentials.SizeExtensions)
