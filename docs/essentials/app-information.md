---
title: "Xamarin.Essentials: información de la aplicación" description: "En este documento se describe la clase AppInfo de Xamarin.Essentials, que proporciona información sobre la aplicación. Por ejemplo, muestra el nombre de la aplicación y la versión."
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA author: jamesmontemagno ms.author: jamont ms.date: 01/29/2019 ms.custom: video no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Información de la aplicación:

La clase **AppInfo** proporciona información sobre la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Uso de AppInfo

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtención de información de la aplicación:

La información siguiente se expone a través de la API:

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Representación de la configuración de la aplicación

La clase **AppInfo** también puede mostrar una página de configuración mantenida por el sistema operativo para la aplicación:

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

Esta página de configuración permite al usuario cambiar los permisos de la aplicación y realizar otras tareas específicas de la plataforma.

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

La información sobre la aplicación se extrae de `AndroidManifest.xml` para estos campos:

- **Build**: `android:versionCode` en el nodo `manifest`
- **Name** - `android:label` en el nodo `application`
- **PackageName**: `package` en el nodo `manifest`
- **VersionString**: `android:versionName` en el nodo `application`

# <a name="ios"></a>[iOS](#tab/ios)

La información sobre la aplicación se extrae de `Info.plist` para estos campos:

- **Build**: `CFBundleVersion`
- **Name** - `CFBundleDisplayName` si se establece; de lo contrario, `CFBundleName`
- **PackageName**: `CFBundleIdentifier`
- **VersionString**: `CFBundleShortVersionString`

# <a name="uwp"></a>[UWP](#tab/uwp)

La información sobre la aplicación se extrae de `Package.appxmanifest` para estos campos:

- **Build**: utiliza el valor `Build` de `Version` en el nodo `Identity`
- **Name** - `DisplayName` en el nodo `Properties`
- **PackageName**: `Name` en el nodo `Identity`
- **VersionString**: `Version` en el nodo `Identity`

--------------

## <a name="api"></a>API

- [Código fuente de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentación de API para AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
