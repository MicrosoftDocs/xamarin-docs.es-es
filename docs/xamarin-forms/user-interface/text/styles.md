---
title: Xamarin.FormsEstilos de texto
description: En este artículo se explica cómo aplicar estilos a texto en Xamarin.Forms aplicaciones. Los estilos pueden definirse una vez y usarse en muchas vistas, pero un estilo solo se puede usar con vistas de un tipo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79a86fd7a2c0f5b82ca4b3e22b3ecedf42c5a0ba
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136154"
---
# <a name="xamarinforms-text-styles"></a>Xamarin.FormsEstilos de texto

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Aplicar estilo a texto en Xamarin. Forms_

Los estilos se pueden utilizar para ajustar la apariencia de las etiquetas, las entradas y los editores. Los estilos pueden definirse una vez y usarse en muchas vistas, pero un estilo solo se puede usar con vistas de un tipo.
A los estilos se les puede dar un `Key` y aplicar de forma selectiva mediante la propiedad de un control concreto `Style` .

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos integrados

Xamarin.Formsincluye varios estilos [integrados](xref:Xamarin.Forms.Device.Styles) para escenarios comunes:

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

<a name="Custom_Styles" />

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

<a name="Applying_Styles" />

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
- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Estilo](xref:Xamarin.Forms.Style)
