---
title: "Xamarin.Essentials: linterna" description: "En este documento se describe la clase Flashlight de Xamarin.Essentials, que tiene la capacidad de activar o desactivar el flash de la cámara del dispositivo para convertirlo en una linterna."
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136 author: jamesmontemagno ms.custom: video ms.author: jamont ms.date: 11/04/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: Linterna

La clase **Flashlight** tiene la capacidad de activar o desactivar el flash de la cámara del dispositivo y convertirlo en una linterna.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Flashlight**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

Los permisos Flashlight y Camera son obligatorios y se deben configurar en el proyecto de Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos necesarios:** y active los permisos **FLASHLIGHT** (Linterna) y **CAMERA** (Cámara). Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

Mediante la adición de estos permisos [Google Play filtrará automáticamente los dispositivos](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) sin necesidad de hardware específico. Para solucionarlo, agregue lo siguiente al archivo AssemblyInfo.cs del proyecto de Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwp"></a>[UWP](#tab/uwp)

No se requiere configuración adicional.

-----

## <a name="using-flashlight"></a>Uso de Flashlight

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La linterna se puede activar y desactivar a través de los métodos `TurnOnAsync` y `TurnOffAsync`:

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

### <a name="android"></a>[Android](#tab/android)

La clase Flashlight se ha optimizado en función del sistema operativo del dispositivo.

#### <a name="api-level-23-and-higher"></a>Nivel de API 23 y superior

En los niveles de API más recientes, se usa el [Modo Linterna](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) para activar o desactivar la unidad de flash del dispositivo.

#### <a name="api-level-22-and-lower"></a>Nivel de API 22 e inferior

Se crea una textura de la superficie de cámara para activar o desactivar el `FlashMode` de la unidad de la cámara.

### <a name="ios"></a>[iOS](#tab/ios)

Se usa [AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) para activar o desactivar el modo de linterna y flash del dispositivo.

### <a name="uwp"></a>[UWP](#tab/uwp)

Se usa [Lamp](https://docs.microsoft.com/uwp/api/windows.devices.lights.lamp) para detectar la primera luz en la parte posterior del dispositivo para activarla o desactivarla.

-----

## <a name="api"></a>API

- [Código fuente de Flashlight](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentación de API para Flashlight](xref:Xamarin.Essentials.Flashlight)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Flashlight-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
