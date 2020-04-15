---
title: Indexación de la aplicación y vinculación en profundidad
description: En este artículo se explica cómo usar la indexación de la aplicación y la vinculación en profundidad para que el contenido de las aplicaciones Xamarin.Forms permita efectuar búsquedas en dispositivos iOS y Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: fcd8333a0623058fceb486183ddb995e85eaf18a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940323"
---
# <a name="application-indexing-and-deep-linking"></a>Indexación de la aplicación y vinculación en profundidad

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_La indexación de la aplicación permite a aplicaciones que de lo contrario se olvidarían tras unos pocos usos mantener su pertinencia al aparecer en los resultados de la búsqueda. La vinculación en profundidad permite a las aplicaciones responder a un resultado de la búsqueda que contiene datos de aplicación, normalmente al navegar a una página a la que se hace referencia a partir de un vínculo profundo. En este artículo se explica cómo usar la indexación de la aplicación y la vinculación en profundidad para que el contenido de las aplicaciones Xamarin.Forms permita efectuar búsquedas en dispositivos iOS y Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Vídeo de vinculación en profundidad con Xamarin.Forms y Azure**

La indexación de la aplicación y la vinculación en profundidad de Xamarin.Forms proporcionan una API para la publicación de metadatos y la indexación de las aplicaciones a medida que los usuarios navegan por ellas. Posteriormente, el contenido indexado se puede buscar en una búsqueda web, de Spotlight o de Google. Al pulsar en un resultado de la búsqueda que contenga un vínculo profundo, se desencadenará un evento que normalmente se usa para navegar a la página a la que se hace referencia desde el vínculo profundo y que puede controlarse mediante una aplicación.

La aplicación de ejemplo muestra una aplicación de lista de tareas pendientes en la que los datos se almacenan en una base de datos SQLite local, tal como aparece en las siguientes capturas de pantalla:

![](deep-linking-images/screenshots.png "TodoList Application")

Se indexan todas las instancias del elemento `TodoItem` que crea el usuario. A continuación, se puede usar una búsqueda específica de la plataforma para localizar los datos indexados de la aplicación. Cuando el usuario pulsa un resultado de la búsqueda de la aplicación, esta se inicia, se navega al elemento `TodoItemPage` y se muestra el elemento `TodoItem` al que se hace referencia desde el vínculo profundo.

Para obtener más información sobre el uso de una base de datos SQLite, consulte [Bases de datos locales de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md).

> [!NOTE]
> La funcionalidad de la indexación de la aplicación y de la vinculación en profundidad de Xamarin.Forms solo está disponible en las plataformas iOS y Android, y requiere como mínimo iOS 9 y API 23, respectivamente.

## <a name="setup"></a>Programa de instalación

En las siguientes secciones se proporcionan las instrucciones de instalación adicionales para usar esta característica en las plataformas iOS y Android.

### <a name="ios"></a>iOS

En la plataforma iOS, asegúrese de que el proyecto de plataforma de iOS establezca el archivo **Entitlements.plist** como de derechos personalizados para firmar el conjunto.

Para usar vínculos universales de iOS, realice lo siguiente:

1. Agregue un derecho de dominios asociados a la aplicación con la clave `applinks`, incluidos todos los dominios que admitirá la aplicación.
1. Agregue un archivo de asociación de sitios de la aplicación de Apple a su sitio web.
1. Agregue la clave `applinks` al archivo de asociación de sitios de la aplicación de Apple.

Para obtener más información, vea [Allowing Apps and Websites to Link to Your Content](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) (Permitir la vinculación de contenido a aplicaciones y sitios web) en developer.apple.com.

### <a name="android"></a>Android

En la plataforma Android, se deben cumplir los siguientes requisitos previos para usar la funcionalidad de la indexación de la aplicación y de la vinculación en profundidad:

1. Es necesario tener una versión de la aplicación publicada en Google Play.
1. Es necesario haber registrado un sitio web complementario para la aplicación en la consola del desarrollador de Google. Una vez que la aplicación esté asociada a un sitio web, las direcciones URL se pueden indexar de forma que funcionen tanto para el sitio web como para la aplicación. De este modo, podrán aparecer después en los resultados de la búsqueda. Para obtener más información, vea [Indexar aplicaciones en la Búsqueda de Google](https://support.google.com/googleplay/android-developer/answer/6041489) en el sitio web de Google.
1. La aplicación debe admitir las intenciones de dirección URL HTTP en la clase `MainActivity`, que indican a la indexación de la aplicación los tipos de esquemas de datos de direcciones URL a los que esta puede dar respuesta. Para obtener más información, vea [Configuración del filtro de intención](~/android/platform/app-linking.md#configure-intent-filter).

Una vez cumplidos estos requisitos previos, para usar la indexación de la aplicación y la vinculación en profundidad de Xamarin.Forms en la plataforma Android se requiere la siguiente configuración adicional:

1. Instale el paquete NuGet [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) en el proyecto de la aplicación de Android.
1. En el archivo **MainActivity.cs**, agregue una declaración para usar el espacio de nombres `Xamarin.Forms.Platform.Android.AppLinks`.
1. En el archivo **MainActivity.cs**, agregue una declaración para usar el espacio de nombres `Firebase`.
1. En un explorador web, cree un nuevo proyecto con la [consola Firebase](https://console.firebase.google.com/).
1. En la consola Firebase, agregue Firebase a la aplicación Android y escriba los datos necesarios.
1. Descargue el archivo **google-services.json** resultante.
1. Agregue el archivo **google-services.json** al directorio raíz del proyecto de Android y establezca el valor **Acción de compilación** en **GoogleServicesJson**.
1. En la invalidación del elemento `MainActivity.OnCreate`, agregue la siguiente línea de código debajo de `Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Al agregar **google-services.json** al proyecto (y al establecer la acción de compilación *GoogleServicesJson*\*), el proceso de compilación extraerá la clave de API y el id. de cliente. A continuación, las credenciales se agregarán al archivo de manifiesto generado.

> [!NOTE]
> En este artículo, los términos vínculos de aplicación y vínculos profundos suelen usarse indistintamente. Sin embargo, en Android estos términos tienen significados diferentes. En Android, un vínculo profundo es un filtro de intención que permite a los usuarios especificar directamente una actividad concreta en la aplicación. Al hacer clic en un vínculo profundo se puede abrir un cuadro de diálogo de desambiguación, que permite al usuario seleccionar una de las distintas aplicaciones que pueden controlar la dirección URL. Un vínculo de aplicación de Android es un vínculo profundo basado en la dirección URL de su sitio web, que se ha verificado que pertenece al sitio web. Al hacer clic en un vínculo de aplicación, se abre la aplicación si está instalada, sin que se abra un cuadro de diálogo de desambiguación.

Para obtener más información, vea [Deep Link Content with Xamarin.Forms URL Navigation](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) (Contenido de vínculos profundos con la navegación de direcciones URL Xamarin.Forms) en el blog de Xamarin.

## <a name="indexing-a-page"></a>Indexación de una página

El proceso de indexación de una página y de exposición en una búsqueda de Spotlight y Google es el que sigue:

1. Cree un elemento [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) que contenga los metadatos necesarios para indexar la página y un vínculo profundo para volver a ella cuando el usuario seleccione el contenido indexado en los resultados de la búsqueda.
1. Registre la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) con el fin de indexarla para la búsqueda.

En el siguiente ejemplo de código se muestra cómo crear una instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry):

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

La instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) contiene un número de propiedades cuyos valores son necesarios para indexar la página y crear un vínculo profundo. Las propiedades [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title), [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) y [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) se usan para identificar el contenido indexado cuando este aparece en los resultados de la búsqueda. La propiedad [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) se establece en `true` para indicar que se está visualizando el contenido indexado. La propiedad [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) es un elemento `Uri` que contiene la información necesaria para volver a la página actual y mostrar el elemento `TodoItem` actual. A continuación encontrará un elemento `Uri` de muestra para la aplicación de ejemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Este elemento `Uri` contiene toda la información necesaria para iniciar la aplicación `deeplinking`, navegar a `DeepLinking.TodoItemPage` y mostrar `TodoItem`, que tiene un valor `ID` de 2.

## <a name="registering-content-for-indexing"></a>Registro del contenido para la indexación

Una vez que la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) se ha creado, esta se debe registrar para la indexación con el fin de que aparezca en los resultados de la búsqueda. Esto se consigue con el método [`RegisterLink`](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)), como se muestra en el ejemplo de código siguiente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Esto agrega la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) a la colección [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) de la aplicación.

> [!NOTE]
> El método `RegisterLink` también se puede utilizar para actualizar el contenido que se ha indexado para una página.

Una vez que la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) se ha registrado para la indexación, esta puede aparecer en los resultados de la búsqueda. La siguiente captura de pantalla muestra el contenido indexado que aparece en los resultados de la búsqueda en la plataforma iOS:

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>Anulación del registro del contenido indexado

El método [`DeregisterLink`](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) se usa para eliminar el contenido indexado de los resultados de la búsqueda, tal como se muestra en el siguiente ejemplo de código:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Esto elimina la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) de la colección [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) de la aplicación.

> [!NOTE]
> En Android no es posible eliminar contenido indexado de los resultados de la búsqueda.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Respuesta a un vínculo profundo

Cuando aparece contenido indexado en los resultados de la búsqueda y un usuario lo selecciona, la clase `App` de la aplicación recibirá una solicitud para controlar el elemento `Uri` incluido en dicho contenido indexado. Esta solicitud se puede procesar en la invalidación del elemento [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)), tal como se muestra en el siguiente ejemplo de código:

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

El método [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) comprueba que el elemento `Uri` recibido esté dirigido a la aplicación, antes de analizar el elemento `Uri` para la página a la que se navegará y el parámetro que se pasará a la página. Luego, se crea una instancia de la página a la que se navegará y se recupera el elemento `TodoItem` representado por el parámetro de la página. Posteriormente, el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página a la que se navegará se establece en `TodoItem`. Esto garantiza que, cuando el método [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) muestre `TodoItemPage`, se muestre también el elemento `TodoItem` cuyo valor `ID` se encuentra en el vínculo profundo.

## <a name="making-content-available-for-search-indexing"></a>Disponibilidad del contenido para la indexación de la búsqueda

Cada vez que se muestra la página representada por un vínculo profundo, la propiedad [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) se puede establecer en `true`. En iOS y Android, esto hace que la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) esté disponible para la indexación de la búsqueda y, solo en iOS, también facilita la instancia `AppLinkEntry` para Handoff. Para obtener más información sobre Handoff, vea la [introducción a Handoff](~/ios/platform/handoff.md).

El siguiente ejemplo de código muestra cómo establecer la propiedad [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) en `true` en la invalidación del elemento [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing):

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

Asimismo, al salir de una página representada por un vínculo profundo, la propiedad [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) se puede establecer en `false`. En iOS y Android, esto evita que la instancia [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) se anuncie en la indexación de la búsqueda y, solo en iOS, también evita anunciar la instancia `AppLinkEntry` para Handoff. Esto se puede conseguir en la invalidación del elemento [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing), tal como se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Envío de datos a Handoff

En iOS, los datos específicos de la aplicación se pueden almacenar al realizar la indexación de la página. Para hacerlo, agregue datos a la colección [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues), que es un elemento `Dictionary<string, string>` para almacenar los pares clave-valor que se usan en Handoff. Handoff es una forma que tiene el usuario de iniciar una actividad en uno de sus dispositivos y continuar con ella en otro (de acuerdo con la identificación de la cuenta de iCloud del usuario). El siguiente código muestra un ejemplo de almacenamiento de pares clave-valor específicos de la aplicación:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Los valores almacenados en la colección [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) se almacenarán en los metadatos de la página indexada y se restaurarán cuando el usuario pulse un resultado de la búsqueda que contenga un vínculo profundo, o bien al utilizar Handoff para ver el contenido en otro dispositivo en el que haya iniciado sesión.

Además, se pueden especificar valores para las siguientes claves:

- `contentType`: `string` que especifica el identificador de tipo uniforme del contenido indexado. La convención que se recomienda usar para este valor es el nombre del tipo de la página que contiene el contenido indexado.
- `associatedWebPage`: `string` que representa la página web que se va a visitar si el contenido indexado también se puede ver en la Web, o bien si la aplicación admite vínculos profundos de Safari.
- `shouldAddToPublicIndex`: `string` de `true` o `false` que controla si se debe agregar el contenido indexado al índice de la nube pública de Apple y que, posteriormente, se puede presentar a los usuarios que no hayan instalado la aplicación en su dispositivo iOS. Sin embargo, el hecho de que el contenido se haya establecido para la indexación pública no significa que se agregue automáticamente al índice de la nube pública de Apple. Para obtener más información, vea [Indexación de búsqueda pública](~/ios/platform/search/nsuseractivity.md). Tenga en cuenta que, al agregar datos personales a la colección [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues), esta clave se debe establecer en `false`.

> [!NOTE]
> La colección `KeyValues` no se usa en la plataforma Android.

Para obtener más información sobre Handoff, vea la [introducción a Handoff](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar la indexación de la aplicación y la vinculación en profundidad para que el contenido de las aplicaciones Xamarin.Forms permita efectuar búsquedas en dispositivos iOS y Android. La indexación de la aplicación permite a aplicaciones que de lo contrario se olvidarían tras unos pocos usos mantener su pertinencia al aparecer en los resultados de la búsqueda.

## <a name="related-links"></a>Vínculos relacionados

- [Muestra de vinculación en profundidad](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)
- [API de búsqueda iOS](~/ios/platform/search/index.md)
- [Vinculación de aplicaciones en Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
