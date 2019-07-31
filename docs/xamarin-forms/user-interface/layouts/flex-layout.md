---
title: El Xamarin.Forms FlexLayout
description: Utilice FlexLayout para apilar o encapsula una colección de vistas secundarias.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648802"
---
# <a name="the-xamarinforms-flexlayout"></a>El Xamarin.Forms FlexLayout

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Utilice FlexLayout para apilar o encapsula una colección de vistas secundarias._

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) es nuevo en la versión 3.0 de Xamarin.Forms. Se basa en la hoja CSS [Flexible diseño módulo](http://www.w3.org/TR/css-flexbox-1/), normalmente conocido como _flex diseño_ o _flex cuadro_, se denomina así porque incluye muchas opciones flexibles para organizar los elementos secundarios en el diseño.

`FlexLayout` es similar a Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) en que pueden organizar sus elementos secundarios horizontal y verticalmente en una pila. Sin embargo, el `FlexLayout` también es capaz de ajuste sus elementos secundarios, si hay demasiados como para caber en una sola fila o columna, y también tiene muchas opciones para adaptarse a diversos tamaños de pantalla, la alineación y la orientación.

`FlexLayout` se deriva de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) y hereda un [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) propiedad de tipo `IList<View>`.

`FlexLayout` define seis propiedades enlazables públicas y cinco propiedades enlazables adjuntas que afectan al tamaño, orientación y alineación de sus elementos secundarios. (Si no está familiarizado con las propiedades enlazables adjuntas, consulte el artículo  **[propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)** .) Estas propiedades se describen en detalle en las secciones siguientes en **[las propiedades enlazables detalladamente](#bindable-properties)** y  **[las propiedades enlazables asociadas con detalle](#attached-properties)** . Sin embargo, en este artículo comienza con una sección en algunas **[escenarios de uso comunes](#common-scenarios)** de `FlexLayout` que muchas de estas propiedades describe de manera más informal. Hacia el final del artículo, verá cómo combinar `FlexLayout` con [hojas de estilos CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Escenarios de uso comunes

El **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** programa de ejemplo contiene varias páginas que se muestran algunos usos habituales de `FlexLayout` y le permite experimentar con sus propiedades.

### <a name="using-flexlayout-for-a-simple-stack"></a>Uso de FlexLayout para una simple pila

El **Simple pila** página se muestra cómo `FlexLayout` puede sustituir un `StackLayout` pero con marcado más sencillo. Todo el contenido de este ejemplo se define en la página XAML. El `FlexLayout` contiene cuatro elementos secundarios:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

Aquí es esa página se ejecuta en la plataforma Universal de Windows, iOS y Android:

[![Página de la pila de la Simple](flex-layout-images/SimpleStack.png "la sencilla página de la pila")](flex-layout-images/SimpleStack-Large.png#lightbox)

Tres propiedades de `FlexLayout` se muestran en el **SimpleStackPage.xaml** archivo:

- El [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propiedad está establecida en un valor de la [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) enumeración. De manera predeterminada, es `Row`. Establecer la propiedad en `Column` hace que los elementos secundarios de la `FlexLayout` se organicen en una sola columna de elementos.

    Cuando los elementos de un `FlexLayout` se organizan en una columna, el `FlexLayout` se dice que tiene una vertical _eje principal_ y horizontal _entre el eje_.

- El [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propiedad es de tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) y especifica cómo se alinean los elementos en el eje cruzado. El `Center` opción hace que cada elemento se centra horizontalmente.

    Si usaba una `StackLayout` en lugar de un `FlexLayout` para esta tarea, ¿centrar todos los elementos mediante la asignación de la `HorizontalOptions` propiedad de cada elemento para `Center`. El `HorizontalOptions` propiedad no funciona para los elementos secundarios de un `FlexLayout`, pero la única `AlignItems` propiedad cumple con el mismo objetivo. Si necesita, puede usar el `AlignSelf` adjunta la propiedad enlazable para invalidar el `AlignItems` propiedad para elementos individuales:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Con este cambio, éste `Label` se coloca en el borde izquierdo de la `FlexLayout` cuando el orden de lectura es de izquierda a derecha.

- El [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propiedad es de tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)y especifica cómo se organizan los elementos en el eje principal. El `SpaceEvenly` opción asigna todo espacio vertical sobrante por igual entre todos los elementos y por encima del primer elemento y por debajo del último elemento.

    Si usaba una `StackLayout`, tiene que asignar el `VerticalOptions` propiedad de cada elemento para `CenterAndExpand` para lograr un efecto similar. Pero la `CenterAndExpand` opción asignaría el doble de espacio entre cada elemento que no sea anterior al primer elemento y después del último elemento. Puede imitar la `CenterAndExpand` opción de `VerticalOptions` estableciendo el `JustifyContent` propiedad de `FlexLayout` a `SpaceAround`.

Estos `FlexLayout` propiedades se describen con más detalle en la sección **[las propiedades enlazables detalladamente](#bindable-properties)** a continuación.

### <a name="using-flexlayout-for-wrapping-items"></a>Uso de FlexLayout de ajuste de los elementos

El **foto ajuste** página de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** muestra cómo `FlexLayout` puede ajustar sus elementos secundarios en filas o columnas adicionales. El archivo XAML crea instancias el `FlexLayout` y asigna dos propiedades de la misma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

El `Direction` propiedad de este `FlexLayout` no se establece, por lo que tiene el valor predeterminado de `Row`, lo que significa que los elementos secundarios se organizan en filas y el eje principal es horizontal.

El [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) propiedad es de un tipo de enumeración [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Si hay demasiados elementos para que quepan en una fila, valor de esta propiedad hace que los elementos que se va a ajustar a la siguiente fila.

Tenga en cuenta que el `FlexLayout` es un elemento secundario de un `ScrollView`. Si hay demasiadas filas para que quepa en la página, el `ScrollView` tiene un valor predeterminado `Orientation` propiedad de `Vertical` y permite el desplazamiento vertical.

El `JustifyContent` propiedad asigna espacio restante en el eje principal (el eje horizontal) para que cada elemento está rodeado por la misma cantidad de espacio en blanco.

El archivo de código subyacente tiene acceso a una colección de fotos de ejemplo y los agrega a la `Children` colección de los `FlexLayout`:

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

Este es el programa de funcionamiento, progresivamente desplazado de arriba a abajo:

[![La página de ajuste de foto](flex-layout-images/PhotoWrapping.png "la página de ajuste de imagen")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Diseño de página con FlexLayout

Hay un diseño estándar en el diseño web que llama el [ _Santo Grial_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) porque es un formato de diseño que es muy conveniente, pero a menudo es difícil darse cuenta con la perfección. La presentación consta de un encabezado en la parte superior de la página y un pie de página en la parte inferior, ambas extender a todo el ancho de la página. Ocupa el centro de la página es el contenido principal, pero a menudo con un menú en columnas a la izquierda de la información complementaria y de contenido (a veces denominado un _reservar_ área) situado a la derecha. [Sección 5.4.1 de la especificación de diseño Flexible de cuadros de CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) describe cómo puede llevarse la piedra angular diseño con un cuadro flex.

El **Santo Grial diseño** página de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** muestra una implementación sencilla de este diseño mediante uno `FlexLayout` anidado en otro. Dado que esta página está diseñada para un teléfono en modo vertical, las áreas a la izquierda y derecha del área de contenido son solo 50 píxeles de ancho:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

Aquí se está ejecutando:

[![La página de diseño Santo Grial](flex-layout-images/HolyGrailLayout.png "la página de diseño Santo Grial")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Las áreas de navegación y aside se representan con un `BoxView` a la izquierda y derecha.

La primera `FlexLayout` en el XAML archivo tiene un principal eje vertical y contiene tres elementos secundarios organizados en una columna. Estos son el encabezado, el cuerpo de la página y el pie de página. Anidado `FlexLayout` tiene un eje horizontal principal con tres elementos secundarios organizados en una fila.

En este programa, se muestran tres propiedades enlazables adjuntadas:

- El `Order` propiedad enlazable asociada se establece en la primera `BoxView`. Esta propiedad es un entero con un valor predeterminado de 0. Puede utilizar esta propiedad para cambiar el orden de diseño. Por lo general a los desarrolladores prefiere el contenido de la página que aparezca en el marcado antes de los elementos de navegación y elementos de un lado. Establecer el `Order` propiedad en la primera `BoxView` en un valor menor que sus hermanas otros hace que aparezca como el primer elemento de la fila. De forma similar, puede asegurarse de que un elemento aparece en último lugar estableciendo el `Order` propiedad en un valor mayor que sus elementos relacionados.

- El `Basis` propiedad enlazable adjunta se establece en los dos `BoxView` elementos para darles un ancho de 50 píxeles. Esta propiedad es de tipo `FlexBasis`, una estructura que define una propiedad estática de tipo `FlexBasis` denominado `Auto`, que es el valor predeterminado. Puede usar `Basis` para especificar un tamaño de píxel o un porcentaje que indica cuánto espacio se ocupa el elemento en el eje principal. Se llama una _base_ porque especifica un tamaño de elemento que es la base del diseño posterior.

- El `Grow` propiedad está establecida en anidado `Layout` y en el `Label` secundarios que representan el contenido. Esta propiedad es de tipo `float` y tiene un valor predeterminado de 0. Cuando se establece en un valor positivo, todo el espacio restante en el eje principal se asigna a ese elemento y a los elementos relacionados con los valores positivos del `Grow`. El espacio se asigna proporcionalmente a los valores, algo como la especificación de estrella en un `Grid`.

    La primera `Grow` se establece la propiedad adjunta en anidado `FlexLayout`, lo que indica que este `FlexLayout` es ocupar todo el espacio vertical no utilizado en el exterior `FlexLayout`. El segundo `Grow` se establece la propiedad adjunta en el `Label` que representa el contenido, que indica que este contenido es ocupar todo el espacio horizontal no utilizado en el interior `FlexLayout`.

    También hay un proceso similar `Shrink` adjunta la propiedad enlazable que puede usar cuando el tamaño de los elementos secundarios supera el tamaño de la `FlexLayout` pero no se desea el ajuste.

### <a name="catalog-items-with-flexlayout"></a>Elementos del catálogo con FlexLayout

El **elementos del catálogo** página en el **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** es similar al ejemplo [Example 1 en la sección 1.1 de la especificación del cuadro de diseño CSS Flex](http://www.w3.org/TR/css-flexbox-1/#overview)excepto en que muestran una serie de imágenes puede desplazar horizontalmente y las descripciones de los tres objetos:

[![Página de elementos de catálogo](flex-layout-images/CatalogItems.png "el catálogo de elementos de página")](flex-layout-images/CatalogItems-Large.png#lightbox)

Cada uno de los tres monos es un `FlexLayout` contenidos en un `Frame` que tiene un ancho y el alto explícita, y que también es un elemento secundario de un mayor `FlexLayout`. En este archivo XAML, la mayoría de las propiedades de la `FlexLayout` elementos secundarios se especifican en los estilos, todos menos uno de los cuales es un estilo implícito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

El estilo implícito para la `Image` incluye la configuración de dos propiedades enlazables asociadas de `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

El `Order` de &ndash;causas 1 la `Image` elemento que se mostrarán en primer lugar en cada uno de los anidados `FlexLayout` vistas independientemente de su posición dentro de la colección de elementos secundarios. El `AlignSelf` propiedad de `Center` hace que el `Image` se centre en la `FlexLayout`. Esto reemplaza la configuración de la `AlignItems` propiedad, que tiene un valor predeterminado de `Stretch`, lo que significa que el `Label` y `Button` elementos secundarios se ajusta a todo el ancho de la `FlexLayout`.

Dentro de cada uno de los tres `FlexLayout` vistas, un espacio en blanco `Label` precede a la `Button`, pero tiene un `Grow` de 1. Esto significa que todo el espacio vertical sobrante está asignado a este en blanco `Label`, que inserta de forma eficaz el `Button` hasta la parte inferior.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Las propiedades enlazables en detalle

Ahora que ha visto algunas aplicaciones comunes de `FlexLayout`, las propiedades de `FlexLayout` se puede explorar con más detalle.
`FlexLayout` define las propiedades enlazables seis que establezca en el `FlexLayout` Sí, ya sea en el código o XAML, la alineación y la orientación del control. (Una de estas propiedades, [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), no se trata en este artículo.)

Puede experimentar con las restantes propiedades enlazables con cinco el **experimentar** página de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** ejemplo. Esta página le permite agregar o quitar elementos secundarios de un `FlexLayout` y establecer las combinaciones de las cinco propiedades enlazables. Todos los elementos secundarios de la `FlexLayout` son `Label` vistas de distintos colores y tamaños, con el `Text` propiedad establecida en un número que corresponde a su posición en la `Children` colección.

Cuando el programa se inicia, cinco `Picker` vistas muestran los valores predeterminados de estos cinco `FlexLayout` propiedades. El `FlexLayout` hacia la parte inferior de la pantalla contiene tres elementos secundarios:

[![La página del experimento: Página]predeterminada(flex-layout-images/ExperimentDefault.png "del experimento: predeterminada")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Cada uno de los `Label` vistas tiene un fondo gris que muestra el espacio asignado a la que `Label` dentro de la `FlexLayout`. El fondo de la `FlexLayout` es blanco azulado. Ocupa el área de la parte inferior de la página, excepto un pequeño margen a la izquierda y derecha.

<a name="direction" />

### <a name="the-direction-property"></a>La propiedad Direction

El [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propiedad es de tipo [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), una enumeración con cuatro miembros:

- `Column`
- `ColumnReverse` (o "columna-reverse" en XAML)
- `Row`, el valor predeterminado
- `RowReverse` (o "fila-reverse" en XAML)

En XAML, puede especificar el valor de esta propiedad mediante los nombres de miembro de enumeración en minúsculas, mayúsculas, o mayúsculas y minúsculas mezcladas, o bien puede usar dos cadenas adicionales que se muestra entre paréntesis que son los mismos que los indicadores CSS. (Las cadenas "columna-reverse" y "fila-reverse" se definen en el [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) clase usada por el analizador XAML.)

Este es el **experimento** página que muestra (de izquierda a derecha), el `Row` dirección, `Column` dirección, y `ColumnReverse` dirección:

[![La página del experimento: Dirección]de(flex-layout-images/ExperimentDirection.png "la página del experimento: dirección")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Tenga en cuenta que para el `Reverse` opciones, los elementos a partir de la derecha o inferior.

<a name="wrap" />

### <a name="the-wrap-property"></a>La propiedad Wrap

El [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) propiedad es de tipo [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), una enumeración con tres miembros:

- `NoWrap`, el valor predeterminado
- `Wrap`
- `Reverse` (o "wrap-reverse" en XAML)

De izquierda a derecha, se muestran estas pantallas el `NoWrap`, `Wrap` y `Reverse` opciones para elementos secundarios de 12:

[![La página del experimento: Ajustar](flex-layout-images/ExperimentWrap.png "la página del experimento")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Cuando el `Wrap` propiedad está establecida en `NoWrap` está restringido el eje principal (como se muestra en este programa) y el eje principal no es amplia o lo suficientemente alto como para ajustarse a todos los elementos secundarios, el `FlexLayout` intenta realizar los elementos más pequeños, como la captura de pantalla de iOS se muestra. Puede controlar la shrinkness de los elementos de la [ `Shrink` ](#shrink) propiedad enlazable adjunta.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>La propiedad JustifyContent

El [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propiedad es de tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), una enumeración con seis miembros:

- `Start` (o "flex-start" en XAML), el valor predeterminado
- `Center`
- `End` (o "flex-end" de XAML)
- `SpaceBetween` (o "espacio entre" en XAML)
- `SpaceAround` (o "espacio alrededor" en XAML)
- `SpaceEvenly`

Esta propiedad especifica cómo se espacian los elementos en el eje principal, que es el eje horizontal en este ejemplo:

[![La página del experimento: Justificar el]contenido(flex-layout-images/ExperimentJustifyContent.png "de la página del experimento: justificación del contenido")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

En todas las capturas de pantalla de tres, el `Wrap` propiedad está establecida en `Wrap`. El `Start` predeterminado se muestra en la captura de pantalla de Android anterior. La captura de pantalla de iOS aquí se muestra el `Center` opción: todos los elementos se mueven al centro. Otras de las tres opciones a partir de la palabra `Space` asignar el espacio adicional que no esté ocupado por los elementos. `SpaceBetween` asigna el espacio por igual entre los elementos; `SpaceAround` coloca igual espacio alrededor de cada elemento, mientras que `SpaceEvenly` coloca igual espacio entre cada elemento y antes del primer elemento y después del último elemento en la fila.

<a name="align-items" />

### <a name="the-alignitems-property"></a>La propiedad AlignItems

El [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propiedad es de tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), una enumeración con cuatro miembros:

- `Stretch`, el valor predeterminado
- `Center`
- `Start` (o "flex-start" en XAML)
- `End` (o "flex-end" de XAML)

Esta es una de las dos propiedades (el otro es [ `AlignContent` ](#align-content)) que indica cómo se alinean los elementos secundarios en el eje cruzado. Dentro de cada fila, los elementos secundarios se estira (como se muestra en la captura de pantalla anterior) o alineados en el inicio, el centro o el final de cada elemento, como se muestra en las siguientes capturas de pantalla de tres:

[![La página del experimento: Alinear]elementos(flex-layout-images/ExperimentAlignItems.png "en la página del experimento: alinear elementos")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

En la captura de pantalla de iOS, se alinean las partes superiores de todos los elementos secundarios. En las capturas de pantalla de Android, los elementos se centran verticalmente según el elemento secundario más alto. En la captura de pantalla UWP, la parte inferior de todos los elementos está alineada.

Para cualquier elemento individual, la `AlignItems` valor puede reemplazarse con el [ `AlignSelf` ](#align-self) propiedad enlazable adjunta.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>La propiedad AlignContent

El [ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) propiedad es de tipo [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), una enumeración con siete miembros:

- `Stretch`, el valor predeterminado
- `Center`
- `Start` (o "flex-start" en XAML)
- `End` (o "flex-end" de XAML)
- `SpaceBetween` (o "espacio entre" en XAML)
- `SpaceAround` (o "espacio alrededor" en XAML)
- `SpaceEvenly`

Al igual que `AlignItems`, el `AlignContent` propiedad también se alinea los elementos secundarios en el eje cruzado, pero afecta a todas las filas o columnas:

[![La página del experimento: Alinear]contenido(flex-layout-images/ExperimentAlignContent.png "en la página del experimento: alinear contenido")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

En la captura de pantalla de iOS, ambas filas están en la parte superior; en la captura de pantalla de Android que están en el centro; y en la captura de pantalla UWP que están en la parte inferior. También se pueden espaciadas las filas de varias maneras:

[![La página del experimento:  Alinear contenido]2(flex-layout-images/ExperimentAlignContent2.png "en la página del experimento: alinear contenido 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

El `AlignContent` no tiene ningún efecto cuando hay solo una fila o columna.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Las propiedades enlazables asociadas en detalle

`FlexLayout` define las cinco propiedades enlazables adjuntas. Estas propiedades se establecen en los elementos secundarios de la `FlexLayout` y pertenecen solo a ese elemento secundario determinado.

<a name="align-self" />

### <a name="the-alignself-property"></a>La propiedad AlignSelf

El [ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propiedad enlazable adjunta es de tipo [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), una enumeración con cinco miembros:

- `Auto`, el valor predeterminado
- `Stretch`
- `Center`
- `Start` (o "flex-start" en XAML)
- `End` (o "flex-end" de XAML)

Para cualquier elemento secundario individual de la `FlexLayout`, esta configuración de invalidaciones de propiedad el [ `AlignItems` ](#align-items) propiedad establecida en el `FlexLayout` propio. El valor predeterminado de `Auto` significa que se usarán el `AlignItems` configuración.

Para un `Label` elemento denominado `label` (o ejemplo), puede establecer el `AlignSelf` propiedad en el código similar al siguiente:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Tenga en cuenta que no hay ninguna referencia a la `FlexLayout` primario de la `Label`. En XAML, establezca la propiedad similar al siguiente:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La propiedad Order

El [ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) propiedad es de tipo `int`. El valor predeterminado es 0.

El `Order` propiedad le permite cambiar el orden que los elementos secundarios de la `FlexLayout` se organizan. Normalmente, los elementos secundarios de un `FlexLayout` se organizan es el mismo orden que aparecen en la `Children` colección. Este orden se puede invalidar estableciendo la `Order` propiedad enlazable se adjunta a un valor entero distinto de cero en uno o más elementos secundarios. El `FlexLayout` , a continuación, organiza sus elementos secundarios en función del valor de la `Order` propiedad en cada elemento secundario, pero los elementos secundarios con el mismo `Order` configuración se organizan en el orden en que aparecen en la `Children` colección.

### <a name="the-basis-property"></a>La propiedad de base

El [ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) propiedad enlazable adjunta indica la cantidad de espacio que se asigna a un elemento secundario de la `FlexLayout` en el eje principal. El tamaño especificado por el `Basis` propiedad es el tamaño a lo largo del eje principal del elemento primario `FlexLayout`. Por lo tanto, `Basis` indica el ancho de un elemento secundario cuando los elementos secundarios se organizan en filas o el alto cuando los elementos secundarios se organizan en columnas.

El `Basis` propiedad es de tipo [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), una estructura. Se puede especificar el tamaño en cualquier unidades independientes del dispositivo o como un porcentaje del tamaño de la `FlexLayout`. El valor predeterminado de la `Basis` es la propiedad estática `FlexBasis.Auto`, lo que significa que el elemento secundario solicitado se utiliza el ancho o alto.

En el código, puede establecer el `Basis` propiedad para un `Label` denominado `label` a 40 unidades independientes del dispositivo como esta:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

El segundo argumento para el `FlexBasis` se denomina constructor `isRelative` e indica si el tamaño es relativo (`true`) o absoluta (`false`). El argumento tiene un valor predeterminado de `false`, por lo que también puede usar el código siguiente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Una conversión implícita de `float` a `FlexBasis` está definido, por lo que puede simplificar aún más:

```csharp
FlexLayout.SetBasis(label, 40);
```

Puede establecer el tamaño del 25% de la `FlexLayout` primario similar al siguiente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Este valor fraccionario debe estar en el intervalo de 0 a 1.

En XAML, puede usar un número para un tamaño en unidades independientes del dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

O bien, puede especificar un porcentaje del intervalo de 0 a 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

El **base experimentar** página de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** ejemplo le permite experimentar con la `Basis` propiedad. La página muestra una columna ajustada de cinco `Label` elementos con background y foreground colores alternos. Dos `Slider` elementos le permiten especificar `Basis` valores para la segunda y cuarta `Label`:

[![La base de experimentar página](flex-layout-images/BasisExperiment.png "base experimentar página")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La captura de pantalla de iOS a la izquierda muestra los dos `Label` elementos determinados altos en unidades independientes del dispositivo. Muestra la pantalla Android determinado altos que suponen una fracción de la altura total del `FlexLayout`. Si se establece en 100%, el elemento secundario es el alto `FlexLayout`de, y se ajustará a la columna siguiente y ocupará el alto completo de esa columna, como muestra la captura de pantalla de UWP: `Basis` Aparece como si los cinco elementos secundarios estuvieran organizados en una fila, pero en realidad están dispuestos en cinco columnas.

### <a name="the-grow-property"></a>La propiedad de crecimiento

El [ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) propiedad enlazable adjunta es de tipo `int`. El valor predeterminado es 0 y el valor debe ser mayor o igual que 0.

El `Grow` propiedad desempeña un rol cuando la `Wrap` propiedad está establecida en `NoWrap` y la fila de elementos secundarios tiene un ancho total menor que el ancho de la `FlexLayout`, o la columna de elementos secundarios tiene un alto más corto que el `FlexLayout`. El `Grow` propiedad indica la forma de distribuir el espacio entre los elementos secundarios sobrante.

En el **crecer experimento** página cinco `Label` elementos de alternancia de colores se organizan en una columna y dos `Slider` elementos le permiten ajustar la `Grow` propiedad de la segunda y cuarta `Label`. La captura de pantalla de iOS en el extremo izquierdo muestra el valor predeterminado `Grow` propiedades 0:

[![La página del experimento crecimiento](flex-layout-images/GrowExperiment.png "la página del experimento de crecimiento")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Si cualquier un elemento secundario no se especifica un positivo `Grow` valor, a continuación, ese elemento secundario ocupa todo el espacio restante, como se muestra en la captura de pantalla de Android. También se puede asignar este espacio entre dos o más elementos secundarios. En la captura de pantalla UWP, la `Grow` propiedad del segundo `Label` se establece en 0,5, mientras el `Grow` propiedad de la cuarta `Label` es la 1.5, que proporciona el cuarto `Label` tres veces más el espacio como el segundo sobrantes`Label`.

Cómo la vista secundaria utiliza ese espacio depende del tipo específico de elemento secundario. Para un `Label`, el texto se puede colocar dentro del espacio total de la `Label` mediante las propiedades `HorizontalTextAlignment` y `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>La propiedad de reducción

El [ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propiedad enlazable adjunta es de tipo `int`. El valor predeterminado es 1 y el valor debe ser mayor o igual que 0.

El `Shrink` propiedad desempeña un papel cuando la `Wrap` propiedad está establecida en `NoWrap` y el ancho agregado de una fila de elementos secundarios es mayor que el ancho de la `FlexLayout`, o el alto agregado de una sola columna de elementos secundarios es mayor que el alto de la `FlexLayout`. Normalmente el `FlexLayout` mostrará estos elementos secundarios por limitante sus tamaños. El `Shrink` propiedad puede indicar qué elementos secundarios tienen prioridad en el que se muestra en su tamaño completo.

El **reducir experimento** página crea un `FlexLayout` con una sola fila de cinco `Label` elementos secundarios que requieren más espacio que el `FlexLayout` ancho. La captura de pantalla de iOS en el lado izquierdo muestra todas las `Label` elementos con los valores predeterminados de 1:

[![La reducción experimentar página](flex-layout-images/ShrinkExperiment.png "la reducción experimentar página")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

En la captura de pantalla de Android, el `Shrink` valor para el segundo `Label` se establece en 0 y que `Label` se muestra en su formato completo. Además, la cuarta `Label` tiene un `Shrink` valor mayor que uno, y ha reducido. Muestra la captura de pantalla UWP `Label` elementos que se concede un `Shrink` el valor 0 para que puedan mostrarse en su tamaño completo, si eso es posible.

Puede establecer tanto el `Grow` y `Shrink` valores para adaptarse a situaciones donde los tamaños de agregados secundarios a veces pueden ser menor que o a veces mayor que el tamaño de la `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Estilo CSS con FlexLayout

Puede usar el [estilos CSS](~/xamarin-forms/user-interface/styles/css/index.md) característica se presentó con Xamarin.Forms 3.0 en relación con `FlexLayout`. El **elementos del catálogo de CSS** página de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** ejemplo duplica el diseño de la **elementos del catálogo** página, pero con CSS hoja de estilos para muchos de los estilos:

[![Página de elementos de catálogo de la CSS](flex-layout-images/CssCatalogItems.png "página elementos de catálogo de CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

La versión original **CatalogItemsPage.xaml** archivo tiene cinco `Style` definiciones en su `Resources` sección con 15 `Setter` objetos. En el **CssCatalogItemsPage.xaml** archivo, que se ha reducido a dos `Style` definiciones con sólo cuatro `Setter` objetos. Estos estilos complementan la hoja de estilos CSS para las propiedades que actualmente no admite la característica de estilo CSS de Xamarin.Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Se hace referencia a la hoja de estilos CSS en la primera línea de la `Resources` sección:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Observe también que se incluyen dos elementos en cada uno de los tres elementos `StyleClass` configuración:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Hacen referencia a los selectores en el **CatalogItemsStyles.css** hoja de estilos:

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

Varios `FlexLayout` adjuntadas propiedades enlazables se hace referencia aquí. En el `label.empty` selector, verá el `flex-grow` atributo, que determina el estilo vacío `Label` para ofrecer algún espacio en blanco por encima del `Button`. El `image` selector contiene un `order` atributo y un `align-self` atributo, que corresponden a `FlexLayout` enlazables propiedades adjuntas.

Hemos visto que puede establecer propiedades directamente en el `FlexLayout` y puede establecer las propiedades enlazables adjuntas en los elementos secundarios de un `FlexLayout`. O bien, puede establecer estas propiedades forma indirecta, mediante los estilos tradicionales basado en XAML o los estilos CSS. Lo importante es conocer y comprender estas propiedades. Estas propiedades son lo que hace que el `FlexLayout` realmente flexible.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout con Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Vídeo de diseño flexible de Xamarin. Forms 3,0**

## <a name="related-links"></a>Vínculos relacionados

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
