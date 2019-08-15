---
title: Creación de una clase ControlTemplate
description: Las plantillas de control se pueden definir en el nivel de aplicación o en el de página. En este artículo se explica cómo crear y consumir plantillas de control.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/14/2019
ms.openlocfilehash: 523113a7b54541733e14f947eefa247e4f774b99
ms.sourcegitcommit: 157da886e1f304c6b482aa3f265ef7d78b696ab7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69024513"
---
# <a name="create-a-controltemplate"></a>Creación de una clase ControlTemplate

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme)

_Las plantillas de control se pueden definir en el nivel de aplicación o en el de página. En este artículo se explica cómo crear y consumir plantillas de control._

## <a name="create-a-controltemplate-in-xaml"></a>Creación de una clase ControlTemplate en XAML

Para definir un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, se debe agregar un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a la clase `App`. De forma predeterminada, en todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla se usa la clase **App** para implementar la subclase [`Application`](xref:Xamarin.Forms.Application). Para declarar un elemento `ControlTemplate` en el nivel de aplicación, en el objeto `ResourceDictionary` de la aplicación con XAML, la clase **App** predeterminada se debe reemplazar por una clase **App** de XAML y el código subyacente asociado, como se muestra en el ejemplo de código siguiente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Cada instancia de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se crea como un objeto reutilizable en un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).  Esto se consigue mediante la asignación de un atributo `x:Key` único a cada declaración, para proporcionarle una clave descriptiva en el objeto `ResourceDictionary`.

En el ejemplo de código siguiente se muestra el código subyacente de `App` asociado:

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

Además de establecer la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage), el código subyacente también debe llamar al método `InitializeComponent` para cargar y analizar el código XAML asociado.

En el ejemplo de código siguiente se muestra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) que aplica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

El elemento `TealTemplate` se asigna a la propiedad [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) mediante la extensión de marcado `StaticResource`. La propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) se establece en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en [`ContentPage`](xref:Xamarin.Forms.ContentPage). Este contenido se mostrará mediante el elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) incluido en `TealTemplate`. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

![](creating-images/teal-theme.png "Plantilla de control de color verde azulado")

### <a name="re-theming-an-application-at-runtime"></a>Cambio de los temas de una aplicación en tiempo de ejecución

Al hacer clic en el botón **Cambiar tema** se ejecuta el método `OnButtonClicked`, que se muestra en el ejemplo de código siguiente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Este método reemplaza la instancia activa de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) con la instancia de `ControlTemplate` alternativa, lo que da como resultado la captura de pantalla siguiente:

![](creating-images/aqua-theme.png "Plantilla de control aguamarina")

> [!NOTE]
> En un elemento `ContentPage`, se puede asignar la propiedad `Content` y también se puede establecer la propiedad `ControlTemplate`. En ese caso, si `ControlTemplate` contiene una instancia de `ContentPresenter`, el contenido asignado a la propiedad `Content` se presentará por medio del elemento `ContentPresenter` de `ControlTemplate`.

### <a name="set-a-controltemplate-with-a-style"></a>Configuración de una clase ControlTemplate con un estilo

Una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) también se puede aplicar mediante un elemento [`Style`](xref:Xamarin.Forms.Style) para expandir la capacidad del tema. Esto se puede lograr mediante la creación de un estilo *implícito* o *explícito* para la vista de destino en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), y estableciendo la propiedad `ControlTemplate` de la vista de destino en la instancia de [`Style`](xref:Xamarin.Forms.Style). En el ejemplo de código siguiente se muestra un estilo *implícito* que se ha agregado al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación:

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Como la instancia de [`Style`](xref:Xamarin.Forms.Style) es *implícita*, se aplicará a todas las instancias de `ContentView` de la aplicación. Por tanto, ya no es necesario establecer la propiedad [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate), como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="create-a-controltemplate-at-page-level"></a>Creación de una clase ControlTemplate en el nivel de página

Además de crear instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, también se pueden crear en el nivel de página, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

Cuando se agrega un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de página, se agrega un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a [`ContentPage`](xref:Xamarin.Forms.ContentPage) y, después, se incluyen las instancias de `ControlTemplate` en el objeto `ResourceDictionary`.

## <a name="create-a-controltemplate-in-c35"></a>Creación de una clase ControlTemplate en C&#35;

Para definir un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, se debe crear un objeto `class` que represente el elemento `ControlTemplate`. La clase se debe derivar del [diseño](~/xamarin-forms/user-interface/layouts/index.yml) que se usa para la plantilla, como se muestra en el ejemplo de código siguiente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

La clase `AquaTemplate` es idéntica a la clase `TealTemplate`, salvo que se usan otros colores para las propiedades [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) y [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor).

En el ejemplo de código siguiente se muestra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) que aplica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

Las instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se crean mediante la especificación del tipo de las clases que definen las plantillas de control, en el constructor `ControlTemplate`.

La propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) se establece en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en [`ContentPage`](xref:Xamarin.Forms.ContentPage). Este contenido se mostrará mediante el elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) incluido en `TealTemplate`. El mismo mecanismo descrito anteriormente se usa para cambiar el tema en tiempo de ejecución para el objeto `AquaTheme`.

## <a name="get-a-named-element-from-a-template"></a>Obtención de un elemento con nombre desde una plantilla

Los elementos con nombre dentro de una plantilla de control se pueden recuperar una vez que se crea una instancia de la plantilla. Esto se puede lograr con el método `GetTemplateChild`, el que devuelve el elemento con nombre en el árbol visual [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) con instancias.

Una vez que se crearon instancias de una plantilla de control, se llama al método `OnApplyTemplate` de la plantilla. Por lo tanto, se debe llamar al método `GetTemplateChild`desde la invalidación `OnApplyTemplate` en una página derivada [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage), como [`ContentPage`](xref:Xamarin.Forms.ContentPage), o una vista derivada [`TemplatedView`](xref:Xamarin.Forms.TemplatedView), como [`ContentView`](xref:Xamarin.Forms.ContentView).

> [!IMPORTANT]
> Se debe llamar al método `GetTemplateChild` solo una vez que se ha llamado al método `OnApplyTemplate`.

En el ejemplo siguiente se muestra la plantilla de control de un control personalizado:

```xaml
<controls:MyCustomControl ...>
    <controls:MyCustomControl.ControlTemplate>
         <ControlTemplate>
              <Label x:Name="myLabel" />
         </ControlTemplate>
    <controls:MyCustomControl.ControlTemplate>
</controls:MyCustomControl>
```

Se le asigna un nombre al elemento [`Label`](xref:Xamarin.Forms.Label) y, por lo tanto, se puede recuperar en el código subyacente del control personalizado. Para lograrlo, se llama al método `GetTemplateChild` desde la invalidación `OnApplyTemplate` del control personalizado:

```csharp
class MyCustomControl : ContentView
{
    Label myLabel;

    protected override void OnApplyTemplate()
    {  
        myLabel = GetTemplateChild("myLabel");
    }
    //...
}
```

En este ejemplo, se recupera el objeto [`Label`](xref:Xamarin.Forms.Label) con nombre `myLabel`. Luego, la clase `MyCustomControl` puede acceder a `myLabel` y manipularlo.

## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema sencillo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
