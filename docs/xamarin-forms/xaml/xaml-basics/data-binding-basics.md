---
title: Parte 4. Conceptos básicos del enlace de datos
description: Enlaces de datos permiten propiedades de dos objetos se vinculen para que un cambio en uno provoca un cambio en el otro.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 3fdc251d3ed24c96d7d3fa9620e483ad47a8491e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227928"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Conceptos básicos del enlace de datos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Enlaces de datos permiten propiedades de dos objetos se vinculen para que un cambio en uno provoca un cambio en el otro. Esto es una herramienta muy valiosa, y mientras los enlaces de datos se pueden definir completamente en código, XAML proporciona accesos directos y comodidad. Por lo tanto, se enlaza una de las extensiones de marcado más importantes en Xamarin.Forms._

## <a name="data-bindings"></a>Enlaces de datos

Enlaces de datos conectan las propiedades de dos objetos, denominados el *origen* y *destino*. En el código, se requieren dos pasos: La `BindingContext` propiedad del objeto de destino debe establecerse en el objeto de origen y se `SetBinding` debe llamar al método (a menudo utilizado `Binding` junto con la clase) en el objeto de destino para enlazar una propiedad de ese objeto a una propiedad del origen. objeto.

La propiedad de destino debe ser una propiedad enlazable, lo que significa que el objeto de destino debe derivar de `BindableObject`. La documentación de Xamarin.Forms en línea indica qué propiedades son propiedades enlazables. Una propiedad de `Label` como `Text` está asociado con la propiedad enlazable `TextProperty`.

En el marcado, también debe realizar los mismos dos pasos que son necesarios en el código, salvo que el `Binding` extensión de marcado ocupa el lugar de la `SetBinding` llamar y la `Binding` clase.

Sin embargo, al definir los enlaces de datos en XAML, hay varias maneras de establecer el `BindingContext` del objeto de destino. A veces se establece desde el archivo de código subyacente, en ocasiones, con un `StaticResource` o `x:Static` extensión de marcado y a veces como el contenido de `BindingContext` etiquetas de elemento de propiedad.

Los enlaces se usan con mayor frecuencia para conectarse a los objetos visuales de un programa con un modelo de datos subyacente, normalmente en una realización de la arquitectura de aplicación MVVM (Model-View-ViewModel), como se describe en [parte 5. Enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), pero también son posibles otros escenarios.

## <a name="view-to-view-bindings"></a>Para ver los enlaces

Puede definir los enlaces de datos para vincular las propiedades de las dos vistas en la misma página. En este caso, Establece el `BindingContext` del objeto de destino mediante el `x:Reference` extensión de marcado.

Este es un archivo XAML que contiene un `Slider` y dos `Label` vistas, uno de los cuales se gira el `Slider` valor y otra que muestra la `Slider` valor:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El `Slider` contiene un `x:Name` atributo que se hace referencia a los dos `Label` vistas mediante el `x:Reference` extensión de marcado.

El `x:Reference` extensión de enlace define una propiedad denominada `Name` para establecer en el nombre del elemento que se hace referencia, en este caso `slider`. Sin embargo, el `ReferenceExtension` clase que define el `x:Reference` extensión de marcado también define un `ContentProperty` atributo `Name`, lo que significa que no requiere de forma explícita. Solo para diversas, la primera `x:Reference` incluye "nombre =" pero no así la segunda:

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

El `Binding` propia extensión de marcado puede tener varias propiedades, al igual que el `BindingBase` y `Binding` clase. El `ContentProperty` para `Binding` es `Path`, pero la "ruta de acceso =" si la ruta de acceso es el primer elemento de parte de la extensión de marcado que se puede omitir el `Binding` extensión de marcado. El primer ejemplo tiene "ruta de acceso =", pero omite el segundo ejemplo:

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Las propiedades pueden estar en una línea o se dividen en varias líneas:

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Hacer lo que sea conveniente.

Tenga en cuenta la `StringFormat` propiedad en el segundo `Binding` extensión de marcado. En Xamarin.Forms, los enlaces no realizan ninguna conversión de tipos implícita, y si necesita mostrar un objeto que no son de cadena como una cadena debe proporcionar un convertidor de tipos o utilizar `StringFormat`. En segundo plano, estático `String.Format` método se utiliza para implementar `StringFormat`. Que es potencialmente un problema, porque las especificaciones de formato .NET implican entre llaves, que también se utilizan para delimitar las extensiones de marcado. Esto crea un riesgo de confundir el analizador XAML. Para evitar esto, coloque toda la cadena de formato en las comillas simples:

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Este es el programa en ejecución:

[![Enlaces de vista a vista](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>El modo de enlace

Una vista única puede tener enlaces de datos en varias de sus propiedades. Sin embargo, cada vista solo puede tener un `BindingContext`, por lo que varios enlaces de datos en esa vista, deben hacer referencia a propiedades del mismo objeto.

La solución a este y otros problemas implica la `Mode` propiedad, que se establece en un miembro de la `BindingMode` enumeración:

- `Default`
- `OneWay` : los valores se transfieren desde el origen al destino
- `OneWayToSource` : los valores se transfieren desde el destino al origen
- `TwoWay` : los valores se transfieren ambos sentidos entre el origen y destino
- `OneTime`: los datos van desde el origen hasta el destino, pero `BindingContext` solo cuando los cambios

El programa siguiente muestra un uso habitual de la `OneWayToSource` y `TwoWay` modos de enlace. Cuatro `Slider` vistas están pensadas para controlar la `Scale`, `Rotate`, `RotateX`, y `RotateY` las propiedades de un `Label`. En primer lugar, parece como si estas cuatro propiedades de la `Label` debe ser los destinos de enlace de datos porque cada una se establece un `Slider`. Sin embargo, el `BindingContext` de `Label` puede ser un solo objeto, y hay cuatro controles deslizantes diferentes.

Por ese motivo, todos los enlaces se establecen de manera aparentemente inversa: La `BindingContext` de cada uno de los cuatro controles deslizantes se establece `Label`en, y los enlaces se establecen en las `Value` propiedades de los controles deslizantes. Mediante el uso de la `OneWayToSource` y `TwoWay` modos, estos `Value` propiedades pueden establecer las propiedades de origen, que son el `Scale`, `Rotate`, `RotateX`, y `RotateY` propiedades de la `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Los enlaces en tres de los `Slider` las vistas son `OneWayToSource`, lo que significa que el `Slider` valor provoca un cambio en la propiedad de su `BindingContext`, que es el `Label` denominado `label`. Estos tres `Slider` vistas provocan cambios en el `Rotate`, `RotateX`, y `RotateY` propiedades de la `Label`.

Sin embargo, el enlace para el `Scale` propiedad es `TwoWay`. Esto es porque el `Scale` propiedad tiene un valor predeterminado de 1 y usar un `TwoWay` enlace hace que el `Slider` inicial del valor que se establecerá en 1 en lugar de 0. Si fuera ese enlace `OneWayToSource`, `Scale` inicialmente se establecería la propiedad en 0 de la `Slider` valor predeterminado. El `Label` no estará visible y que podría provocar confusión en el usuario.

 [![Enlaces hacia atrás](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > El [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) clase también tiene [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) y [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propiedades, que escalen el `VisualElement` en los ejes x y y respectivamente.

## <a name="bindings-and-collections"></a>Los enlaces y colecciones

Nada ilustra el poder de XAML y enlaces de datos mejores que una plantilla `ListView`.

`ListView` define un `ItemsSource` propiedad de tipo `IEnumerable`, y muestra los elementos de la colección. Estos elementos pueden ser objetos de cualquier tipo. De forma predeterminada, `ListView` usa el `ToString` método de cada elemento para mostrar ese elemento. A veces esto es lo que desea, pero en muchos casos, `ToString` devuelve solo el nombre de clase completo del objeto.

Sin embargo, los elementos de la `ListView` colección puede mostrarse como desee mediante el uso de un *plantilla*, lo que implica una clase que deriva de `Cell`. La plantilla se clona para todos los elementos de la `ListView`, y los enlaces de datos que se han establecido en la plantilla se transfieren a los clones individuales.

Muy a menudo, deseará crear una celda personalizada para estos elementos mediante el `ViewCell` clase. Este proceso es un tanto confuso en código, pero en XAML resulta muy sencillo.

Incluye en el XamlSamples proyecto es una clase llamada `NamedColor`. Cada `NamedColor` objeto tiene `Name` y `FriendlyName` las propiedades de tipo `string`y un `Color` propiedad de tipo `Color`. Además, `NamedColor` tiene 141 campos estáticos de sólo lectura de tipo `Color` correspondientes a los colores definidos en Xamarin.Forms `Color` clase. Crea un constructor estático una `IEnumerable<NamedColor>` colección que contiene `NamedColor` objetos correspondientes a estos campos estáticos y se asigna a su público estático `All` propiedad.

Establecimiento estático `NamedColor.All` propiedad a la `ItemsSource` de un `ListView` es fácil mediante el `x:Static` extensión de marcado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La presentación resultante se establece que los elementos son realmente del tipo `XamlSamples.NamedColor`:

[![Enlazar a una colección](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

No es toda la información, pero la `ListView` es desplazable y seleccionable.

Para definir una plantilla para los elementos, desea dividir el `ItemTemplate` propiedad como un elemento de propiedad y establézcalo en un `DataTemplate`, que hace referencia a continuación, un `ViewCell`. Para el `View` propiedad de la `ViewCell` puede definir un diseño de una o más vistas para mostrar cada elemento. Este es un ejemplo sencillo:

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> El origen de enlace para las celdas y los elementos secundarios de las `ListView.ItemsSource` celdas es la colección.

El `Label` elemento está establecido en el `View` propiedad de la `ViewCell`. (El `ViewCell.View` etiquetas no son necesarios porque el `View` propiedad es la propiedad content de `ViewCell`.) Este marcado se muestra el `FriendlyName` propiedad de cada uno `NamedColor` objeto:

[![Enlazar a una colección con un DataTemplate](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

Mucho mejor. Ahora todo lo necesario es refinar la plantilla de elemento con el color real y obtener más información. Para admitir esta plantilla, algunos valores y los objetos se han definido en el diccionario de recursos de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Tenga en cuenta el uso de `OnPlatform` para definir el tamaño de un `BoxView` y el alto de la `ListView` filas. Aunque los valores para todas las plataformas son iguales, el marcado fácilmente podría ser adaptado para otros valores ajustar la visualización.

## <a name="binding-value-converters"></a>Convertidores de valores de enlace

La anterior **ListView demostración** archivo XAML muestra el individuo `R`, `G`, y `B` propiedades de Xamarin.Forms `Color` estructura. Estas propiedades son de tipo `double` y el intervalo comprendido entre 0 y 1. Si desea mostrar los valores hexadecimales, no basta con utilizar `StringFormat` con una especificación de formato "X2". Esto sólo funciona para números enteros y además, el `double` valores deben ser multiplicado por 255.

Este pequeño problema se resolvió con una *convertidor*, también denominado una *conversor de enlaces*. Se trata de una clase que implementa el `IValueConverter` interfaz, lo que significa tiene dos métodos denominados `Convert` y `ConvertBack`. El `Convert` método se llama cuando se transfiere un valor de origen al destino; el `ConvertBack` se llama al método para las transferencias de destino al origen en `OneWayToSource` o `TwoWay` enlaces:

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

El `ConvertBack` método desempeñan un papel en este programa porque los enlaces son sólo una forma de origen al destino.

Un enlace hace referencia a un conversor de enlaces con el `Converter` propiedad. Un conversor de enlaces también puede aceptar un parámetro especificado con el `ConverterParameter` propiedad. Para algunos versatilidad, es cómo se especifica el multiplicador. El conversor de enlaces comprueba el parámetro de convertidor para válido `double` valor.

Por lo que se puede compartir entre varios enlaces, se crea una instancia de convertidor en el diccionario de recursos:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Enlaces de datos de tres hacen referencia a esta instancia. Tenga en cuenta que el `Binding` extensión de marcado contiene incrustada `StaticResource` extensión de marcado:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Este es el resultado:

[![Enlazar a una colección con un DataTemplate y convertidores](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

El `ListView` es bastante sofisticada en el control de cambios que se produzcan dinámicamente en los datos subyacentes, pero solo si realizar ciertos pasos. Si la colección de elementos que se asigna a la `ItemsSource` propiedad de la `ListView` cambios en tiempo de ejecución: es decir, si los elementos se pueden agregar a o quitar de la colección, utilice un `ObservableCollection` clase para estos elementos. `ObservableCollection` implementa el `INotifyCollectionChanged` interfaz, y `ListView` instalará un controlador para el `CollectionChanged` eventos.

Si las propiedades de los propios elementos cambian en tiempo de ejecución, los elementos de la colección deben implementar la `INotifyPropertyChanged` cambios de interfaz y la señal a los valores de propiedad mediante la `PropertyChanged` eventos. Esto se muestra en la siguiente parte de esta serie, [parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

Los enlaces de datos proporcionan un mecanismo eficaz para vincular las propiedades entre dos objetos dentro de una página, o entre objetos visuales y los datos subyacentes. Pero cuando la aplicación comienza a funcionar con orígenes de datos, un patrón arquitectónico popular aplicación comienza a aparecer como un paradigma útil. Este tema se trata en [parte 5. Enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción a XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis de XAML esencial (ejemplo)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado de XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. De enlaces de datos a MVVM (ejemplo)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
