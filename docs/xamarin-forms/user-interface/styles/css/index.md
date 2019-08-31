---
title: Aplicar estilos a las aplicaciones de Xamarin.Forms con hojas de estilos en cascada (CSS)
description: Xamarin.Forms es compatible con los elementos de estilo visual con hojas de estilos en cascada (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: a86d4c6d6737bd6de5e6f115111e072863927fbe
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198828"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicar estilos a las aplicaciones de Xamarin.Forms con hojas de estilos en cascada (CSS)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin.Forms es compatible con los elementos de estilo visual con hojas de estilos en cascada (CSS)._

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
> Actualmente, todos los estilos que es posible con estilos XAML no se puede realizar con CSS. Sin embargo, estilos XAML se pueden utilizar para complementar CSS para las propiedades que actualmente no son compatibles con Xamarin.Forms. Para obtener más información sobre los estilos XAML, vea [aplicar estilos a las aplicaciones de Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

El [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) ejemplo muestra cómo utilizar CSS para aplicar estilo a una aplicación simple y se muestra en las capturas de pantalla siguiente:

[![Página principal de MonkeyApp con estilos CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main página con estilo de CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main página con estilo de CSS")

[![Página de detalles de MonkeyApp con estilos CSS](css-images/MonkeyAppDetailPage.png "MonkeyApp página de detalles con la aplicación de estilos CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp página de detalles con la aplicación de estilos CSS")

## <a name="consuming-a-style-sheet"></a>Consumo de una hoja de estilos

El proceso para agregar una hoja de estilos a una solución es como sigue:

1. Agregue un archivo CSS vacío al proyecto de biblioteca .NET Standard.
1. Establezca la acción de compilación del archivo CSS para **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Cargar una hoja de estilos

Hay varios enfoques que puede utilizarse para cargar una hoja de estilos.

### <a name="xaml"></a>XAML

Una hoja de estilos se puede cargar y analizar con el [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) clase antes de que se agrega a un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

El [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propiedad especifica la hoja de estilos como un URI relativo a la ubicación del archivo XAML envolvente o en relación con la raíz del proyecto si el URI comienza con un `/`.

> [!WARNING]
> El archivo CSS no se cargará si su acción de compilación no está establecido en **EmbeddedResource**.

Como alternativa, puede cargar y analizar con una hoja de estilos el [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) (clase), antes de que se agrega a un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), por inserción en un `CDATA` sección:

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

Para obtener más información acerca de los diccionarios de recursos, consulte [los diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C\#

En C#, una hoja de estilos se puede cargar desde `StringReader` un y agregar a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)un:

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

El argumento para el `StyleSheet.FromReader` método es el `TextReader` que leyó la hoja de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Selección de elementos y aplicación de propiedades

CSS usa los selectores para determinar qué elementos de destino. Estilos con coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar. Para obtener más información acerca de los selectores compatibles, consulte [Selector de referencia](#selector-reference).

CSS usa las propiedades para definir el estilo de un elemento seleccionado. Cada propiedad tiene un conjunto de valores posibles, y algunas propiedades pueden afectar a cualquier tipo de elemento, mientras que otras se aplican a los grupos de elementos. Para obtener más información acerca de las propiedades admitidas, consulte [referencia de propiedad](#property-reference).

### <a name="selecting-elements-by-type"></a>Selección de elementos por tipo

Los elementos en el árbol visual pueden seleccionarse por tipo con las mayúsculas y minúsculas `element` selector:

```css
stacklayout {
    margin: 20;
}
```

Este selector identifica cualquier [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos en las páginas que consumen la hoja de estilos y establecen sus márgenes para un grosor uniforme de 20.

> [!NOTE]
> El `element` selector no identifica las subclases del tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selección de elementos mediante la clase base

Se pueden seleccionar elementos en el árbol visual mediante la clase base con las mayúsculas y minúsculas `^base` selector:

```css
^contentpage {
    background-color: lightgray;
}
```

Este selector identifica cualquier [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) elementos que consumen la hoja de estilos y establece su fondo de color a `lightgray`.

> [!NOTE]
> El `^base` selector es específico de Xamarin.Forms y no forma parte de la especificación de CSS.

### <a name="selecting-an-element-by-name"></a>Seleccionar un elemento por nombre

Se pueden seleccionar elementos individuales en el árbol visual con las mayúsculas y minúsculas `#id` selector:

```css
#listView {
    background-color: lightgray;
}
```

Este selector identifica el elemento cuyo [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propiedad está establecida en `listView`. Sin embargo, si la `StyleId` no está establecida la propiedad, el selector se vuelven a usar el `x:Name` del elemento. Por lo tanto, en el siguiente ejemplo XAML, el `#listView` selector identificará la [ `ListView` ](xref:Xamarin.Forms.ListView) cuyo `x:Name` atributo está establecido en `listView`y establece su color de fondo en `lightgray`.

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

Se pueden seleccionar elementos con un atributo de clase específica con las mayúsculas y minúsculas `.class` selector:

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

Una clase CSS que puede asignarse a un elemento XAML estableciendo el [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) propiedad del elemento en el nombre de clase CSS. Por lo tanto, en el siguiente ejemplo XAML, los estilos definidos por el `.detailPageTitle` clase se asignan a la primera [ `Label` ](xref:Xamarin.Forms.Label), mientras que los estilos definidos por el `.detailPageSubtitle` clase se asignan a la segunda `Label`.

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

Se pueden seleccionar los elementos secundarios en el árbol visual con las mayúsculas y minúsculas `element element` selector:

```css
listview image {
    height: 60;
    width: 60;
}
```

Este selector identifica cualquier [ `Image` ](xref:Xamarin.Forms.Image) elementos secundarios de [ `ListView` ](xref:Xamarin.Forms.ListView) elementos y su alto y ancho se establece en 60. Por lo tanto, en el siguiente ejemplo XAML, el `listview image` selector identificará la [ `Image` ](xref:Xamarin.Forms.Image) que es un elemento secundario de la [ `ListView` ](xref:Xamarin.Forms.ListView)y su alto y ancho se establece en 60.

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
> El `element element` selector no requiere el elemento secundario sea un _directo_ secundario del miembro primario: el elemento secundario puede tener un elemento primario diferente. Selección se produce siempre que un nodo primario es el primer elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selección de elementos secundarios directos

Dirigir los elementos secundarios en el árbol visual se pueden seleccionar con las mayúsculas y minúsculas `element>element` selector:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este selector identifica cualquier [ `Image` ](xref:Xamarin.Forms.Image) directa de los elementos que son elementos secundarios de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos y su alto y ancho se establece en 200. Por lo tanto, en el siguiente ejemplo XAML, el `stacklayout>image` selector identificará la [ `Image` ](xref:Xamarin.Forms.Image) que es un elemento secundario directo de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)y establece el alto y ancho en 200.

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
> El `element>element` selector requiere que el elemento secundario es un _directo_ secundario del miembro primario.

## <a name="selector-reference"></a>Referencia de selector

Xamarin.Forms admite los siguientes selectores de CSS:

|Selector|Ejemplo|Descripción|
|---|---|---|
|`.class`|`.header`|Selecciona todos los elementos con el `StyleClass` propiedad que contiene "header". Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`#id`|`#email`|Selecciona todos los elementos con `StyleId` establecido en `email`. Si `StyleId` no se establece, el retroceso a `x:Name`. Cuando se usa XAML, `x:Name` es preferible `StyleId`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`*`|`*`|Selecciona todos los elementos.|
|`element`|`label`|Selecciona todos los elementos de `Label`tipo, pero no las subclases. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`^base`|`^contentpage`|Selecciona todos los elementos con `ContentPage` como clase base, incluyendo `ContentPage` propio. Tenga en cuenta que este selector distingue mayúsculas de minúsculas y no forma parte de la especificación de CSS.|
|`element,element`|`label,button`|Todos los selecciona `Button` elementos y todos `Label` elementos. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element element`|`stacklayout label`|Todos los selecciona `Label` elementos dentro de un `StackLayout`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element>element`|`stacklayout>label`|Todos los selecciona `Label` elementos con `StackLayout` como elemento primario directo. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element+element`|`label+entry`|Todos los selecciona `Entry` elementos directamente detrás de un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`element~element`|`label~entry`|Todos los selecciona `Entry` elementos precedido por un `Label`. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|

Estilos con coincidencia de los selectores se aplican de forma consecutiva, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar.

> [!TIP]
> Los selectores se pueden combinar sin limitación, tales como `StackLayout>ContentView>label.email`.

Se admite actualmente los siguientes selectores:

- `[attribute]`
- `@media` y `@supports`
- `:` y `::`

> [!NOTE]
> No se admiten especificidad e invalidaciones de especificidad.

## <a name="property-reference"></a>Referencia de propiedad

Las siguientes propiedades CSS son compatibles con Xamarin.Forms (en el **valores** son de tipos de columna, _cursiva_, mientras que los literales de cadena son `gray`):

|Property|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_Color_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Cadena_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_Color_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_Doble_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_Doble_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_Color_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_Doble_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Además, se puede especificar un porcentaje en el intervalo de 0 a 100% con el `%` inicio de sesión.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_Float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_Float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Cadena_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Double_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Doble_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`, `Span`|_Doble_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_Grosor_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Grosor_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Grosor_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Grosor_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Grosor_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_Int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_Doble_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Doble_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Doble_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_Int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_Grosor_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_Doble_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _Doble_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _Doble_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _Doble_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _Doble_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` y `right` deben evitarse en entornos de derecha a izquierda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _dobles_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_Doble_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` es un valor válido para todas las propiedades. Borra el valor (restablece en el valor predeterminado) que se ha establecido de otro estilo.

Las siguientes propiedades no se admiten actualmente:

- `all: initial`.
- Propiedades de diseño (cuadro o cuadrícula).
- Propiedades de la forma abreviada, como `font`, y `border`.

Además, no hay ningún `inherit` herencia del valor de modo que no se admite. Por lo tanto, por ejemplo, no es posible, establezca el `font-size` propiedad en un diseño y esperar que todos los [ `Label` ](xref:Xamarin.Forms.Label) instancias en el diseño para heredar el valor. La única excepción es el `direction` propiedad, que tiene un valor predeterminado de `inherit`.

### <a name="xamarinforms-specific-properties"></a>Propiedades específicas de Xamarin. Forms

También se admiten las siguientes propiedades CSS específicas de Xamarin.Forms (en el **valores** son de tipos de columna, _cursiva_, mientras que los literales de cadena son `gray`):

|Property|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_texto entre comillas_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_Color_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`, `Editor`|_Int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_Color_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_Color_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` solo se admite en un `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_Color_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_Color_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_Color_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_Doble_ \| `initial` |`-xf-spacing: 8;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propiedades específicas del shell de Xamarin. Forms

También se admiten las siguientes propiedades CSS específicas del shell de Xamarin. Forms (en la columna **valores** , los tipos son _cursiva_, `gray`mientras que los literales de cadena son):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_Color_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_Color_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_Color_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_Color_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_Color_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_Color_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_Color_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_Color_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_Color_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_Color_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_Color_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

La siguiente `color` se admiten valores:

- `X11` [colores](https://en.wikipedia.org/wiki/X11_color_names/), que coincide con los colores CSS, los colores predefinidos de UWP y Xamarin.Forms colores. Tenga en cuenta que estos valores de color distinguen mayúsculas de minúsculas.
- hex colores: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- los colores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Los valores son en el intervalo 0-255, o 0-100%.
- los colores RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.
- colores HSL: `hsl(120, 100%, 50%)`. El valor h está en el intervalo 0-360, mientras s y l están en el intervalo 0-100%.
- colores de HSLA: `hsla(120, 100%, 50%, .8)`. Es el valor de opacidad en el intervalo de 0,0 a 1,0.

### <a name="thickness"></a>Thickness

Uno, dos, tres o cuatro `thickness` se admiten valores, separados por espacios en blanco:

- Un único valor indica el grosor uniforme.
- Dos valores indican grosor vertical y horizontal.
- Estos tres valores indican la parte superior, horizontal (izquierda y derecha) y, luego, grosor de la parte inferior.
- Cuatro valores indican la parte superior, derecha y, a continuación, inferior y, luego, grosor de la izquierda.

> [!NOTE]
> CSS `thickness` valores difieren XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness) valores. Por ejemplo, en XAML, un valor de dos `Thickness` indica el grosor horizontal y vertical, mientras que un valor de cuatro `Thickness` indica izquierda, superior y, luego, derecha, abajo, a continuación, grosor. Además, XAML `Thickness` valores están delimitados por comas.

### <a name="namedsize"></a>NamedSize

Lo siguiente entre mayúsculas y minúsculas `namedsize` se admiten valores:

- `default`
- `micro`
- `small`
- `medium`
- `large`

El significado exacto de cada `namedsize` valor es dependiente de la plataforma y dependientes de la vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS en Xamarin.Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vídeo de CSS de Xamarin. Forms 3,0**

## <a name="related-links"></a>Vínculos relacionados

- [MonkeyAppCSS (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
