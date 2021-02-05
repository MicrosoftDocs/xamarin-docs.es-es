---
title: Xamarin.Forms Estilos de texto
description: En este artículo se explica cómo aplicar estilos a texto en Xamarin.Forms aplicaciones. Los estilos pueden definirse una vez y usarse en muchas vistas, pero un estilo solo se puede usar con vistas de un tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: db32d4250bf5ba63761c59f67b64b59fa565e651
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "93371019"
---
# <a name="xamarinforms-text-styles"></a>Xamarin.Forms Estilos de texto

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Aplicar estilo a texto en Xamarin.Forms_

Los estilos se pueden utilizar para ajustar la apariencia de las etiquetas, las entradas y los editores. Los estilos pueden definirse una vez y usarse en muchas vistas, pero un estilo solo se puede usar con vistas de un tipo.
A los estilos se les puede dar un `Key` y aplicar de forma selectiva mediante la propiedad de un control concreto `Style` .

## <a name="built-in-styles"></a>Estilos de Built-In

Xamarin.Forms incluye varios estilos [integrados](xref:Xamarin.Forms.Device.Styles) para escenarios comunes:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Para aplicar uno de los estilos integrados, use la extensión de `DynamicResource` marcado para especificar el estilo:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, los estilos integrados se seleccionan en `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Ejemplo de estilos de dispositivo](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>Estilos personalizados

Los estilos constan de establecedores y establecedores compuestos de propiedades y los valores en los que se establecerán las propiedades.

En C#, un estilo personalizado para una etiqueta con texto rojo de tamaño 30 se definiría de la siguiente manera:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

En XAML:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Tenga en cuenta que los recursos (incluidos todos los estilos) se definen en `ContentPage.Resources` , que es un elemento del mismo nivel que el conocido `ContentPage.Content` .

![Ejemplo de estilos personalizados](styles-images/customstyle.png)

## <a name="applying-styles"></a>Aplicar estilos

Una vez creado un estilo, se puede aplicar a cualquier vista que coincida con su `TargetType` .

En XAML, los estilos personalizados se aplican a las vistas proporcionando su `Style` propiedad con una `StaticResource` extensión de marcado que haga referencia al estilo deseado:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

En C#, los estilos se pueden aplicar directamente a una vista o agregarse y recuperarse de la de una página `ResourceDictionary` . Para agregar directamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Para agregar y recuperar desde la página `ResourceDictionary` :

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Los estilos integrados se aplican de forma diferente, ya que necesitan responder a la configuración de accesibilidad. Para aplicar estilos integrados en XAML, `DynamicResource` se usa la extensión de marcado:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, los estilos integrados se seleccionan en `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accesibilidad

Los estilos integrados existen para que sea más fácil respetar las preferencias de accesibilidad. Cuando se usa cualquiera de los estilos integrados, los tamaños de fuente aumentarán automáticamente si un usuario establece sus preferencias de accesibilidad en consecuencia.

Considere el siguiente ejemplo de la misma página de vistas con estilo con los estilos integrados con la configuración de accesibilidad habilitada y deshabilitada:

Deshabilitado:

![Estilos de dispositivo con accesibilidad deshabilitada](styles-images/pre-access.png)

Habilitado:

![Estilos de dispositivo con accesibilidad habilitada](styles-images/post-access.png)

Para garantizar la accesibilidad, asegúrese de que los estilos integrados se usan como base para los estilos relacionados con el texto dentro de la aplicación y de que usa estilos de forma coherente. Vea [estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener más información sobre cómo extender y trabajar con estilos en general.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de Mobile Apps con Xamarin.Forms , capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Estilo](xref:Xamarin.Forms.Style)