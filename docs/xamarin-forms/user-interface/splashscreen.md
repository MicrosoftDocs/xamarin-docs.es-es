---
title: Pantalla de presentación de Xamarin. Forms
description: En este artículo se explica cómo crear una pantalla de presentación para una aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035777"
---
# <a name="xamarinforms-splash-screen"></a>Pantalla de presentación de Xamarin. Forms

Las aplicaciones suelen tener un retraso de inicio mientras la aplicación completa el proceso de inicialización. Los desarrolladores pueden querer ofrecer una experiencia de marca, normalmente denominada pantalla de presentación, mientras se inicia la aplicación. En este artículo se explica cómo crear pantallas de presentación para aplicaciones de Xamarin. Forms.

Xamarin. Forms se inicializa en cada plataforma una vez completada la secuencia de inicio nativa. Se ha inicializado Xamarin. Forms:

- En el método `OnCreate` de la clase `MainActivity` en Android.
- En el método `FinishedLaunching` de la clase `AppDelegate` en iOS.
- En el método `OnLaunched` de la clase `App` en UWP.

La pantalla de presentación debe mostrarse lo antes posible cuando se inicia la aplicación, pero Xamarin. Forms no se inicializa hasta el final de la secuencia de inicio, lo que significa que la pantalla de presentación debe implementarse fuera de Xamarin. Forms en cada plataforma. En las secciones siguientes se explica cómo crear una pantalla de presentación en cada plataforma.

## <a name="xamarinforms-android-splash-screen"></a>Pantalla de presentación de Android de Xamarin. Forms

La creación de una pantalla de presentación en Android requiere la creación de un `Activity` de presentación como `MainLauncher` con un tema especial. En cuanto se inicia el `Activity` de presentación, se inicia el `Activity` principal con el tema normal de la aplicación.

Para obtener más información sobre las pantallas de presentación en Xamarin. Android, consulte la [pantalla de presentación de Xamarin. Android](~/android/user-interface/splash-screen.md).

## <a name="xamarinforms-ios-splash-screen"></a>Pantalla de presentación de Xamarin. Forms iOS

Una pantalla de presentación en iOS se conoce como pantalla de inicio. Para crear una pantalla de inicio en iOS, es necesario crear un guion gráfico que defina la interfaz de usuario de la pantalla de inicio y, a continuación, establecer el guión gráfico como la pantalla de inicio en **info. plist**.

Para obtener más información acerca de las pantallas de inicio de Xamarin. iOS, consulte la [pantalla de inicio de Xamarin. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md).

## <a name="xamarinforms-uwp-splash-screen"></a>Pantalla de presentación de Xamarin. Forms UWP

En UWP, el **paquete. appxmanifest** contiene una pestaña **activos visuales** con un submenú de **pantalla de presentación** . Los gráficos de la pantalla de presentación se pueden especificar en este menú:

[![configuración de la pantalla de presentación en UWP](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Pantalla de presentación de Xamarin. Android](~/android/user-interface/splash-screen.md)
- [Pantalla de inicio de Xamarin. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
