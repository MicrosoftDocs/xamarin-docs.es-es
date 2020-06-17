---
title: "Introducción con las páginas de datos" Descripción: "en este artículo se explica cómo empezar a crear una página simple controlada por datos con las páginas de datos Xamarin.Forms ".
MS. Prod: Xamarin ms. AssetID: 6416E5FA-6384-4298-BAA1-A89381E47210 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/01/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="getting-started-with-datapages"></a>Introducción con páginas de páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> Las páginas de página requieren una Xamarin.Forms referencia de tema que se va a representar. Esto implica la instalación de [ Xamarin.Forms . Paquete NuGet Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) en el proyecto, seguido de [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [ Xamarin.Forms . Paquetes NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Para empezar a crear una página simple controlada por datos mediante la vista previa de las páginas de datos, siga los pasos que se indican a continuación. En esta demostración se usa un estilo codificado ("Events") en las compilaciones de vista previa que solo funciona con el formato JSON específico en el código.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. agregar paquetes NuGet

Agregue estos paquetes de NuGet a la Xamarin.Forms biblioteca de .net Standard y los proyectos de aplicación:

- Xamarin.Forms. Páginas
- Xamarin.Forms. Theme. base
- Un NuGet de implementación de tema (p. ej., Xamarin.Forms. Theme. Light)

## <a name="2-add-theme-reference"></a>2. Agregar referencia de tema

En el archivo **app. Xaml** , agregue un personalizado `xmlns:mytheme` para el tema y asegúrese de que el tema se combina en el Diccionario de recursos de la aplicación:

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
> También debe seguir los pasos para [cargar ensamblados de tema (a continuación)](#troubleshooting) agregando código reutilizable a iOS `AppDelegate` y Android `MainActivity` . Esto se mejorará en una versión preliminar futura.

## <a name="3-add-a-xaml-page"></a>3. agregar una página XAML

Agregue una nueva página XAML a la Xamarin.Forms aplicación y *cambie la clase base* de `ContentPage` a `Xamarin.Forms.Pages.ListDataPage` . Esto se debe hacer en C# y en el código XAML:

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

Además de cambiar el elemento raíz al `<p:ListDataPage>` espacio de nombres personalizado para `xmlns:p` también se debe agregar:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Subclase de aplicación**

Cambie el `App` constructor de clase para que `MainPage` se establezca en un valor `NavigationPage` que contenga el nuevo `SessionDataPage` . Se *debe* usar una página de navegación.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. agregar el origen de los

Elimine el `Content` elemento y reemplácelo por un `p:ListDataPage.DataSource` para rellenar la página con datos. En el ejemplo siguiente se carga un archivo de datos JSON remoto desde una dirección URL.

> [!NOTE]
> La vista previa *requiere* un `StyleClass` atributo para proporcionar sugerencias de representación para el origen de datos. El `StyleClass="Events"` hace referencia a un diseño que está predefinido en la vista previa y contiene estilos *codificados* para coincidir con el origen de datos JSON que se está usando.

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

**Datos de JSON**

A continuación se muestra un ejemplo de los datos JSON del origen de la demostración:

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

## <a name="4-run"></a>4. ejecutar

Los pasos anteriores deben dar lugar a una página de datos de trabajo:

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

Esto funciona porque el estilo predefinido **"eventos"** existe en el paquete NuGet de tema claro y tiene estilos definidos que coinciden con el origen de datos (por ejemplo, "title", "Image", "Presenter").

Los "eventos" `StyleClass` se compilan para mostrar el `ListDataPage` control con un `CardView` control personalizado que se define en Xamarin.Forms . Páginas. El `CardView` control tiene tres propiedades: `ImageSource` , `Text` y `Detail` . El tema está codificado de forma rígida para enlazar los tres campos de DataSource (del archivo JSON) a estas propiedades para su presentación.

## <a name="5-customize"></a>5. personalizar

El estilo heredado puede invalidarse mediante la especificación de una plantilla y el uso de enlaces de origen de datos. El código XAML siguiente declara una plantilla personalizada para cada fila utilizando la nueva `ListItemControl` `{p:DataSourceBinding}` sintaxis y que se incluye en ** Xamarin.Forms . Páginas** NuGet:

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

Al proporcionar un `DataTemplate` código, se invalida el `StyleClass` y, en su lugar, se usa el diseño predeterminado para `ListItemControl` .

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

Los desarrolladores que prefieren C# a XAML pueden crear enlaces de origen de datos también (Recuerde incluir una `using Xamarin.Forms.Pages;` instrucción):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

Es un poco más trabajo crear temas desde cero, pero las versiones preliminares futuras harán que esto sea más fácil.

## <a name="troubleshooting"></a>Solucionar problemas

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>No se pudo cargar el archivo o ensamblado ' Xamarin.Forms . Theme. Light ' o una de sus dependencias

En la versión preliminar, es posible que los temas no se puedan cargar en tiempo de ejecución. Agregue el código que se muestra a continuación en los proyectos pertinentes para corregir este error.

**iOS**

En **AppDelegate.CS** , agregue las siguientes líneas después de`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

En **MainActivity.CS** , agregue las siguientes líneas después de`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
