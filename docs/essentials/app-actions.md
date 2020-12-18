---
title: 'Xamarin.Essentials: Acciones de aplicación'
description: La clase Accelerometer en Xamarin.Essentials permite crear y responder a accesos directos de aplicaciones desde el icono de la aplicación.
ms.assetid: 5edf9bc5-b721-448c-a8a2-0a9d4d0c792c
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e85206d8e48db1a6f168c6f89ca494519a0fe95c
ms.sourcegitcommit: 4bb12419da2547c0afc17903ae46052b29cd0dc4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628188"
---
# <a name="no-locxamarinessentials-app-actions"></a>Xamarin.Essentials: Acciones de aplicación

La clase **AppActions** permite crear y responder a accesos directos de aplicaciones desde el icono de la aplicación.

![API de versión preliminar](~/media/shared/preview.png)

## <a name="get-started"></a>Introducción

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la función de **AppActions**, se requiere la configuración específica siguiente para la plataforma.

# <a name="android"></a>[Android](#tab/android)

Agregue el filtro de intención a la clase `MainActivity`:

```csharp
[IntentFilter(
        new[] { Xamarin.Essentials.Platform.Intent.ActionAppAction },
        Categories = new[] { Intent.CategoryDefault })]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    ...
```

Luego, agregue la lógica siguiente para controlar las acciones:

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume(this);
}

protected override void OnNewIntent(Intent intent)
{
    base.OnNewIntent(intent);

    Xamarin.Essentials.Platform.OnNewIntent(intent);
}
```

# <a name="ios"></a>[iOS](#tab/ios)

En `AppDelegate.cs`, agregue la lógica siguiente para controlar las acciones:

```csharp
public override void PerformActionForShortcutItem(UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
    => Xamarin.Essentials.Platform.PerformActionForShortcutItem(application, shortcutItem, completionHandler);
```

# <a name="uwp"></a>[UWP](#tab/uwp)

En el archivo `App.xaml.cs` del método `OnLaunched` agregue la lógica siguiente en la parte inferior del método:

```csharp
Xamarin.Essentials.Platform.OnLaunched(e);
```

-----

## <a name="create-actions"></a>Creación de acciones

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```
Las acciones de aplicación se pueden crear en cualquier momento, pero se crean a menudo cuando se inicia una aplicación. Llame al método `SetAsync` para crear la lista de acciones de la aplicación.


```csharp
try
{
    await AppActions.SetAsync(
        new AppAction("app_info", "App Info", icon: "app_info_action_icon"),
        new AppAction("battery_info", "Battery Info"));
}
catch (FeatureNotSupportedException ex)
{
    Debug.WriteLine("App Actions not supported");
}
```

Si las acciones de aplicación no se admiten en la versión específica del sistema operativo, se producirá un elemento `FeatureNotSupportedException`. 

Se pueden especificar las propiedades siguientes en un elemento `AppAction`:

* Id: identificador único que se usa para responder a la acción pulsar.
* Título: título visible que se va a mostrar.
* Subtítulo: si se admite un subtítulo para mostrar bajo el título.
* Icono: debe coincidir con los iconos del directorio de recursos correspondiente en cada plataforma.

![Acciones de aplicación en la pantalla de inicio](images/appactions.png)

## <a name="responding-to-actions"></a>Respuesta a acciones

Cuando la aplicación empieza el registro para el evento `OnAppAction`. Cuando se selecciona una acción de aplicación, el evento se enviará con información relativa a qué acción se ha seleccionado.

```csharp
public App()
{
    //...
    AppActions.OnAppAction += AppActions_OnAppAction;
}

void AppActions_OnAppAction(object sender, AppActionEventArgs e)
{
    // Don't handle events fired for old application instances
    // and cleanup the old instance's event handler
    if (Application.Current != this && Application.Current is App app)
    {
        AppActions.OnAppAction -= app.AppActions_OnAppAction;
        return;
    }
    Device.BeginInvokeOnMainThread(async () =>
    {
        var page = e.AppAction.Id switch
        {
            "battery_info" => new BatteryPage(),
            "app_info" => new AppInfoPage(),
            _ => default(Page)
        };
        if (page != null)
        {
            await Application.Current.MainPage.Navigation.PopToRootAsync();
            await Application.Current.MainPage.Navigation.PushAsync(page);
        }
    });
}
```

## <a name="getactions"></a>GetActions
Se puede obtener la lista actual de las acciones de aplicación mediante una llamada a `AppActions.GetAsync()`.

## <a name="api"></a>API

- [Código fuente de AppActions](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppActions)
- [Documentación de AppActions API](xref:Xamarin.Essentials.AppActions)
