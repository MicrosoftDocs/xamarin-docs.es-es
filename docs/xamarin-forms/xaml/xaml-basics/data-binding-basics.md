---
Título: "parte 4. Conceptos básicos del enlace de datos "Descripción:" los enlaces de datos permiten vincular propiedades de dos objetos para que un cambio en uno produzca un cambio en el otro ".
MS. Prod: Xamarin ms. Technology: Xamarin-Forms ms. AssetID: 342288C3-BB4C-4924-B178-72E112D777BA autor: davidbritch ms. Author: dabritch ms. Date: 10/25/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-4-data-binding-basics"></a>Parte 4. Conceptos básicos del enlace de datos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Los enlaces de datos permiten vincular las propiedades de dos objetos para que un cambio en uno produzca un cambio en el otro. Se trata de una herramienta muy valiosa y, mientras que los enlaces de datos se pueden definir completamente en el código, XAML proporciona accesos directos y comodidad. Por consiguiente, una de las extensiones de marcado más importantes en Xamarin.Forms es Binding._

## <a name="data-bindings"></a>Enlaces de datos

Los enlaces de datos conectan las propiedades de dos objetos, denominados *origen* y *destino*. En el código, se requieren dos pasos: la `BindingContext` propiedad del objeto de destino debe establecerse en el objeto de origen y `SetBinding` se debe llamar al método (que se usa a menudo junto con la `Binding` clase) en el objeto de destino para enlazar una propiedad de ese objeto a una propiedad del objeto de origen.

La propiedad de destino debe ser una propiedad enlazable, lo que significa que el objeto de destino debe derivarse de `BindableObject` . La documentación en línea Xamarin.Forms indica qué propiedades son propiedades que se pueden enlazar. Una propiedad de como `Label` `Text` está asociada a la propiedad enlazable `TextProperty` .

En el marcado, también debe realizar los mismos dos pasos que se requieren en el código, con la excepción de que la `Binding` extensión de marcado toma el lugar de la `SetBinding` llamada y la `Binding` clase.

Sin embargo, cuando se definen los enlaces de datos en XAML, hay varias maneras de establecer el `BindingContext` del objeto de destino. A veces se establece desde el archivo de código subyacente, a veces mediante `StaticResource` una `x:Static` extensión de marcado o, y a veces como el contenido de las `BindingContext` etiquetas de elemento de propiedad.

Los enlaces se usan con mayor frecuencia para conectar los objetos visuales de un programa con un modelo de datos subyacente, normalmente en una realización de la arquitectura de la aplicación MVVM (modelo-vista-ViewModel), como se describe en la [parte 5. De los enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), pero también se pueden realizar otros escenarios.

## <a name="view-to-view-bindings"></a>Enlaces de vista a vista

Puede definir enlaces de datos para vincular las propiedades de dos vistas en la misma página. En este caso, se establece el `BindingContext` del objeto de destino mediante la `x:Reference` extensión de marcado.

Este es un archivo XAML que contiene una `Slider` y dos `Label` vistas, una de las cuales se gira por el `Slider` valor y otra que muestra el `Slider` valor:

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

`Slider`Contiene un `x:Name` atributo al que hacen referencia las dos `Label` vistas mediante la extensión de `x:Reference` marcado.

La `x:Reference` extensión de enlace define una propiedad denominada `Name` para establecer en el nombre del elemento al que se hace referencia, en este caso `slider` . Sin embargo, la `ReferenceExtension` clase que define la `x:Reference` extensión de marcado también define un `ContentProperty` atributo para `Name` , lo que significa que no se requiere explícitamente. Solo para la variedad, el primero `x:Reference` incluye "Name =", pero el segundo no:

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

La `Binding` extensión de marcado puede tener varias propiedades, al igual que `BindingBase` las `Binding` clases y. `ContentProperty`Para `Binding` es `Path` , pero la parte "path =" de la extensión de marcado se puede omitir si la ruta de acceso es el primer elemento de la extensión de `Binding` marcado. El primer ejemplo tiene "path =", pero el segundo ejemplo lo omite:

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Todas las propiedades pueden estar en una línea o dividirse en varias líneas:

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Haga lo que sea conveniente.

Observe la `StringFormat` propiedad en la segunda `Binding` extensión de marcado. En Xamarin.Forms , los enlaces no realizan ninguna conversión de tipo implícita y, si necesita mostrar un objeto que no es una cadena como una cadena, debe proporcionar un convertidor de tipos o utilizar `StringFormat` . En segundo plano, el `String.Format` método estático se usa para implementar `StringFormat` . Esto puede ser un problema, ya que las especificaciones de formato de .NET incluyen llaves, que también se utilizan para delimitar las extensiones de marcado. Esto crea un riesgo de confusión en el analizador de XAML. Para evitarlo, coloque toda la cadena de formato entre comillas simples:

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Este es el programa en ejecución:

[![Enlaces de vista a vista](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>Modo de enlace

Una sola vista puede tener enlaces de datos en algunas de sus propiedades. Sin embargo, cada vista solo puede tener una `BindingContext` , por lo que varios enlaces de datos de esa vista deben tener todas las propiedades de referencia del mismo objeto.

La solución para este y otros problemas implica la `Mode` propiedad, que se establece en un miembro de la `BindingMode` enumeración:

- `Default`
- `OneWay`: los valores se transfieren desde el origen al destino.
- `OneWayToSource`: los valores se transfieren desde el destino al origen.
- `TwoWay`: los valores se transfieren en ambos sentidos entre el origen y el destino.
- `OneTime`: los datos van desde el origen hasta el destino, pero solo cuando los `BindingContext` cambios

En el programa siguiente se muestra un uso común de los `OneWayToSource` `TwoWay` modos de enlace y. `Slider`Hay cuatro vistas diseñadas para controlar las `Scale` `Rotate` propiedades,, `RotateX` y `RotateY` de `Label` . En primer lugar, parece como si estas cuatro propiedades de `Label` deben ser destinos de enlace de datos porque cada una se establece mediante `Slider` . Sin embargo, el `BindingContext` de `Label` solo puede ser un objeto y hay cuatro controles deslizantes diferentes.

Por ese motivo, todos los enlaces se establecen de manera aparentemente inversa: el `BindingContext` de cada uno de los cuatro controles deslizantes se establece en `Label` , y los enlaces se establecen en las `Value` propiedades de los controles deslizantes. Mediante el uso de los `OneWayToSource` `TwoWay` modos y, estas `Value` propiedades pueden establecer las propiedades de origen, que son las `Scale` `Rotate` propiedades,, `RotateX` y `RotateY` de `Label` :

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

Los enlaces de tres de las `Slider` vistas son `OneWayToSource` , lo que significa que el `Slider` valor produce un cambio en la propiedad de `BindingContext` , que es el `Label` denominado `label` . Estas tres `Slider` vistas producen cambios en las `Rotate` `RotateX` propiedades, y `RotateY` de `Label` .

Sin embargo, el enlace para la `Scale` propiedad es `TwoWay` . Esto se debe a que la `Scale` propiedad tiene un valor predeterminado de 1 y el uso de un `TwoWay` enlace hace que el `Slider` valor inicial se establezca en 1 en lugar de 0. Si ese enlace era `OneWayToSource` , la `Scale` propiedad se establecería inicialmente en 0 desde el `Slider` valor predeterminado. `Label`No sería visible y eso podría causar confusión al usuario.

 [![Enlaces hacia atrás](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase también tiene [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) las propiedades y, que escalan `VisualElement` en el eje x e y, respectivamente.

## <a name="bindings-and-collections"></a>Enlaces y colecciones

Nada ilustra la eficacia de los enlaces de datos y XAML mejor que una plantilla `ListView` .

`ListView`define una `ItemsSource` propiedad de tipo `IEnumerable` y muestra los elementos de esa colección. Estos elementos pueden ser objetos de cualquier tipo. De forma predeterminada, `ListView` usa el `ToString` método de cada elemento para mostrar ese elemento. A veces, esto es exactamente lo que desea, pero en muchos casos, `ToString` solo devuelve el nombre de clase completo del objeto.

Sin embargo, los elementos de la `ListView` colección se pueden mostrar de la forma que desee mediante el uso de una *plantilla*, que implica una clase que deriva de `Cell` . La plantilla se clona para cada elemento de `ListView` , y los enlaces de datos que se han establecido en la plantilla se transfieren a los clones individuales.

Con mucha frecuencia, querrá crear una celda personalizada para estos elementos mediante la `ViewCell` clase. Este proceso es un poco confuso en el código, pero en XAML resulta muy sencillo.

En el proyecto XamlSamples se incluye una clase denominada `NamedColor` . Cada `NamedColor` objeto tiene `Name` `FriendlyName` propiedades y de tipo `string` , y una `Color` propiedad de tipo `Color` . Además, `NamedColor` tiene 141 campos estáticos de solo lectura de tipo `Color` correspondientes a los colores definidos en la Xamarin.Forms `Color` clase. Un constructor estático crea una `IEnumerable<NamedColor>` colección que contiene `NamedColor` objetos correspondientes a estos campos estáticos y lo asigna a su propiedad estática pública `All` .

Establecer la `NamedColor.All` propiedad estática en `ItemsSource` de un `ListView` es fácil con la extensión de `x:Static` marcado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La presentación resultante establece que los elementos son realmente de tipo `XamlSamples.NamedColor` :

[![Enlazar a una colección](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

No es mucha información, pero `ListView` es desplazable y seleccionable.

Para definir una plantilla para los elementos, querrá dividir la `ItemTemplate` propiedad como un elemento de propiedad y establecerla en un `DataTemplate` , que luego hace referencia a un `ViewCell` . En la `View` propiedad de puede `ViewCell` definir un diseño de una o varias vistas para mostrar cada elemento. Este es un ejemplo sencillo:

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
> El origen de enlace para las celdas y los elementos secundarios de las celdas es la `ListView.ItemsSource` colección.

El `Label` elemento se establece en la `View` propiedad de `ViewCell` . (Las `ViewCell.View` etiquetas no son necesarias porque la `View` propiedad es el contenido de `ViewCell` .) Este marcado muestra la `FriendlyName` propiedad de cada `NamedColor` objeto:

[![Enlazar a una colección con un DataTemplate](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

Mucho mejor. Ahora todo lo que se necesita es spruce de la plantilla de elemento con más información y el color real. Para admitir esta plantilla, algunos valores y objetos se han definido en el Diccionario de recursos de la página:

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

Observe el uso de `OnPlatform` para definir el tamaño de un `BoxView` y el alto de las `ListView` filas. Aunque los valores de todas las plataformas son los mismos, el marcado se puede adaptar fácilmente a otros valores para ajustar la pantalla.

## <a name="binding-value-converters"></a>Enlace de convertidores de valores

En el archivo XAML de **demostración de ListView** anterior se muestran las `R` propiedades individuales, `G` y `B` de la Xamarin.Forms `Color` estructura. Estas propiedades son del tipo `double` y van de 0 a 1. Si desea mostrar los valores hexadecimales, no se puede usar `StringFormat` con una especificación de formato "x2". Eso solo sirve para enteros y, además, los `double` valores se deben multiplicar por 255.

Este pequeño problema se resolvió con un *convertidor de valores*, también denominado convertidor de *enlace*. Se trata de una clase que implementa la `IValueConverter` interfaz, lo que significa que tiene dos métodos denominados `Convert` y `ConvertBack` . `Convert`Se llama al método cuando se transfiere un valor desde el origen al destino; `ConvertBack` se llama al método para las transferencias del destino al origen en los `OneWayToSource` `TwoWay` enlaces o:

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

El `ConvertBack` método no desempeña un rol en este programa porque los enlaces son solo una forma de origen a destino.

Un enlace hace referencia a un convertidor de enlace con la `Converter` propiedad. Un convertidor de enlaces también puede aceptar un parámetro especificado con la `ConverterParameter` propiedad. Para algunas versatilidad, este es el modo en que se especifica el multiplicador. El convertidor de enlace comprueba si el parámetro de convertidor tiene un `double` valor válido.

Se crea una instancia del convertidor en el Diccionario de recursos para que se pueda compartir entre varios enlaces:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Tres enlaces de datos hacen referencia a esta instancia única. Tenga en cuenta que la `Binding` extensión de marcado contiene una extensión de marcado incrustada `StaticResource` :

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Este es el resultado:

[![Enlazar a una colección con un DataTemplate y convertidores](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

`ListView`Es bastante sofisticado para controlar los cambios que podrían producirse dinámicamente en los datos subyacentes, pero solo si se realizan determinados pasos. Si la colección de elementos asignada a la `ItemsSource` propiedad de `ListView` cambia durante el tiempo de ejecución, es decir, si se pueden agregar o quitar elementos de la colección, utilice una `ObservableCollection` clase para estos elementos. `ObservableCollection`implementa la `INotifyCollectionChanged` interfaz e `ListView` instalará un controlador para el `CollectionChanged` evento.

Si las propiedades de los elementos cambian durante el tiempo de ejecución, los elementos de la colección deben implementar la `INotifyPropertyChanged` interfaz y los cambios de señal en los valores de propiedad mediante el `PropertyChanged` evento. Esto se muestra en la siguiente parte de esta serie, [parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

Los enlaces de datos proporcionan un mecanismo eficaz para vincular propiedades entre dos objetos dentro de una página o entre objetos visuales y datos subyacentes. Pero cuando la aplicación comienza a trabajar con orígenes de datos, un patrón de arquitectura de aplicación popular comienza a surgir como un paradigma útil. Esto se trata en la [parte 5. De enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introducción con XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML esencial (ejemplo)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. De enlace de datos a MVVM (ejemplo)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
