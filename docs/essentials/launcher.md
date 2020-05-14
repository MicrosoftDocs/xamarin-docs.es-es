---
title: 'Xamarin.Essentials: Launcher'
description: La clase Launcher de Xamarin.Essentials permite que una aplicación abra un URI por el sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: f84d3bf379562c8ddd96a32dfb9f15b03b0a6827
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149788"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Selector

La clase **Launcher** permite que una aplicación abra un URI por el sistema. A menudo se usa al vincular en profundidad en los esquemas de URI personalizados de otra aplicación. Si quiere abrir el explorador en un sitio web, debe hacer referencia a la API **[Browser](open-browser.md)** .

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Uso de Launcher

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la funcionalidad Launcher, llame al método `OpenAsync` y pase un `string` o `Uri` para abrirla. Si quiere, el método `CanOpenAsync` se puede usar para comprobar si el esquema de URI se puede administrar desde una aplicación del dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

Esto se puede combinar en una sola llamada con `TryOpenAsync`, que comprueba si se puede abrir el parámetro y, si es así, lo abre.

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

### <a name="additional-platform-setup"></a>Configuración de una plataforma adicional

# <a name="android"></a>[Android](#tab/android)

Sin configuración adicional.

# <a name="ios"></a>[iOS](#tab/ios)

En iOS 9 y versiones posteriores, Apple aplica los esquemas que una aplicación puede consultar. Para especificar los esquemas que desea usar, debe indicar `LSApplicationQueriesSchemes` en el archivo de `Info.plist`.

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Sin configuración adicional.

-----

## <a name="files"></a>Archivos

Esta característica permite a una aplicación solicitar a otras que abran y vean un archivo. Xamarin.Essentials detectará automáticamente el tipo de archivo (MIME) y solicitará su apertura.

A continuación se muestra un ejemplo de cómo escribir texto y cómo solicitar la apertura:

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

# <a name="ios"></a>[iOS](#tab/ios)

Si la aplicación de destino en este dispositivo nunca antes se abrió por `OpenAsync` desde la aplicación, iOS le pedirá una vez al usuario que permita que la aplicación la abra.

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

[Aquí](xref:UIKit.UIApplication.CanOpenUrl*) encontrará más información sobre la implementación de iOS.

# <a name="uwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="api"></a>API

- [Código fuente de Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentación de API para Launcher](xref:Xamarin.Essentials.Launcher)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Launcher-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
