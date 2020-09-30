---
title: Xamarin.FormsFlexLayout
description: Use FlexLayout para apilar o ajustar una colección de vistas secundarias.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 88f89e3d17c693c48176a45605b036f56599ee6d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563632"
---
# <a name="the-no-locxamarinforms-flexlayout"></a>Xamarin.FormsFlexLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Use FlexLayout para apilar o ajustar una colección de vistas secundarias._

Xamarin.Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) Es nuevo en la Xamarin.Forms versión 3,0. Se basa en el módulo de [diseño de caja flexible](https://www.w3.org/TR/css-flexbox-1/)de CSS, conocido normalmente como _diseño_ flexible o _flexible_, por lo que se llama porque incluye muchas opciones flexibles para organizar los elementos secundarios dentro del diseño.

`FlexLayout` es similar a Xamarin.Forms [`StackLayout`](~/xamarin-forms/user-interface/layouts/stacklayout.md) en que puede organizar sus elementos secundarios horizontal y verticalmente en una pila. Sin embargo, `FlexLayout` también es capaz de ajustar sus elementos secundarios si hay demasiados para caber en una sola fila o columna, y también tiene muchas opciones para la orientación, la alineación y la adaptación a distintos tamaños de pantalla.

`FlexLayout` deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) y hereda una [`Children`](xref:Xamarin.Forms.Layout`1.Children) propiedad de tipo `IList<View>` .

`FlexLayout` define seis propiedades públicas enlazables y cinco propiedades enlazables asociadas que afectan al tamaño, la orientación y la alineación de sus elementos secundarios. (Si no está familiarizado con las propiedades enlazables conectadas, vea el artículo **[propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)**). Estas propiedades se describen en detalle en las secciones siguientes en **[las propiedades enlazables en detalle](#the-bindable-properties-in-detail)** y en las **[propiedades enlazables asociadas en detalle](#the-attached-bindable-properties-in-detail)**. Sin embargo, en este artículo se empieza con una sección sobre algunos **[escenarios de uso comunes](#common-usage-scenarios)** de `FlexLayout` que describen muchas de estas propiedades de forma más eficaz. Al final del artículo, verá cómo combinar `FlexLayout` con [hojas de estilos CSS](~/xamarin-forms/user-interface/styles/css/index.md).

## <a name="common-usage-scenarios"></a>Escenarios de uso comunes

El programa de ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** contiene varias páginas que muestran algunos usos comunes de `FlexLayout` y permiten experimentar con sus propiedades.

### <a name="using-flexlayout-for-a-simple-stack"></a>Usar FlexLayout para una pila simple

En la página **pila simple** se muestra cómo `FlexLayout` puede sustituir por un, `StackLayout` pero con el marcado más sencillo. Todo lo que se muestra en este ejemplo se define en la página XAML. `FlexLayout`Contiene cuatro elementos secundarios:

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

Esta es la página que se ejecuta en iOS, Android y el Plataforma universal de Windows:

[![La página de pila simple](flex-layout-images/SimpleStack.png "La página de pila simple")](flex-layout-images/SimpleStack-Large.png#lightbox)

Se muestran tres propiedades de `FlexLayout` en el archivo **SimpleStackPage. Xaml** :

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propiedad se establece en un valor de la [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) enumeración. El valor predeterminado es `Row`. Establecer la propiedad en `Column` hace que los elementos secundarios de `FlexLayout` se organicen en una sola columna de elementos.

    Cuando los elementos de un `FlexLayout` se organizan en una columna, `FlexLayout` se dice que tiene un _eje principal_ vertical y un _eje cruzado_horizontal.

- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propiedad es de tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) y especifica cómo se alinean los elementos en el eje cruzado. La `Center` opción hace que cada elemento se Centre horizontalmente.

    Si usaba un `StackLayout` en lugar de un `FlexLayout` para esta tarea, centraría todos los elementos asignando la `HorizontalOptions` propiedad de cada elemento a `Center` . La `HorizontalOptions` propiedad no funciona para los elementos secundarios de un `FlexLayout` , pero la `AlignItems` propiedad única alcanza el mismo objetivo. Si es necesario, puede usar la `AlignSelf` propiedad enlazable adjunta para invalidar la `AlignItems` propiedad de elementos individuales:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Con ese cambio, este `Label` se coloca en el borde izquierdo de `FlexLayout` cuando el orden de lectura es de izquierda a derecha.

- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propiedad es de tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) y especifica cómo se organizan los elementos en el eje principal. La `SpaceEvenly` opción asigna todos los espacios verticales sobrantes entre todos los elementos y por encima del primer elemento, y por debajo del último elemento.

    Si estaba usando, debe `StackLayout` asignar la `VerticalOptions` propiedad de cada elemento a `CenterAndExpand` para lograr un efecto similar. Sin embargo `CenterAndExpand` , la opción asignaría el doble de espacio entre cada elemento que antes del primer elemento y después del último elemento. Puede imitar la `CenterAndExpand` opción de estableciendo `VerticalOptions` la `JustifyContent` propiedad de `FlexLayout` en `SpaceAround` .

Estas `FlexLayout` propiedades se describen con más detalle en la sección las **[propiedades enlazables en detalle](#the-bindable-properties-in-detail)** a continuación.

### <a name="using-flexlayout-for-wrapping-items"></a>Usar FlexLayout para ajustar elementos

En la página de **ajuste de fotografía** del ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** se muestra cómo `FlexLayout` puede ajustar sus elementos secundarios a filas o columnas adicionales. El archivo XAML crea una instancia `FlexLayout` de y le asigna dos propiedades:

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

La `Direction` propiedad de este `FlexLayout` no se establece, por lo que tiene la configuración predeterminada de `Row` , lo que significa que los elementos secundarios están organizados en filas y el eje principal es horizontal.

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propiedad es de un tipo de enumeración [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) . Si hay demasiados elementos para caber en una fila, este valor de propiedad hace que los elementos se ajusten a la fila siguiente.

Observe que `FlexLayout` es un elemento secundario de `ScrollView` . Si hay demasiadas filas para caber en la página, el `ScrollView` tiene una propiedad predeterminada `Orientation` de `Vertical` y permite el desplazamiento vertical.

La `JustifyContent` propiedad asigna espacio sobrante en el eje principal (el eje horizontal) para que cada elemento esté rodeado por la misma cantidad de espacio en blanco.

El archivo de código subyacente obtiene acceso a una colección de fotos de ejemplo y las agrega a la `Children` colección de `FlexLayout` :

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

Este es el programa que se ejecuta, desplazado progresivamente de arriba abajo:

[![La página de ajuste de fotografías](flex-layout-images/PhotoWrapping.png "La página de ajuste de fotografías")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Diseño de página con FlexLayout

Hay un diseño estándar en el diseño web denominado The [_Santo permite_](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) porque es un formato de diseño que es muy conveniente, pero que a menudo es difícil de comprender con la perfección. El diseño está compuesto de un encabezado en la parte superior de la página y un pie de página en la parte inferior, que se extiende hasta el ancho completo de la página. Ocupar el centro de la página es el contenido principal, pero a menudo con un menú en columnas a la izquierda del contenido y la información complementaria (a veces denominada área de _retirada_ ) a la derecha. En [la sección 5.4.1 de la especificación de diseño de caja flexible de CSS](https://www.w3.org/TR/css-flexbox-1/#order-accessibility) se describe cómo se puede realizar el diseño Santo permite con un cuadro flexible.

La página de **diseño de Santa permite** del ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** muestra una implementación sencilla de este diseño mediante un `FlexLayout` anidado en otro. Dado que esta página está diseñada para un teléfono en modo vertical, las áreas situadas a la izquierda y a la derecha del área de contenido son solo de 50 píxeles de ancho:

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

Aquí se ejecuta:

[![Página de diseño de Santa permite](flex-layout-images/HolyGrailLayout.png "Página de diseño de Santa permite")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Las áreas de navegación y de reserva se representan con a `BoxView` la izquierda y a la derecha.

La primera del `FlexLayout` archivo XAML tiene un eje principal vertical y contiene tres elementos secundarios organizados en una columna. Estos son el encabezado, el cuerpo de la página y el pie de página. El anidado `FlexLayout` tiene un eje principal horizontal con tres elementos secundarios organizados en una fila.

En este programa se muestran tres propiedades enlazables asociadas:

- La `Order` propiedad enlazable adjunta está establecida en la primera `BoxView` . Esta propiedad es un entero con un valor predeterminado de 0. Puede usar esta propiedad para cambiar el orden de diseño. Por lo general, los desarrolladores prefieren que el contenido de la página aparezca en el marcado antes que los elementos de navegación y los elementos. Si se establece la `Order` propiedad en el primero `BoxView` en un valor menor que el de los demás elementos del mismo nivel, aparecerá como el primer elemento de la fila. Del mismo modo, puede asegurarse de que un elemento aparece en último lugar si establece la `Order` propiedad en un valor mayor que sus elementos relacionados.

- La `Basis` propiedad enlazable adjunta está establecida en los dos `BoxView` elementos para darles un ancho de 50 píxeles. Esta propiedad es de tipo `FlexBasis` , una estructura que define una propiedad estática de tipo `FlexBasis` denominado `Auto` , que es el valor predeterminado. Puede usar `Basis` para especificar un tamaño de píxel o un porcentaje que indique cuánto espacio ocupa el elemento en el eje principal. Se denomina _base_ porque especifica un tamaño de elemento que es la base de todos los diseños posteriores.

- La `Grow` propiedad se establece en el objeto anidado `Layout` y en el `Label` elemento secundario que representa el contenido. Esta propiedad es de tipo `float` y su valor predeterminado es 0. Cuando se establece en un valor positivo, todo el espacio restante a lo largo del eje principal se asigna a ese elemento y a los elementos del mismo nivel que los valores positivos de `Grow` . El espacio se asigna proporcionalmente a los valores, algo parecido a la especificación de estrella en un `Grid` .

    La primera `Grow` propiedad adjunta se establece en el anidado `FlexLayout` , lo que indica que `FlexLayout` se va a ocupar todo el espacio vertical sin usar dentro del exterior `FlexLayout` . La segunda `Grow` propiedad adjunta se establece en `Label` que representa el contenido, lo que indica que este contenido va a ocupar todo el espacio horizontal sin usar dentro del interior `FlexLayout` .

    También hay una `Shrink` propiedad enlazable adjunta similar que puede usar cuando el tamaño de los elementos secundarios supera el tamaño de, `FlexLayout` pero no se desea el ajuste.

### <a name="catalog-items-with-flexlayout"></a>Catalogar elementos con FlexLayout

La página de **elementos de catálogo** en el ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** es similar a la del [ejemplo 1 de la sección 1,1 de la especificación del cuadro de diseño Flex de CSS](https://www.w3.org//TR/css-flexbox-1/#overview) , con la diferencia de que muestra una serie de imágenes desplazables horizontalmente y descripciones de tres Monkeys:

[![La página elementos del catálogo](flex-layout-images/CatalogItems.png "La página elementos del catálogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Cada uno de los tres Monkeys es un `FlexLayout` contenido en un `Frame` que recibe un alto y ancho explícitos, y que también es un elemento secundario de un mayor `FlexLayout` . En este archivo XAML, la mayoría de las propiedades de los `FlexLayout` elementos secundarios se especifican en estilos, excepto uno de los cuales es un estilo implícito:

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

El estilo implícito para `Image` incluye la configuración de dos propiedades enlazables adjuntas de `Flexlayout` :

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

El `Order` valor &ndash; 1 hace que el `Image` elemento se muestre en primer lugar en cada una de las `FlexLayout` vistas anidadas, independientemente de su posición dentro de la colección de elementos secundarios. La `AlignSelf` propiedad de `Center` hace que `Image` se Centre dentro de `FlexLayout` . Esto invalida el valor de la `AlignItems` propiedad, que tiene un valor predeterminado de `Stretch` , lo que significa que los `Label` `Button` elementos secundarios y se ajustan al ancho completo de `FlexLayout` .

Dentro de cada una de las tres `FlexLayout` vistas, un espacio en blanco `Label` precede a `Button` , pero tiene un `Grow` valor de 1. Esto significa que todo el espacio vertical adicional se asigna a este espacio en blanco `Label` , lo que realmente envía el `Button` a la parte inferior.

## <a name="the-bindable-properties-in-detail"></a>Detalles de las propiedades enlazables

Ahora que ha visto algunas aplicaciones comunes de `FlexLayout` , las propiedades de `FlexLayout` se pueden explorar con más detalle.
`FlexLayout` define seis propiedades enlazables que se establecen en el `FlexLayout` propio, ya sea en código o XAML, para controlar la orientación y la alineación. (Una de estas propiedades, [`Position`](xref:Xamarin.Forms.FlexLayout.Position) , no se trata en este artículo).

Puede experimentar con las cinco propiedades enlazables restantes mediante la página **experimento** del ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** . Esta página permite agregar o quitar elementos secundarios de `FlexLayout` y establecer combinaciones de las cinco propiedades enlazables. Todos los elementos secundarios de `FlexLayout` son `Label` vistas de varios colores y tamaños, con la `Text` propiedad establecida en un número correspondiente a su posición en la `Children` colección.

Cuando se inicia el programa, cinco `Picker` vistas muestran los valores predeterminados de estas cinco `FlexLayout` propiedades. `FlexLayout`Hacia la parte inferior de la pantalla contiene tres elementos secundarios:

[![La página del experimento: predeterminada](flex-layout-images/ExperimentDefault.png "Página del experimento: predeterminada")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Cada una de las `Label` vistas tiene un fondo gris que muestra el espacio asignado a ese `Label` dentro de `FlexLayout` . El fondo del `FlexLayout` propio es Alicia azul. Ocupa todo el área inferior de la página, excepto un margen pequeño a la izquierda y a la derecha.

### <a name="the-direction-property"></a>Propiedad direction

La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propiedad es de tipo [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) , una enumeración con cuatro miembros:

- `Column`
- `ColumnReverse` (o "columna-inversa" en XAML)
- `Row`, la opción predeterminada
- `RowReverse` (o "Row-REVERSE" en XAML)

En XAML, puede especificar el valor de esta propiedad mediante los nombres de miembro de enumeración en minúsculas, mayúsculas o minúsculas, o puede usar dos cadenas adicionales que se muestran entre paréntesis que son iguales que los indicadores de CSS. (Las cadenas "Column-REVERSE" y "Row-REVERSE" se definen en la [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) clase utilizada por el analizador de XAML).

Esta es la página del **experimento** que muestra (de izquierda a derecha), la `Row` dirección, la `Column` dirección y la `ColumnReverse` Dirección:

[![La página del experimento: Direction](flex-layout-images/ExperimentDirection.png "La página del experimento: dirección")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Tenga en cuenta que para las `Reverse` Opciones, los elementos se inician en la parte derecha o inferior.

### <a name="the-wrap-property"></a>La propiedad Wrap

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propiedad es de tipo [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) , una enumeración con tres miembros:

- `NoWrap`, la opción predeterminada
- `Wrap`
- `Reverse` (o "ajustar-invertir" en XAML)

De izquierda a derecha, estas pantallas muestran las `NoWrap` `Wrap` Opciones, y `Reverse` para 12 elementos secundarios:

[![Página del experimento: Wrap](flex-layout-images/ExperimentWrap.png "Página experimento-Wrap")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Cuando la `Wrap` propiedad se establece en `NoWrap` y el eje principal está restringido (como en este programa), y el eje principal no es ancho o lo suficientemente alto como para ajustarse a todos los elementos secundarios, `FlexLayout` intenta reducir los elementos como se muestra en la captura de pantalla de iOS. Puede controlar la reducción de los elementos con la [`Shrink`](#the-shrink-property) propiedad enlazable asociada.

### <a name="the-justifycontent-property"></a>La propiedad JustifyContent

La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propiedad es de tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) , una enumeración con seis miembros:

- `Start` (o "Flex-Start" en XAML), el valor predeterminado
- `Center`
- `End` (o "Flex-end" en XAML)
- `SpaceBetween` (o "espacio entre" en XAML)
- `SpaceAround` (o "espacio alrededor" en XAML)
- `SpaceEvenly`

Esta propiedad especifica cómo se espacian los elementos en el eje principal, que es el eje horizontal en este ejemplo:

[![Página del experimento: justificar contenido](flex-layout-images/ExperimentJustifyContent.png "Página del experimento: justificar contenido")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

En las tres capturas de pantallas, la `Wrap` propiedad se establece en `Wrap` . El `Start` valor predeterminado se muestra en la captura de pantalla anterior de Android. La captura de pantalla de iOS muestra la `Center` opción: todos los elementos se mueven al centro. Las otras tres opciones a partir de la palabra `Space` asignan el espacio adicional no ocupado por los elementos. `SpaceBetween` asigna el espacio equitativamente entre los elementos; `SpaceAround` coloca el mismo espacio alrededor de cada elemento, mientras que `SpaceEvenly` coloca el mismo espacio entre cada elemento y antes del primer elemento y después del último elemento de la fila.

### <a name="the-alignitems-property"></a>La propiedad AlignItems

La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propiedad es de tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) , una enumeración con cuatro miembros:

- `Stretch`, la opción predeterminada
- `Center`
- `Start` (o "Flex-Start" en XAML)
- `End` (o "Flex-end" en XAML)

Esta es una de las dos propiedades (otra [`AlignContent`](#the-aligncontent-property) ) que indica cómo se alinean los elementos secundarios en el eje cruzado. Dentro de cada fila, los elementos secundarios se ajustan (como se muestra en la captura de pantalla anterior) o se alinean en el inicio, el centro o el final de cada elemento, tal como se muestra en las tres capturas de pantalla siguientes:

[![Página experimento: alinear elementos](flex-layout-images/ExperimentAlignItems.png "Página experimento: alinear elementos")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

En la captura de pantalla de iOS, la parte superior de todos los elementos secundarios está alineada. En las capturas de pantallas de Android, los elementos se centran verticalmente en función del elemento secundario más alto. En la captura de pantalla de UWP, se alinean las parte inferior de todos los elementos.

Para cualquier elemento individual, el `AlignItems` valor se puede invalidar con la [`AlignSelf`](#the-alignself-property) propiedad enlazable asociada.

### <a name="the-aligncontent-property"></a>La propiedad AlignContent

La [`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent) propiedad es de tipo [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent) , una enumeración con siete miembros:

- `Stretch`, la opción predeterminada
- `Center`
- `Start` (o "Flex-Start" en XAML)
- `End` (o "Flex-end" en XAML)
- `SpaceBetween` (o "espacio entre" en XAML)
- `SpaceAround` (o "espacio alrededor" en XAML)
- `SpaceEvenly`

Al igual `AlignItems` que, la `AlignContent` propiedad también alinea los elementos secundarios en el eje cruzado, pero afecta a las filas o columnas completas:

[![Página del experimento: alinear contenido](flex-layout-images/ExperimentAlignContent.png "Página del experimento: alinear contenido")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

En la captura de pantalla de iOS, ambas filas se encuentran en la parte superior; en la captura de pantalla de Android, se encuentran en el centro; y, en la captura de pantalla de UWP, se encuentran en la parte inferior. Las filas también se pueden espaciar de varias maneras:

[![Página del experimento: alinear contenido 2](flex-layout-images/ExperimentAlignContent2.png "Página experimento: alinear contenido 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`No tiene ningún efecto cuando solo hay una fila o columna.

## <a name="the-attached-bindable-properties-in-detail"></a>Propiedades enlazables adjuntas en detalle

`FlexLayout` define cinco propiedades enlazables adjuntas. Estas propiedades se establecen en los elementos secundarios de `FlexLayout` y solo pertenecen a ese elemento secundario determinado.

### <a name="the-alignself-property"></a>La propiedad AlignSelf

La [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propiedad enlazable asociada es de tipo [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent) , una enumeración con cinco miembros:

- `Auto`, la opción predeterminada
- `Stretch`
- `Center`
- `Start` (o "Flex-Start" en XAML)
- `End` (o "Flex-end" en XAML)

Para cualquier elemento secundario individual de `FlexLayout` , este valor de propiedad invalida la [`AlignItems`](#the-alignitems-property) propiedad establecida en el `FlexLayout` propio. La configuración predeterminada de `Auto` significa usar el `AlignItems` valor.

En el caso de un `Label` elemento denominado `label` (o ejemplo), puede establecer la `AlignSelf` propiedad en el código como se indica a continuación:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Observe que no hay ninguna referencia al `FlexLayout` elemento primario de `Label` . En XAML, establezca la propiedad de la siguiente manera:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La propiedad Order

La [`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty) propiedad es de tipo `int` . El valor predeterminado es 0.

La `Order` propiedad permite cambiar el orden en el que se organizan los elementos secundarios de `FlexLayout` . Normalmente, los elementos secundarios de un `FlexLayout` objeto están organizados en el mismo orden en que aparecen en la `Children` colección. Puede invalidar este orden estableciendo la `Order` propiedad enlazable adjunta en un valor entero distinto de cero en uno o varios elementos secundarios. `FlexLayout`A continuación, organiza sus elementos secundarios en función del valor de la `Order` propiedad en cada elemento secundario, pero los elementos secundarios con la misma `Order` configuración se organizan en el orden en que aparecen en la `Children` colección.

### <a name="the-basis-property"></a>Propiedad base

La [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) propiedad enlazable asociada indica la cantidad de espacio que se asigna a un elemento secundario de `FlexLayout` en el eje principal. El tamaño especificado por la `Basis` propiedad es el tamaño a lo largo del eje principal del elemento primario `FlexLayout` . Por consiguiente, `Basis` indica el ancho de un elemento secundario cuando los elementos secundarios están organizados en filas o el alto cuando los elementos secundarios se organizan en columnas.

La `Basis` propiedad es de tipo [`FlexBasis`](xref:Xamarin.Forms.FlexBasis) , una estructura. El tamaño se puede especificar en unidades independientes del dispositivo o como un porcentaje del tamaño de `FlexLayout` . El valor predeterminado de la `Basis` propiedad es la propiedad estática `FlexBasis.Auto` , lo que significa que se utiliza el ancho o el alto solicitados del secundario.

En el código, puede establecer la `Basis` propiedad de un `Label` con nombre `label` en unidades independientes del dispositivo 40 como esta:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

El segundo argumento del `FlexBasis` constructor se denomina `isRelative` e indica si el tamaño es relativo ( `true` ) o absoluto ( `false` ). El argumento tiene un valor predeterminado de `false` , por lo que también puede usar el código siguiente:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Se define una conversión implícita de `float` a `FlexBasis` , por lo que puede simplificarla aún más:

```csharp
FlexLayout.SetBasis(label, 40);
```

Puede establecer el tamaño en el 25% del elemento primario de la siguiente `FlexLayout` manera:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Este valor fraccionario debe estar en el intervalo de 0 a 1.

En XAML, puede usar un número para un tamaño en unidades independientes del dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

O bien, puede especificar un porcentaje en el intervalo de 0% a 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

La página **experimento base** del ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** le permite experimentar con la `Basis` propiedad. En la página se muestra una columna ajustada de cinco `Label` elementos con colores de fondo y de primer plano alternativos. Dos `Slider` elementos permiten especificar `Basis` valores para el segundo y el cuarto `Label` :

[![Página experimento base](flex-layout-images/BasisExperiment.png "Página experimento base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La captura de pantalla de iOS de la izquierda muestra los dos `Label` elementos que se proporcionan con alto en unidades independientes del dispositivo. La pantalla de Android muestra que se les proporciona alto que son una fracción del alto total de `FlexLayout` . Si `Basis` se establece en 100%, el elemento secundario es el alto de `FlexLayout` , y se ajustará a la columna siguiente y ocupará el alto completo de esa columna, como muestra la captura de pantalla de UWP: aparece como si los cinco elementos secundarios estuvieran organizados en una fila, pero en realidad están dispuestos en cinco columnas.

### <a name="the-grow-property"></a>La propiedad Grow

La [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) propiedad enlazable asociada es de tipo `int` . El valor predeterminado es 0 y el valor debe ser mayor o igual que 0.

La `Grow` propiedad desempeña un rol cuando la `Wrap` propiedad se establece en `NoWrap` y la fila de elementos secundarios tiene un ancho total menor que el ancho de `FlexLayout` , o bien la columna de elementos secundarios tiene un alto más corto que el `FlexLayout` . La `Grow` propiedad indica cómo prorratear el espacio sobrante entre los elementos secundarios.

En la página **aumentar experimento** , `Label` se organizan cinco elementos de colores alternos en una columna y dos `Slider` elementos permiten ajustar la `Grow` propiedad del segundo y cuarto `Label` . La captura de pantalla de iOS en el extremo izquierdo muestra las propiedades predeterminadas `Grow` de 0:

[![La página del experimento de crecimiento](flex-layout-images/GrowExperiment.png "La página del experimento de crecimiento")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Si a un elemento secundario se le asigna un `Grow` valor positivo, ese elemento secundario ocupa todo el espacio restante, como se muestra en la captura de pantalla de Android. Este espacio también puede asignarse entre dos o más elementos secundarios. En la captura de pantalla de UWP, la `Grow` propiedad de la segunda `Label` está establecida en 0,5, mientras que la `Grow` propiedad del cuarto `Label` es 1,5, que proporciona el cuarto `Label` de las tres veces como gran parte del espacio sobrante como segundo `Label` .

La forma en que la vista secundaria utiliza ese espacio depende del tipo de elemento secundario. En el caso de `Label` , el texto se puede colocar en el espacio total de `Label` mediante las propiedades `HorizontalTextAlignment` y `VerticalTextAlignment` .

### <a name="the-shrink-property"></a>Propiedad Shrink

La [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propiedad enlazable asociada es de tipo `int` . El valor predeterminado es 1 y el valor debe ser mayor o igual que 0.

La `Shrink` propiedad desempeña un rol cuando la `Wrap` propiedad se establece en `NoWrap` y el ancho agregado de una fila de elementos secundarios es mayor que el ancho de `FlexLayout` , o el alto agregado de una sola columna de elementos secundarios es mayor que el alto de `FlexLayout` . Normalmente, mostrará `FlexLayout` estos elementos secundarios restringiendo sus tamaños. La `Shrink` propiedad puede indicar a qué elementos secundarios se les da prioridad en su tamaño completo.

La página **reducir experimento** crea un `FlexLayout` con una sola fila de cinco `Label` elementos secundarios que requieren más espacio que el `FlexLayout` ancho. La captura de pantalla de iOS de la izquierda muestra todos los `Label` elementos con valores predeterminados de 1:

[![La página para reducir el experimento](flex-layout-images/ShrinkExperiment.png "La página para reducir el experimento")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

En la captura de pantalla de Android, el `Shrink` valor del segundo `Label` se establece en 0 y `Label` se muestra en su ancho completo. Además, a la cuarta `Label` se le asigna un `Shrink` valor mayor que uno y se ha reducido. La captura de pantalla de UWP muestra que los dos `Label` elementos reciben un `Shrink` valor de 0 para permitir que se muestren en su tamaño completo, si es posible.

Puede establecer los `Grow` `Shrink` valores y para dar cabida a situaciones en las que los tamaños de los elementos secundarios agregados pueden ser a veces menor o igual que el tamaño de `FlexLayout` .

## <a name="css-styling-with-flexlayout"></a>Estilos CSS con FlexLayout

Puede usar la característica de [estilo CSS](~/xamarin-forms/user-interface/styles/css/index.md) que se presentó con Xamarin.Forms 3,0 en conexión con `FlexLayout` . La página **elementos del catálogo CSS** del ejemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** duplica el diseño de la página **elementos del catálogo** , pero con una hoja de estilos CSS para muchos de los estilos:

[![La página elementos del catálogo CSS](flex-layout-images/CssCatalogItems.png "La página elementos del catálogo CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

El archivo **CatalogItemsPage. Xaml** original tiene cinco `Style` definiciones en su `Resources` sección con 15 `Setter` objetos. En el archivo **CssCatalogItemsPage. Xaml** , se ha reducido a dos `Style` definiciones con solo cuatro `Setter` objetos. Estos estilos complementan la hoja de estilos CSS para las propiedades que Xamarin.Forms actualmente no admite la característica de estilo CSS:

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

En la primera línea de la sección se hace referencia a la hoja de estilos CSS `Resources` :

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Observe también que dos elementos de cada uno de los tres elementos incluyen `StyleClass` valores de configuración:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Estos se refieren a los selectores de la hoja de estilos **CatalogItemsStyles. CSS** :

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

`FlexLayout`Aquí se hace referencia a varias propiedades enlazables adjuntas. En el `label.empty` selector, verá el `flex-grow` atributo, cuyo estilo es un vacío `Label` para proporcionar espacio en blanco por encima de `Button` . El `image` selector contiene un `order` atributo y un `align-self` atributo, que se corresponden con `FlexLayout` las propiedades enlazables adjuntas.

Ha visto que puede establecer las propiedades directamente en `FlexLayout` y puede establecer las propiedades enlazables asociadas en los elementos secundarios de `FlexLayout` . O bien, puede establecer estas propiedades indirectamente mediante estilos tradicionales basados en XAML o estilos CSS. Lo importante es saber y comprender estas propiedades. Estas propiedades son lo que hace que sea `FlexLayout` realmente flexible.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout con Xamarin. University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms vídeo de diseño de Flex 3,0**

## <a name="related-links"></a>Vínculos relacionados

- [FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)