---
title: Administración de la configuración
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers implementa la administración de configuración para proporcionar la configuración de la aplicación y la configuración de usuario.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 618b3ee23698ef2d5e74d0fc4997f5f2e215d47a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374243"
---
# <a name="configuration-management"></a>Administración de la configuración

> [!NOTE]
> Este libro electrónico se publicó en el muelle de 2017 y no se ha actualizado desde entonces. Hay mucho en el libro que sigue siendo útil, pero parte del material es obsoleto.

La configuración permite la separación de los datos que configura el comportamiento de una aplicación desde el código, lo que permite cambiar el comportamiento sin volver a compilar la aplicación. Hay dos tipos de configuración: la configuración de la aplicación y la configuración de usuario.

La configuración de la aplicación son los datos que una aplicación crea y administra. Puede incluir datos como los puntos de conexión de servicio Web fijos, las claves de API y el estado de tiempo de ejecución. La configuración de la aplicación está vinculada a la existencia de la aplicación y solo es significativa para esa aplicación.

La configuración de usuario es la configuración personalizable de una aplicación que afecta al comportamiento de la aplicación y no requiere un reajuste frecuente. Por ejemplo, una aplicación podría permitir al usuario especificar de dónde recuperar datos y cómo mostrarlo en la pantalla.

Xamarin.Forms incluye un diccionario persistente que se puede usar para almacenar los datos de configuración. Se puede tener acceso a este diccionario mediante la [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) propiedad, y los datos que se colocan en él se guardan cuando la aplicación entra en un estado de suspensión y se restaura cuando la aplicación se reanuda o se inicia de nuevo. Además, la [`Application`](xref:Xamarin.Forms.Application) clase también tiene un [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) método que permite que una aplicación tenga su configuración guardada cuando sea necesario. Para obtener más información sobre este diccionario, consulte [propiedades Diccionario](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary).

Una desventaja de almacenar los datos mediante el Xamarin.Forms Diccionario persistente es que no se enlazan fácilmente a los datos. Por lo tanto, la aplicación móvil eShopOnContainers usa la biblioteca XAM. plugins. Settings, disponible en [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Esta biblioteca proporciona un enfoque coherente, con seguridad de tipos y multiplataforma para conservar y recuperar la configuración de la aplicación y del usuario, a la vez que usa la administración de configuración nativa proporcionada por cada plataforma. Además, es sencillo usar el enlace de datos para tener acceso a los datos de configuración expuestos por la biblioteca.

> [!NOTE]
> Aunque la biblioteca XAM. plugin. Settings puede almacenar la configuración de la aplicación y del usuario, no realiza ninguna distinción entre los dos.

## <a name="creating-a-settings-class"></a>Crear una clase de configuración

Al usar la biblioteca XAM. plugins. Settings, debe crearse una única clase estática que contendrá la configuración de aplicación y de usuario que requiere la aplicación. En el ejemplo de código siguiente se muestra la clase de configuración en la aplicación móvil eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

La configuración se puede leer y escribir a través de la `ISettings` API, que se proporciona mediante la biblioteca XAM. plugins. Settings. Esta biblioteca proporciona un singleton que se puede usar para tener acceso a la API, `CrossSettings.Current` , y una clase de configuración de la aplicación debe exponer este singleton a través de una `ISettings` propiedad.

> [!NOTE]
> Las directivas Using para los espacios de nombres plugin. Settings y plugin. Settings. Abstractions se deben agregar a una clase que requiera acceso a los tipos de biblioteca XAM. plugins. Settings.

## <a name="adding-a-setting"></a>Agregar una configuración

Cada configuración consta de una clave, un valor predeterminado y una propiedad. En el ejemplo de código siguiente se muestran los tres elementos de una configuración de usuario que representa la dirección URL base para la servicios en línea a la que se conecta la aplicación móvil eShopOnContainers:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

La clave siempre es una cadena const que define el nombre de clave, con el valor predeterminado para que la configuración sea un valor de solo lectura estático del tipo requerido. Si se proporciona un valor predeterminado, se garantiza que un valor válido esté disponible si se recupera una configuración no establecida.

La `UrlBase` propiedad estática utiliza dos métodos de la `ISettings` API para leer o escribir el valor de configuración. El `ISettings.GetValueOrDefault` método se usa para recuperar el valor de una configuración de almacenamiento específico de la plataforma. Si no se define ningún valor para la configuración, se recupera su valor predeterminado en su lugar. Del mismo modo, el `ISettings.AddOrUpdateValue` método se usa para conservar el valor de una configuración en un almacenamiento específico de la plataforma.

En lugar de definir un valor predeterminado dentro de la `Settings` clase, la `UrlBaseDefault` cadena obtiene su valor de la `GlobalSetting` clase. En el ejemplo de código siguiente `BaseEndpoint` se muestra la propiedad y el `UpdateEndpoint` método de esta clase:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Cada vez que `BaseEndpoint` se establece la propiedad, `UpdateEndpoint` se llama al método. Este método actualiza una serie de propiedades, que se basan en la configuración de `UrlBase` usuario proporcionada por la `Settings` clase que representa los diferentes puntos de conexión a los que se conecta la aplicación móvil eShopOnContainers.

## <a name="data-binding-to-user-settings"></a>Enlace de datos a la configuración del usuario

En la aplicación móvil eShopOnContainers, `SettingsView` expone dos configuraciones de usuario. Esta configuración permite configurar si la aplicación debe recuperar datos de microservicios que se implementan como contenedores de Docker, o si la aplicación debe recuperar datos de servicios ficticios que no requieren una conexión a Internet. Al elegir recuperar datos de microservicios en contenedor, se debe especificar una dirección URL de punto de conexión base para los microservicios. En la figura 7-1 `SettingsView` se muestra el momento en que el usuario ha elegido recuperar datos de microservicios en contenedores.

![Configuración de usuario expuesta por la aplicación móvil eShopOnContainers](configuration-management-images/settings-endpoint.png)

**Figura 7-1** : configuración de usuario expuesta por la aplicación móvil eShopOnContainers

El enlace de datos se puede utilizar para recuperar y establecer la configuración expuesta por la `Settings` clase. Esto se logra mediante controles en el enlace de la vista para ver las propiedades del modelo que, a su vez, tienen acceso a las propiedades de la `Settings` clase y para generar una notificación de cambio de propiedad si el valor de configuración ha cambiado. Para obtener información sobre cómo la aplicación móvil eShopOnContainers construye modelos de vista y los asocia a las vistas, vea [crear automáticamente un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

En el ejemplo de código siguiente [`Entry`](xref:Xamarin.Forms.Entry) se muestra el control de `SettingsView` que permite al usuario escribir una dirección URL de punto de conexión base para los microservicios en contenedores:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Este [`Entry`](xref:Xamarin.Forms.Entry) control se enlaza a la `Endpoint` propiedad de la `SettingsViewModel` clase mediante un enlace bidireccional. En el ejemplo de código siguiente se muestra la propiedad de punto de conexión:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Cuando se `Endpoint` establece la propiedad `UpdateEndpoint` , se llama al método, siempre que el valor proporcionado sea válido y se genere la notificación de cambio de propiedad. El siguiente ejemplo de código muestra el método `UpdateEndpoint`:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Este método actualiza la `UrlBase` propiedad de la `Settings` clase con el valor de dirección URL del punto de conexión base especificado por el usuario, lo que hace que se conserve en el almacenamiento específico de la plataforma.

Cuando `SettingsView` se navega a, `InitializeAsync` se ejecuta el método en la `SettingsViewModel` clase. El siguiente ejemplo de código muestra este método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

El método establece la `Endpoint` propiedad en el valor de la `UrlBase` propiedad en la `Settings` clase. El acceso a la `UrlBase` propiedad hace que la biblioteca XAM. plugins. Settings recupere el valor de configuración del almacenamiento específico de la plataforma. Para obtener información sobre cómo `InitializeAsync` se invoca el método, vea [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing-parameters-during-navigation).

Este mecanismo garantiza que cada vez que un usuario navega a SettingsView, la configuración de usuario se recupera del almacenamiento específico de la plataforma y se presenta a través del enlace de datos. Después, si el usuario cambia los valores de configuración, el enlace de datos garantiza que se vuelven a guardar inmediatamente en el almacenamiento específico de la plataforma.

## <a name="summary"></a>Resumen

La configuración permite la separación de los datos que configura el comportamiento de una aplicación desde el código, lo que permite cambiar el comportamiento sin volver a compilar la aplicación. La configuración de la aplicación son los datos que una aplicación crea y administra, y la configuración de usuario es la configuración personalizable de una aplicación que afecta al comportamiento de la aplicación y no requiere un reajuste frecuente.

La biblioteca XAM. plugins. Settings proporciona un enfoque coherente, con seguridad de tipos y multiplataforma para conservar y recuperar la configuración de la aplicación y del usuario, y el enlace de datos se puede usar para tener acceso a la configuración creada con la biblioteca.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
