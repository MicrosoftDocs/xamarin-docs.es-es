---
title: Aplicación de estilos para aplicaciones Xamarin.Forms con hojas de estilo (CSS)
description: Xamarin.Forms es compatible con los elementos de estilo visual con hojas de estilos en cascada (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 726ebd55b38460ee966113e4ee487327cd42b03d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305604"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicar estilos a las aplicaciones de Xamarin.Forms con hojas de estilos en cascada (CSS)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin. Forms admite el estilo de los elementos visuales mediante Hojas de estilo CSS (CSS)._

Las aplicaciones de Xamarin.Forms pueden cambiar el estilo mediante CSS. Una hoja de estilos consta de una lista de reglas, con cada regla que consta de uno o varios de los selectores y un bloque de declaración. Un bloque de declaración consta de una lista de declaraciones de llaves, con cada declaración que consta de una propiedad, un signo de dos puntos y un valor. Cuando hay varias declaraciones en un bloque, se inserta un punto y coma como separador. El ejemplo de código siguiente muestra algunas CSS compatibles con Xamarin.Forms:

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

En Xamarin.Forms, hojas de estilo CSS se analizan y se evalúan en tiempo de ejecución, en lugar de tiempo de compilación y las hojas de estilo son volver a analizar en uso.

> [!NOTE]
> Actualmente, todos los estilos que es posible con estilos XAML no se puede realizar con CSS. Sin embargo, estilos XAML se pueden utilizar para complementar CSS para las propiedades que actualmente no son compatibles con Xamarin.Forms. Para obtener más información sobre los estilos XAML, vea [Styling Xamarin.Forms Apps using XAML Styles](~/xamarin-forms/user-interface/styles/xaml/index.md) (Aplicación de estilo a aplicaciones Xamarin.Forms mediante XAML).

En el ejemplo [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) se muestra cómo usar CSS para aplicar estilo a una aplicación sencilla y se muestra en las siguientes capturas de pantallas:

[![Página principal de MonkeyApp con estilo CSS](css-images/MonkeyAppMainPage.png "Página principal de MonkeyApp con estilo CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Página principal de MonkeyApp con estilo CSS")

[![Página de detalles de MonkeyApp con estilo CSS](css-images/MonkeyAppDetailPage.png "Página de detalles de MonkeyApp con estilo CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Página de detalles de MonkeyApp con estilo CSS")

## <a name="consuming-a-style-sheet"></a>Consumo de una hoja de estilos

El proceso para agregar una hoja de estilos a una solución es como sigue:

1. Agregue un archivo CSS vacío al proyecto de biblioteca .NET Standard.
1. Establezca la acción de compilación del archivo CSS en **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Cargar una hoja de estilos

Hay varios enfoques que puede utilizarse para cargar una hoja de estilos.

### <a name="xaml"></a>XAML

Una hoja de estilos se puede cargar y analizar con la clase [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) antes de que se agregue a un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La propiedad [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) especifica la hoja de estilos como un URI relativo a la ubicación del archivo XAML envolvente, o relativa a la raíz del proyecto si el URI comienza con un `/`.

> [!WARNING]
> El archivo CSS no se cargará si su acción de compilación no está establecida en **EmbeddedResource**.

Como alternativa, se puede cargar y analizar una hoja de estilos con la clase [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) , antes de agregarla a una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), si se inserta en una sección `CDATA`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Para obtener más información acerca de los diccionarios de recursos, consulte [diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C\#

En C#, una hoja de estilos se puede cargar desde un `StringReader` y agregarse a un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

El argumento para el método `StyleSheet.FromReader` es el `TextReader` que ha leído la hoja de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Selección de elementos y aplicación de propiedades

CSS usa los selectores para determinar qué elementos de destino. Estilos con coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar. Para obtener más información sobre los selectores admitidos, vea [Referencia del selector](#selector-reference).

CSS usa las propiedades para definir el estilo de un elemento seleccionado. Cada propiedad tiene un conjunto de valores posibles, y algunas propiedades pueden afectar a cualquier tipo de elemento, mientras que otras se aplican a los grupos de elementos. Para obtener más información sobre las propiedades admitidas, vea [referencia de propiedades](#property-reference).

### <a name="selecting-elements-by-type"></a>Selección de elementos por tipo

Los elementos del árbol visual se pueden seleccionar por tipo con el selector `element` no distingue mayúsculas de minúsculas:

```css
stacklayout {
    margin: 20;
}
```

Este selector identifica cualquier [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementos de las páginas que consumen la hoja de estilos y establece sus márgenes en un grosor uniforme de 20.

> [!NOTE]
> El selector de `element` no identifica las subclases del tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selección de elementos mediante la clase base

Los elementos del árbol visual se pueden seleccionar por clase base con el selector `^base` sin distinción entre mayúsculas y minúsculas:

```css
^contentpage {
    background-color: lightgray;
}
```

Este selector identifica los elementos [`ContentPage`](xref:Xamarin.Forms.ContentPage) que consumen la hoja de estilos y establece su color de fondo en `lightgray`.

> [!NOTE]
> El selector de `^base` es específico de Xamarin. Forms y no forma parte de la especificación de CSS.

### <a name="selecting-an-element-by-name"></a>Seleccionar un elemento por nombre

Los elementos individuales del árbol visual se pueden seleccionar con el selector de `#id` con distinción entre mayúsculas y minúsculas:

```css
#listView {
    background-color: lightgray;
}
```

Este selector identifica el elemento cuya propiedad [`StyleId`](xref:Xamarin.Forms.Element.StyleId) está establecida en `listView`. Sin embargo, si no se establece la propiedad `StyleId`, el selector volverá a usar el `x:Name` del elemento. Por lo tanto, en el siguiente ejemplo de XAML, el selector de `#listView` identificará el [`ListView`](xref:Xamarin.Forms.ListView) cuyo atributo de `x:Name` está establecido en `listView`y establecerá el color de fondo de `lightgray`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selección de elementos con un atributo de clase específica

Los elementos con un atributo de clase específico se pueden seleccionar con el selector de `.class` con distinción entre mayúsculas y minúsculas:

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Se puede asignar una clase CSS a un elemento XAML estableciendo la propiedad [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) del elemento en el nombre de clase CSS. Por lo tanto, en el siguiente ejemplo de XAML, los estilos definidos por la clase `.detailPageTitle` se asignan a la primera [`Label`](xref:Xamarin.Forms.Label), mientras que los estilos definidos por la clase `.detailPageSubtitle` se asignan a la segunda `Label`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Selección de elementos secundarios

Los elementos secundarios del árbol visual se pueden seleccionar con el selector `element element` no distingue mayúsculas de minúsculas:

```css
listview image {
    height: 60;
    width: 60;
}
```

Este selector identifica cualquier [`Image`](xref:Xamarin.Forms.Image) elementos que sean elementos secundarios de [`ListView`](xref:Xamarin.Forms.ListView) elementos y establece su alto y ancho en 60. Por lo tanto, en el siguiente ejemplo de XAML, el selector de `listview image` identificará el [`Image`](xref:Xamarin.Forms.Image) que es un elemento secundario del [`ListView`](xref:Xamarin.Forms.ListView)y establece su alto y ancho en 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> El selector de `element element` no requiere que el elemento secundario sea un elemento secundario _directo_ del elemento primario: el elemento secundario puede tener un elemento primario diferente. Selección se produce siempre que un nodo primario es el primer elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selección de elementos secundarios directos

Los elementos secundarios directos del árbol visual se pueden seleccionar con el selector de `element>element` sin distinción de mayúsculas y minúsculas:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este selector identifica cualquier [`Image`](xref:Xamarin.Forms.Image) elementos que sean elementos secundarios directos de [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementos y establece su alto y ancho en 200. Por lo tanto, en el siguiente ejemplo de XAML, el selector de `stacklayout>image` identificará el [`Image`](xref:Xamarin.Forms.Image) que es un elemento secundario directo del [`StackLayout`](xref:Xamarin.Forms.StackLayout)y establece su alto y ancho en 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> El selector de `element>element` requiere que el elemento secundario sea un elemento secundario _directo_ del elemento primario.

## <a name="selector-reference"></a>Referencia de selector

Xamarin.Forms admite los siguientes selectores de CSS:

|Selector|Ejemplo|Descripción|
|---|---|---|
|`.class`|`.header`|Selecciona todos los elementos con la propiedad `StyleClass` que contiene ' header '. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`#id`|`#email`|Selecciona todos los elementos con `StyleId` establecida en `email`. Si no se establece `StyleId`, se reserva a `x:Name`. Al usar XAML, se prefiere `x:Name` a `StyleId`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`*`|`*`|Selecciona todos los elementos.|
|`element`|`label`|Selecciona todos los elementos de tipo `Label`, pero no las subclases. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`^base`|`^contentpage`|Selecciona todos los elementos que tienen `ContentPage` como la clase base, incluidos `ContentPage`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas y no forma parte de la especificación de CSS.|
|`element,element`|`label,button`|Selecciona todos los elementos `Button` y todos los elementos `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element element`|`stacklayout label`|Selecciona todos los elementos `Label` dentro de un `StackLayout`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element>element`|`stacklayout>label`|Selecciona todos los elementos `Label` con `StackLayout` como un elemento primario directo. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element+element`|`label+entry`|Selecciona todos los elementos `Entry`s directamente después de un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element~element`|`label~entry`|Selecciona todos los elementos `Entry` precedidos por un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|

Estilos con coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar.

> [!TIP]
> Los selectores se pueden combinar sin limitación, como `StackLayout>ContentView>label.email`.

Se admite actualmente los siguientes selectores:

- `[attribute]`
- `@media` y `@supports`
- `:` y `::`

> [!NOTE]
> No se admiten especificidad e invalidaciones de especificidad.

## <a name="property-reference"></a>Referencia de propiedades

Las siguientes propiedades CSS son compatibles con Xamarin. Forms (en la columna **valores** , los tipos están en _cursiva_, mientras que los literales de cadena se `gray`):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|\| de _colores_ `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|\| de _cadena_ `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|\| de _colores_ `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|\| _doble_ `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|\| _doble_ `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|\| de _colores_ `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|\| _doble_ `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|\| _float_ `auto` \| `initial`. Además, se puede especificar un porcentaje en el intervalo comprendido entre 0% y 100% con el signo `%`.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|\| _float_ `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|\| _float_ `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|\| de _cadena_ `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|\| _doble_ `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|\| _doble_ `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|\| _doble_ `initial` |`line-height: 1.8;`|
|`margin`|`View`|_grosor_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_grosor_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_grosor_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_grosor_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_grosor_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|\| _doble_ `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|\| _doble_ `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|\| _doble_ `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_grosor_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|\| _doble_ `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| \| _doble_ `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| \| _doble_ `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| \| _doble_ `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| \| _doble_ `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` y `right` deben evitarse en entornos de derecha a izquierda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _doble_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|\| _doble_ `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` es un valor válido para todas las propiedades. Borra el valor (restablece en el valor predeterminado) que se ha establecido de otro estilo.

Las siguientes propiedades no se admiten actualmente:

- `all: initial`.
- Propiedades de diseño (cuadro o cuadrícula).
- Propiedades abreviadas, como `font`, y `border`.

Además, no hay ningún valor `inherit` y, por tanto, no se admite la herencia. Por lo tanto, no es posible, por ejemplo, establecer la propiedad `font-size` en un diseño y esperar que todas las instancias de [`Label`](xref:Xamarin.Forms.Label) del diseño hereden el valor. La única excepción es la `direction` propiedad, que tiene un valor predeterminado de `inherit`.

El destino de los elementos de `Span` tiene un problema conocido que evita que los intervalos sean el destino de los estilos CSS por elemento y nombre (mediante el símbolo de `#`). El elemento `Span` deriva de `GestureElement`, que no tiene la propiedad `StyleClass`, por lo que los intervalos no admiten el destino de clase CSS. Para obtener más información, vea [no se puede aplicar el estilo CSS al control span](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Propiedades específicas de Xamarin. Forms

También se admiten las siguientes propiedades CSS específicas de Xamarin. Forms (en la columna **valores** , los tipos están en _cursiva_, mientras que los literales de cadena son `gray`):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|\| de _colores_ `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|\| de _colores_ `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|\| de _colores_ `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|\| de _colores_ `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` solo se admite en un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|\| de _texto entre comillas_ `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|\| de _colores_ `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|\| _doble_ `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|\| de _colores_ `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|\| de _cadena_ `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propiedades específicas del shell de Xamarin. Forms

También se admiten las siguientes propiedades CSS específicas del shell de Xamarin. Forms (en la columna **valores** , los tipos están en _cursiva_, mientras que los literales de cadena son `gray`):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|\| de _colores_ `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|\| de _colores_ `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|\| de _colores_ `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|\| de _colores_ `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|\| de _colores_ `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|\| de _colores_ `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|\| de _colores_ `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|\| de _colores_ `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|\| de _colores_ `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|\| de _colores_ `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|\| de _colores_ `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

Se admiten los siguientes valores de `color`:

- `X11` [colores](https://en.wikipedia.org/wiki/X11_color_names), que coinciden con los colores CSS, los colores predefinidos de UWP y los colores de Xamarin. Forms. Tenga en cuenta que estos valores de color distinguen mayúsculas de minúsculas.
- colores hex: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- colores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Los valores son en el intervalo 0-255, o 0-100%.
- colores RGBA: `rgba(255, 0, 0, 0.8)``rgba(100%, 0%, 0%, 0.8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.
- HSL Colors: `hsl(120, 100%, 50%)`. El valor h está en el intervalo 0-360, mientras s y l están en el intervalo 0-100%.
- HSLA colores: `hsla(120, 100%, 50%, .8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.

### <a name="thickness"></a>Thickness

Se admiten uno, dos, tres o cuatro valores `thickness`, cada uno separado por un espacio en blanco:

- Un único valor indica el grosor uniforme.
- Dos valores indican grosor vertical y horizontal.
- Estos tres valores indican la parte superior, horizontal (izquierda y derecha) y, luego, grosor de la parte inferior.
- Cuatro valores indican la parte superior, derecha y, a continuación, inferior y, luego, grosor de la izquierda.

> [!NOTE]
> Los valores de `thickness` de CSS se diferencian de los valores de [`Thickness`](xref:Xamarin.Forms.Thickness) de XAML. Por ejemplo, en XAML, un `Thickness` de dos valores indica el grosor horizontal y, a continuación, el grosor vertical, mientras que una `Thickness` de cuatro valores indica izquierda, superior, derecha y, a continuación, grosor inferior. Además, los valores de `Thickness` de XAML son delimitados por comas.

### <a name="namedsize"></a>NamedSize

Se admiten los siguientes valores de `namedsize` que no distinguen mayúsculas de minúsculas:

- `default`
- `micro`
- `small`
- `medium`
- `large`

El significado exacto de cada valor de `namedsize` depende de la plataforma y depende de la vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS en Xamarin.Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vídeo de CSS de Xamarin. Forms 3,0**

## <a name="related-links"></a>Vínculos relacionados

- [MonkeyAppCSS (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
