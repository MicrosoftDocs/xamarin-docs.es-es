---
title: Consumo de extensiones de marcado XAML
description: En este artículo se explica cómo usar las extensiones de marcado XAML de Xamarin.Forms para mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento debe establecerse en una variedad de orígenes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 736627edc8628f229e6e1a1e079fca24b8ff97db
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490121"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Las extensiones de marcado XAML que ayudan a mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento debe establecerse en una variedad de orígenes. Varias extensiones de marcado XAML forman parte de la especificación de XAML 2009. Esta información aparece en los archivos XAML con el habitual `x` prefijo de espacio de nombres y se conocen comúnmente por este prefijo. En este artículo se describe las extensiones de marcado siguiente:

- [`x:Static`](#static) : hacer referencia a propiedades estáticas, campos o los miembros de enumeración.
- [`x:Reference`](#reference) – con el nombre de elementos de la página de referencia.
- [`x:Type`](#type) : establezca un atributo en un `System.Type` objeto.
- [`x:Array`](#array) : construya una matriz de objetos de un tipo determinado.
- [`x:Null`](#null) : establezca un atributo en un `null` valor.
- [`OnPlatform`](#onplatform) : personalizar la apariencia de la interfaz de usuario en forma de acuerdo con la plataforma.
- [`OnIdiom`](#onidiom) : personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo se está ejecutando la aplicación en.
- [`DataTemplate`](#datatemplate-markup-extension) : convierte un tipo en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension) : mostrar un icono de fuente en cualquier vista que pueda mostrar un `ImageSource`.

Las extensiones de marcado XAML adicionales históricamente han sido compatible con otras implementaciones de XAML y también son compatibles con Xamarin.Forms. Estos se describen con más detalle en otros artículos:

- `StaticResource`: se hace referencia a los objetos de un diccionario de recursos, como se describe en el artículo [**diccionarios de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`: responde a los cambios en los objetos de un diccionario de recursos, como se describe en el artículo [**estilos dinámicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`: establezca un vínculo entre las propiedades de dos objetos, como se describe en el artículo [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`: realiza el enlace de datos de una plantilla de control, como se describe en el artículo [**enlace de una plantilla de control**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md).
- `RelativeSource`: establece el origen de enlace en relación con la posición del destino de enlace, como se describe en el artículo [enlaces relativos](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

El [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) diseño hace uso de la extensión de marcado personalizada [ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression). Esta extensión de marcado se describe en el artículo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static (extensión de marcado)

El `x:Static` extensión de marcado es compatible con la [ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension) clase. La clase tiene una propiedad única denominada [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de tipo `string` establecer en el nombre de una constante pública, una propiedad estática, campo estático o miembro de enumeración.

Una forma común de usar `x:Static` consiste en definir primero una clase con algunas constantes o variables estáticas, como Este diminuto `AppConstants` clase en el [ **MarkupExtensions** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) programa:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

El **x: Static demostración** página muestra varias maneras de usar el `x:Static` extensión de marcado. El enfoque más detallado, crea una instancia del `StaticExtension` clase entre `Label.FontSize` etiquetas de elemento de propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

El analizador XAML también permite la `StaticExtension` clase va a abreviar como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Esto se puede simplificar aún más, pero el cambio presenta parte de la nueva sintaxis: consta de colocar el `StaticExtension` clase y la configuración entre llaves del miembro. La expresión resultante se establece directamente en el `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Tenga en cuenta que hay *ningún* comillas encerrado entre llaves. El `Member` propiedad de `StaticExtension` ya no es un atributo XML. En realidad es parte de la expresión para la extensión de marcado.

Tal como se puede abreviar `x:StaticExtension` a `x:Static` cuando se usa como un elemento de objeto, también puede abreviar en la expresión entre llaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

El `StaticExtension` clase tiene un `ContentProperty` que hacen referencia a la propiedad de atributo `Member`, que marca esta propiedad como propiedad de contenido de la clase predeterminada. Para las extensiones de marcado XAML que expresa entre llaves, puede eliminar el `Member=` forma parte de la expresión:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Se trata de la forma más común de la `x:Static` extensión de marcado.

El **demostración estático** página contiene dos ejemplos de otros. La etiqueta a la raíz del archivo XAML contiene una declaración de espacio de nombres XML para .NET `System` espacio de nombres:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Esto permite la `Label` tamaño de fuente debe establecerse en el campo estático `Math.PI`. Esto da como resultado texto pequeño en su lugar, por lo que la `Scale` propiedad está establecida en `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

El último ejemplo se muestra la `Device.RuntimePlatform` valor. El `Environment.NewLine` propiedad estática se utiliza para insertar un carácter de nueva línea entre los dos `Span` objetos:

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Este es el ejemplo en ejecución:

[![Demo de x:Static](consuming-images/staticdemo-small.png "Demo de x:Static")](consuming-images/staticdemo-large.png#lightbox "Demo de x:Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>extensión de marcado x:Reference

El `x:Reference` extensión de marcado es compatible con la [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) clase. La clase tiene una propiedad única denominada [ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) typu `string` establecer en el nombre de un elemento en la página que se asignó un nombre con `x:Name`. Esto `Name` propiedad es la propiedad content de `ReferenceExtension`, por lo que `Name=` no es necesaria cuando `x:Reference` aparece entre llaves.

El `x:Reference` extensión de marcado se utiliza exclusivamente con los enlaces de datos, que se describen con más detalle en el artículo [ **enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

El **x: Reference demostración** página muestra dos usos de `x:Reference` con enlaces de datos, la primera que se usa para establecer el `Source` propiedad de la `Binding` objeto y el segundo donde se usa para establecer el `BindingContext` propiedad para los enlaces de datos de dos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Ambos `x:Reference` expresiones usan la versión abreviada de la `ReferenceExtension` nombre de clase y elimine el `Name=` forma parte de la expresión. En el primer ejemplo, el `x:Reference` extensión de marcado está incrustada en el `Binding` extensión de marcado. Tenga en cuenta que el `Source` y `StringFormat` configuración está separada por comas. Esta es la ejecución del programa:

[![Demostración de x:Reference](consuming-images/referencedemo-small.png "Demostración de x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demostración de x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (extensión de marcado)

El `x:Type` extensión de marcado es el equivalente XAML de C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) palabra clave. Es compatible con la [ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension) (clase), que define una propiedad denominada [ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) de tipo `string` que se establece en un nombre de clase o estructura. El `x:Type` devuelve de la extensión de marcado el [ `System.Type` ](xref:System.Type) objeto de esa clase o estructura. `TypeName` es la propiedad content de `TypeExtension`, por lo que `TypeName=` no es necesaria cuando `x:Type` aparece entre llaves.

Dentro de Xamarin.Forms, hay varias propiedades que tienen argumentos de tipo `Type`. Algunos ejemplos son el [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propiedad de `Style`y el [x: TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) atributo utilizado para especificar los argumentos en las clases genéricas. Sin embargo, el analizador XAML se realiza el `typeof` operación automáticamente y el `x:Type` extensión de marcado no se usa en estos casos.

Un único lugar donde `x:Type` *es* necesarios es con el `x:Array` extensión de marcado, como se describe en el [siguiente sección](#array).

El `x:Type` extensión de marcado también es útil al construir un menú donde cada elemento de menú correspondiente a un objeto de un tipo determinado. Puede asociar un `Type` con cada elemento de menú de objetos y, a continuación, cree una instancia del objeto cuando se selecciona el elemento de menú.

Se trata cómo el menú de navegación en `MainPage` en el **las extensiones de marcado** programa works. El **MainPage.xaml** archivo contiene un `TableView` con cada `TextCell` correspondiente a una página determinada en el programa:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Aquí está la página principal de apertura en **las extensiones de marcado**:

[![Página principal](consuming-images/mainpage-small.png "Página principal")](consuming-images/mainpage-large.png#lightbox "Página principal")

Cada `CommandParameter` propiedad está establecida en un `x:Type` extensión de marcado que se hace referencia a una de las otras páginas. El `Command` propiedad está enlazada a una propiedad denominada `NavigateCommand`. Esta propiedad se define en el `MainPage` archivo de código subyacente:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

El `NavigateCommand` propiedad es un `Command` objeto que implementa un comando execute con un argumento de tipo `Type` &mdash; el valor de `CommandParameter`. Usa el método `Activator.CreateInstance` para crear una instancia de la página y, a continuación, navega a ella. El constructor concluye estableciendo el `BindingContext` de la página a sí mismo, lo que permite el `Binding` en `Command` funcione. Consulte la [ **enlace de datos** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) artículo y especialmente los [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) artículo para obtener más detalles sobre este tipo de código.

El **x: Type demostración** página usa una técnica similar para crear instancias de elementos de Xamarin.Forms y agregarlos a un `StackLayout`. El archivo XAML inicialmente consta de tres `Button` elementos con sus `Command` propiedades establecidas en un `Binding` y `CommandParameter` propiedades establecidas en los tipos de las tres vistas de Xamarin.Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente define e inicializa la `CreateCommand` propiedad:

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

El método que se ejecuta cuando un `Button` se presiona crea una nueva instancia del argumento, se establece su `VerticalOptions` propiedad y lo agrega a la `StackLayout`. Los tres `Button` elementos, a continuación, compartan la página con las vistas creadas dinámicamente:

[![Demostración de x:Type](consuming-images/typedemo-small.png "Demostración de x:Type")](consuming-images/typedemo-large.png#lightbox "Demostración de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (extensión de marcado)

El `x:Array` extensión de marcado permite definir una matriz en el marcado. Es compatible con la [ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension) (clase), que define dos propiedades:

- `Type` de tipo `Type`, lo que indica el tipo de los elementos de la matriz.
- `Items` de tipo `IList`, que es una colección de los propios elementos. Se trata de la propiedad content de `ArrayExtension`.

El `x:Array` propia extensión de marcado nunca aparece entre llaves. En su lugar, `x:Array` etiquetas inicial y final delimitan la lista de elementos. Establecer el `Type` propiedad a un `x:Type` extensión de marcado.

El **x: Array demostración** página muestra cómo usar `x:Array` para agregar elementos a un `ListView` estableciendo el `ItemsSource` propiedad a una matriz:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

El `ViewCell` crea un simple `BoxView` para cada entrada de color:

[![Demostración de x:Array](consuming-images/arraydemo-small.png "Demostración de x:Array")](consuming-images/arraydemo-large.png#lightbox "Demostración de x:Array")

Hay varias maneras de especificar la persona `Color` elementos de esta matriz. Puede usar un `x:Static` extensión de marcado:

```xaml
<x:Static Member="Color.Blue" />
```

O bien, puede usar `StaticResource` para recuperar un color de un diccionario de recursos:

```xaml
<StaticResource Key="myColor" />
```

Hacia el final de este artículo, verá una extensión de marcado XAML personalizada que también crea un nuevo valor de color:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Al definir matrices de tipos comunes, como cadenas o números, use las etiquetas enumeradas en el [ **pasar argumentos de Constructor** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) artículo para delimitar los valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null (extensión de marcado)

El `x:Null` extensión de marcado es compatible con la [ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension) clase. No tiene ninguna propiedad lo que es simplemente el equivalente XAML de C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) palabra clave.

El `x:Null` extensión de marcado es rara vez es necesario y se usan en contadas ocasiones, pero si lo encuentra lo necesita, será muy contento de que existe.

El **x: Null demostración** página ilustra un escenario cuando `x:Null` podría ser conveniente. Supongamos que define un modo implícito `Style` para `Label` que incluye un `Setter` que establece el `FontFamily` propiedad a un nombre de familia depende de la plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

A continuación, verá que para uno de los `Label` elementos, desea que todos los valores de propiedades en la parte implícita `Style` , excepto para el `FontFamily`, que desee que sea el valor predeterminado. Puede definir otra `Style` para ese propósito, pero un enfoque más sencillo consiste simplemente en establecer el `FontFamily` propiedad de la instancia concreta `Label` a `x:Null`, tal y como se muestra en el centro de `Label`.

Esta es la ejecución del programa:

[![Demo de x:Null](consuming-images/nulldemo-small.png "Demo de x:Null")](consuming-images/nulldemo-large.png#lightbox "Demo de x:Null")

Tenga en cuenta que cuatro de los `Label` elementos tienen una fuente de serif, pero el centro `Label` tiene la fuente sans serif de forma predeterminada.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensión de marcado en la plataforma

El `OnPlatform` extensión de marcado permite personalizar la apariencia de la interfaz de usuario en forma de acuerdo con la plataforma. Proporciona la misma funcionalidad que el [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) y [ `On` ](xref:Xamarin.Forms.On) clases, pero con una representación más concisa.

El `OnPlatform` extensión de marcado es compatible con la [ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension) (clase), que define las siguientes propiedades:

- `Default` de tipo `object`, establecido en un valor predeterminado que se aplicará a las propiedades que representan las plataformas.
- `Android` de tipo `object`, que se establece en un valor que se aplicará de Android.
- `GTK` de tipo `object`, que se establece en un valor que se aplicará de plataformas de GTK +.
- `iOS` de tipo `object`, que se establece en un valor que se aplicará de iOS.
- `macOS` de tipo `object`, que se establece en un valor que se aplicará de macOS.
- `Tizen` de tipo `object`, que se establece en un valor que se aplicará de la plataforma Tizen.
- `UWP` de tipo `object`, que se establece en un valor que se aplicará de la plataforma Universal de Windows.
- `WPF` de tipo `object`, que se establece en un valor que se aplicará de la plataforma Windows Presentation Foundation.
- `Converter` de tipo `IValueConverter`, que se establece en un `IValueConverter` implementación.
- `ConverterParameter` de tipo `object`, que se establecen en un valor para pasar el `IValueConverter` implementación.

> [!NOTE]
> El analizador XAML permite la [ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension) clase va a abreviar como `OnPlatform`.

El `Default` propiedad es la propiedad content de `OnPlatformExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar el `Default=` forma parte de la expresión siempre que sea el primer argumento. Si no se establece la propiedad `Default`, se usará de forma predeterminada el valor de la propiedad [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) , siempre que la extensión de marcado tenga como destino una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).

> [!IMPORTANT]
> El analizador XAML espera que se proporcionen valores del tipo correcto a las propiedades que se consume el `OnPlatform` extensión de marcado. Si es necesaria, la conversión de tipos la `OnPlatform` extensión de marcado se intentará realizar mediante los convertidores predeterminados proporcionados por Xamarin.Forms. Sin embargo, hay algunas conversiones de tipos que no se puede realizar los convertidores de forma predeterminada y en estos casos el `Converter` propiedad debe establecerse en un `IValueConverter` implementación.

El **OnPlatform demostración** página muestra cómo usar el `OnPlatform` extensión de marcado:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres `OnPlatform` expresiones usan la versión abreviada de la `OnPlatformExtension` nombre de clase. Los tres `OnPlatform` conjunto de extensiones de marcado el [ `Color` ](xref:Xamarin.Forms.BoxView.Color), [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest), y [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propiedades de la [ `BoxView` ](xref:Xamarin.Forms.BoxView) en valores distintos en iOS, Android y UWP. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las plataformas que no están especificadas, al tiempo que elimina la `Default=` forma parte de la expresión. Tenga en cuenta que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demostración en la plataforma](consuming-images/onplatformdemo-small.png "Demostración en la plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demostración en la plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensión de marcado en idioma

La extensión de marcado de `OnIdiom` permite personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación. Es compatible con la [ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension) (clase), que define las siguientes propiedades:

- `Default` de tipo `object`, establecido en un valor predeterminado que se aplicará a las propiedades que representan las expresiones de dispositivo.
- `Phone` de tipo `object`, que se establece en un valor que se aplicará de teléfonos.
- `Tablet` de tipo `object`, que se establece en un valor que se aplicará de tabletas.
- `Desktop` de tipo `object`, que se establece en un valor que se aplicará de plataformas de escritorio.
- `TV` de tipo `object`, que se establece en un valor que se aplicará de plataformas de TV.
- `Watch` de tipo `object`, que se establece en un valor que se aplicará de plataformas de inspección.
- `Converter` de tipo `IValueConverter`, que se establece en un `IValueConverter` implementación.
- `ConverterParameter` de tipo `object`, que se establecen en un valor para pasar el `IValueConverter` implementación.

> [!NOTE]
> El analizador XAML permite la [ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension) clase va a abreviar como `OnIdiom`.

El `Default` propiedad es la propiedad content de `OnIdiomExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar el `Default=` forma parte de la expresión siempre que sea el primer argumento.

> [!IMPORTANT]
> El analizador XAML espera que se proporcionen valores del tipo correcto a las propiedades que se consume el `OnIdiom` extensión de marcado. Si es necesaria, la conversión de tipos la `OnIdiom` extensión de marcado se intentará realizar mediante los convertidores predeterminados proporcionados por Xamarin.Forms. Sin embargo, hay algunas conversiones de tipos que no se puede realizar los convertidores de forma predeterminada y en estos casos el `Converter` propiedad debe establecerse en un `IValueConverter` implementación.

El **OnIdiom demostración** página muestra cómo usar el `OnIdiom` extensión de marcado:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres `OnIdiom` expresiones usan la versión abreviada de la `OnIdiomExtension` nombre de clase. Los tres `OnIdiom` conjunto de extensiones de marcado el [ `Color` ](xref:Xamarin.Forms.BoxView.Color), [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest), y [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propiedades de la [ `BoxView` ](xref:Xamarin.Forms.BoxView) en valores distintos en el teléfono, tableta y escritorio expresiones. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las expresiones que no se especifican, mientras se elimina el `Default=` forma parte de la expresión. Tenga en cuenta que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demo de idioma](consuming-images/onidiomdemo-small.png "Demo de idioma")](consuming-images/onidiomdemo-large.png#lightbox "Demo de idioma")

## <a name="datatemplate-markup-extension"></a>Extensión de marcado DataTemplate

La extensión de marcado `DataTemplate` permite convertir un tipo en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Es compatible con la clase `DataTemplateExtension`, que define una propiedad `TypeName`, de tipo `string`, que se establece en el nombre del tipo que se va a convertir en un `DataTemplate`. El `TypeName` propiedad es la propiedad content de `DataTemplateExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la parte `TypeName=` de la expresión.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `DataTemplateExtension` como `DataTemplate`.

Un uso típico de esta extensión de marcado está en una aplicación de Shell, tal y como se muestra en el ejemplo siguiente:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

En este ejemplo, `MonkeysPage` se convierte de un [`ContentPage`](xref:Xamarin.Forms.ContentPage) a un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la propiedad `ShellContent.ContentTemplate`. Esto garantiza que `MonkeysPage` solo se crea cuando se produce la navegación a la página, en lugar de al inicio de la aplicación.

Para obtener más información sobre las aplicaciones de Shell, consulte [Shell de Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extensión de marcado FontImage

La extensión de marcado `FontImage` permite mostrar un icono de fuente en cualquier vista que pueda mostrar un `ImageSource`. Proporciona la misma funcionalidad que la clase `FontImageSource`, pero con una representación más concisa.

La clase `FontImageExtension` admite la extensión de marcado `FontImage`, que define las siguientes propiedades:

- `FontFamily` de tipo `string`, la familia de fuentes a la que pertenece el icono de fuente.
- `Glyph` de tipo `string`, el valor del carácter Unicode del icono de fuente.
- `Color` de tipo [`Color`](xref:Xamarin.Forms.Color), el color que se va a utilizar al mostrar el icono de fuente.
- `Size` de tipo `double`, el tamaño, en unidades independientes del dispositivo, del icono de fuente representada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `FontImageExtension` como `FontImage`.

El `Glyph` propiedad es la propiedad content de `FontImageExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar el `Glyph=` forma parte de la expresión siempre que sea el primer argumento.

La página de **demostración de FontImage** muestra cómo usar la extensión de marcado `FontImage`:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

En este ejemplo, la versión abreviada del nombre de la clase `FontImageExtension` se usa para mostrar un icono XBox, de la familia de fuentes Ionicons, en un [`Image`](xref:Xamarin.Forms.Image). La expresión también utiliza la extensión de marcado `OnPlatform` para especificar diferentes valores de propiedad `FontFamily` en iOS y Android. Además, se elimina el `Glyph=` parte de la expresión y las propiedades de extensión de marcado que se establecen se separan mediante comas. Tenga en cuenta que, mientras que el carácter Unicode para el icono es `\uf30c`, debe tener un carácter de escape en XAML, por lo que se convierte en `&#xf30c;`.

Esta es la ejecución del programa:

[![Captura de pantalla de la extensión de marcado FontImage](consuming-images/fontimagedemo.png "Demostración de FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demostración de FontImage")

Para obtener información sobre cómo mostrar los iconos de fuente especificando los datos del icono de fuente en un `FontImageSource` objeto, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="define-your-own-markup-extensions"></a>Definir sus propias extensiones de marcado

Si se ha encontrado una necesidad de una extensión de marcado XAML que no está disponible en Xamarin.Forms, puede [crear las suyas propias](creating.md).

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensiones de marcado XAML de Xamarin.Forms libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell de Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).
