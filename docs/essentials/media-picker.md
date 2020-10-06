---
title: 'Xamarin.Essentials: Selector de archivos multimedia'
description: La clase MediaPicker de Xamarin.Essentials permite al usuario escoger o tomar una foto o vídeo en el dispositivo.
ms.assetid: 23460875-6cf9-4440-a97b-46c55b0bca69
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 536c0da222441808a842656853c642a2e876188b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432248"
---
# <a name="no-locxamarinessentials-media-picker"></a>Xamarin.Essentials: Selector de archivos multimedia

La clase **MediaPicker** permite al usuario escoger o tomar una foto o vídeo en el dispositivo.

![API de versión preliminar](~/media/shared/preview.png)

## <a name="get-started"></a>Introducción

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la función de **MediaPicker**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

Los permisos siguientes son requeridos y se deben configurar en el proyecto de Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
// Needed for Picking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]

// Needed for Taking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage)]
[assembly: UsesPermission(Android.Manifest.Permission.CameraExternalStorage)]

// Add these properties if you would like to filter out cameras that do not have cameras or set to false to make them optional
[assembly: UsesFeature("android.hardware.camera", Required = true)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.CAMERA" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active estos permisos. Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

En `Info.plist`, agregue las claves siguientes:

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

Asegúrese de actualizar el elemento `<string>` de cada una de ellas a una descripción específica de la aplicación, ya que se mostrará a los usuarios.

# <a name="uwp"></a>[UWP](#tab/uwp)

En `Package.appxmanifest` en **Capacidades**, asegúrese de que las capacidades `Microphone` y `Webcam` están activadas.

-----

## <a name="using-media-picker"></a>Uso del Selector de archivos multimedia

La clase `MediaPicker` tiene los métodos siguientes que devuelven un elemento `FileResult` que se puede usar para obtener la ubicación de los archivos o para leerlo como `Stream`.

* `PickPhotoAsync`: abre el explorador de elementos multimedia para seleccionar una foto.
* `CapturePhotoAsync`: abre la cámara para tomar una foto.
* `PickVideoAsync`: abre el explorador de elementos multimedia para seleccionar un vídeo.
* `CaptureVideoAsync`: abre la cámara para grabar un vídeo.

Cada método toma opcionalmente un parámetro `MediaPickerOptions` que permite establecer el elemento `Title` en algunos sistemas operativos que se muestran a los usuarios.

## <a name="general-usage"></a>Uso general

```csharp
async Task TakePhotoAsync()
{
    try
    {
        var photo = await MediaPicker.CapturePhotoAsync();
        await LoadPhotoAsync(photo);
        Console.WriteLine($"CapturePhotoAsync COMPLETED: {PhotoPath}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"CapturePhotoAsync THREW: {ex.Message}");
    }
}

async Task LoadPhotoAsync(FileResult photo)
{
    // canceled
    if (photo == null)
    {
        PhotoPath = null;
        return;
    }
    // save the file into local storage
    var newFile = Path.Combine(FileSystem.CacheDirectory, photo.FileName);
    using (var stream = await photo.OpenReadAsync())
    using (var newStream = File.OpenWrite(newFile))
        await stream.CopyToAsync(newStream);

    PhotoPath = newFile;
}
```


## <a name="api"></a>API

- [Código fuente de MediaPicker](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MediaPicker)
- [Documentación de MediaPicker API](xref:Xamarin.Essentials.MediaPicker)
