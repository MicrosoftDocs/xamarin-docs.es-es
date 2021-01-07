---
title: ¿Cómo migrar mi aplicación a Xamarin.Forms 5,0?
description: Cómo migrar la aplicación a Xamarin.Forms 5,0, con el foco en Android en UWP.
ms.assetid: AD04FEE9-B8F5-4CA5-AB31-EF1225867E4B
ms.prod: xamarin
ms.technology: xamarin-forms
ms.topic: troubleshooting
author: davidbritch
ms.author: dabritch
ms.date: 10/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f93d20dac789abed57f8f41bf41778ad50a5fb5
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972323"
---
# <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>¿Cómo migrar mi aplicación a Xamarin.Forms 5,0?

Xamarin.Forms 5,0 incluye los siguientes cambios importantes:

- `Expander` ha pasado al kit de herramientas de Xamarin Community. Para obtener más información, vea [características que Xamarin.Forms se han cambiado de ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- `MediaElement` ha pasado al kit de herramientas de Xamarin Community. Para obtener más información, vea [características que Xamarin.Forms se han cambiado de ](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms).
- Las páginas de página y los proyectos asociados se han quitado de Xamarin.Forms .
- Se ha cambiado el nombre de `MasterDetailPage` a `FlyoutPage`. Del mismo modo, se ha cambiado el nombre de la `MasterBehavior` enumeración a `FlyoutLayoutBehavior` .
- Las referencias a `UIWebView` se han quitado de Xamarin.Forms en iOS.
- Se ha quitado la compatibilidad con Visual Studio 2017.
- Se ha quitado XFCorePostProcessor. Tasks. Este proyecto insertó IL para mantener la Xamarin.Forms compatibilidad con 2,5.

Además, los proyectos de Android y UWP compilados con Xamarin.Forms 5,0 requerirán una actualización.

## <a name="android"></a>Android

Los proyectos de Android creados con Xamarin.Forms 5,0 requieren que haya instalado la plataforma AndroidX (Android 10,0) en el entorno de desarrollo. Esto puede realizarse con el administrador de Android SDK. Para obtener más información sobre AndroidX, consulte [migración de Xamarin.Forms AndroidX en ](~/xamarin-forms/platform/android/androidx-migration.md).

Los proyectos de Android requerirán varias actualizaciones para compilar correctamente.

### <a name="minimum-targetframeworkversion"></a>Valor mínimo de TargetFrameworkVersion

Xamarin.Forms 5,0 requiere una versión de .NET Framework de destino mínima de 10,0 (AndroidX) para los proyectos de Android. La versión de .NET Framework de destino se puede establecer en Visual Studio o en el archivo Android. csproj:

```xml
<TargetFrameworkVersion>v10.0</TargetFrameworkVersion>
```

Se producirá un error de compilación si no se cumple este requisito mínimo:

```
error XF005: The $(TargetFrameworkVersion) for MyProject.Android (v9.0) is less than the minimum required $(TargetFrameworkVersion) for Xamarin.Forms (10.0). You need to increase the $(TargetFrameworkVersion) for MyProject.Android.
```

### <a name="minimum-targetsdkversion"></a>TargetSDKVersion mínimo

AndroidX requiere que el manifiesto de Android establezca `targetSdkVersion` en 29 +:

```xml
<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="29" />
```

Si no lo hace, el se `targetSdkVersion` establecerá en `minSdkVersion` . Además, en algunas circunstancias `Android.Views.InflateException` se generará una si `targetSdkVersion` no se establece correctamente:

```
Android.View.InflateException has been thrown.

Binary XML file line #1 in com.companyname.myproject:layout/toolbar: Binary XML file line #1 in com.companyname.myproject:/layout/toolbar: Error inflating class android.support.v7.widget.Toolbar
```

> [!NOTE]
> En Visual Studio 2019, el manifiesto de Android se actualizará automáticamente para especificar una `targetSdkVersion` de API 29 cuando la versión de .NET Framework de destino esté establecida en v 10.0.

### <a name="automatic-migration-to-androidx"></a>Migración automática a AndroidX

Si el proyecto de Android hace referencia a cualquier biblioteca de compatibilidad de Android, ya sea como dependencias directas o dependencias transitivas, estas dependencias y enlaces de la biblioteca de soporte se intercambian automáticamente con las dependencias de AndroidX durante el proceso de compilación. Para obtener más información acerca de la migración automática [de AndroidX, Xamarin.Forms consulte migración automática en ](~/xamarin-forms/platform/android/androidx-migration.md#automatic-migration-in-xamarinforms).

### <a name="manual-migration-to-androidx"></a>Migración manual a AndroidX

Si el proyecto de Android no tiene dependencias directas o transitivas en las bibliotecas de compatibilidad de Android, pero sigue intentando usar tipos de biblioteca de soporte a través del código, tendrá que migrar manualmente la aplicación a AndroidX. Esto puede realizarse mediante el uso de tipos AndroidX y quitando los archivos AXML que no haya personalizado.

> [!TIP]
> La migración manual a AndroidX dará lugar al proceso de compilación más rápido de la aplicación.

#### <a name="use-androidx-types"></a>Uso de tipos AndroidX

AndroidX reemplaza las bibliotecas de compatibilidad con Android y, por tanto, cualquier referencia a los tipos de biblioteca de compatibilidad con Android debe reemplazarse por referencias a tipos de AndroidX.

Esto puede realizarse mediante la actualización de las `using` instrucciones para utilizar `AndroidX` espacios de nombres, en lugar de `Android.Support` espacios de nombres. En la tabla siguiente se enumeran algunos de los cambios de espacio de nombres comunes al pasar de las bibliotecas de compatibilidad de Android a AndroidX:

| Espacio de nombres de la biblioteca de compatibilidad con Android | Espacio de nombres AndroidX |
| --- | --- |
| `Android.Support.V4.App` | `AndroidX.Core.App` |
| `Android.Support.V4.Content` | `AndroidX.Core.Content` |
| `Android.Support.V4.App` | `AndroidX.Fragment.App` |
| `Android.Support.V7.App` | `AndroidX.AppCompat.App` |
| `Android.Support.V7.Widget` | `AndroidX.AppCompat.Widget` |

Para obtener una lista completa de las asignaciones de clases de las bibliotecas de compatibilidad con AndroidX, vea [support Library Class mappings](https://developer.android.com/jetpack/androidx/migrate/class-mappings) on Developer.Android.com.

#### <a name="remove-axml-files"></a>Quitar archivos AXML

Debe eliminar todos los archivos AXML del proyecto de Android, siempre que no use archivos AXML personalizados. Después de la eliminación, deben quitarse las siguientes líneas de la `MainActivity` clase:

```csharp
TabLayoutResource = Resource.Layout.Tabbar;
ToolbarResource = Resource.Layout.Toolbar;
```

> [!IMPORTANT]
> Los proyectos de Android con archivos AXML personalizados deben actualizarse para que estos archivos usen tipos AndroidX.

## <a name="uwp"></a>UWP

Xamarin.Forms 5,0 recomienda una versión de plataforma de destino de >= 10.0.18362.0 para los proyectos de UWP. La versión de la plataforma de destino se puede establecer en Visual Studio o en el archivo UWP. csproj:

```xml
<TargetPlatformVersion Condition=" '$(TargetPlatformVersion)' == '' ">10.0.18362.0</TargetPlatformVersion>
```

Se generará una advertencia de compilación si el proyecto de UWP usa una versión de plataforma de destino inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Características movidas desde Xamarin.Forms](https://github.com/xamarin/XamarinCommunityToolkit/wiki/Features-moved-from-Xamarin.Forms)
- [Migración de AndroidX en Xamarin.Forms](~/xamarin-forms/platform/android/androidx-migration.md)
