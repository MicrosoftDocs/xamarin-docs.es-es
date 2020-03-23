---
title: 'Xamarin.Essentials: Tema de la aplicación'
description: En este documento se describe la API del tema de la aplicación solicitado en Xamarin.Essentials, que ofrece información sobre el estilo de tema solicitado para la aplicación en ejecución.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: c83b55f60ece20e9163b3e587130250fdf113a16
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2020
ms.locfileid: "79190314"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema de la aplicación

La API **RequestedTheme** forma parte de la clase [`AppInfo`](app-information.md) y proporciona información sobre el tema que el sistema solicita para la aplicación en ejecución.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Uso de RequestedTheme

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtener información sobre el tema

El tema de la aplicación solicitado puede detectarse con la API siguiente:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Esto proporcionará el tema que el sistema ha solicitado actualmente para la aplicación. El valor devuelto será cualquiera de los siguientes:

* Sin especificar
* Claro
* Oscuro

Se devolverá Sin especificar cuando el sistema operativo no tenga un estilo de interfaz de usuario específico para solicitarlo. Un ejemplo de esto se encuentran en los dispositivos que ejecutan versiones de iOS anteriores a la 13.0.


## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

Android usa modos de configuración para especificar el tipo de tema que se va a solicitar al usuario. En función de la versión de Android, el usuario puede modificarlo o se cambia cuando está habilitado el modo de ahorro de batería.

Puede obtener más información en la [documentación oficial de Android sobre el tema oscuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Siempre se devolverá Sin especificar en las versiones de iOS anteriores a la 13.0. 


# <a name="uwp"></a>[UWP](#tab/uwp)

De forma predeterminada, la aplicación se ejecuta con el tema establecido por el usuario en la configuración de Windows (**Configuración > Personalización > Colores > Elija su modo de aplicación predeterminado**). Puede establecer la propiedad RequestedTheme de la aplicación para reemplazar el valor predeterminado del usuario y especificar qué tema se usa.

Puede obtener más información en la [documentación sobre el tema solicitado en UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Código fuente de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentación de API para AppInfo](xref:Xamarin.Essentials.AppInfo)
