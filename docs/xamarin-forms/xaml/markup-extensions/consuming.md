---
title: Consumo de extensiones de marcado XAML
description: En este artículo se explica cómo usar las extensiones de marcado XAML de Xamarin. Forms para mejorar la eficacia y flexibilidad de XAML, permitiendo que los atributos de elemento se establezcan desde diversos orígenes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/21/2020
ms.openlocfilehash: 7f46bc84543a1838241923ab91809bd01c706f44
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517110"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensiones de marcado XAML

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Las extensiones de marcado XAML ayudan a mejorar la eficacia y flexibilidad de XAML al permitir que se establezcan atributos de elemento desde diversos orígenes. Varias extensiones de marcado XAML forman parte de la especificación de XAML 2009. Aparecen en archivos XAML con el prefijo de `x` espacio de nombres personalizado y se suele hacer referencia a ellos con este prefijo. En este artículo se describen las siguientes extensiones de marcado:

- [`x:Static`](#static): hace referencia a propiedades estáticas, campos o miembros de enumeración.
- [`x:Reference`](#reference): hace referencia a los elementos con nombre de la página.
- [`x:Type`](#type): establezca un atributo en un `System.Type` objeto.
- [`x:Array`](#array): construye una matriz de objetos de un tipo determinado.
- [`x:Null`](#null): establezca un atributo en un `null` valor.
- [`OnPlatform`](#onplatform): personalización de la apariencia de la interfaz de usuario en cada plataforma.
- [`OnIdiom`](#onidiom): Personalice la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación.
- [`DataTemplate`](#datatemplate-markup-extension): convierte un tipo en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension): muestra un icono de fuente en cualquier vista que puede mostrar `ImageSource`un.
- [`OnAppTheme`](#onapptheme-markup-extension): consumir un recurso basado en el tema del sistema actual.

Históricamente, las extensiones de marcado XAML adicionales han sido compatibles con otras implementaciones de XAML y también se admiten en Xamarin. Forms. Estos se describen con más detalle en otros artículos:

- `StaticResource`: hace referencia a los objetos de un diccionario de recursos, como se describe en el artículo [**diccionarios de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`: responde a los cambios en los objetos de un diccionario de recursos, como se describe en el artículo [**estilos dinámicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`: establezca un vínculo entre las propiedades de dos objetos, como se describe en el artículo [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`: realiza el enlace de datos de una plantilla de control, como se describe en el artículo [**plantillas de control de Xamarin. Forms**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`: establece el origen de enlace con respecto a la posición del destino de enlace, como se describe en el artículo [enlaces relativos](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

El [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) diseño hace uso de la extensión [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)de marcado personalizada. Esta extensión de marcado se describe en el artículo [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static (extensión de marcado)

La `x:Static` extensión de marcado es compatible con [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) la clase. La clase tiene una propiedad única denominada [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de tipo `string` que se establece en el nombre de una constante pública, una propiedad estática, un campo estático o un miembro de enumeración.

Una forma habitual de usar `x:Static` es definir primero una clase con algunas constantes o variables estáticas, como esta pequeña `AppConstants` clase en el programa [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

En la página de **demostración de x:Static** se muestran varias `x:Static` maneras de usar la extensión de marcado. El enfoque más detallado crea instancias de la `StaticExtension` clase entre `Label.FontSize` las etiquetas de elemento de propiedad:

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

El analizador de XAML también permite `StaticExtension` que la clase se abrevie como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Esto se puede simplificar aún más, pero el cambio introduce una nueva sintaxis: consiste en colocar la `StaticExtension` clase y el valor de miembro entre llaves. La expresión resultante se establece directamente en el `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe *que no hay comillas dentro* de las llaves. La `Member` propiedad de `StaticExtension` ya no es un atributo XML. En su lugar, es parte de la expresión para la extensión de marcado.

Del mismo modo que se puede `x:StaticExtension` abreviar a `x:Static` cuando se usa como un elemento de objeto, también se puede abreviar en la expresión entre llaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La `StaticExtension` clase tiene un `ContentProperty` atributo que hace referencia `Member`a la propiedad, que marca esta propiedad como la propiedad de contenido predeterminada de la clase. En el caso de las extensiones de marcado XAML, que se expresan `Member=` con llaves, puede eliminar la parte de la expresión:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Esta es la forma más común de la `x:Static` extensión de marcado.

La página de **demostración estática** contiene otros dos ejemplos. La etiqueta raíz del archivo XAML contiene una declaración de espacio de nombres XML para `System` el espacio de nombres de .net:

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Esto permite establecer `Label` el tamaño de fuente en el campo `Math.PI`estático. Esto da como resultado un texto bastante pequeño, `Scale` por lo que la `Math.E`propiedad se establece en:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

En el último ejemplo se `Device.RuntimePlatform` muestra el valor. La `Environment.NewLine` propiedad estática se usa para insertar un carácter de nueva línea entre los dos `Span` objetos:

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

La `x:Reference` extensión de marcado es compatible con [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) la clase. La clase tiene una propiedad única denominada [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) de tipo `string` que se establece en el nombre de un elemento de la página al que se le ha asignado un `x:Name`nombre. Esta `Name` propiedad es la propiedad de contenido `ReferenceExtension`de, `Name=` por lo que no `x:Reference` es necesario cuando aparece entre llaves.

La `x:Reference` extensión de marcado se usa exclusivamente con los enlaces de datos, que se describen con más detalle en el artículo [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

En la página de **demostración de x:Reference** se `x:Reference` muestran dos usos de con enlaces de datos, el primero en el que `Source` se usa para `Binding` establecer la propiedad del objeto y el segundo donde se usa para `BindingContext` establecer la propiedad para dos enlaces de datos:

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

Ambas `x:Reference` expresiones utilizan la versión abreviada del nombre `ReferenceExtension` de la clase y eliminan la `Name=` parte de la expresión. En el primer ejemplo, la `x:Reference` extensión de marcado se incrusta en `Binding` la extensión de marcado. Tenga en cuenta `Source` que `StringFormat` los valores de y se separan mediante comas. Esta es la ejecución del programa:

[![Demostración de x:Reference](consuming-images/referencedemo-small.png "Demostración de x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demostración de x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (extensión de marcado)

La `x:Type` extensión de marcado es el equivalente de XAML de [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) la palabra clave de C#. Es compatible con la [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) clase, que define una propiedad denominada [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) de tipo `string` que se establece en un nombre de clase o estructura. La `x:Type` extensión de marcado devuelve [`System.Type`](xref:System.Type) el objeto de esa clase o estructura. `TypeName`es la propiedad de contenido `TypeExtension`de, `TypeName=` por lo que no `x:Type` es necesario cuando aparece con llaves.

En Xamarin. Forms, hay varias propiedades que tienen argumentos de tipo `Type`. Entre los ejemplos [`TargetType`](xref:Xamarin.Forms.Style.TargetType) se incluye `Style`la propiedad de y el atributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) que se usa para especificar argumentos en clases genéricas. Sin embargo, el analizador de XAML `typeof` realiza la operación automáticamente y `x:Type` la extensión de marcado no se utiliza en estos casos.

Un lugar donde `x:Type` *se* requiere es con la `x:Array` extensión de marcado, que se describe en la [sección siguiente](#array).

La `x:Type` extensión de marcado también es útil cuando se crea un menú en el que cada elemento de menú corresponde a un objeto de un tipo determinado. Puede asociar un `Type` objeto a cada elemento de menú y, a continuación, crear una instancia del objeto cuando se selecciona el elemento de menú.

Así es como funciona el menú de `MainPage` navegación en en el programa de **extensiones de marcado** . El archivo **mainpage. Xaml** contiene un `TableView` con cada `TextCell` correspondiente a una página determinada del programa:

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

Cada `CommandParameter` propiedad se establece en una `x:Type` extensión de marcado que hace referencia a una de las otras páginas. La `Command` propiedad está enlazada a una propiedad `NavigateCommand`denominada. Esta propiedad se define en el `MainPage` archivo de código subyacente:

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

La `NavigateCommand` propiedad es un `Command` objeto que implementa un comando Execute con un argumento de tipo `Type` &mdash; el valor de `CommandParameter`. El método utiliza `Activator.CreateInstance` para crear una instancia de la página y, a continuación, navega hasta ella. El constructor concluye estableciendo el `BindingContext` de la página en sí mismo, lo que permite `Binding` `Command` que funcione. Vea el artículo sobre el [**enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md) y, en particular, el artículo sobre [**comandos**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) para obtener más información sobre este tipo de código.

En la página de **demostración de x:Type** se usa una técnica similar para crear instancias de los elementos de Xamarin. `StackLayout`Forms y agregarlos a. Inicialmente `Binding` , el archivo XAML consta de `Button` tres elementos con `Command` sus propiedades establecidas en y las `CommandParameter` propiedades establecidas en tipos de tres vistas de Xamarin. Forms:

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

El método que se ejecuta cuando `Button` se presiona se crea una nueva instancia del argumento, establece su `VerticalOptions` propiedad y la `StackLayout`agrega a. Los tres `Button` elementos compartirán la página con vistas creadas dinámicamente:

[![Demostración de x:Type](consuming-images/typedemo-small.png "Demostración de x:Type")](consuming-images/typedemo-large.png#lightbox "Demostración de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (extensión de marcado)

La `x:Array` extensión de marcado le permite definir una matriz en el marcado. Es compatible con la [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) clase, que define dos propiedades:

- `Type`de tipo `Type`, que indica el tipo de los elementos de la matriz.
- `Items`de tipo `IList`, que es una colección de los propios elementos. Esta es la propiedad de contenido `ArrayExtension`de.

La `x:Array` extensión de marcado no aparece nunca entre llaves. En su lugar `x:Array` , las etiquetas de inicio y fin delimitan la lista de elementos. Establezca la `Type` propiedad en una `x:Type` extensión de marcado.

La página de **demostración de x:Array** muestra cómo `x:Array` usar para agregar elementos a `ListView` un estableciendo la `ItemsSource` propiedad en una matriz:

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

`ViewCell` Crea un sencillo `BoxView` para cada entrada de color:

[![Demostración de x:Array](consuming-images/arraydemo-small.png "Demostración de x:Array")](consuming-images/arraydemo-large.png#lightbox "Demostración de x:Array")

Hay varias maneras de especificar los elementos individuales `Color` de esta matriz. Puede usar una `x:Static` extensión de marcado:

```xaml
<x:Static Member="Color.Blue" />
```

O bien, puede usar `StaticResource` para recuperar un color de un diccionario de recursos:

```xaml
<StaticResource Key="myColor" />
```

Al final de este artículo, verá una extensión de marcado XAML personalizada que también crea un nuevo valor de color:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Al definir matrices de tipos comunes, como cadenas o números, use las etiquetas enumeradas en el artículo [**pasar argumentos de constructor**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para delimitar los valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null (extensión de marcado)

La `x:Null` extensión de marcado es compatible con [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) la clase. No tiene propiedades y es simplemente el equivalente XAML de la palabra clave [`null`](/dotnet/csharp/language-reference/keywords/null/) de C#.

La `x:Null` extensión de marcado rara vez es necesaria y raramente se usa, pero si lo necesita, le alegrará de que exista.

La página de **demostración de x:null** muestra un escenario `x:Null` en el que puede resultar práctico. Suponga que define `Style` un para `Label` que incluya un `Setter` que establezca la `FontFamily` propiedad en un nombre de familia dependiente de la plataforma:

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

A continuación, descubrirá que para `Label` uno de los elementos, deseará todos los valores de `Style` propiedad de la `FontFamily`implícita, salvo el valor de, que desee que sea el predeterminado. Podría definir otro `Style` para ese propósito, pero un enfoque más sencillo es simplemente establecer la `FontFamily` propiedad de en particular `Label` en `x:Null`, como se muestra en el centro `Label`.

Esta es la ejecución del programa:

[![Demo de x:Null](consuming-images/nulldemo-small.png "Demo de x:Null")](consuming-images/nulldemo-large.png#lightbox "Demo de x:Null")

Observe que cuatro de los `Label` elementos tienen una fuente serif, pero el centro `Label` tiene la fuente sans-serif predeterminada.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensión de marcado en la plataforma

La extensión de marcado `OnPlatform` le permite personalizar la apariencia de la interfaz de usuario para cada plataforma. Proporciona la misma funcionalidad que las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) clases y [`On`](xref:Xamarin.Forms.On) , pero con una representación más concisa.

La `OnPlatform` [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) clase admite la extensión de marcado, que define las siguientes propiedades:

- `Default`de tipo `object`, que se establece en un valor predeterminado que se va a aplicar a las propiedades que representan las plataformas.
- `Android`de tipo `object`, que se establece en un valor que se va a aplicar en Android.
- `GTK`de tipo `object`, que se establece en un valor que se va a aplicar en plataformas GTK.
- `iOS`de tipo `object`, que se establece en un valor que se va a aplicar en iOS.
- `macOS`de tipo `object`, que se establece en un valor que se va a aplicar en MacOS.
- `Tizen`de tipo `object`, que se establece en un valor que se va a aplicar a la plataforma Tizen.
- `UWP`de tipo `object`, que se establece en un valor que se va a aplicar en el plataforma universal de Windows.
- `WPF`de tipo `object`, que se establece en un valor que se va a aplicar en la plataforma Windows Presentation Foundation.
- `Converter`de tipo `IValueConverter`, que se puede establecer en una `IValueConverter` implementación de.
- `ConverterParameter`de tipo `object`, que se puede establecer en un valor que se va a `IValueConverter` pasar a la implementación.

> [!NOTE]
> El analizador XAML permite abreviar la [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) clase como. `OnPlatform`

La `Default` propiedad es la propiedad de contenido `OnPlatformExtension`de. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede `Default=` eliminar la parte de la expresión siempre que sea el primer argumento. Si no `Default` se establece la propiedad, se usará de forma [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) predeterminada el valor de la propiedad, siempre que la extensión de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)marcado esté destinada a un.

> [!IMPORTANT]
> El analizador de XAML espera que se proporcionen valores del tipo correcto a las propiedades que consumen la extensión de `OnPlatform` marcado. Si es necesaria la conversión de tipos `OnPlatform` , la extensión de marcado intentará realizarla utilizando los convertidores predeterminados proporcionados por Xamarin. Forms. Sin embargo, hay algunas conversiones de tipos que no pueden realizar los convertidores predeterminados y, en estos `Converter` casos, la propiedad debe establecerse en una `IValueConverter` implementación de.

En la página de demostración de la **plataforma** , se `OnPlatform` muestra cómo usar la extensión de marcado:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres `OnPlatform` expresiones utilizan la versión abreviada del nombre `OnPlatformExtension` de clase. Las tres `OnPlatform` extensiones de marcado establecen [`Color`](xref:Xamarin.Forms.BoxView.Color)las [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)propiedades, [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y de [`BoxView`](xref:Xamarin.Forms.BoxView) en valores diferentes en iOS, Android y UWP. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las plataformas que no se especifican, a la vez que se elimina la `Default=` parte de la expresión. Observe que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demostración en la plataforma](consuming-images/onplatformdemo-small.png "Demostración en la plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demostración en la plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensión de marcado en idioma

La `OnIdiom` extensión de marcado le permite personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación. Es compatible con la [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) clase, que define las siguientes propiedades:

- `Default`de tipo `object`, que se establece en un valor predeterminado que se va a aplicar a las propiedades que representan los expresiones de dispositivo.
- `Phone`de tipo `object`, que se establece en un valor que se va a aplicar en los teléfonos.
- `Tablet`de tipo `object`, que se establece en un valor que se va a aplicar en tabletas.
- `Desktop`de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de escritorio.
- `TV`de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de TV.
- `Watch`de tipo `object`, que se establece en un valor que se va a aplicar en las plataformas de inspección.
- `Converter`de tipo `IValueConverter`, que se puede establecer en una `IValueConverter` implementación de.
- `ConverterParameter`de tipo `object`, que se puede establecer en un valor que se va a `IValueConverter` pasar a la implementación.

> [!NOTE]
> El analizador XAML permite abreviar la [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) clase como. `OnIdiom`

La `Default` propiedad es la propiedad de contenido `OnIdiomExtension`de. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede `Default=` eliminar la parte de la expresión siempre que sea el primer argumento.

> [!IMPORTANT]
> El analizador de XAML espera que se proporcionen valores del tipo correcto a las propiedades que consumen la extensión de `OnIdiom` marcado. Si es necesaria la conversión de tipos `OnIdiom` , la extensión de marcado intentará realizarla utilizando los convertidores predeterminados proporcionados por Xamarin. Forms. Sin embargo, hay algunas conversiones de tipos que no pueden realizar los convertidores predeterminados y, en estos `Converter` casos, la propiedad debe establecerse en una `IValueConverter` implementación de.

En la página de **demostración de idioma** se muestra cómo `OnIdiom` usar la extensión de marcado:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

En este ejemplo, las tres `OnIdiom` expresiones utilizan la versión abreviada del nombre `OnIdiomExtension` de clase. Las tres `OnIdiom` extensiones de marcado establecen [`Color`](xref:Xamarin.Forms.BoxView.Color)las [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)propiedades, [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y de [`BoxView`](xref:Xamarin.Forms.BoxView) en diferentes valores en las expresiones de teléfono, tableta y escritorio. Las extensiones de marcado también proporcionan valores predeterminados para estas propiedades en las expresiones que no se especifican `Default=` , a la vez que se elimina la parte de la expresión. Observe que las propiedades de extensión de marcado que se establecen están separadas por comas.

Esta es la ejecución del programa:

[![Demo de idioma](consuming-images/onidiomdemo-small.png "Demo de idioma")](consuming-images/onidiomdemo-large.png#lightbox "Demo de idioma")

## <a name="datatemplate-markup-extension"></a>Extensión de marcado DataTemplate

La `DataTemplate` extensión de marcado le permite convertir un tipo en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Es compatible `DataTemplateExtension` con la clase, que define una `TypeName` propiedad, de tipo `string`, que se establece en el nombre del tipo que se va a convertir en. `DataTemplate` La `TypeName` propiedad es la propiedad de contenido `DataTemplateExtension`de. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede eliminar la parte `TypeName=` de la expresión.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `DataTemplateExtension` como `DataTemplate`.

Un uso típico de esta extensión de marcado está en una aplicación de Shell, tal y como se muestra en el ejemplo siguiente:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

En este ejemplo, `MonkeysPage` se convierte de [`ContentPage`](xref:Xamarin.Forms.ContentPage) a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la `ShellContent.ContentTemplate` propiedad. Esto garantiza que `MonkeysPage` solo se crea cuando se produce la navegación a la página, en lugar de al inicio de la aplicación.

Para obtener más información sobre las aplicaciones de Shell, consulte [Shell de Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extensión de marcado FontImage

La `FontImage` extensión de marcado le permite mostrar un icono de fuente en cualquier vista que pueda mostrar `ImageSource`un. Proporciona la misma funcionalidad que la `FontImageSource` clase, pero con una representación más concisa.

La clase `FontImageExtension` admite la extensión de marcado `FontImage`, que define las siguientes propiedades:

- `FontFamily`de tipo `string`, la familia de fuentes a la que pertenece el icono de fuente.
- `Glyph`de tipo `string`, el valor de carácter Unicode del icono de fuente.
- `Color`de tipo [`Color`](xref:Xamarin.Forms.Color), el color que se va a utilizar al mostrar el icono de fuente.
- `Size`del tipo `double`, el tamaño, en unidades independientes del dispositivo, del icono de fuente representada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `FontImageExtension` como `FontImage`.

La `Glyph` propiedad es la propiedad de contenido `FontImageExtension`de. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede `Glyph=` eliminar la parte de la expresión siempre que sea el primer argumento.

La página de **demostración de FontImage** muestra cómo usar `FontImage` la extensión de marcado:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

En este ejemplo, la versión abreviada del nombre `FontImageExtension` de clase se usa para mostrar un icono Xbox, de la familia de fuentes Ionicons, en [`Image`](xref:Xamarin.Forms.Image)un. La expresión también utiliza la `OnPlatform` extensión de marcado para especificar `FontFamily` diferentes valores de propiedad en iOS y Android. Además, se elimina `Glyph=` la parte de la expresión y las propiedades de extensión de marcado que se establecen se separan mediante comas. Tenga en cuenta que, mientras que el carácter Unicode `\uf30c`para el icono es, tiene que incluir un carácter de `&#xf30c;`escape en XAML, por lo que se convierte en.

Esta es la ejecución del programa:

[![Captura de pantalla de la extensión de marcado FontImage](consuming-images/fontimagedemo.png "Demostración de FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demostración de FontImage")

Para obtener información sobre cómo mostrar los iconos de fuente especificando los datos del `FontImageSource` icono de fuente de un objeto, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="onapptheme-markup-extension"></a>Extensión de marcado OnAppTheme

La `OnAppTheme` extensión de marcado le permite especificar el recurso que se va a consumir, como una imagen o un color, basándose en el tema del sistema actual. Proporciona la misma funcionalidad que la `OnAppTheme<T>` clase, pero con una representación más concisa.

> [!IMPORTANT]
> La `OnAppTheme` extensión de marcado tiene requisitos mínimos de sistema operativo. Para obtener más información, vea [responder a los cambios de tema del sistema en las aplicaciones de Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md).

La clase `OnAppThemeExtension` admite la extensión de marcado `OnAppTheme`, que define las siguientes propiedades:

- `Default`, de tipo `object`, que se establece en el recurso que se va a usar de forma predeterminada.
- `Light`, de tipo `object`, que se establece en el recurso que se va a usar cuando el dispositivo esté usando su tema claro.
- `Dark`, de tipo `object`, que se establece en el recurso que se va a usar cuando el dispositivo esté usando su tema oscuro.
- `Value`, de tipo `object`, que devuelve el recurso que está utilizando actualmente la extensión de marcado.
- `Converter`de tipo `IValueConverter`, que se puede establecer en una `IValueConverter` implementación de.
- `ConverterParameter`de tipo `object`, que se puede establecer en un valor que se va a `IValueConverter` pasar a la implementación.

> [!NOTE]
> El analizador de XAML permite abreviar la clase `OnAppThemeExtension` como `OnAppTheme`.

La `Default` propiedad es la propiedad de contenido `OnAppThemeExtension`de. Por lo tanto, para las expresiones de marcado XAML expresadas con llaves, puede `Default=` eliminar la parte de la expresión siempre que sea el primer argumento.

La página de **demostración de OnAppTheme** muestra cómo usar `OnAppTheme` la extensión de marcado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.OnAppThemeDemoPage"
             Title="OnAppTheme Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{OnAppTheme Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{DynamicResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el color del texto de la [`Label`](xref:Xamarin.Forms.Label) primera se establece en verde cuando el dispositivo usa su tema claro y se establece en rojo cuando el dispositivo está usando su tema oscuro. La segunda `Label` tiene su [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propiedad establecida a través [`Style`](xref:Xamarin.Forms.Style)de. De `Style` forma predeterminada, se establece el `Label` color del texto de en negro, en azul cuando el dispositivo usa el tema claro y en verde azulado cuando el dispositivo usa su tema oscuro.

> [!NOTE]
> [`Style`](xref:Xamarin.Forms.Style) Que utiliza la `OnAppTheme` extensión de marcado debe aplicarse a un control con la `DynamicResource` extensión de marcado, de modo que la interfaz de usuario de la aplicación se actualice cuando cambie el tema del sistema.

Esta es la ejecución del programa:

![Demostración de OnAppTheme](consuming-images/onappthemedemo.png "Demostración de OnAppTheme")

## <a name="define-markup-extensions"></a>Definir extensiones de marcado

Si ha encontrado una necesidad de una extensión de marcado XAML que no está disponible en Xamarin. Forms, puede [crear la suya propia](creating.md).

## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de las extensiones de marcado XAML del libro de Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell de Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Responder a los cambios de tema del sistema en las aplicaciones de Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
