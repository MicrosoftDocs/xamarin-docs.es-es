---
title: Opciones de diseño de Xamarin.Forms
description: Cada vista Xamarin.Forms tiene propiedades HorizontalOptions y VerticalOptions, del tipo LayoutOptions. En este artículo se explica el efecto que tiene cada valor LayoutOptions en la alineación y la expansión de una vista.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 63c8cb285c51d7c10e2109c9d0b7cffbd0fb0898
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770341"
---
# <a name="layout-options-in-xamarinforms"></a>Opciones de diseño de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Cada vista Xamarin.Forms tiene propiedades HorizontalOptions y VerticalOptions, del tipo LayoutOptions. En este artículo se explica el efecto que tiene cada valor LayoutOptions en la alineación y la expansión de una vista._

## <a name="overview"></a>Información general

El [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estructura encapsula dos de las preferencias de diseño:

- **Alineación** – preferido de la vista de la alineación, que determina su posición y tamaño en su diseño principal.
- **Expansión** : se usa únicamente por un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e indica si la vista debe utilizar el espacio adicional, si está disponible.

Estas preferencias de diseño se pueden aplicar a un [ `View` ](xref:Xamarin.Forms.View)respecto a su elemento primario, estableciendo el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) o [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedad de la `View` a uno de los campos públicos de la [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estructura. Los campos públicos son los siguientes:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

El `Start`, `Center`, `End`, y `Fill` campos se usan para definir la alineación de la vista del diseño del elemento primario:

- Para la alineación horizontal, [ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start) posiciones el [ `View` ](xref:Xamarin.Forms.View) en el lado izquierdo del diseño del elemento primario y para la alineación vertical, coloca el `View` en la parte superior de la diseño del elemento primario.
- Para la alineación horizontal y vertical, [ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center) centra horizontalmente o verticalmente el [ `View` ](xref:Xamarin.Forms.View).
- Para la alineación horizontal, [ `End` ](xref:Xamarin.Forms.LayoutOptions.End) posiciones el [ `View` ](xref:Xamarin.Forms.View) en el lado derecho del diseño del elemento primario y para la alineación vertical, coloca el `View` en la parte inferior del diseño del elemento primario.
- Para la alineación horizontal, [ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill) garantiza que el [ `View` ](xref:Xamarin.Forms.View) rellena el ancho del diseño del elemento primario y para la alineación vertical, garantiza que el `View` rellena el alto del diseño del elemento primario.

El `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, y `FillAndExpand` valores se usan para definir la preferencia de alineación, y si la vista ocupan más espacio si está disponible dentro del elemento primario [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> El valor predeterminado de una vista [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades es [ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Alineación

Alineación controla cómo se coloca una vista en su diseño principal cuando el diseño del elemento primario contiene espacio no utilizado (es decir, el diseño del elemento primario es mayor que el tamaño combinado de todos sus elementos secundarios).

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) solo respeta el `Start`, `Center`, `End`, y `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campos en las vistas secundarias que se encuentran en la dirección opuesta para el `StackLayout` orientación. Por lo tanto, las vistas secundarias dentro de una orientación vertical `StackLayout` puede establecer sus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propiedades a uno de los `Start`, `Center`, `End`, o `Fill` campos. De forma similar, las vistas secundarios dentro de una orientación horizontal `StackLayout` puede establecer sus [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades a uno de los `Start`, `Center`, `End`, o `Fill` campos.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) no respeta el `Start`, `Center`, `End`, y `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campos en las vistas secundarias que se encuentran en la misma dirección que el `StackLayout` orientación. Por lo tanto, una orientación vertical `StackLayout` omite el `Start`, `Center`, `End`, o `Fill` campos si están establecidos en el [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades de vistas secundarias. De forma similar, una orientación horizontal `StackLayout` omite el `Start`, `Center`, `End`, o `Fill` campos si están establecidos en el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propiedades de vistas secundarias.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) por lo general invalidaciones cambiar el tamaño de las solicitudes que se especifican mediante el [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) y [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) propiedades.

El ejemplo de código XAML siguiente muestra una orientación vertical [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) donde cada elemento secundario [ `Label` ](xref:Xamarin.Forms.Label) establece su [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propiedad en uno de los campos de cuatro de alineación de la [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

El código de C# equivalente se muestra a continuación:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

El código da como resultado el diseño que se muestra en las capturas de pantalla siguiente:

[![](layout-options-images/alignment.png "Opciones de diseño de alineación")](layout-options-images/alignment-large.png#lightbox "las opciones de diseño de alineación")

<a name="expansion" />

## <a name="expansion"></a>Expansión

Expansión controla si una vista ocupa más espacio, si está disponible dentro de un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Si el `StackLayout` contiene espacio no utilizado (es decir, el `StackLayout` es mayor que el tamaño combinado de todos sus elementos secundarios), el espacio no utilizado se comparte de forma equitativa todas las vistas secundarias que solicitan expansión estableciendo sus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)o [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades para un [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campo que utiliza el `AndExpand` sufijo. Tenga en cuenta que, cuando todo el espacio en el `StackLayout` es utilizado, las opciones de expansión no tienen ningún efecto.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) sólo se puede expandir vistas secundarias en la dirección de su orientación. Por lo tanto, una orientación vertical `StackLayout` puede expandir las vistas secundarias que establecen sus [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedades a uno de los `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` campos, si la `StackLayout` contiene espacio no utilizado. De forma similar, una orientación horizontal `StackLayout` puede expandir las vistas secundarias que establecen sus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propiedades a uno de los `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` campos, si la `StackLayout` contiene espacio no utilizado.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) no se puede expandir vistas secundarias en la dirección opuesta a la orientación. Por lo tanto, en orientación vertical `StackLayout`, estableciendo el [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propiedad en una vista secundaria a [ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) tiene el mismo efecto que establecer la propiedad en [ `Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> Tenga en cuenta que habilitar la expansión no cambia el tamaño de una vista a menos que lo use [ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand).

El ejemplo de código XAML siguiente muestra una orientación vertical [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) donde cada elemento secundario [ `Label` ](xref:Xamarin.Forms.Label) establece su [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedad en uno de los campos de cuatro de expansión desde la [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

El código de C# equivalente se muestra a continuación:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

El código da como resultado el diseño que se muestra en las capturas de pantalla siguiente:

[![](layout-options-images/expansion.png "Opciones de diseño de expansión")](layout-options-images/expansion-large.png#lightbox "las opciones de diseño de expansión")

Cada [ `Label` ](xref:Xamarin.Forms.Label) ocupa la misma cantidad de espacio en el [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Sin embargo, solo la última `Label`, que establece su [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propiedad [ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tiene un tamaño diferente. Además, cada `Label` separados por una pequeña roja [ `BoxView` ](xref:Xamarin.Forms.BoxView), lo que permite el espacio de la `Label` ocupa para verse fácilmente.

## <a name="summary"></a>Resumen

En este artículo se explica el efecto que cada [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) tiene el valor de la estructura en la alineación y la expansión de una vista, en relación con su elemento primario. El `Start`, `Center`, `End`, y `Fill` campos se usan para definir la alineación de la vista del diseño del elemento primario y el `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, y `FillAndExpand` campos se usan para definir la preferencia de alineación y para determinar si la vista ocupa más espacio, si está disponible dentro de un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

## <a name="related-links"></a>Vínculos relacionados

- [LayoutOptions (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
