---
title: Xamarin.FormsBoxView
description: En este artículo se explica cómo usar un rectángulo de color para decoración, gráficos e interacción en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 06f1813bafb34a9c32603490e66f8caa6c6a6a22
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573825"
---
# <a name="xamarinforms-boxview"></a>Xamarin.FormsBoxView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView)representa un rectángulo simple con el ancho, el alto y el color especificados. Puede usar `BoxView` para la decoración, los gráficos rudimentarios y la interacción con el usuario a través de la entrada táctil.

Dado Xamarin.Forms que no tiene un sistema de gráficos vectoriales integrado, `BoxView` ayuda a compensar. Algunos de los programas de ejemplo que se describen en este artículo se usan `BoxView` para representar gráficos. `BoxView`Se puede cambiar el tamaño para que se parezca a una línea de un ancho y un grosor específicos y, a continuación, girar cualquier ángulo mediante la `Rotation` propiedad.

Aunque `BoxView` puede imitar gráficos sencillos, es posible que desee investigar [usando Xamarin.Forms SkiaSharp en](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) para obtener requisitos de gráficos más sofisticados.

## <a name="setting-boxview-color-and-size"></a>Establecer el color y el tamaño de BoxView

Normalmente, establecerá las siguientes propiedades de `BoxView` :

- [`Color`](xref:Xamarin.Forms.BoxView.Color)para establecer su color.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius)para establecer su radio de redondeo.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)para establecer el ancho de `BoxView` en unidades independientes del dispositivo.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)para establecer el alto del `BoxView` .

La `Color` propiedad es de tipo `Color` ; la propiedad se puede establecer en cualquier `Color` valor, incluidos los campos de solo lectura estáticos 141 de los colores con nombre que abarcan alfabéticamente de `AliceBlue` a `YellowGreen` .

La `CornerRadius` propiedad es de tipo [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) ; la propiedad se puede establecer en un único `double` valor de radio de esquina uniforme, o una `CornerRadius` estructura definida por cuatro `double` valores que se aplican a la parte superior izquierda, superior derecha, parte inferior izquierda y parte inferior derecha de `BoxView` .

Las `WidthRequest` `HeightRequest` propiedades y solo desempeñan un rol si `BoxView` no está *restringido* en el diseño. Este es el caso cuando el contenedor de diseño necesita conocer el tamaño del elemento secundario, por ejemplo, cuando `BoxView` es un elemento secundario de una celda de tamaño automático en el `Grid` diseño. `BoxView`También se puede anular la restricción cuando `HorizontalOptions` sus `VerticalOptions` propiedades y se establecen en valores distintos de `LayoutOptions.Fill` . Si `BoxView` no está restringido, pero `WidthRequest` `HeightRequest` no se han establecido las propiedades y, el ancho o el alto se establecen en los valores predeterminados de 40 unidades o aproximadamente 1/4 pulgadas en los dispositivos móviles.

Las `WidthRequest` `HeightRequest` propiedades y se omiten si `BoxView` está *restringido* en el diseño, en cuyo caso el contenedor de diseño impone su propio tamaño en `BoxView` .

Una vista `BoxView` se puede restringir en una dimensión y sin restricciones en la otra. Por ejemplo, si `BoxView` es un elemento secundario de un elemento vertical `StackLayout` , la dimensión vertical de la `BoxView` no está restringida y su dimensión horizontal está normalmente restringida. Pero hay excepciones para esa dimensión horizontal: Si `BoxView` tiene su `HorizontalOptions` propiedad establecida en un valor distinto de `LayoutOptions.Fill` , la dimensión horizontal tampoco tiene restricciones. También es posible que el `StackLayout` propio tenga una dimensión horizontal sin restricciones, en cuyo caso `BoxView` también se anulará la restricción horizontal.

En el ejemplo [**BasicBoxView**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview) se muestra un cuadrado de una pulgada sin restricciones `BoxView` en el centro de su página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Este es el resultado:

[![BoxView básico](boxview-images/basicboxview-small.png "BoxView básico")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Si las `VerticalOptions` `HorizontalOptions` propiedades y se quitan de la `BoxView` etiqueta o se establecen en `Fill` , el `BoxView` se vuelve limitado por el tamaño de la página y se expande para rellenar la página.

`BoxView`También puede ser un elemento secundario de `AbsoluteLayout` . En ese caso, la ubicación y el tamaño de `BoxView` se establecen utilizando la `LayoutBounds` propiedad enlazable adjunta. `AbsoluteLayout`Se describe en el artículo [**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Verá ejemplos de todos estos casos en los programas de ejemplo que se indican a continuación.

## <a name="rendering-text-decorations"></a>Representación de decoraciones de texto

Puede usar el `BoxView` para agregar algunas decoraciones simples en las páginas en forma de líneas horizontales y verticales. El ejemplo [**TextDecoration**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration) muestra esto. Todos los objetos visuales del programa se definen en el archivo **mainpage. Xaml** , que contiene varios `Label` elementos y `BoxView` en el `StackLayout` mostrado aquí:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Todo el marcado que se muestra a continuación son elementos secundarios de `StackLayout` . Este marcado se compone de varios tipos de `BoxView` elementos decorativos utilizados con el `Label` elemento:

[![Decoración de texto](boxview-images/textdecoration-small.png "Decoración de texto")](boxview-images/textdecoration-large.png#lightbox "Decoración de texto")

El encabezado estilizado en la parte superior de la página se logra con un `AbsoluteLayout` cuyos elementos secundarios son cuatro `BoxView` elementos y `Label` , todos ellos asignados a ubicaciones y tamaños específicos:

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

En el archivo XAML, va `AbsoluteLayout` seguido de un `Label` con texto con formato que describe `AbsoluteLayout` .

Puede subrayar una cadena de texto mediante la inclusión de `Label` y `BoxView` en `StackLayout` que tiene su `HorizontalOptions` valor establecido en un valor distinto de `Fill` . A continuación, el ancho de `StackLayout` se rige por el ancho de `Label` , que luego impone ese ancho en `BoxView` . `BoxView`Solo se asigna un alto explícito:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

No puede usar esta técnica para subrayar palabras individuales en cadenas de texto más largas o en un párrafo.

También es posible usar un `BoxView` para que se parezca a un `hr` elemento HTML (regla horizontal). Simplemente deje que el ancho de `BoxView` esté determinado por su contenedor primario, que en este caso es `StackLayout` :

```xaml
<BoxView HeightRequest="3" />
```

Por último, puede dibujar una línea vertical en un lado de un párrafo de texto incluyendo `BoxView` y `Label` en horizontal `StackLayout` . En este caso, el alto de `BoxView` es el mismo que el alto de `StackLayout` , que se rige por el alto del `Label` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

## <a name="listing-colors-with-boxview"></a>Enumerar los colores con BoxView

`BoxView`Es conveniente para mostrar los colores. Este programa usa `ListView` para enumerar todos los campos estáticos públicos de solo lectura de la Xamarin.Forms `Color` estructura:

[![Colores de ListView](boxview-images/listviewcolors-small.png "Colores de ListView")](boxview-images/listviewcolors-large.png#lightbox "Colores de ListView")

El programa [**ListViewColors**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors) incluye una clase denominada `NamedColor` . El constructor estático utiliza la reflexión para tener acceso a todos los campos de la `Color` estructura y crear un `NamedColor` objeto para cada uno de ellos. Estos se almacenan en la `All` propiedad estática:

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Los objetos visuales de programa se describen en el archivo XAML. La `ItemsSource` propiedad de `ListView` se establece en la propiedad estática `NamedColor.All` , lo que significa que `ListView` muestra todos los objetos individuales `NamedColor` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Los `NamedColor` objetos se formatean mediante el `ViewCell` objeto que se establece como la plantilla de datos de `ListView` . Esta plantilla incluye una `BoxView` cuya `Color` propiedad está enlazada a la `Color` propiedad del `NamedColor` objeto.

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Reproducir el juego de vida mediante la subclase de BoxView

El juego de vida es un autómata de telefonía móvil inventado por mathematician John Conway y popular en las páginas de Ciencias *científicas* de la década de 1970. El [juego de vida del artículo Conway de](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)la Wikipedia proporciona una buena introducción.

El Xamarin.Forms programa [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife) define una clase denominada `LifeCell` que se deriva de `BoxView` . Esta clase encapsula la lógica de una celda individual en el juego de vida:

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell`agrega tres propiedades más a `BoxView` : las `Col` `Row` propiedades y almacenan la posición de la celda dentro de la cuadrícula y la `IsAlive` propiedad indica su estado. La `IsAlive` propiedad también establece la `Color` propiedad de `BoxView` en negro Si la celda está activa y en blanco si la celda no está activa.

`LifeCell`también instala un `TapGestureRecognizer` para permitir que el usuario alterne el estado de las celdas al puntear en ellas. La clase convierte el `Tapped` evento del reconocedor de gestos en su propio `Tapped` evento.

El programa **GameOfLife** también incluye una `LifeGrid` clase que encapsula gran parte de la lógica del juego y una `MainPage` clase que controla los objetos visuales del programa. Incluyen una superposición que describe las reglas del juego. Este es el programa en acción que muestra un par de cientos `LifeCell` de objetos en la página:

[![Juego de vida](boxview-images/gameoflife-small.png "Juego de vida")](boxview-images/gameoflife-large.png#lightbox "Juego de vida")

## <a name="creating-a-digital-clock"></a>Crear un reloj digital

El programa [**DotMatrixClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock) crea 210 `BoxView` elementos para simular los puntos de una pantalla de matriz de puntos de 5 por 7 antigua. Puede leer la hora en modo vertical u horizontal, pero es más grande en horizontal:

[![Reloj de matriz de puntos](boxview-images/dotmatrixclock-small.png "Reloj de matriz de puntos")](boxview-images/dotmatrixclock-large.png#lightbox "Reloj de matriz de puntos")

El archivo XAML hace poco más que crear una instancia del que se `AbsoluteLayout` usa para el reloj:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Todo lo demás se produce en el archivo de código subyacente. La lógica de visualización de matriz de puntos se ha simplificado en gran medida por la definición de varias matrices que describen los puntos correspondientes a cada uno de los 10 dígitos y dos puntos:

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Estos campos finalizan con una matriz tridimensional de `BoxView` elementos para almacenar los patrones de puntos para los seis dígitos.

El constructor crea todos los `BoxView` elementos de los dígitos y dos puntos, y también inicializa la `Color` propiedad de los `BoxView` elementos de los dos puntos:

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Este programa utiliza la característica de posicionamiento y ajuste de tamaño relativa de `AbsoluteLayout` . El ancho y el alto de cada `BoxView` se establecen en valores fraccionarios, en concreto, el 85% de 1 dividido entre el número de puntos horizontales y verticales. Las posiciones también se establecen en valores fraccionarios.

Dado que todas las posiciones y tamaños son relativos al tamaño total de `AbsoluteLayout` , el `SizeChanged` controlador de la página solo necesita establecer un `HeightRequest` de `AbsoluteLayout` :

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

El ancho de `AbsoluteLayout` se establece automáticamente porque se ajusta al ancho completo de la página.

El código final de la `MainPage` clase procesa la devolución de llamada del temporizador y colorea los puntos de cada dígito. La definición de las matrices multidimensionales al principio del archivo de código subyacente ayuda a que esta lógica sea la parte más sencilla del programa:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```

## <a name="creating-an-analog-clock"></a>Crear un reloj analógico

Un reloj de matriz de puntos podría parecer una aplicación obvia de `BoxView` , pero `BoxView` los elementos también son capaces de realizar un reloj analógico:

[![BoxView reloj](boxview-images/boxviewclock-small.png "BoxView reloj")](boxview-images/boxviewclock-large.png#lightbox "BoxView reloj")

Todos los objetos visuales del programa [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) son elementos secundarios de `AbsoluteLayout` . Estos elementos se ajustan mediante la `LayoutBounds` propiedad adjunta y se giran con la `Rotation` propiedad.

`BoxView`Se crean instancias de los tres elementos de las manecillas del reloj en el archivo XAML, pero no se colocan ni se ajusta su tamaño:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

El constructor del archivo de código subyacente crea instancias de los `BoxView` elementos 60 para las marcas de graduación en torno a la circunferencia del reloj:

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

El tamaño y la posición de todos los `BoxView` elementos se producen en el `SizeChanged` controlador de `AbsoluteLayout` . Una pequeña estructura interna de la clase denominada `HandParams` describe el tamaño de cada una de las tres manecillas en relación con el tamaño total del reloj:

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

El `SizeChanged` controlador determina el centro y el radio de y, `AbsoluteLayout` a continuación, ajusta el tamaño y coloca los `BoxView` elementos 60 usados como marcas de graduación. El `for` bucle concluye estableciendo la `Rotation` propiedad de cada uno de estos `BoxView` elementos. Al final del `SizeChanged` controlador, `LayoutHand` se llama al método para ajustar el tamaño y la posición de las tres manos del reloj:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

El `LayoutHand` método dimensiona y coloca cada mano para apuntar directamente hasta la posición 12:00. Al final del método, la `AnchorY` propiedad se establece en una posición que corresponde al centro del reloj. Indica el centro de rotación.

Las manos se giran en la función de devolución de llamada del temporizador:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

La segunda mano se trata de un poco diferente: se aplica una función de aceleración de animación para que el movimiento parezca mecánico, en lugar de suave. En cada paso, la segunda mano vuelve un poco y, a continuación, supera su destino. Este pequeño fragmento de código agrega mucho al realismo del movimiento.

## <a name="related-links"></a>Vínculos relacionados

- [BoxView básico (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [Decoración de texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [Colores de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [Juego de vida (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [Reloj de matriz de puntos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [Reloj BoxView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)
