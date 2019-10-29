---
title: Uso de recursos de Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 9c8db5ad7bcb012befb2fa8dcd1ecd13fa355a55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025427"
---
# <a name="using-android-assets"></a>Uso de recursos de Android

Los _recursos_ proporcionan una manera de incluir archivos arbitrarios como texto, XML, fuentes, música y vídeo en la aplicación. Si intenta incluir estos archivos como "recursos", Android los procesará en su sistema de recursos y no podrá obtener los datos sin procesar. Si desea tener acceso a los datos sin tocar, los recursos son una forma de hacerlo.

Los recursos agregados al proyecto se mostrarán como un sistema de archivos que puede leer la aplicación mediante [AssetManager](xref:Android.Content.Res.AssetManager).
En esta sencilla demostración, vamos a agregar un recurso de archivo de texto a nuestro proyecto, a leerlo mediante `AssetManager`y a mostrarlo en una TextView.

## <a name="add-asset-to-project"></a>Agregar activo al proyecto

Los recursos van en la carpeta `Assets` del proyecto. Agregue un nuevo archivo de texto a esta carpeta llamada `read_asset.txt`. Coloque texto en él como "he recibido de un recurso".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio debe haber establecido la **acción de compilación** para este archivo en **AndroidAsset**:

![Establecer la acción de compilación en AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac debe haber establecido la **acción de compilación** para este archivo en **AndroidAsset**:

[![establecer la acción de compilación en AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Al seleccionar la opción **BuildAction** correcta se garantiza que el archivo se empaquetará en el APK en tiempo de compilación.

## <a name="reading-assets"></a>Leer recursos

Los recursos se leen mediante un [AssetManager](xref:Android.Content.Res.AssetManager). Una instancia del `AssetManager` está disponible mediante el acceso a la propiedad [assets](xref:Android.Content.Context.Assets) en una `Android.Content.Context`, como una actividad.
En el código siguiente, se abre nuestro recurso **read_asset. txt** , se lee el contenido y se muestra mediante una TextView.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

## <a name="running-the-application"></a>Ejecutar la aplicación

Ejecute la aplicación y verá lo siguiente:

![Captura de pantalla de ejemplo](android-assets-images/screenshot.png)

## <a name="related-links"></a>Vínculos relacionados

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [Contexto](xref:Android.Content.Context)
