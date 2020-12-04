---
title: 'Xamarin.Essentials: Selector de archivos'
description: La clase FilePicker de Xamarin.Essentials permite a un usuario seleccionar uno o varios archivos del dispositivo.
ms.assetid: 00bdbd57-56b1-47ca-8abe-cebe1b01f61a
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f98f0cdea812a91bbe6caf1e248e468d9a960ac6
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303043"
---
# <a name="no-locxamarinessentials-file-picker"></a>Xamarin.Essentials: Selector de archivos

La clase **FilePicker** permite a un usuario seleccionar uno o varios archivos del dispositivo.

![API de versión preliminar](~/media/shared/preview.png)

## <a name="get-started"></a>Introducción

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la función de **FilePicker**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

El permiso `ReadExternalStorage` es necesario y se debe configurar en el proyecto Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active este permiso. Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwp"></a>[UWP](#tab/uwp)

No se requiere configuración adicional.

-----

> [!TIP]
> Se debe llamar a todos los métodos del subproceso de interfaz de usuario porque Xamarin.Essentialscontrola automáticamente las solicitudes y comprobaciones de permisos.

## <a name="pick-file"></a>Selección de un archivo

El método `FilePicker.PickAsync()` permite al usuario seleccionar un archivo desde el dispositivo. Se pueden seleccionar diferentes elementos `PickOptions` al llamar al método, lo que permite especificar el título que se va a mostrar y los tipos de archivo que el usuario puede elegir. De forma predeterminada 

```csharp
async Task<FileResult> PickAndShow(PickOptions options)
{
    try
    {
        var result = await FilePicker.PickAsync();
        if (result != null)
        {
            Text = $"File Name: {result.FileName}";
            if (result.FileName.EndsWith("jpg", StringComparison.OrdinalIgnoreCase) ||
                result.FileName.EndsWith("png", StringComparison.OrdinalIgnoreCase))
            {
                var stream = await result.OpenReadAsync();
                Image = ImageSource.FromStream(() => stream);
            }
        }
    }
    catch (Exception ex)
    {
        // The user canceled or something went wrong
    }
}
```

Los tipos de archivo predeterminados se proporcionan con `FilePickerFileType.Images`, `FilePickerFileType.Png` y `FilePickerFilerType.Videos`. Puede especificar tipos de archivos personalizados al crear `PickOptions` y se pueden personalizar por plataforma. Por ejemplo, aquí se muestra cómo especificaría determinados tipos de archivo de cómic:

```csharp
var customFileType =
    new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
    {
        { DevicePlatform.iOS, new[] { "public.my.comic.extension" } }, // or general UTType values
        { DevicePlatform.Android, new[] { "application/comics" } },
        { DevicePlatform.UWP, new[] { ".cbr", ".cbz" } },
        { DevicePlatform.Tizen, new[] { "*/*" } },
        { DevicePlatform.macOS, new[] { "cbr", "cbz" } }, // or general UTType values
    });
var options = new PickOptions
{
    PickerTitle = "Please select a comic file",
    FileTypes = customFileType,
};
```

## <a name="pick-multiple-files"></a>Selección de varios archivos

Si desea que el usuario elija varios archivos, puede llamar al método `FilePicker.PickMultipleAsync()`. También toma `PickOptions` como parámetro para especificar información adicional. Los resultados son los mismos que `PickAsync`, pero en lugar de un único elemento `FileResult` se devuelve un elemento `IEnumerable<FileResult>` que se puede iterar.


## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

- Es posible que el URI del archivo resultante no se pueda conservar entre los reinicios.

# <a name="ios"></a>[iOS](#tab/ios)

No hay diferencias entre las plataformas.

# <a name="uwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

## <a name="api"></a>API

- [Código fuente de FilePicker](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FilePicker)
- [Documentación de FilePicker API](xref:Xamarin.Essentials.FilePicker)
