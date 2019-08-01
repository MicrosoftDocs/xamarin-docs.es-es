---
title: Enlace de una clase ControlTemplate de Xamarin.Forms
description: Los enlaces a plantilla permiten a los controles en una plantilla de control enlazar datos a propiedades públicas, lo que permite que los valores de propiedad en los controles de la plantilla de control se puedan cambiar fácilmente. En este artículo se muestra cómo usar enlaces a plantilla para realizar el enlace de datos desde una plantilla de control.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 41e5bbc42ccde5cdd5223a7d2cb0a77da66e10c1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647004"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Enlace de una clase ControlTemplate de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)

_Los enlaces a plantilla permiten a los controles en una plantilla de control enlazar datos a propiedades públicas, lo que permite que los valores de propiedad en los controles de la plantilla de control se puedan cambiar fácilmente. En este artículo se muestra cómo usar enlaces a plantilla para realizar el enlace de datos desde una plantilla de control._

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) se usa para enlazar la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la vista de *destino* propietaria de la plantilla de control. Por ejemplo, en lugar de definir el texto mostrado por las instancias de [`Label`](xref:Xamarin.Forms.Label) dentro de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), se podría usar un enlace a plantilla para enlazar la propiedad [`Label.Text`](xref:Xamarin.Forms.Label.Text) a propiedades enlazables que definen el texto que se va a mostrar.

Un elemento [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) es similar a un elemento [`Binding`](xref:Xamarin.Forms.Binding) existente, salvo que el *origen* de `TemplateBinding` siempre se establece de forma automática en el elemento primario de la vista de *destino* propietaria de la plantilla de control. Pero tenga en cuenta que no se admite el uso de `TemplateBinding` fuera de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

## <a name="creating-a-templatebinding-in-xaml"></a>Creación de TemplateBinding en XAML

En XAML, un elemento [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) se crea mediante la extensión de marcado [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension), como se muestra en el ejemplo de código siguiente:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

En lugar de establecer las propiedades [`Label.Text`](xref:Xamarin.Forms.Label.Text) en texto estático, las propiedades pueden usar enlaces a plantilla para enlazar a propiedades enlazables del elemento primario de la vista de *destino* propietaria del elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Pero tenga en cuenta que los enlaces a plantilla se enlazan a `Parent.HeaderText` y `Parent.FooterText`, en lugar de a `HeaderText` y `FooterText`. En este ejemplo, esto se debe a que las propiedades enlazables se definen en el elemento primario principal de la vista de *destino*, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

El *origen* del enlace a plantilla siempre se establece de forma automática en el elemento primario de la vista de *destino* propietaria de la plantilla de control, que aquí es la instancia de [`ContentView`](xref:Xamarin.Forms.ContentView). El enlace a plantilla usa la propiedad [`Parent`](xref:Xamarin.Forms.Element.Parent) para devolver el elemento primario de la instancia de `ContentView`, que es la instancia de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Por tanto, al usar [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) en el elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para enlazar a `Parent.HeaderText` y `Parent.FooterText` se buscan las propiedades enlazables definidas en `ContentPage`, como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

![](template-binding-images/teal-theme.png "Plantilla de control de color verde azulado con enlaces de plantilla")

## <a name="creating-a-templatebinding-in-c35"></a>Creación de TemplateBinding en C&#35;

En C#, un elemento [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) se crea mediante el constructor de `TemplateBinding`, como se muestra en el ejemplo de código siguiente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

En lugar de establecer las propiedades [`Label.Text`](xref:Xamarin.Forms.Label.Text) en texto estático, las propiedades pueden usar enlaces a plantilla para enlazar a propiedades enlazables del elemento primario de la vista de *destino* propietaria del elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). El enlace a plantilla se crea con el método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)), y se especifica una instancia de [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) como segundo parámetro. Tenga en cuenta que los enlaces a plantilla enlazan a `Parent.HeaderText` y `Parent.FooterText`, porque las propiedades enlazables se definen en el elemento primario principal de la vista de *destino*, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

Las propiedades enlazables se definen en `ContentPage`, como se describe anteriormente.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Enlace de BindableProperty a una propiedad ViewModel

Como se ha indicado antes, [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) se usa para enlazar la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la vista de *destino* propietaria de la plantilla de control. A su vez, estas propiedades enlazables se pueden enlazar a propiedades del modelo de vista.

En el ejemplo de código siguiente se definen dos propiedades en un modelo de vista:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Las propiedades de modelo de vista `HeaderText` y `FooterText` se pueden enlazar a lo siguiente, como se muestra en este ejemplo de código XAML:

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

Las propiedades enlazables `HeaderText` y `FooterText` se enlazan a las propiedades `HomePageViewModel.HeaderText` y `HomePageViewModel.FooterText`, debido al establecimiento de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) en una instancia de la clase `HomePageViewModel`. En general, esto da como resultado el enlace de las propiedades de control de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a las instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) en [`ContentPage`](xref:Xamarin.Forms.ContentPage), que a su vez se enlazan a propiedades del modelo de vista.

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

También puede enlazar a las propiedades del modelo de vista directamente, para que no sea necesario declarar elementos `BindableProperty` para `HeaderText` y `FooterText` en el elemento `ContentPage`, si se enlaza la plantilla de control a Parent.BindingContext._NombreDePropiedad_, por ejemplo:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Para obtener más información sobre el enlace de datos a modelos de vista, vea [Enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo usar enlaces a plantilla para realizar el enlace de datos desde una plantilla de control. Los enlaces a plantilla permiten a los controles en una plantilla de control enlazar datos a propiedades públicas, lo que permite que los valores de propiedad en los controles de la plantilla de control se puedan cambiar fácilmente.

## <a name="related-links"></a>Vínculos relacionados

- [Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema sencillo con enlace a plantilla (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)
- [Tema sencillo con enlace a plantilla y modelo de vista (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebindingandviewmodel)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
