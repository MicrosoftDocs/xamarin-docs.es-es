---
title: Estilos de texto de Xamarin.Forms
description: Este artículo se explica cómo los estilos de texto en las aplicaciones de Xamarin.Forms. Estilos pueden definirse una vez y utilizados por muchas vistas, pero solo puede usarse un estilo con vistas de un tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: b639a7fdefb8fca67d833b07ef9aa1a85da67ef6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642886"
---
# <a name="xamarinforms-text-styles"></a>Estilos de texto de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Estilos de texto en Xamarin.Forms_

Los estilos se pueden usar para ajustar la apariencia de las etiquetas, las entradas y editores. Estilos pueden definirse una vez y utilizados por muchas vistas, pero solo puede usarse un estilo con vistas de un tipo.
Los estilos se pueden proporcionar un `Key` y aplicar selectivamente mediante un control específico `Style` propiedad.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos integrados

Xamarin.Forms incluye varios [integrada](xref:Xamarin.Forms.Device.Styles) estilos para escenarios comunes:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Para aplicar uno de los estilos integrados, use el `DynamicResource` extensión de marcado para especificar el estilo:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, se seleccionan los estilos integrados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "Ejemplo de estilos de dispositivo")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Estilos personalizados

Estilos constan de establecedores y constan de establecedores de propiedades y los valores de las propiedades se establecerá en.

En C#, un estilo personalizado para una etiqueta con texto rojo de tamaño 30 se definiría como sigue:

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

Tenga en cuenta que los recursos (incluidos todos los estilos) se definen dentro de `ContentPage.Resources`, que es un elemento relacionado de cuanto más se familiarice `ContentPage.Content` elemento.

![](styles-images/customstyle.png "Ejemplo de estilos personalizados")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Aplicar estilos

Una vez que se ha creado un estilo, se puede aplicar a cualquier coincidencia de vista su `TargetType`.

En XAML, los estilos personalizados se aplican a vistas proporcionando sus `Style` propiedad con un `StaticResource` el estilo deseado de referencia de extensión de marcado:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

En C#, estilos pueden se aplican directamente a una vista o agrega a y recuperarse de una página `ResourceDictionary`. Para agregar directamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Para agregar y recuperar desde la página `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Estilos integrados se aplican de forma diferente, ya que se deben responder a la configuración de accesibilidad. Para aplicar los estilos integrados en XAML, el `DynamicResource` se utiliza la extensión de marcado:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, se seleccionan los estilos integrados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accesibilidad

Los estilos integrados existen para que sea más fácil respetar las preferencias de accesibilidad. Al utilizar cualquiera de los estilos integrados, los tamaños de fuente aumentará automáticamente si un usuario establece las preferencias de accesibilidad en consecuencia.

Considere el siguiente ejemplo de la misma página de vistas de un estilo con los estilos integrados con la configuración de accesibilidad habilitados y deshabilitados:

Deshabilitado:

![](styles-images/pre-access.png "Estilos de dispositivo con accesibilidad deshabilitado")

Habilitado:

![](styles-images/post-access.png "Estilos de dispositivo con accesibilidad habilitado")

Para garantizar la accesibilidad, asegúrese de que los estilos integrados se utilizan como base para cualquier estilo relacionada con el texto dentro de la aplicación y que está utilizando estilos de forma coherente. Consulte [estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener más detalles sobre la extensión y trabajar con estilos en general.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Estilo](xref:Xamarin.Forms.Style)
