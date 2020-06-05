---
title: "Xamarin.Essentials: permisos" description: "En este documento se describe la clase Permissions de Xamarin.Essentials, que proporciona la capacidad de comprobar y solicitar permisos en tiempo de ejecución."
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57 author: jamesmontemagno ms.author: jamont ms.custom: video ms.date: 01/06/2020 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Permisos

La clase **Permissions** proporciona la capacidad de comprobar y solicitar permisos en tiempo de ejecución.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

[!include[](~/essentials/includes/android-permissions.md)]

## <a name="using-permissions"></a>Uso de permisos

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Comprobación de permisos

Para comprobar el estado actual de un permiso, use el método `CheckStatusAsync` junto con el permiso específico para el que obtener el estado.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Se produce una excepción `PermissionException` si no se declara el permiso necesario.

Es mejor comprobar el estado del permiso antes de solicitarlo. Cada sistema operativo devuelve un estado predeterminado diferente si nunca se ha solicitado el usuario. iOS devuelve `Unknown`, mientras que otros devuelven `Denied`.

## <a name="requesting-permissions"></a>Solicitar permisos

Para solicitar un permiso a los usuarios, use el método `RequestAsync` junto con el permiso específico que se va a solicitar. Si el usuario ha concedido previamente el permiso y no lo ha revocado, este método devuelve `Granted` inmediatamente y no muestra ningún cuadro de diálogo.

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Se produce una excepción `PermissionException` si no se declara el permiso necesario.

Tenga en cuenta que, en algunas plataformas, una solicitud de permiso solo se puede activar una vez. El desarrollador debe controlar otros mensajes para comprobar si un permiso tiene el estado `Denied` y pedir al usuario que lo active manualmente.

## <a name="permission-status"></a>Estado del permiso

Al usar los elementos `CheckStatusAsync` o `RequestAsync`, se devuelve un estado `PermissionStatus` que se puede usar para determinar los pasos siguientes:

* Desconocido: el permiso tiene un estado desconocido.
* Denegado: el usuario denegó la solicitud del permiso.
* Deshabilitado: la característica está deshabilitada en el dispositivo.
* Concedido: el usuario concedió el permiso o se ha concedido automáticamente.
* Restringido: en un estado restringido.

## <a name="available-permissions"></a>Permisos disponibles

Xamarin.Essentials intenta abstraer tantos permisos como sea posible. Pero cada sistema operativo tiene un conjunto diferente de permisos en tiempo de ejecución. Además, hay diferencias a la hora de proporcionar una única API para algunos permisos. Esta es una guía para los permisos disponibles actualmente:

Guía de iconos:

* ![Totalmente compatible](~/media/shared/yes.png "Totalmente compatible"): compatible
* ![No compatible](~/media/shared/no.png "No compatible o requerido"): no compatible/requerido

| Permiso | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---:
| CalendarRead   | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| CalendarWrite | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Cámara | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![Compatible con Tizen](~/media/shared/yes.png "Compatible con Tizen") |
| ContactsRead | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| ContactsWrite | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Linterna | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![No compatible con iOS](~/media/shared/no.png "No compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![Compatible con Tizen](~/media/shared/yes.png "Compatible con Tizen") |
| LocationWhenInUse | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![Compatible con tvOS](~/media/shared/yes.png "Compatible con tvOS")  | ![Compatible con Tizen](~/media/shared/yes.png "Compatible con Tizen") |
| LocationAlways | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![Compatible con Tizen](~/media/shared/yes.png "Compatible con Tizen") |
| Multimedia | ![No compatible con Android](~/media/shared/no.png "No compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Micrófono | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![Compatible con Tizen](~/media/shared/yes.png "Compatible con Tizen") |
| Teléfono | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Fotos | ![No compatible con Android](~/media/shared/no.png "No compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![Compatible con tvOS](~/media/shared/yes.png "Compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Recordatorios | ![No compatible con Android](~/media/shared/no.png "No compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Sensores | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![Compatible con UWP](~/media/shared/yes.png "Compatible con UWP") | ![Compatible con watchOS](~/media/shared/yes.png "Compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| SMS | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| Voz | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![Compatible con iOS](~/media/shared/yes.png "Compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| StorageRead | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![No compatible con iOS](~/media/shared/no.png "No compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |
| StorageWrite | ![Compatible con Android](~/media/shared/yes.png "Compatible con Android") | ![No compatible con iOS](~/media/shared/no.png "No compatible con iOS") | ![No compatible con UWP](~/media/shared/no.png "No compatible con UWP") | ![No compatible con watchOS](~/media/shared/no.png "No compatible con watchOS") | ![No compatible con tvOS](~/media/shared/no.png "No compatible con tvOS") | ![No compatible con Tizen](~/media/shared/no.png "No compatible con Tizen") |

Si un permiso se marca con ![no admitido](~/media/shared/no.png "no admitido"), siempre devolverá `Granted` cuando se compruebe o solicite.

## <a name="general-usage"></a>Uso general
Aquí se muestra un patrón de uso general para controlar los permisos.

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

Cada tipo de permiso puede tener una instancia de este creada, para que se pueda llamar directamente a los métodos.

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>Extensión de permisos

La API Permissions se creó para aportar flexibilidad y extensibilidad a las aplicaciones que requieren validación o permisos adicionales que no están incluidos en Xamarin.Essentials. Cree una clase que herede de `BasePermission` e implemente los métodos abstractos necesarios. A continuación

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

Al implementar un permiso en una plataforma específica, se puede heredar de la clase `BasePlatformPermission`. Esto proporciona métodos auxiliares de la plataforma adicionales para comprobar automáticamente las declaraciones. Esto puede ayudar a la hora de crear permisos personalizados para realizar agrupaciones. Por ejemplo, puede solicitar acceso Leer y Escribir en el almacenamiento en Android con el siguiente permiso personalizado.

Cree un nuevo permiso en el proyecto desde el que está llamando a los permisos.

```csharp
public partial class ReadWriteStoragePermission  : Xamarin.Essentials.Permissions.BasePlatformPermission
{

}
```

En el proyecto de Android, amplíe el permiso con los permisos que quiere solicitar.

```csharp
public partial class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

Después, puede llamar al nuevo permiso desde la lógica compartida.

```csharp
await Permissions.RequestAsync<ReadWriteStoragePermission>();
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

Los permisos deben tener los atributos coincidentes establecidos en el archivo de manifiesto de Android.

Obtenga más información en el documento [Permisos en Xamarin.Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions).

# <a name="ios"></a>[iOS](#tab/ios)

Los permisos deben tener una cadena coincidente en el archivo `Info.plist`. Después de que se solicita y deniega un permiso, ya no aparece ningún elemento emergente al solicitar el permiso por segunda vez. Debe solicitar al usuario que ajuste la configuración manualmente en la pantalla Configuración de la aplicación en iOS.

Obtenga más información en el documento [Características de seguridad y privacidad de iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy).

# <a name="uwp"></a>[UWP](#tab/uwp)

Los permisos deben tener funcionalidades coincidentes declaradas en el manifiesto de paquete.

Obtenga más información en el documento [Declaración de las funcionalidades de la aplicación](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

--------------

## <a name="api"></a>API

- [Código fuente de los permisos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentación de la API Permisos](xref:Xamarin.Essentials.Permissions)


## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Permissions-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
