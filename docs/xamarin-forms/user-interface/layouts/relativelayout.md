---
title: Xamarin.Forms RelativeLayout
description: El Xamarin.Forms RelativeLayout se usa para los elementos secundarios de posición y tamaño en relación con las propiedades del diseño o de los elementos del mismo nivel.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d350ceee778c9f9ba9f25555a89a925558c6d38b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556846"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![::: no-LOC (Xamarin. Forms)::: RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Se utiliza para colocar y cambiar el tamaño de los elementos secundarios en relación con las propiedades del diseño o de los elementos del mismo nivel. Esto permite que las interfaces de la creación se escalen proporcionalmente entre los tamaños de los dispositivos. Además, a diferencia de otras clases de diseño, `RelativeLayout` puede colocar los elementos secundarios para que se superpongan.

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) clase define las siguientes propiedades:

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty), de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que es una propiedad adjunta que representa la restricción en la posición X del elemento secundario.
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty), de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que es una propiedad adjunta que representa la restricción en la posición Y del elemento secundario.
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty), de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que es una propiedad adjunta que representa la restricción en el ancho del elemento secundario.
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty), de tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que es una propiedad adjunta que representa la restricción en el alto del elemento secundario.
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty), de tipo [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) , que es una propiedad adjunta que representa la restricción en la posición y el tamaño del elemento secundario. Esta propiedad no se puede consumir fácilmente desde XAML.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos y con estilo. Para obtener más información sobre las propiedades adjuntas, consulte [ Xamarin.Forms propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md).

> [!NOTE]
> El ancho y el alto de un elemento secundario de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) también se pueden especificar a través de las propiedades y del elemento secundario `WidthRequest` `HeightRequest` , en lugar de las [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propiedades adjuntas y.

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) clase se deriva de la `Layout<T>` clase, que define una `Children` propiedad de tipo `IList<T>` . La `Children` propiedad es `ContentProperty` de la `Layout<T>` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

> [!TIP]
> Evite el uso de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) siempre que sea posible. Como resultado, la CPU tendrá que realizar mucho más trabajo.

## <a name="constraints"></a>Restricciones

Dentro de [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , la posición y el tamaño de los elementos secundarios se especifican como restricciones usando valores absolutos o valores relativos. Cuando no se especifican restricciones, se colocará un elemento secundario en la esquina superior izquierda del diseño.

En la tabla siguiente se muestra cómo especificar restricciones en XAML y C#:

|     | XAML | C# |
| --- | ---- | -- |
| **Valores absolutos** | Las restricciones absolutas se especifican mediante el establecimiento de las [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) propiedades adjuntas en `double` valores. | Las restricciones absolutas se especifican mediante el [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) método o mediante la `Children.Add` sobrecarga que requiere un `Func<Rectangle>` argumento. |
| **Valores relativos** | Las restricciones relativas se especifican mediante el establecimiento de las [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) propiedades adjuntas en [`Constraint`](xref:Xamarin.Forms.Constraint) objetos devueltos por la `ConstraintExpression` extensión de marcado. | Las restricciones relativas se especifican mediante [`Constraint`](xref:Xamarin.Forms.Constraint) objetos devueltos por los métodos de la `Constraint` clase. |

Para obtener más información sobre cómo especificar restricciones mediante valores absolutos, vea [posicionamiento absoluto y ajuste de tamaño](#absolute-positioning-and-sizing). Para obtener más información sobre cómo especificar restricciones usando valores relativos, vea [posicionamiento relativo y ajuste de tamaño](#relative-positioning-and-sizing).

En C#, se pueden agregar elementos secundarios a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) tres `Add` sobrecargas. La primera sobrecarga requiere `Expression<Func<Rectangle>>` que especifique la posición y el tamaño de un elemento secundario. La segunda sobrecarga requiere `Expression<Func<double>>` objetos opcionales para los `x` `y` argumentos,, `width` y `height` . La tercera sobrecarga requiere `Constraint` objetos opcionales para los `x` `y` argumentos,, `width` y `height` .

Es posible cambiar la posición y el tamaño de un elemento secundario en [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) con los [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) métodos, [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) , [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) y [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) . El primer argumento de cada uno de estos métodos es el elemento secundario y el segundo es un [`Constraint`](xref:Xamarin.Forms.Constraint) objeto. Además, el [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) método también se puede utilizar para cambiar la posición y el tamaño de un elemento secundario. El primer argumento de este método es el elemento secundario y el segundo es un [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) objeto.

## <a name="absolute-positioning-and-sizing"></a>Posicionamiento y ajuste de tamaño absolutos

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Puede colocar y cambiar el tamaño de los elementos secundarios usando valores absolutos, que se especifican en unidades independientes del dispositivo, que definen explícitamente dónde se deben colocar los elementos secundarios en el diseño. Para ello, se agregan elementos secundarios a la `Children` colección de `RelativeLayout` y se establece la [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propiedad,, [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) y [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) las propiedades adjuntas en cada elemento secundario en la posición absoluta y/o los valores de tamaño.

> [!WARNING]
> El uso de valores absolutos para el posicionamiento y el ajuste de tamaño de los elementos secundarios puede ser problemático, ya que los distintos dispositivos tienen diferentes tamaños de pantalla y resoluciones. Por lo tanto, las coordenadas del centro de la pantalla en un dispositivo pueden estar desplazadas en otros dispositivos.

En el código XAML siguiente [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) se muestra un cuyos elementos secundarios se colocan usando valores absolutos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <RelativeLayout Margin="20">
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="10"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="20"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="10"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="20"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <Label Text="Stylish header"
               FontSize="24"
               RelativeLayout.XConstraint="30"
               RelativeLayout.YConstraint="25" />
    </RelativeLayout>
</ContentPage>
```

En este ejemplo, la posición de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto se define usando los valores especificados en [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) las [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propiedades adjuntas y. El tamaño de cada `BoxView` se define utilizando los valores especificados en [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) las [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propiedades adjuntas y. La posición del [`Label`](xref:Xamarin.Forms.Label) objeto también se define con los valores especificados en las `XConstraint` `YConstraint` propiedades adjuntas y. Sin embargo, los valores de tamaño no se especifican para y `Label` , por tanto, no están restringidos y tienen un tamaño propio. En todos los casos, los valores absolutos representan unidades independientes del dispositivo.

En las capturas de pantalla siguientes se muestra el diseño resultante:

![Los elementos secundarios se colocan en un RelativeLayout con valores absolutos](relativelayout-images/absolute-values.png)

A continuación se muestra el código de C# equivalente:

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout
        {
            Margin = new Thickness(20)
        };

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 10, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 20, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(10, 0, 5, 65));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(20, 0, 5, 65));

        relativeLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, Constraint.Constant(30), Constraint.Constant(25));

        Title = "Stylish header demo";
        Content = relativeLayout;
    }
}
```

En este ejemplo, [`BoxView`](xref:Xamarin.Forms.BoxView) los objetos se agregan a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) mediante una `Add` sobrecarga que requiere que `Expression<Func<Rectangle>>` especifique la posición y el tamaño de cada elemento secundario. La posición de [`Label`](xref:Xamarin.Forms.Label) se define utilizando una `Add` sobrecarga que requiere objetos opcionales [`Constraint`](xref:Xamarin.Forms.Constraint) , en este caso creados por el [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) método.

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Que usa valores absolutos puede colocar y ajustar el tamaño de los elementos secundarios para que no quepan dentro de los límites del diseño.

## <a name="relative-positioning-and-sizing"></a>Posicionamiento y ajuste de tamaño relativos

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Puede colocar y cambiar el tamaño de los elementos secundarios con valores relativos a las propiedades del diseño, o a los elementos relacionados. Para ello, se agregan elementos secundarios a la `Children` colección de `RelativeLayout` y se establece la [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propiedad,, [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) y [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) las propiedades adjuntas en cada elemento secundario en valores relativos mediante [`Constraint`](xref:Xamarin.Forms.Constraint) objetos.

Las restricciones pueden ser una constante, relativa a un elemento primario o relativa a un elemento relacionado. El tipo de restricción se representa mediante la [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) enumeración, que define los siguientes miembros:

- `RelativeToParent`, que indica una restricción relativa a un elemento primario.
- `RelativeToView`, que indica una restricción relativa a una vista (o al mismo nivel).
- `Constant`, que indica una restricción Constant.

### <a name="constraint-markup-extension"></a>Extensión de marcado de restricción

En XAML, un [`Constraint`](xref:Xamarin.Forms.Constraint) objeto se puede crear mediante la [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extensión de marcado. Esta extensión de marcado se usa normalmente para relacionar la posición y el tamaño de un elemento secundario dentro de un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) con su elemento primario o con un elemento relacionado.

La [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) clase define las siguientes propiedades:

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant), de tipo `double` , que representa el valor constante de restricción.
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName), de tipo `string` , que representa el nombre de un elemento de origen en el que se va a calcular la restricción.
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor), de tipo `double` , que representa el factor por el que se va a escalar una dimensión restringida, en relación con el elemento de origen. El valor predeterminado de esta propiedad es 1.
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property), de tipo `string` , que representa el nombre de la propiedad en el elemento de origen que se va a utilizar en el cálculo de la restricción.
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type), de tipo [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) , que representa el tipo de la restricción.

Para más información sobre las extensiones de marcado de Xamarin.Forms, vea [Extensiones de marcado para el lenguaje XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

En el código XAML siguiente [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) se muestra un cuyos elementos secundarios están restringidos por la [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extensión de marcado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.RelativePositioningAndSizingDemoPage"
             Title="RelativeLayout demo">
    <RelativeLayout>
        <BoxView Color="Red"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Green"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Silver"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}" />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Black"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=X}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Y}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Height, Factor=0.33}" />
    </RelativeLayout>
</ContentPage>
```

En este ejemplo, la posición de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto se define estableciendo las [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propiedades adjuntas y. El primero `BoxView` tiene sus `XConstraint` `YConstraint` propiedades y adjuntas establecidas en constantes, que son valores absolutos. El resto de los `BoxView` objetos tienen su posición establecida mediante al menos un valor relativo. Por ejemplo, el `BoxView` objeto amarillo establece la `XConstraint` propiedad adjunta en el ancho de su elemento primario (el [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ) menos 40. Del mismo modo, `BoxView` establece la `YConstraint` propiedad adjunta en el alto de su elemento primario menos 40. Esto garantiza que el amarillo `BoxView` aparezca en la esquina inferior derecha de la pantalla.

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView) los objetos que no especifican un tamaño se ajustan automáticamente a 40 x 40 por Xamarin.Forms .

El Silver [`BoxView`](xref:Xamarin.Forms.BoxView) denominado `oneThird` se coloca centralmente, con respecto a su elemento primario. También se ajusta en relación con su elemento primario, siendo un tercio de su ancho y alto. Esto se logra estableciendo las [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) propiedades adjuntas y en el ancho del elemento primario (el [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ), multiplicado por 0,33. Del mismo modo, las [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propiedades adjuntas y se establecen en el alto del elemento primario, multiplicado por 0,33.

El color negro [`BoxView`](xref:Xamarin.Forms.BoxView) está colocado y tiene un tamaño relativo a `oneThird` `BoxView` . Esto se logra estableciendo sus [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) propiedades y [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) adjuntas en `X` los `Y` valores y, respectivamente, del elemento relacionado. Del mismo modo, su tamaño se establece en un tercio del ancho y el alto de su elemento relacionado. Esto se logra estableciendo sus [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) propiedades y [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) adjuntas en `Width` los `Height` valores y del elemento relacionado, respectivamente, que se multiplican por 0,33.

En la captura de pantalla siguiente se muestra el diseño resultante:

![Los elementos secundarios se colocan en un RelativeLayout con valores relativos](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>Objetos de restricción

La [`Constraint`](xref:Xamarin.Forms.Constraint) clase define los siguientes métodos estáticos públicos, que devuelven `Constraint` objetos:

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*), que restringe un elemento secundario a un tamaño especificado con un `double` .
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*), que restringe un elemento secundario mediante una expresión lambda.
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*), que restringe un elemento secundario en relación con el tamaño de su elemento primario.
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*), que restringe un elemento secundario en relación con el tamaño de una vista.

Además, la [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) clase define un método único, [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) , que devuelve un `BoundsConstraint` que restringe la posición y el tamaño de un elemento secundario con un `Expression<Func<Rectangle>>` . Este método se puede utilizar para establecer la [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) propiedad adjunta.

En el siguiente código de C# [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) se muestra un cuyos elementos secundarios están restringidos por [`Constraint`](xref:Xamarin.Forms.Constraint) objetos:

```csharp
public class RelativePositioningAndSizingDemoPageCS : ContentPage
{
    public RelativePositioningAndSizingDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout();

        // Four BoxView's
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Red },
            Constraint.Constant(0),
            Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Green },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }), Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Blue },
            Constraint.Constant(0),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Yellow },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        // Centered and 1/3 width and height of parent
        BoxView silverBoxView = new BoxView { Color = Color.Silver };
        relativeLayout.Children.Add(
            silverBoxView,
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }));

        // 1/3 width and height of previous
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Black },
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.X;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Y;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Width * 0.33;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Height * 0.33;
            }));

        Title = "RelativeLayout demo";
        Content = relativeLayout;
    }
}
```

En este ejemplo, los elementos secundarios se agregan a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) mediante la `Add` sobrecarga que requiere un `Constraint` objeto opcional para los `x` `y` argumentos,, `width` y `height` .

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Que usa valores relativos puede colocar y cambiar el tamaño de los elementos secundarios para que no quepan dentro de los límites del diseño.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de RelativeLayout (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.Forms Propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Elegir un Xamarin.Forms diseño](choose-layout.md)
- [Mejorar el rendimiento de las Xamarin.Forms aplicaciones](~/xamarin-forms/deploy-test/performance.md)