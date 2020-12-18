---
title: Xamarin.Forms AbsoluteLayout
description: Xamarin.FormsAbsoluteLayout se utiliza para colocar y ajustar el tamaño de los elementos mediante valores explícitos, o valores proporcionales al tamaño del diseño.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
ms.custom: contperf-fy21q1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38ab33799ba2bce634817e571587b0bcef89b019
ms.sourcegitcommit: c5e72d2ca4152b62ab6583f0dbe84b3ba29d8283
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677537"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![::: no-LOC (Xamarin. Forms)::: AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Se utiliza para colocar y cambiar el tamaño de los elementos secundarios usando valores explícitos. La posición se especifica en la esquina superior izquierda del elemento secundario con respecto a la esquina superior izquierda de `AbsoluteLayout` , en unidades independientes del dispositivo. `AbsoluteLayout` también implementa una característica de posicionamiento y ajuste de tamaño proporcional. Además, a diferencia de otras clases de diseño, `AbsoluteLayout` puede colocar los elementos secundarios para que se superpongan.

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Debe considerarse como un diseño especial para usarse solo cuando se puede imponer un tamaño en los elementos secundarios o cuando el tamaño del elemento no afecta a la posición de otros elementos secundarios.

La [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) clase define las siguientes propiedades:

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty), de tipo [`Rectangle`](xref:Xamarin.Forms.Rectangle) , que es una propiedad adjunta que representa la posición y el tamaño de un elemento secundario. El valor predeterminado de esta propiedad es (0,0, tamaño automático, ajuste automático de tamaño).
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), de tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , que es una propiedad adjunta que indica si las propiedades de los límites de diseño utilizadas para colocar y ajustar el tamaño del elemento secundario se interpretan proporcionalmente. El valor predeterminado de esta propiedad es `AbsoluteLayoutFlags.None`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos y con estilo. Para obtener más información sobre las propiedades adjuntas, consulte [ Xamarin.Forms propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md).

La [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) clase se deriva de la `Layout<T>` clase, que define una `Children` propiedad de tipo `IList<T>` . La `Children` propiedad es `ContentProperty` de la `Layout<T>` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

> [!TIP]
> Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="position-and-size-children"></a>Posición y tamaño secundarios

La posición y el tamaño de los elementos secundarios en un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) se define estableciendo la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta de cada elemento secundario, usando valores absolutos o valores proporcionales. Los valores absolutos y proporcionales se pueden mezclar para los elementos secundarios cuando la posición debe escalarse, pero el tamaño debe permanecer fijo, o viceversa. Para obtener información sobre los valores absolutos, vea [posicionamiento absoluto y ajuste de tamaño](#absolute-positioning-and-sizing). Para obtener información sobre los valores proporcionales, vea [posicionamiento proporcional y ajuste de tamaño](#proportional-positioning-and-sizing).

La [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta se puede establecer utilizando dos formatos, independientemente de si se usan valores absolutos o proporcionados:

- `x, y`. Con este formato, los `x` `y` valores y indican la posición de la esquina superior izquierda del elemento secundario con respecto a su elemento primario. El elemento secundario no tiene restricciones y su propio tamaño.
- `x, y, width, height`. Con este formato, los `x` `y` valores y indican la posición de la esquina superior izquierda del elemento secundario con respecto a su elemento primario, mientras que los `width` `height` valores y indican el tamaño del elemento secundario.

Para especificar que un elemento secundario tiene un tamaño horizontal o vertical, o ambos, establezca los `width` valores y/o `height` en la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propiedad. Sin embargo, el uso excesivo de esta propiedad puede perjudicar el rendimiento de la aplicación, ya que hace que el motor de diseño realice cálculos de diseño adicionales.

> [!IMPORTANT]
> Las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y no tienen ningún efecto en los elementos secundarios de [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) .

## <a name="absolute-positioning-and-sizing"></a>Posicionamiento y ajuste de tamaño absolutos

De forma predeterminada, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) coloca y ajusta el tamaño de los elementos secundarios usando valores absolutos, que se especifican en unidades independientes del dispositivo, que definen explícitamente dónde se deben colocar los elementos secundarios en el diseño. Esto se logra mediante la adición de elementos secundarios a la `Children` colección de `AbsoluteLayout` y el establecimiento de la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta en cada elemento secundario en la posición absoluta y/o los valores de tamaño.

> [!WARNING]
> El uso de valores absolutos para el posicionamiento y el ajuste de tamaño de los elementos secundarios puede ser problemático, ya que los distintos dispositivos tienen diferentes tamaños de pantalla y resoluciones. Por lo tanto, las coordenadas del centro de la pantalla en un dispositivo pueden estar desplazadas en otros dispositivos.

En el código XAML siguiente [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) se muestra un cuyos elementos secundarios se colocan usando valores absolutos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <AbsoluteLayout Margin="20">
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
        <Label Text="Stylish Header"
               FontSize="24"
               AbsoluteLayout.LayoutBounds="30, 25" />
    </AbsoluteLayout>
</ContentPage>
```

En este ejemplo, la posición de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto se define utilizando los dos primeros valores absolutos que se especifican en la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta. El tamaño de cada `BoxView` se define mediante los valores tercero y adelante. La posición del [`Label`](xref:Xamarin.Forms.Label) objeto se define utilizando los dos valores absolutos que se especifican en la `AbsoluteLayout.LayoutBounds` propiedad adjunta. Los valores de tamaño no se especifican para y `Label` , por tanto, no están restringidos y tienen el mismo tamaño. En todos los casos, los valores absolutos representan unidades independientes del dispositivo.

En la captura de pantalla siguiente se muestra el diseño resultante:

![Los elementos secundarios se colocan en un AbsoluteLayout con valores absolutos](absolutelayout-images/absolute-values.png)

El código de C# equivalente se muestra a continuación:

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        AbsoluteLayout absoluteLayout = new AbsoluteLayout
        {
            Margin = new Thickness(20)
        };

        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver,
        }, new Rectangle(0, 10, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(0, 20, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(10, 0, 5, 65));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(20, 0, 5, 65));

        absoluteLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, new Point(30,25));                    

        Title = "Stylish header demo";
        Content = absoluteLayout;
    }
}
```

En este ejemplo, la posición y el tamaño de cada [`BoxView`](xref:Xamarin.Forms.BoxView) se definen mediante un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto. La posición de [`Label`](xref:Xamarin.Forms.Label) se define utilizando un [`Point`](xref:Xamarin.Forms.Point) objeto.

En C#, también es posible establecer la posición y el tamaño de un elemento secundario de una [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) después de que se haya agregado a `Children` la colección, utilizando el [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) método. El primer argumento de este método es el elemento secundario y el segundo es un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto.

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Que usa valores absolutos puede colocar y ajustar el tamaño de los elementos secundarios para que no quepan dentro de los límites del diseño.

## <a name="proportional-positioning-and-sizing"></a>Posicionamiento proporcional y tamaño

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Puede colocar y cambiar el tamaño de los elementos secundarios usando valores proporcionales. Para ello, se agregan elementos secundarios a la `Children` colección de `AbsoluteLayout` y se establece la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta en cada elemento secundario en la posición proporcional o en los valores de tamaño del intervalo 0-1. Los valores de posición y tamaño se hacen proporcionales estableciendo la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta en cada elemento secundario.

La [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta, de tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , permite establecer una marca que indica que los valores de posición y tamaño de los límites del diseño de un elemento secundario son proporcionales al tamaño de `AbsoluteLayout` . Al diseñar un elemento secundario, `AbsoluteLayout` escala los valores de posición y tamaño de forma adecuada a cualquier tamaño de dispositivo.

La [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) enumeración define los siguientes miembros:

- `None`, indica que los valores se interpretarán como absolutos. Este es el valor predeterminado de la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta.
- `XProportional`, indica que el `x` valor se interpretará como proporcional, al mismo tiempo que se tratan todos los demás valores como absolutos.
- `YProportional`, indica que el `y` valor se interpretará como proporcional, al mismo tiempo que se tratan todos los demás valores como absolutos.
- `WidthProportional`, indica que el `width` valor se interpretará como proporcional, al mismo tiempo que se tratan todos los demás valores como absolutos.
- `HeightProportional`, indica que el `height` valor se interpretará como proporcional, al mismo tiempo que se tratan todos los demás valores como absolutos.
- `PositionProportional`, indica que los `x` valores y se `y` interpretarán como proporcional, mientras que los valores de tamaño se interpretan como absolutos.
- `SizeProportional`, indica que los `width` valores y se `height` interpretarán como proporcional, mientras que los valores de posición se interpretan como absolutos.
- `All`, indica que todos los valores se interpretarán como proporcionales.

> [!TIP]
> La [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) enumeración es una `Flags` enumeración, lo que significa que los miembros de enumeración se pueden combinar. Esto se logra en XAML con una lista separada por comas y en C# con el operador OR bit a bit.

Por ejemplo, si usa la `SizeProportional` marca y establece el ancho de un elemento secundario en 0,25 y el alto en 0,1, el elemento secundario será un cuarto del ancho de [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) y una décima parte del alto. La `PositionProportional` marca es similar. Una posición de (0,0) coloca el elemento secundario en la esquina superior izquierda, mientras que una posición de (1,1) coloca el elemento secundario en la esquina inferior derecha y una posición de (0.5, 0.5) centra el elemento secundario dentro de `AbsoluteLayout` .

En el código XAML siguiente [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) se muestra un cuyos elementos secundarios se colocan usando valores proporcionales:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.ProportionalDemoPage"
             Title="Proportional demo">
    <AbsoluteLayout>
        <BoxView Color="Blue"
                 AbsoluteLayout.LayoutBounds="0.5,0,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Green"
                 AbsoluteLayout.LayoutBounds="0,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Red"
                 AbsoluteLayout.LayoutBounds="1,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Black"
                 AbsoluteLayout.LayoutBounds="0.5,1,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <Label Text="Centered text"
               AbsoluteLayout.LayoutBounds="0.5,0.5,110,25"
               AbsoluteLayout.LayoutFlags="PositionProportional" />
    </AbsoluteLayout>
</ContentPage>
```

En este ejemplo, cada elemento secundario se coloca usando valores proporcionales pero tiene un tamaño absoluto. Esto se logra estableciendo la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propiedad adjunta de cada elemento secundario en `PositionProportional` . Los dos primeros valores que se especifican en la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propiedad adjunta, para cada elemento secundario, definen la posición mediante valores proporcionales. El tamaño de cada elemento secundario se define con los valores absolutos tercero y adelante, mediante unidades independientes del dispositivo.

En la captura de pantalla siguiente se muestra el diseño resultante:

![Los elementos secundarios se colocan en un AbsoluteLayout con valores de posición proporcional](absolutelayout-images/proportional-position.png)

El código de C# equivalente se muestra a continuación:

```csharp
public class ProportionalDemoPageCS : ContentPage
{
    public ProportionalDemoPageCS()
    {
        BoxView blue = new BoxView { Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds(blue, new Rectangle(0.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags(blue, AbsoluteLayoutFlags.PositionProportional);

        BoxView green = new BoxView { Color = Color.Green };
        AbsoluteLayout.SetLayoutBounds(green, new Rectangle(0, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(green, AbsoluteLayoutFlags.PositionProportional);

        BoxView red = new BoxView { Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds(red, new Rectangle(1, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(red, AbsoluteLayoutFlags.PositionProportional);

        BoxView black = new BoxView { Color = Color.Black };
        AbsoluteLayout.SetLayoutBounds(black, new Rectangle(0.5, 1, 100, 25));
        AbsoluteLayout.SetLayoutFlags(black, AbsoluteLayoutFlags.PositionProportional);

        Label label = new Label { Text = "Centered text" };
        AbsoluteLayout.SetLayoutBounds(label, new Rectangle(0.5, 0.5, 110, 25));
        AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.PositionProportional);

        Title = "Proportional demo";
        Content = new AbsoluteLayout
        {
            Children = { blue, green, red, black, label }
        };
    }
}
```

En este ejemplo, la posición y el tamaño de cada elemento secundario se establece con el [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) método. El primer argumento del método es el elemento secundario y el segundo es un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto. La posición de cada elemento secundario se establece con valores proporcionales, mientras que el tamaño de cada elemento secundario se establece con valores absolutos, mediante unidades independientes del dispositivo.

> [!NOTE]
> Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) que usa valores proporcionales puede colocar y cambiar el tamaño de los elementos secundarios para que no quepan dentro de los límites del diseño usando valores fuera del intervalo de 0-1.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de AbsoluteLayout (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms Propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)
- [Elegir un Xamarin.Forms diseño](choose-layout.md)
- [Mejorar el rendimiento de las Xamarin.Forms aplicaciones](~/xamarin-forms/deploy-test/performance.md)
