---
title: Diseño para tabletas y aplicaciones de escritorio
description: En este artículo se explica cómo optimizar los Xamarin.Forms diseños de aplicaciones para tabletas, en lugar de teléfonos.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5eb6a13888d96cac6fe193457ccc20659869ab68
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940387"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Diseño para tabletas y aplicaciones de escritorio

Xamarin.Forms admite todos los tipos de dispositivos disponibles en las plataformas compatibles, por lo que además de los teléfonos, las aplicaciones también se pueden ejecutar en:

- iPad
- Tabletas Android,
- Tabletas y equipos de escritorio con Windows (que ejecutan Windows 10).

En esta página se describe brevemente:

- los [tipos de dispositivo](#device-types)admitidos y
- Cómo [optimizar](#optimize-for-tablet-and-desktop) los diseños para tabletas en comparación con los teléfonos.

## <a name="device-types"></a>Tipos de dispositivos

Los dispositivos de pantalla más grandes están disponibles para todas las plataformas admitidas por Xamarin.Forms .

### <a name="ipads-ios"></a>iPad (iOS)

La Xamarin.Forms plantilla incluye automáticamente la compatibilidad con iPad mediante la configuración de la opción **info. plist > dispositivos** en **universal** (lo que significa que se admiten tanto iPhone como iPad).

Para proporcionar una experiencia de inicio agradable y asegurarse de que se utiliza la resolución de pantalla completa en todos los dispositivos, debe asegurarse de que se proporciona una [pantalla de inicio específica de iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (con un guion gráfico). Esto garantiza que la aplicación se represente correctamente en los dispositivos iPad mini, iPad y iPad Pro.

Antes de iOS 9, todas las aplicaciones requerían la pantalla completa en el dispositivo, pero algunos iPad ahora pueden realizar varias [tareas de pantalla dividida](~/ios/platform/multitasking.md).
Esto significa que la aplicación podría ocupar simplemente una columna delgada en el lateral de la pantalla, el 50% del ancho de la pantalla o toda la pantalla.

[![Ejemplo de pantalla dividida de iPad](tablet-images/ipad-sml.png)](tablet-images/ipad.png#lightbox "Ejemplo de pantalla dividida de iPad")

La funcionalidad de pantalla dividida significa que debe diseñar la aplicación para que funcione bien con tan solo 320 píxeles de ancho o hasta 1366 píxeles de ancho.

### <a name="android-tablets"></a>Tabletas Android

El ecosistema de Android tiene una gran cantidad de tamaños de pantalla compatibles, desde pequeños teléfonos hasta grandes tabletas. Xamarin.Forms puede admitir todos los tamaños de pantalla, pero, al igual que con las otras plataformas, puede que desee ajustar la interfaz de usuario para dispositivos de mayor tamaño.

Cuando se admiten muchas resoluciones de pantalla diferentes, puede proporcionar los recursos de imagen nativa en distintos tamaños para optimizar la experiencia del usuario.
Revise la documentación de [los recursos de Android](~/android/app-fundamentals/resources-in-android/index.md) (y, en particular, [cree recursos para diferentes tamaños de pantalla](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para más información sobre cómo estructurar las carpetas y los nombres de archivo en el proyecto de aplicación de Android para incluir recursos de imagen optimizados en la aplicación.

### <a name="windows-tablets-and-desktops"></a>Tabletas y escritorios de Windows

Para admitir tabletas y equipos de escritorio que ejecuten Windows, deberá usar la [compatibilidad con Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), que crea aplicaciones universales que se ejecutan en Windows 10.

Se puede cambiar el tamaño de las aplicaciones que se ejecutan en tabletas y escritorios de Windows a dimensiones arbitrarias, además de ejecutarse en pantalla completa.

[![Ejemplo de pantalla dividida de Windows](tablet-images/splitscreen-sml.png)](tablet-images/splitscreen.png#lightbox "Ejemplo de pantalla dividida de Windows")

## <a name="optimize-for-tablet-and-desktop"></a>Optimizar para tabletas y equipos de escritorio

Puede ajustar la Xamarin.Forms interfaz de usuario en función de si se usa un dispositivo de teléfono o tableta o escritorio. Esto significa que puede optimizar la experiencia del usuario en dispositivos de gran tamaño, como tabletas y equipos de escritorio.

### <a name="deviceidiom"></a>Dispositivo. expresión

Puede usar la [`Device`](~/xamarin-forms/platform/device.md) clase para cambiar el comportamiento de la aplicación o la interfaz de usuario. Mediante la `Device.Idiom` enumeración puede

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Este enfoque se puede expandir para realizar cambios significativos en los diseños de página individuales o incluso para representar páginas completamente diferentes en pantallas más grandes.

### <a name="leverage-flyoutpage"></a>Aprovechar FlyoutPage

[`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage)Es ideal para pantallas de mayor tamaño, especialmente en el iPad donde usa [`UISplitViewController`](xref:UIKit.UISplitViewController) para proporcionar una experiencia de iOS nativa.

Revise [esta entrada de blog de Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/) para ver cómo puede adaptar la interfaz de usuario para que los teléfonos usen un diseño y las pantallas más grandes puedan usar otro (con `FlyoutPage` ).

## <a name="related-links"></a>Vínculos relacionados

- [Blog de Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [Ejemplo de mis compradores](https://github.com/jamesmontemagno/myshoppe)
