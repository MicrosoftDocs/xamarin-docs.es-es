---
title: Permisos en Xamarin. Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: d3513dc086998306ae79855a2c4d8c1a3060ac47
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249705"
---
# <a name="permissions-in-xamarinandroid"></a>Permisos en Xamarin. Android

## <a name="overview"></a>Información general

Las aplicaciones de Android se ejecutan en su propio espacio aislado y, por motivos de seguridad, no tienen acceso a determinados recursos del sistema o hardware del dispositivo. El usuario debe conceder explícitamente el permiso a la aplicación para que pueda usar estos recursos. Por ejemplo, una aplicación no puede tener acceso al GPS en un dispositivo sin permiso explícito del usuario. Android producirá una `Java.Lang.SecurityException` excepción si una aplicación intenta tener acceso a un recurso protegido sin permiso.

El desarrollador de la aplicación declara los permisos en **archivo AndroidManifest. XML** cuando se desarrolla la aplicación. Android tiene dos flujos de trabajo diferentes para obtener el consentimiento del usuario para esos permisos:

- En el caso de las aplicaciones destinadas a Android 5,1 (nivel de API 22) o inferior, la solicitud de permiso se produjo cuando se instaló la aplicación. Si el usuario no ha concedido los permisos, no se instalará la aplicación. Una vez instalada la aplicación, no hay ninguna manera de revocar los permisos excepto desinstalando la aplicación.
- A partir de Android 6,0 (nivel de API 23), a los usuarios se les dio más control sobre los permisos. pueden conceder o revocar permisos, siempre y cuando la aplicación esté instalada en el dispositivo. En esta captura de pantalla se muestra la configuración de permisos de la aplicación Google contacts. Muestra los distintos permisos y permite al usuario habilitar o deshabilitar permisos:

![Pantalla de permisos de ejemplo](permissions-images/01-permissions-check.png) 

Las aplicaciones de Android deben comprobar en tiempo de ejecución para ver si tienen permiso de acceso a un recurso protegido. Si la aplicación no tiene permiso, debe realizar solicitudes mediante las nuevas API proporcionadas por el Android SDK para que el usuario conceda los permisos. Los permisos se dividen en dos categorías:

- **Permisos normales** &ndash; Se trata de permisos que suponen un pequeño riesgo de seguridad para la seguridad o la privacidad del usuario. Android 6,0 concederá automáticamente permisos normales en el momento de la instalación. Consulte la documentación de Android para obtener una [lista completa de los permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html).
- **Permisos peligrosos** &ndash; A diferencia de los permisos normales, los permisos peligrosos son aquellos que protegen la seguridad o la privacidad del usuario. El usuario debe concederlas explícitamente. El envío o la recepción de un mensaje SMS es un ejemplo de una acción que requiere un permiso peligroso.

> [!IMPORTANT]
> La categoría a la que pertenece un permiso puede cambiar con el tiempo.  Es posible que un permiso categorizado como permiso "normal" se pueda elevar en los niveles de API futuros a un permiso peligroso.

Los permisos peligrosos se subdividen en [_grupos de permisos_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups). Un grupo de permisos contendrá permisos que están relacionados lógicamente. Cuando el usuario concede el permiso a un miembro de un grupo de permisos, Android concede automáticamente permiso a todos los miembros de ese grupo. Por ejemplo, el [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE) grupo de permisos contiene los `WRITE_EXTERNAL_STORAGE` permisos `READ_EXTERNAL_STORAGE` y. Si el usuario concede el permiso `READ_EXTERNAL_STORAGE`a, el `WRITE_EXTERNAL_STORAGE` permiso se concede automáticamente al mismo tiempo.

Antes de solicitar uno o más permisos, se recomienda proporcionar una lógica en lo que respecta a la razón por la que la aplicación requiere el permiso antes de solicitar el permiso. Una vez que el usuario entiende la razón, la aplicación puede solicitar permiso al usuario. Al comprender el razonamiento, el usuario puede tomar una decisión informada si desea conceder el permiso y comprender las repercusiones en caso contrario. 

Todo el flujo de trabajo de comprobación y solicitud de permisos se conoce como comprobación de _permisos en tiempo de ejecución_ y se puede resumir en el diagrama siguiente: 

[![Gráfico de flujo de comprobación de permisos de tiempo de ejecución](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

La biblioteca de compatibilidad de Android transporta algunas de las nuevas API para los permisos de versiones anteriores de Android. Estas API de traslado comprobarán automáticamente la versión de Android en el dispositivo, por lo que no es necesario realizar una comprobación de nivel de API cada vez.  

En este documento se explica cómo agregar permisos a una aplicación de Xamarin. Android y cómo las aplicaciones destinadas a Android 6,0 (nivel de API 23) o superior deben realizar una comprobación de permisos en tiempo de ejecución.

> [!NOTE]
> Es posible que los permisos del hardware afecten al modo en que la aplicación se filtra por Google Play. Por ejemplo, si la aplicación requiere el permiso para la cámara, Google Play no mostrará la aplicación en el Google Play Store en un dispositivo que no tenga una cámara instalada.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

Se recomienda encarecidamente que los proyectos de Xamarin. Android incluyan el paquete NuGet [Xamarin. Android. support. compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) . Este paquete trasladará las API específicas de permisos a versiones anteriores de Android, lo que proporciona una interfaz común sin necesidad de comprobar constantemente la versión de Android en la que se ejecuta la aplicación.

## <a name="requesting-system-permissions"></a>Solicitar permisos del sistema

El primer paso para trabajar con los permisos de Android es declarar los permisos en el archivo de manifiesto de Android. Esto se debe hacer independientemente del nivel de API que destino la aplicación.

Las aplicaciones que tienen como destino Android 6,0 o posterior no pueden suponer que, como el usuario concedió permiso en algún momento del pasado, el permiso será válido la próxima vez. Una aplicación destinada a Android 6,0 siempre debe realizar una comprobación de permiso en tiempo de ejecución. Las aplicaciones destinadas a Android 5,1 o inferior no necesitan realizar una comprobación de permisos en tiempo de ejecución.

> [!NOTE]
> Las aplicaciones solo deben solicitar los permisos que requieran.

### <a name="declaring-permissions-in-the-manifest"></a>Declarar permisos en el manifiesto

Los permisos se agregan al **archivo archivo AndroidManifest. XML** con el `uses-permission` elemento. Por ejemplo, si una aplicación va a buscar la posición del dispositivo, requiere permisos de ubicación correctos y del curso. Los dos elementos siguientes se agregan al manifiesto: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Es posible declarar los permisos mediante la compatibilidad de la herramienta integrada en Visual Studio:

1. Haga doble clic en **propiedades** en el **Explorador de soluciones** y seleccione la pestaña **manifiesto de Android** en el ventana Propiedades:

    [![Permisos necesarios en la pestaña manifiesto de Android](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. Si la aplicación aún no tiene un archivo AndroidManifest. XML, haga clic **en no se encontró archivo AndroidManifest. Xml. Haga clic para agregar** una, como se muestra a continuación:

    [![No hay ningún mensaje archivo AndroidManifest. XML](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación en la lista de **permisos necesarios** y guárdelos:

    [![Ejemplo de permisos de cámara seleccionados](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Es posible declarar los permisos mediante la compatibilidad de la herramienta integrada en Visual Studio para Mac:

1. Haga doble clic en el proyecto en el **Panel de solución** y seleccione **Opciones > compilar > aplicación Android**:

    [![Se muestra la sección permisos necesarios](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. Haga clic en el botón **Agregar manifiesto de Android** si el proyecto aún no tiene un **archivo AndroidManifest. XML**:

    [![Falta el manifiesto de Android del proyecto](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. Seleccione los permisos que necesita la aplicación en la lista **permisos necesarios** y haga clic en **Aceptar**:

    [![Ejemplo de permisos de cámara seleccionados](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin. Android agregará automáticamente algunos permisos en el momento de la compilación para depurar las compilaciones. Esto hará que la depuración de la aplicación sea más sencilla. En concreto, dos permisos importantes son `INTERNET` y `READ_EXTERNAL_STORAGE`. Estos permisos definidos automáticamente no aparecerán en la lista de **permisos necesarios** . Sin embargo, las compilaciones de versión solo usan los permisos que se establecen explícitamente en la lista de **permisos necesarios** . 

En el caso de las aplicaciones que tienen como destino Android 5.1 (nivel de API 22) o inferior, no es necesario hacer nada más. Las aplicaciones que se ejecutarán en Android 6,0 (API 23 nivel 23) o superior deben continuar con la siguiente sección sobre cómo realizar comprobaciones de permisos en tiempo de ejecución. 

### <a name="runtime-permission-checks-in-android-60"></a>Comprobaciones de permisos en tiempo de ejecución en Android 6,0

El `ContextCompat.CheckSelfPermission` método (disponible con la biblioteca de compatibilidad de Android) se usa para comprobar si se ha concedido un permiso específico. Este método devolverá [`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission) una enumeración que tiene uno de dos valores:

- **`Permission.Granted`** &ndash; Se ha concedido el permiso especificado.
- **`Permission.Denied`** &ndash; No se ha concedido el permiso especificado.

Este fragmento de código es un ejemplo de cómo comprobar el permiso de cámara en una actividad: 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

Es aconsejable informar al usuario sobre por qué es necesario un permiso para una aplicación, de modo que se pueda tomar una decisión informada para conceder el permiso. Un ejemplo de esto sería una aplicación que toma fotos y las etiqueta geográficamente. Está claro al usuario que el permiso de cámara es necesario, pero puede que no esté claro por qué la aplicación también necesita la ubicación del dispositivo. El razonamiento debe mostrar un mensaje para ayudar al usuario a entender por qué es conveniente el permiso de ubicación y que se requiere el permiso de la cámara.

El `ActivityCompat.ShouldShowRequestPermissionRationale` método se usa para determinar si se debe mostrar al usuario la lógica. Este método devolverá `true` si se debe mostrar el razonamiento para un permiso determinado. En esta captura de pantalla se muestra un ejemplo de un snackbar que se muestra en una aplicación que explica el motivo por el que la aplicación necesita conocer la ubicación del dispositivo:

![Razonamiento de la ubicación](permissions-images/07-rationale-snackbar.png) 

Si el usuario concede el permiso, se `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` debe llamar al método. Este método requiere los siguientes parámetros:

- **actividad** de &ndash; Se trata de la actividad que solicita los permisos y que se va a notificar mediante Android de los resultados.
- **permisos** de &ndash; Una lista de los permisos que se solicitan.
- **antes si requestcode** Valor entero que se usa para hacer coincidir los resultados de la solicitud de permiso con `RequestPermissions` una llamada. &ndash; Este valor debería ser mayor que cero.

Este fragmento de código es un ejemplo de los dos métodos que se han analizado. En primer lugar, se realiza una comprobación para determinar si se debe mostrar la racionalización de permisos. Si se va a mostrar la justificación, se muestra un snackbar con la lógica. Si el usuario hace clic en **Aceptar** en snackbar, la aplicación solicitará los permisos. Si el usuario no acepta la razón, la aplicación no debe continuar para solicitar permisos. Si no se muestra la racionalización, la actividad solicitará el permiso:

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission`se puede llamar a aunque el usuario ya haya concedido el permiso. Las llamadas posteriores no son necesarias, pero proporcionan al usuario la oportunidad de confirmar (o revocar) el permiso. Cuando `RequestPermission` se llama a, el control se entrega al sistema operativo, que mostrará una interfaz de usuario para aceptar los permisos:  

![Cuadro de diálogo permssion](permissions-images/08-location-permission-dialog.png)

Una vez finalizado el usuario, Android devolverá los resultados a la actividad a través de `OnRequestPermissionResult`un método de devolución de llamada,. Este método forma parte de la interfaz `ActivityCompat.IOnRequestPermissionsResultCallback` que debe ser implementada por la actividad. Esta interfaz tiene un único método, `OnRequestPermissionsResult`, que se invocará mediante Android para informar a la actividad de las opciones del usuario. Si el usuario ha concedido el permiso, la aplicación puede continuar y usar el recurso protegido. A continuación se muestra un ejemplo `OnRequestPermissionResult` de cómo implementar: 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  

## <a name="summary"></a>Resumen

En esta guía se describe cómo agregar y comprobar los permisos en un dispositivo Android. Las diferencias en el modo en que los permisos funcionan entre aplicaciones Android anteriores (nivel de API < 23) y nuevas aplicaciones Android (nivel de API > 22). Se ha explicado cómo realizar comprobaciones de permisos en tiempo de ejecución en Android 6,0.

## <a name="related-links"></a>Vínculos relacionados

- [Lista de permisos normales](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [Aplicación de ejemplo de permisos en tiempo de ejecución](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Controlar permisos en Xamarin. Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
