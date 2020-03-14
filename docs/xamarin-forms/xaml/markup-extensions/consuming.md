---
title: Consumo de extensiones de marcado XAML
description: En este artículo se explica cómo usar las extensiones de marcado XAML de Xamarin.Forms para mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento debe establecerse en una variedad de orígenes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 693dd29e59aee73a206398a8a44cc4f15e385d8d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305622"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Las extensiones de marcado XAML que ayudan a mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento debe establecerse en una variedad de orígenes. Varias extensiones de marcado XAML forman parte de la especificación de XAML 2009. Aparecen en archivos XAML con el prefijo de espacio de nombres de `x` personalizado y se suelen conocer con este prefijo. En este artículo se describe las extensiones de marcado siguiente:

- [`x:Static`](#static) : haga referencia a propiedades estáticas, campos o miembros de enumeración.
- [`x:Reference`](#reference) : haga referencia a los elementos con nombre en la página.
- [`x:Type`](#type) : establezca un atributo en un objeto `System.Type`.
- [`x:Array`](#array) : construir una matriz de objetos de un tipo determinado.
- [`x:Null`](#null) : establezca un atributo en un valor `null`.
- [`OnPlatform`](#onplatform) : personalización de la apariencia de la interfaz de usuario en cada plataforma.
- [`OnIdiom`](#onidiom) : Personalice la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación.
- [`DataTemplate`](#datatemplate-markup-extension) : convierte un tipo en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension) : mostrar un icono de fuente en cualquier vista que pueda mostrar un `ImageSource`.

Las extensiones de marcado XAML adicionales históricamente han sido compatible con otras implementaciones de XAML y también son compatibles con Xamarin.Forms. Estos se describen con más detalle en otros artículos:

- `StaticResource`: se hace referencia a los objetos de un diccionario de recursos, como se describe en el artículo [**diccionarios de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`: responde a los cambios en los objetos de un diccionario de recursos, como se describe en el artículo [**estilos dinámicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`: establezca un vínculo entre las propiedades de dos objetos, como se describe en el artículo [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`: realiza el enlace de datos de una plantilla de control, como se describe en el artículo [**plantillas de control de Xamarin. Forms**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`: establece el origen de enlace en relación con la posición del destino de enlace, como se describe en el artículo [enlaces relativos](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

El diseño de [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) usa la extensión de marcado personalizada [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression). Esta extensión de marcado se describe en el artículo [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static (extensión de marcado)

La clase [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) admite el `x:Static` extensión de marcado. La clase tiene una propiedad única denominada [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de tipo `string` que se establece en el nombre de una constante pública, una propiedad estática, un campo estático o un miembro de enumeración.

Una forma habitual de usar `x:Static` es definir primero una clase con algunas constantes o variables estáticas, como esta pequeña `AppConstants` clase en el programa [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

En la página de **demostración de x:Static** se muestran varias maneras de usar la extensión de marcado `x:Static`. El enfoque más detallado crea instancias de la clase `StaticExtension` entre `Label.FontSize` etiquetas de elemento de propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
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

El analizador de XAML también permite abreviar la clase `StaticExtension` como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Esto se puede simplificar aún más, pero el cambio introduce una nueva sintaxis: consiste en colocar la clase de `StaticExtension` y la configuración de miembro entre llaves. La expresión resultante se establece directamente en el atributo `FontSize`:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe *que no hay comillas dentro* de las llaves. La propiedad `Member` de `StaticExtension` ya no es un atributo XML. En realidad es parte de la expresión para la extensión de marcado.

Del mismo modo que puede abreviar `x:StaticExtension` para `x:Static` cuando lo usa como un elemento de objeto, también puede abreviarlo en la expresión entre llaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La clase `StaticExtension` tiene un atributo `ContentProperty` que hace referencia a la propiedad `Member`, que marca esta propiedad como la propiedad de contenido predeterminada de la clase. En el caso de las extensiones de marcado XAML, que se expresan con llaves, puede eliminar el `Member=` parte de la expresión:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Esta es la forma más común de la extensión de marcado `x:Static`.

La página de **demostración estática** contiene otros dos ejemplos. La etiqueta raíz del archivo XAML contiene una declaración de espacio de nombres XML para el espacio de nombres de .NET `System`:

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Esto permite establecer el tamaño de fuente `Label` en el `Math.PI`de campo estático. Esto da como resultado un texto bastante pequeño, por lo que la propiedad `Scale` se establece en `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

En el último ejemplo se muestra el valor de `Device.RuntimePlatform`. La propiedad estática `Environment.NewLine` se usa para insertar un carácter de nueva línea entre los dos objetos `Span`:

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

La clase [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) admite el `x:Reference` extensión de marcado. La clase tiene una propiedad única denominada [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) de tipo `string` que se establece en el nombre de un elemento de la página al que se ha asignado un nombre con `x:Name`. Esta propiedad `Name` es la propiedad content de `ReferenceExtension`, por lo que `Name=` no es necesario cuando `x:Reference` aparece entre llaves.

La extensión de marcado de `x:Reference` se usa exclusivamente con enlaces de datos, que se describen con más detalle en el artículo [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

En la página de **demostración de x:Reference** se muestran dos usos de `x:Reference` con enlaces de datos, el primero en el que se usa para establecer la propiedad `Source` del objeto `Binding` y el segundo donde se usa para establecer la propiedad `BindingContext` para dos enlaces de datos:

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

Ambas expresiones `x:Reference` utilizan la versión abreviada del nombre de la clase `ReferenceExtension` y eliminan la parte `Name=` de la expresión. En el primer ejemplo, el `x:Reference` extensión de marcado se incrusta en la extensión de marcado `Binding`. Tenga en cuenta que los valores de `Source` y `StringFormat` se separan mediante comas. Esta es la ejecución del programa:

[![Demostración de x:Reference](consuming-images/referencedemo-small.png "Demostración de x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demostración de x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (extensión de marcado)

La extensión de marcado `x:Type` es el equivalente XAML de C# la palabra clave [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) . Es compatible con la clase [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) , que define una propiedad denominada [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) de tipo `string` que se establece en un nombre de clase o estructura. La extensión de marcado `x:Type` devuelve el objeto [`System.Type`](xref:System.Type) de esa clase o estructura. `TypeName` es la propiedad de contenido de `TypeExtension`, por lo que `TypeName=` no es necesario cuando `x:Type` aparece con llaves.

En Xamarin. Forms, hay varias propiedades que tienen argumentos de tipo `Type`. Entre los ejemplos se incluye la propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de `Style`y el atributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) que se usa para especificar argumentos en clases genéricas. Sin embargo, el analizador de XAML realiza la operación de `typeof` automáticamente y la extensión de marcado `x:Type` no se utiliza en estos casos.

Un lugar donde *se* requiere `x:Type` es con la extensión de marcado `x:Array`, que se describe en la [sección siguiente](#array).

La extensión de marcado `x:Type` también es útil al construir un menú en el que cada elemento de menú corresponde a un objeto de un tipo determinado. Puede asociar un objeto `Type` a cada elemento de menú y, a continuación, crear una instancia del objeto cuando se selecciona el elemento de menú.

Así es como funciona el menú de navegación en `MainPage` en el programa de **extensiones de marcado** . El archivo **mainpage. Xaml** contiene una `TableView` con cada `TextCell` correspondiente a una página determinada del programa:

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

Esta es la Página principal de apertura en **las extensiones de marcado**:

[![Página principal](consuming-images/mainpage-small.png "Página principal")](consuming-images/mainpage-large.png#lightbox "Página principal")

Cada propiedad `CommandParameter` está establecida en una extensión de marcado `x:Type` que hace referencia a una de las otras páginas. La propiedad `Command` se enlaza a una propiedad denominada `NavigateCommand`. Esta propiedad se define en el `MainPage` archivo de código subyacente:

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

La propiedad `NavigateCommand` es un objeto `Command` que implementa un comando Execute con un argumento de tipo `Type` &mdash; el valor de `CommandParameter`. El método utiliza `Activator.CreateInstance` para crear una instancia de la página y, a continuación, navega hasta ella. El constructor termina estableciendo el `BindingContext` de la página en sí mismo, lo que permite que el `Binding` de `Command` funcione. Vea el artículo sobre el [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md) y, en particular, el artículo sobre [**comandos**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) para obtener más información sobre este tipo de código.

En la página de **demostración de x:Type** se usa una técnica similar para crear instancias de los elementos de Xamarin. Forms y agregarlos a un `StackLayout`. Inicialmente, el archivo XAML consta de tres elementos `Button` con sus propiedades `Command` establecidas en un `Binding` y las propiedades `CommandParameter` establecidas en tipos de tres vistas de Xamarin. Forms:

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

El archivo de código subyacente define e inicializa la propiedad `CreateCommand`:

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

El método que se ejecuta cuando se presiona un `Button` crea una nueva instancia del argumento, establece su propiedad `VerticalOptions` y la agrega a la `StackLayout`. Los tres elementos `Button` compartirán la página con vistas creadas dinámicamente:

[![Demostración de x:Type](consuming-images/typedemo-small.png "Demostración de x:Type")](consuming-images/typedemo-large.png#lightbox "Demostración de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (extensión de marcado)

La extensión de marcado `x:Array` permite definir una matriz en el marcado. Es compatible con la clase [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) , que define dos propiedades:

- `Type` de tipo `Type`, que indica el tipo de los elementos de la matriz.
- `Items` de tipo `IList`, que es una colección de los propios elementos. Esta es la propiedad de contenido de `ArrayExtension`.

La extensión de marcado `x:Array` no aparece nunca entre llaves. En su lugar, `x:Array` etiquetas inicial y final delimitan la lista de elementos. Establezca la propiedad `Type` en una extensión de marcado `x:Type`.

La página de **demostración de x:Array** muestra cómo usar `x:Array` para agregar elementos a un `ListView` estableciendo la propiedad `ItemsSource` en una matriz:

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

El `ViewCell` crea un `BoxView` simple para cada entrada de color:

[![Demostración de x:Array](consuming-images/arraydemo-small.png "Demostración de x:Array")](consuming-images/arraydemo-large.png#lightbox "Demostración de x:Array")

Hay varias maneras de especificar los elementos de `Color` individuales en esta matriz. Puede usar una extensión de marcado `x:Static`:

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

Al definir matrices de tipos comunes, como cadenas o números, use las etiquetas enumeradas en el artículo [**pasar argumentos de constructor**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para delimitar los valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null (extensión de marcado)

La clase [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) admite el `x:Null` extensión de marcado. No tiene propiedades y es simplemente el equivalente XAML de la C# palabra clave [`null`](/dotnet/csharp/language-reference/keywords/null/) .

La extensión de marcado de `x:Null` rara vez es necesaria y rara vez se usa, pero si lo necesita, le alegrará de que exista.

La página de **demostración de x:null** muestra un escenario en el que `x:Null` puede resultar práctico. Supongamos que define una `Style` implícita para `Label` que incluye un `Setter` que establece la propiedad `FontFamily` en un nombre de familia dependiente de la plataforma:

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

A continuación, descubrirá que para uno de los elementos `Label`, deseará todos los valores de propiedad del `Style` implícito, excepto el `FontFamily`, que desea que sea el valor predeterminado. Podría definir otro `Style` para ese propósito, pero un enfoque más sencillo es simplemente establecer la propiedad `FontFamily` de la `Label` determinada en `x:Null`, tal como se muestra en el centro `Label`.

Esta es la ejecución del programa:

[![Demo de x:Null](consuming-images/nulldemo-small.png "Demo de x:Null")](consuming-images/nulldemo-large.png#lightbox "Demo de x:Null")

Observe que cuatro de los elementos `Label` tienen una fuente serif, pero el centro `Label` tiene la fuente sans-serif predeterminada.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensión de marcado en la plataforma

La extensión de marcado de `OnPlatform` permite personalizar la apariencia de la interfaz de usuario en cada plataforma. Proporciona la misma funcionalidad que las clases [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) y [`On`](xref:Xamarin.Forms.On) , pero con una representación más concisa.

La clase [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) admite el `OnPlatform` extensión de marcado, que define las siguientes propiedades:

- `Default` de tipo `object`, que se establece en un valor predeterminado que se va a aplicar a las propiedades que representan las plataformas.
- `Android` de tipo `object`, que se establece en un valor que se va a aplicar en Android.
- `GTK` de tipo `object`, que se establece en un valor que se va a aplicar en plataformas GTK.
- `iOS` de tipo `object`, que se establece en un valor que se va a aplicar en iOS.
- `macOS` de tipo `object`, que se establece en un valor que se va a aplicar en macOS.
- `Tizen` de tipo `object`, que se establece en un valor que se va a aplicar a la plataforma Tizen.
- `UWP` de tipo `object`, que se establece en un valor que se va a aplicar a la Plataforma universal de Windows.
- `WPF` de tipo `object`, que se establece en un valor que se va a aplicar a la plataforma de Windows Presentation Foundation.
- `Converter` de tipo `IValueConverter`, que se establece en una implementación de `IValueConverter`.
- `ConverterParameter` de tipo `object`, que se establece en un valor que se va a pasar a la implementación de `IValueConverter`.

> [!NOTE]
> El analizador de XAML permite abreviar la clase [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) como `OnPlatform`.

La propiedad `Default` es la propiedad content de `OnPlatformExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la `Default=` parte de la expresión siempre que sea el primer argumento. Si no se establece la propiedad `Default`, se usará de forma predeterminada el valor de la propiedad [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) , siempre que la extensión de marcado tenga como destino una [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).

> [!IMPORTANT]
> El analizador de XAML espera que se proporcionen valores del tipo correcto a las propiedades que consumen el `OnPlatform` extensión de marcado. Si es necesaria la conversión de tipos, la extensión de marcado `OnPlatform` intentará realizarla mediante los convertidores predeterminados proporcionados por Xamarin. Forms. Sin embargo, hay algunas conversiones de tipos que no pueden realizar los convertidores predeterminados y, en estos casos, la propiedad `Converter` debe establecerse en una implementación `IValueConverter`.

En la página de demostración de la **plataforma** , se muestra cómo usar la extensión de marcado `OnPlatform`:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres expresiones `OnPlatform` utilizan la versión abreviada del nombre de la clase `OnPlatformExtension`. Las tres `OnPlatform` extensiones de marcado establecen las propiedades [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) del [`BoxView`](xref:Xamarin.Forms.BoxView) en valores diferentes en iOS, Android y UWP. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las plataformas que no se especifican, a la vez que se eliminan los `Default=` parte de la expresión. Tenga en cuenta que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demostración en la plataforma](consuming-images/onplatformdemo-small.png "Demostración en la plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demostración en la plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensión de marcado en idioma

La extensión de marcado de `OnIdiom` permite personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación. Es compatible con la clase [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) , que define las siguientes propiedades:

- `Default` de tipo `object`, que se establece en un valor predeterminado que se va a aplicar a las propiedades que representan los expresiones de dispositivo.
- `Phone` de tipo `object`, que se establece en un valor que se va a aplicar en los teléfonos.
- `Tablet` de tipo `object`, que se establece en un valor que se va a aplicar a las tabletas.
- `Desktop` de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de escritorio.
- `TV` de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de TV.
- `Watch` de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de inspección.
- `Converter` de tipo `IValueConverter`, que se establece en una implementación de `IValueConverter`.
- `ConverterParameter` de tipo `object`, que se establece en un valor que se va a pasar a la implementación de `IValueConverter`.

> [!NOTE]
> El analizador de XAML permite abreviar la clase [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) como `OnIdiom`.

La propiedad `Default` es la propiedad content de `OnIdiomExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la `Default=` parte de la expresión siempre que sea el primer argumento.

> [!IMPORTANT]
> El analizador de XAML espera que se proporcionen valores del tipo correcto a las propiedades que consumen el `OnIdiom` extensión de marcado. Si es necesaria la conversión de tipos, la extensión de marcado `OnIdiom` intentará realizarla mediante los convertidores predeterminados proporcionados por Xamarin. Forms. Sin embargo, hay algunas conversiones de tipos que no pueden realizar los convertidores predeterminados y, en estos casos, la propiedad `Converter` debe establecerse en una implementación `IValueConverter`.

En la página de **demostración de idioma** se muestra cómo usar la extensión de marcado `OnIdiom`:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres expresiones `OnIdiom` utilizan la versión abreviada del nombre de la clase `OnIdiomExtension`. Las tres `OnIdiom` extensiones de marcado establecen las propiedades [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) del [`BoxView`](xref:Xamarin.Forms.BoxView) en valores diferentes en las expresiones de teléfono, tableta y escritorio. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las expresiones que no se especifican, a la vez que se eliminan los `Default=` parte de la expresión. Tenga en cuenta que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demo de idioma](consuming-images/onidiomdemo-small.png "Demo de idioma")](consuming-images/onidiomdemo-large.png#lightbox "Demo de idioma")

## <a name="datatemplate-markup-extension"></a>Extensión de marcado DataTemplate

La extensión de marcado `DataTemplate` permite convertir un tipo en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Es compatible con la clase `DataTemplateExtension`, que define una propiedad `TypeName`, de tipo `string`, que se establece en el nombre del tipo que se va a convertir en un `DataTemplate`. La propiedad `TypeName` es la propiedad content de `DataTemplateExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la parte `TypeName=` de la expresión.

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

La clase `FontImage` admite la extensión de marcado `FontImageExtension`, que define las siguientes propiedades:

- `FontFamily` de tipo `string`, la familia de fuentes a la que pertenece el icono de fuente.
- `Glyph` de tipo `string`, el valor del carácter Unicode del icono de fuente.
- `Color` de tipo [`Color`](xref:Xamarin.Forms.Color), el color que se va a utilizar al mostrar el icono de fuente.
- `Size` de tipo `double`, el tamaño, en unidades independientes del dispositivo, del icono de fuente representada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `FontImageExtension` como `FontImage`.

La propiedad `Glyph` es la propiedad content de `FontImageExtension`. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la `Glyph=` parte de la expresión siempre que sea el primer argumento.

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

Si ha encontrado una necesidad de una extensión de marcado XAML que no está disponible en Xamarin. Forms, puede [crear la suya propia](creating.md).

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de las extensiones de marcado XAML del libro de Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell de Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).
