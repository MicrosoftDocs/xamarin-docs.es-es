---
title: Xamarin.Forms AbsoluteLayout
description: En este artículo se explica cómo usar la clase Xamarin.Forms AbsoluteLayout para crear interfaces de usuario perfecto de píxeles. Esta clase posiciones y tamaños de los elementos secundarios proporcionales a su propio tamaño y posición o por valores absolutos.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 0389587b2abffa751349a66eedc4a3800aa2a99d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888530"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) coloca y ajusta el tamaño de los elementos secundarios proporcionales a su propio tamaño y posición o por valores absolutos. Pueden ser vistas secundarias posición y tamaño utilizando valores proporcionales o valores estáticos y proporcional y se pueden combinar los valores estáticos.

[![](absolute-layout-images/layouts-sml.png "Los diseños de Xamarin.Forms")](absolute-layout-images/layouts.png#lightbox "los diseños de Xamarin.Forms")

Este artículo se trata:

- **[Propósito](#Purpose)**  &ndash; usos comunes de `AbsoluteLayout`.
- **[Uso](#Usage)**  &ndash; cómo usar `AbsoluteLayout` para lograr el diseño deseado.
  - **[Diseños proporcionales](#Proportional_Layouts)**  &ndash; comprender valores proporcionales cómo funcionan en un `AbsoluteLayout`.
  - **[Especificar valores](#Specifying_Values)**  &ndash; comprender cómo se especifican proporcional y los valores absolutos.
  - **[Valores proporcionales](#Proportional_Values)**  &ndash; comprender valores proporcionales cómo funcionan.
    - **[Valores absolutos](#Absolute_Values)**  &ndash; comprender cómo funcionan los valores absolutos.

<a name="Purpose" />

## <a name="purpose"></a>Propósito

Debido al modelo de posicionamiento de `AbsoluteLayout`, el diseño resulta relativamente sencilla colocar los elementos para que sean vaciado con cualquier lado del diseño o centrada. Con las posiciones y tamaños proporcionales, los elementos en un `AbsoluteLayout` puede escalar automáticamente a cualquier tamaño de la vista. Para los elementos que debe escalarse sólo la posición, pero no el tamaño, se pueden combinar valores proporcionales y absolutos.

`AbsoluteLayout` se puede usar cualquier parte los elementos se deben colocar dentro de una vista y es especialmente útil cuando se alinean elementos a los bordes.

<a name="Usage" />

## <a name="usage"></a>Uso

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Diseños proporcionales

`AbsoluteLayout` tiene un modelo de delimitador único mediante el cual se coloca el delimitador del elemento respecto a su elemento como el elemento se coloca en relación con el diseño cuando se usa el posicionamiento proporcional. Cuando se usa el posicionamiento absoluto, el delimitador se encuentra en (0,0) en la vista. Esto tiene dos consecuencias importantes:

- No se puede colocar elementos fuera de la pantalla con los valores proporcionales.
- Los elementos se pueden colocar de forma confiable en cualquier parte del diseño o en el centro, independientemente del tamaño de la distribución o el dispositivo.

`AbsoluteLayout`, como `RelativeLayout`, puede colocar los elementos de forma que se superpongan.

Tenga en cuenta en la siguiente captura de pantalla, el delimitador del cuadro es un punto blanco. Tenga en cuenta la relación entre el delimitador y el cuadro de medida que avanza el diseño:

![](absolute-layout-images/anchor-start.png "Delimitador al principio")
![](absolute-layout-images/anchor-center.png "delimitador en centro")
![](absolute-layout-images/anchor-end.png "anclaje final")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Especificar valores

Vistas dentro de un `AbsoluteLayout` se sitúan mediante cuatro valores:

- **X** &ndash; la posición de x (horizontal) del delimitador de la vista
- **Y** &ndash; la posición y (vertical) del delimitador de la vista
- **Ancho** &ndash; el ancho de la vista
- **Alto** &ndash; el alto de la vista

Cada uno de estos valores puede establecerse como un [proporcional](#Proportional_Values) valor o una [absoluta](#Absolute_Values) valor.

Los valores se especifican como una combinación de los límites y una marca. `LayoutBounds` es un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) que consta de cuatro valores: `x`, `y`, `width`, `height`.

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) Especifica cómo se interpretará los valores y tiene las siguientes opciones predefinidas:

- **Ninguno** &ndash; interpreta todos los valores como un absoluto. Este es el valor predeterminado si no se especifica ninguna marca de diseño.
- **Todos los** &ndash; interpreta todos los valores como proporcional.
- **WidthProportional** &ndash; interpreta el `Width` valor como proporcional y todos los demás valores como un absoluto.
- **HeightProportional** &ndash; interpreta solo el valor del alto como proporcional con todos los demás valores absolutos.
- **XProportional** &ndash; interpreta el `X` valor como proporcional, mientras que todos los demás valores como un absoluto.
- **YProportional** &ndash; interpreta el `Y` valor como proporcional, mientras que todos los demás valores como un absoluto.
- **PositionProportional** &ndash; interpreta el `X` y `Y` valores proporcionales, mientras que los valores de tamaño se interpretan como un absoluto.
- **SizeProportional** &ndash; interpreta el `Width` y `Height` valores proporcionales mientras que los valores de posición son absolutos.

En XAML, límites y las marcas se establecen como parte de la definición de vistas en el diseño, utilizando el `AbsoluteLayout.LayoutBounds` propiedad. Los límites se establecen como una lista separada por comas de valores, `X`, `Y`, `Width`, y `Height`, en ese orden. También se especifican en la declaración de vistas en el diseño mediante el `AbsoluteLayout.LayoutFlags` propiedad. Tenga en cuenta que se pueden combinar flags en XAML mediante una lista separada por comas. Considere el ejemplo siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "Ejemplos de AbsoluteLayout")

Tenga en cuenta lo siguiente:

- La etiqueta en el centro se coloca con los valores de tamaño y posición absolutos. Por este motivo, aparece centrado en iPhone 4S y menores, pero no centrado en dispositivos de mayor tamaño.
- Se coloca el texto en la parte inferior del diseño con los valores proporcionales de tamaño y posición. Siempre aparecerá en la parte inferior central del diseño, pero aumentará su tamaño con tamaños más grandes de diseño.
- Tres colores `BoxView`s se colocan en los bordes superiores, izquierdos y derecho de la pantalla con proporcional posición y tamaño absoluto.

El siguiente obtiene el mismo diseño de C#:

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```

<a name="Proportional_Values" />

### <a name="proportional-values"></a>Valores proporcionales

Valores proporcionales definen una relación entre un diseño y una vista. Esta relación define la posición o el valor de escala de una vista secundaria como una proporción del valor correspondiente del diseño del elemento primario. Estos valores se expresan como `double`s con valores comprendidos entre 0 y 1.

Se usan valores proporcionales a la posición y las vistas de tamaño en el diseño. Por lo tanto, cuando se establece el ancho de la vista como una proporción, el valor del ancho resultante es la proporción multiplicada por el `AbsoluteLayout`del ancho. Por ejemplo, con un `AbsoluteLayout` del ancho `500` y una vista que define para tener un ancho proporcional de.5, el ancho representado de la vista será 250 (500 x.5.

Para usar valores proporcionales, establezca `LayoutBounds` utilizando (x, y) proporciones y tamaños proporcionales, a continuación, establezca `LayoutFlags` a `All`.

En XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

En C#:

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>Valores absolutos

Valores absolutos definir explícitamente dónde deben situarse vistas dentro del diseño. A diferencia de los valores proporcionales, valores absolutos son capaces de posicionar y ajustar el tamaño de una vista que no cabe dentro de los límites del diseño.

Use valores absolutos para el posicionamiento puede ser peligroso cuando no se conoce el tamaño del diseño. Al usar posiciones absolutas, se puede desplazar un elemento en el centro de la pantalla con un tamaño en cualquier otro tamaño. Es importante probar la aplicación a través de los distintos tamaños de pantalla de los dispositivos compatibles.

Para usar los valores de diseño absoluto, establezca `LayoutBounds` utilizando (x, y) coordenadas y los tamaños explícitos, a continuación, establezca `LayoutFlags` a `None`.

En XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

En C#:

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Exploración de un diseño complejo

Cada uno de los diseños tienen ventajas y desventajas para crear diseños determinados. A lo largo de esta serie de artículos de diseño, una aplicación de ejemplo creada con el mismo diseño de página que se implementa mediante tres diseños diferentes.

Tenga en cuenta el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

El código anterior da como resultado el siguiente diseño:

![](absolute-layout-images/abs.png "AbsoluteLayout complejo")

Tenga en cuenta que `AbsoluteLayout`s están anidadas, porque en algunos casos los diseños de anidamiento puede ser más sencillo que presentar todos los elementos del diseño del mismo.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
