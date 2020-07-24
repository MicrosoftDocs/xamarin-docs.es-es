---
title: Xamarin.FormsAbsoluteLayout
description: En este artículo se explica cómo usar la Xamarin.Forms clase AbsoluteLayout para crear interfaces de IU perfectas en píxeles. Esta clase coloca y ajusta el tamaño de los elementos secundarios en proporción con su propio tamaño y posición, o con valores absolutos.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 923f7643bd1e137192bfb80dbbc7c5d2c25b5471
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938429"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)coloca y ajusta el tamaño de los elementos secundarios en proporción con su propio tamaño y posición, o con valores absolutos. Las vistas secundarias se pueden colocar y cambiar de tamaño mediante valores proporcionales o valores estáticos, y los valores proporcionales y estáticos se pueden mezclar.

[![Xamarin.FormsDiseños](absolute-layout-images/layouts-sml.png)](absolute-layout-images/layouts.png#lightbox "[! Operador. Diseños de NO-LOC (Xamarin. Forms)]")

En este artículo se tratarán lo siguiente:

- **[Propósito](#purpose)** &ndash; de usos habituales de `AbsoluteLayout` .
- **[Uso](#usage)** &ndash; de Cómo usar `AbsoluteLayout` para lograr el diseño deseado.
  - **[Diseños](#proportional-layouts)** &ndash; proporcionales comprenda cómo funcionan los valores proporcionales en `AbsoluteLayout` .
  - **[Especificar valores](#specifying-values)** &ndash; comprenda cómo se especifican los valores proporcionales y absolutos.
  - **[Valores](#proportional-values)** &ndash; proporcionales comprender cómo funcionan los valores proporcionales.
    - **[Valores](#absolute-values)** &ndash; absolutos comprender cómo funcionan los valores absolutos.

## <a name="purpose"></a>Propósito

Debido al modelo de posicionamiento de `AbsoluteLayout` , el diseño hace que sea relativamente sencillo colocar los elementos de forma que se vacíen con cualquier lado del diseño o se centren. Con tamaños y posiciones proporcionales, los elementos de un `AbsoluteLayout` se pueden escalar automáticamente a cualquier tamaño de vista. En el caso de los elementos en los que solo se debe escalar la posición, pero no el tamaño, se pueden mezclar los valores absolutos y proporcionales.

`AbsoluteLayout`puede usarse en cualquier lugar en que los elementos deban colocarse en una vista y resulte especialmente útil al alinear los elementos con los bordes.

## <a name="usage"></a>Uso

### <a name="proportional-layouts"></a>Diseños proporcionales

`AbsoluteLayout`tiene un modelo de delimitador único por el que el delimitador del elemento se coloca en relación con su elemento cuando se coloca el elemento en relación con el diseño cuando se usa la posición proporcional. Cuando se usa la posición absoluta, el delimitador está en (0,0) dentro de la vista. Esto tiene dos consecuencias importantes:

- Los elementos no se pueden colocar fuera de la pantalla con valores proporcionales.
- Los elementos se pueden colocar de forma confiable a lo largo de cualquier lado del diseño o en el centro, independientemente del tamaño del diseño o del dispositivo.

`AbsoluteLayout`, como `RelativeLayout` , puede colocar los elementos para que se superpongan.

Nota en la siguiente captura de pantalla, el delimitador del cuadro es un punto blanco. Observe la relación entre el delimitador y el cuadro cuando se desplaza por el diseño:

![Anclar en el anclaje inicial ](absolute-layout-images/anchor-start.png)
 ![ en el ](absolute-layout-images/anchor-center.png)
 ![ anclaje central al final](absolute-layout-images/anchor-end.png)

### <a name="specifying-values"></a>Especificar valores

Las vistas dentro de `AbsoluteLayout` se colocan con cuatro valores:

- **X** &ndash; la posición x (horizontal) del delimitador de la vista
- **Y** &ndash; la posición y (vertical) del delimitador de la vista
- **Ancho** &ndash; de ancho de la vista
- **Alto** &ndash; de alto de la vista

Cada uno de esos valores se puede establecer como un valor [proporcional](#proportional-values) o como un valor [absoluto](#absolute-values) .

Los valores se especifican como una combinación de límites y una marca. `LayoutBounds`se [`Rectangle`](xref:Xamarin.Forms.Rectangle) compone de cuatro valores: `x` , `y` , `width` , `height` .

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)Especifica cómo se interpretarán los valores y tiene las siguientes opciones predefinidas:

- **Ninguno** &ndash; interpreta todos los valores como absolutos. Este es el valor predeterminado si no se especifican marcas de diseño.
- **Todo** &ndash; interpreta todos los valores como proporcionales.
- **WidthProportional** &ndash; interpreta el `Width` valor como proporcional y todos los demás valores como absolutos.
- **HeightProportional** &ndash; interpreta solo el valor del alto como proporcional con todos los demás valores absolutos.
- **XProportional** &ndash; interpreta el `X` valor como proporcional, mientras trata todos los demás valores como absolutos.
- **YProportional** &ndash; interpreta el `Y` valor como proporcional, mientras trata todos los demás valores como absolutos.
- **PositionProportional** &ndash; interpreta los `X` valores y `Y` como proporcional, mientras que los valores de tamaño se interpretan como absolutos.
- **SizeProportional** &ndash; interpreta los `Width` valores y `Height` como proporcional mientras que los valores de posición son absolutos.

En XAML, los límites y las marcas se establecen como parte de la definición de vistas en el diseño, mediante la `AbsoluteLayout.LayoutBounds` propiedad. Los límites se establecen como una lista de valores separados por comas,,, `X` `Y` `Width` y `Height` , en ese orden. Las marcas también se especifican en la declaración de vistas en el diseño mediante la `AbsoluteLayout.LayoutFlags` propiedad. Tenga en cuenta que las marcas se pueden combinar en XAML mediante una lista separada por comas. Considere el ejemplo siguiente:

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

![Ejemplos de AbsoluteLayout](absolute-layout-images/exploration.png)

Tenga en cuenta lo siguiente:

- La etiqueta del centro se coloca usando valores absolutos de tamaño y posición. Por eso, aparece centrado en los centros de iPhone y versiones inferiores, pero no en los dispositivos más grandes.
- El texto situado en la parte inferior del diseño se coloca usando valores de tamaño y posición proporcionales. Aparecerá siempre en la parte inferior central del diseño, pero su tamaño aumentará con tamaños de diseño mayores.
- Los tres colores `BoxView` se colocan en los bordes superior, izquierdo y derecho de la pantalla con una posición proporcional y un tamaño absoluto.

Lo siguiente consigue el mismo diseño en C#:

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

### <a name="proportional-values"></a>Valores proporcionales

Los valores proporcionales definen una relación entre un diseño y una vista. Esta relación define el valor de la posición o la escala de una vista secundaria como una proporción del valor correspondiente del diseño primario. Estos valores se expresan como `double` s con valores entre 0 y 1.

Los valores proporcionales se usan para colocar y ajustar el tamaño de las vistas en el diseño. Por lo tanto, cuando el ancho de una vista se establece como una proporción, el valor de ancho resultante es la proporción multiplicada por el `AbsoluteLayout` ancho de. Por ejemplo, con un `AbsoluteLayout` valor de ancho `500` y un conjunto de vistas para tener un ancho proporcional de 0,5, el ancho representado de la vista será 250 (500 x. 5).

Para usar valores proporcionales, establezca las `LayoutBounds` proporciones de (x, y) y los tamaños proporcionales y, a continuación, establezca `LayoutFlags` en `All` .

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

### <a name="absolute-values"></a>Valores absolutos

Los valores absolutos definen explícitamente dónde se deben colocar las vistas en el diseño. A diferencia de los valores proporcionales, los valores absolutos son capaces de colocar y ajustar el tamaño de una vista que no cabe dentro de los límites del diseño.

El uso de valores absolutos para la posición puede ser peligroso cuando no se conoce el tamaño del diseño. Cuando se usan posiciones absolutas, un elemento del centro de la pantalla de un tamaño podría desplazarse con cualquier otro tamaño. Es importante probar la aplicación en los distintos tamaños de pantalla de los dispositivos compatibles.

Para usar valores de diseño absolutos, establezca `LayoutBounds` mediante coordenadas (x, y) y tamaños explícitos; después, establezca `LayoutFlags` en `None` .

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

Cada uno de los diseños tiene puntos fuertes y débiles para crear diseños concretos. En esta serie de artículos de diseño, se ha creado una aplicación de ejemplo con el mismo diseño de página implementado mediante tres diseños diferentes.

Considere el siguiente código XAML:

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

![AbsoluteLayout complejos](absolute-layout-images/abs.png)

Observe que `AbsoluteLayout` los elementos están anidados porque, en algunos casos, el anidamiento de diseños puede ser más fácil que presentar todos los elementos dentro del mismo diseño.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de Mobile Apps con Xamarin.Forms , capítulo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
