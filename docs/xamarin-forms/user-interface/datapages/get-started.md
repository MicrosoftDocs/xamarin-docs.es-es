---
title: Introducción a DataSourceControl
description: En este artículo se explica cómo empezar a crear una página sencilla controlada por datos mediante Xamarin.Forms DataSourceControl.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c6534067388b59c1e191426011fd0e6b8560348f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759732"
---
# <a name="getting-started-with-datapages"></a>Introducción a DataSourceControl

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "Esta API está actualmente en versión preliminar")

> [!IMPORTANT]
> Las páginas de formularios requieren una referencia de tema de Xamarin. Forms para representar. Esto implica la instalación del paquete de Nuget [Xamarin. Forms. theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) en el proyecto, seguido de los paquetes de Nuget [Xamarin. Forms. theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [Xamarin. Forms. theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Para empezar a crear una página sencilla controlada por datos mediante la versión preliminar de DataSourceControl, siga estos pasos. Este usa demostración crea un estilo codificado de forma rígida ("eventos") en la vista previa que sólo funciona con el formato específico de JSON en el código.

[![](get-started-images/demo-sml.png "Aplicación de ejemplo DataSourceControl")](get-started-images/demo.png#lightbox "aplicación de ejemplo DataSourceControl")

## <a name="1-add-nuget-packages"></a>1. Agregar paquetes de NuGet

Agregue estos paquetes de Nuget a los proyectos de biblioteca y aplicación Xamarin.Forms .NET Standard:

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- Una implementación de tema Nuget (p ej. Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. Agregar referencia de tema

En el **App.xaml** , agregue un personalizado `xmlns:mytheme` para el tema y asegúrese de que el tema se combina con el diccionario de recursos de la aplicación:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> También debe seguir los pasos para [cargar ensamblados de tema (a continuación)](#loadtheme) agregando código reutilizable `AppDelegate` a iOS `MainActivity`y Android. Se mejorará en versión preliminar futura.

## <a name="3-add-a-xaml-page"></a>3. Agregue una página XAML

Agregar una nueva página XAML a la aplicación de Xamarin.Forms y *cambie la clase base* desde `ContentPage` a `Xamarin.Forms.Pages.ListDataPage`. Esto debe hacerse en C# y el XAML:

**Archivo de C#**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**Archivo XAML**

Además de cambiar el elemento raíz para `<p:ListDataPage>` el espacio de nombres personalizado `xmlns:p` también se debe agregar:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Subclase de la aplicación**

Cambiar el `App` constructor de clase para que la `MainPage` está establecido en un `NavigationPage` que contiene el nuevo `SessionDataPage`. Una página de navegación *debe* usarse.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Agregar el origen de datos

Eliminar el `Content` elemento y reemplazarlo con un `p:ListDataPage.DataSource` para rellenar la página con datos. En el ejemplo siguiente Json remoto se está cargando el archivo de datos desde una dirección URL.

> [!NOTE]
> La vista previa requiere `StyleClass` un atributo para proporcionar sugerencias de representación para el origen de datos. El `StyleClass="Events"` hace referencia a un diseño que está predefinido en la vista previa y contiene los estilos *codificado de forma rígida* para que coincida con el origen de datos JSON que se va a usar.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**Datos JSON**

Un ejemplo de los datos JSON desde el [origen demostración](http://demo3143189.mockable.io/sessions) se muestra a continuación:

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. ¡Ejecución!

Deben dar como resultado de los pasos anteriores en una página de datos de trabajo:

[![](get-started-images/demo-sml.png "Aplicación de ejemplo DataSourceControl")](get-started-images/demo.png#lightbox "aplicación de ejemplo DataSourceControl")

Esto funciona porque el estilo pregenerado **"Eventos"** existe en el paquete Nuget del tema claro y tiene los estilos definidos que coinciden con el origen de datos (p ej. "title", "image", "presentador").

Los "eventos" `StyleClass` está diseñado para mostrar el `ListDataPage` control con un personalizado `CardView` control que es definido en Xamarin.Forms.Pages. El `CardView` control tiene tres propiedades: `ImageSource`, `Text`, y `Detail`. El tema está codificado para enlazar tres campos del origen de datos (desde el archivo JSON) a estas propiedades para mostrarlas.

## <a name="5-customize"></a>5. Personalizar

El estilo heredado puede invalidarse especificando una plantilla y usar enlaces de orígenes de datos. El XAML siguiente declara una plantilla personalizada para cada fila con el nuevo `ListItemControl` y `{p:DataSourceBinding}` sintaxis que se incluye en el **Xamarin.Forms.Pages** Nuget:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

Proporcionando un `DataTemplate` este código invalida el `StyleClass` y en su lugar, usa el diseño predeterminado para un `ListItemControl`.

[![](get-started-images/custom-sml.png "Aplicación de ejemplo DataSourceControl")](get-started-images/custom.png#lightbox "aplicación de ejemplo DataSourceControl")

Los programadores que prefieren C# o XAML puede crear datos de origen enlaces demasiado (Recuerde incluir un `using Xamarin.Forms.Pages;` instrucción):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

Es un poco más trabajo crear temas desde cero, pero las versiones preliminares futuras harán que esto sea más fácil.

## <a name="troubleshooting"></a>Solución de problemas

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>No se pudo cargar el archivo o ensamblado 'Xamarin.Forms.Theme.Light' o uno de sus dependencias

En la versión preliminar, los temas no pueda cargar en tiempo de ejecución. Agregue el código que se muestra a continuación, en los proyectos correspondientes para corregir este error.

**iOS**

En el **AppDelegate.cs** agregue las líneas siguientes después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

En el **MainActivity.cs** agregue las líneas siguientes después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
