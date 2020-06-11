---
title: "el estilo de Xamarin.Forms aplicaciones con hojas de estilo CSS (CSS)" Description: " Xamarin.Forms admite el estilo de los elementos visuales mediante hojas de estilo CSS (CSS)".
MS. Prod: Xamarin ms. AssetID: C89D57A6-DAB9-4C42-963F-26D67627DDC2 ms. Technology: Xamarin-Forms ms. Custom: Xamu-video Author: davidbritch ms. Author: dabritch ms. Date: 04/17/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicación de estilos a Xamarin.Forms aplicaciones mediante hojas de estilo CSS (CSS)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin. Forms admite el estilo de los elementos visuales mediante Hojas de estilo CSS (CSS)._

Xamarin.Formslas aplicaciones pueden tener estilo mediante CSS. Una hoja de estilos se compone de una lista de reglas, donde cada regla consta de uno o varios selectores y un bloque de declaración. Un bloque de declaración se compone de una lista de declaraciones entre llaves, donde cada declaración consta de una propiedad, un signo de dos puntos y un valor. Cuando hay varias declaraciones en un bloque, se inserta un punto y coma como separador. En el ejemplo de código siguiente se muestra alguna Xamarin.Forms CSS compatible:

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

En Xamarin.Forms , las hojas de estilos CSS se analizan y evalúan en tiempo de ejecución, en lugar de en tiempo de compilación, y las hojas de estilos se vuelven a analizar en el uso.

> [!NOTE]
> Actualmente, todo el estilo que es posible con el estilo XAML no se puede realizar con CSS. Sin embargo, los estilos XAML se pueden usar para complementar CSS para propiedades que actualmente no admite Xamarin.Forms . Para obtener más información sobre los estilos XAML, vea [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

En el ejemplo [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) se muestra cómo usar CSS para aplicar estilo a una aplicación sencilla y se muestra en las siguientes capturas de pantallas:

[![Página principal de MonkeyApp con estilo CSS](css-images/MonkeyAppMainPage.png "Página principal de MonkeyApp con estilo CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Página principal de MonkeyApp con estilo CSS")

[![Página de detalles de MonkeyApp con estilo CSS](css-images/MonkeyAppDetailPage.png "Página de detalles de MonkeyApp con estilo CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Página de detalles de MonkeyApp con estilo CSS")

## <a name="consuming-a-style-sheet"></a>Consumir una hoja de estilos

El proceso para agregar una hoja de estilos a una solución es el siguiente:

1. Agregue un archivo CSS vacío al proyecto de biblioteca de .NET Standard.
1. Establezca la acción de compilación del archivo CSS en **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Cargar una hoja de estilos

Existen varios enfoques que se pueden usar para cargar una hoja de estilos.

> [!NOTE]
> Actualmente no es posible cambiar una hoja de estilos en tiempo de ejecución y aplicar la nueva hoja de estilos.

### <a name="xaml"></a>XAML

Una hoja de estilos se puede cargar y analizar con la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) clase antes de que se agregue a un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

La [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propiedad especifica la hoja de estilos como un URI relativo a la ubicación del archivo XAML envolvente, o relativa a la raíz del proyecto si el identificador URI comienza con `/` .

> [!WARNING]
> El archivo CSS no se cargará si su acción de compilación no está establecida en **EmbeddedResource**.

Como alternativa, se puede cargar y analizar una hoja de estilos con la [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) clase, antes de agregarla a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , si se inserta en una `CDATA` sección:

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

En C#, una hoja de estilos se puede cargar desde un `StringReader` y agregar a un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

El argumento para el `StyleSheet.FromReader` método es el `TextReader` que ha leído la hoja de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Seleccionar elementos y aplicar propiedades

CSS usa selectores para determinar los elementos de destino. Los estilos con selectores coincidentes se aplican consecutivamente, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar. Para obtener más información sobre los selectores admitidos, vea [Referencia del selector](#selector-reference).

CSS utiliza las propiedades para aplicar estilo a un elemento seleccionado. Cada propiedad tiene un conjunto de valores posibles y algunas propiedades pueden afectar a cualquier tipo de elemento, mientras que otros se aplican a grupos de elementos. Para obtener más información sobre las propiedades admitidas, vea [referencia de propiedades](#property-reference).

> [!IMPORTANT]
> No se admiten las variables CSS.

### <a name="selecting-elements-by-type"></a>Seleccionar elementos por tipo

Los elementos del árbol visual se pueden seleccionar por tipo con el selector sin distinción de mayúsculas y minúsculas `element` :

```css
stacklayout {
    margin: 20;
}
```

Este selector identifica los [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementos de las páginas que utilizan la hoja de estilos y establece sus márgenes en un grosor uniforme de 20.

> [!NOTE]
> El `element` selector no identifica las subclases del tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Seleccionar elementos por clase base

Los elementos del árbol visual se pueden seleccionar por clase base con el selector sin distinción de mayúsculas y minúsculas `^base` :

```css
^contentpage {
    background-color: lightgray;
}
```

Este selector identifica los [`ContentPage`](xref:Xamarin.Forms.ContentPage) elementos que utilizan la hoja de estilos y establece su color de fondo en `lightgray` .

> [!NOTE]
> El `^base` selector es específico de Xamarin.Forms y no forma parte de la especificación de CSS.

### <a name="selecting-an-element-by-name"></a>Seleccionar un elemento por su nombre

Los elementos individuales del árbol visual se pueden seleccionar con el selector distinguir mayúsculas de minúsculas `#id` :

```css
#listView {
    background-color: lightgray;
}
```

Este selector identifica el elemento cuya [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propiedad está establecida en `listView` . Sin embargo, si `StyleId` no se establece la propiedad, el selector volverá a usar el `x:Name` del elemento. Por consiguiente, en el siguiente ejemplo de XAML, el `#listView` selector identificará el [`ListView`](xref:Xamarin.Forms.ListView) cuyo `x:Name` atributo está establecido en `listView` y establecerá el color de fondo en `lightgray` .

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Seleccionar elementos con un atributo de clase específico

Los elementos con un atributo de clase específico se pueden seleccionar con el selector que distingue mayúsculas de minúsculas `.class` :

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

Una clase CSS se puede asignar a un elemento XAML estableciendo la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propiedad del elemento en el nombre de clase CSS. Por lo tanto, en el siguiente ejemplo de XAML, los estilos definidos por la `.detailPageTitle` clase se asignan al primero [`Label`](xref:Xamarin.Forms.Label) , mientras que los estilos definidos por la `.detailPageSubtitle` clase se asignan a la segunda `Label` .

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

### <a name="selecting-child-elements"></a>Seleccionar elementos secundarios

Los elementos secundarios del árbol visual se pueden seleccionar con el selector sin distinción de mayúsculas y minúsculas `element element` :

```css
listview image {
    height: 60;
    width: 60;
}
```

Este selector identifica los [`Image`](xref:Xamarin.Forms.Image) elementos que son elementos secundarios de [`ListView`](xref:Xamarin.Forms.ListView) los elementos y establece su alto y ancho en 60. Por lo tanto, en el siguiente ejemplo de XAML, el `listview image` selector identificará el [`Image`](xref:Xamarin.Forms.Image) que es un elemento secundario de y [`ListView`](xref:Xamarin.Forms.ListView) establecerá el alto y el ancho en 60.

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
> El `element element` selector no requiere que el elemento secundario sea un elemento secundario _directo_ del elemento primario: el elemento secundario puede tener un elemento primario diferente. La selección se produce siempre que un antecesor es el primer elemento especificado.

### <a name="selecting-direct-child-elements"></a>Seleccionar elementos secundarios directos

Los elementos secundarios directos del árbol visual se pueden seleccionar con el selector sin distinción de mayúsculas y minúsculas `element>element` :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este selector identifica los [`Image`](xref:Xamarin.Forms.Image) elementos que son elementos secundarios directos de [`StackLayout`](xref:Xamarin.Forms.StackLayout) los elementos y establece su alto y ancho en 200. Por lo tanto, en el siguiente ejemplo de XAML, el `stacklayout>image` selector identificará el [`Image`](xref:Xamarin.Forms.Image) que es un elemento secundario directo de [`StackLayout`](xref:Xamarin.Forms.StackLayout) y establecerá el alto y el ancho en 200.

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
> El `element>element` selector requiere que el elemento secundario sea un elemento secundario _directo_ del elemento primario.

## <a name="selector-reference"></a>Referencia del selector

Admite los siguientes selectores de CSS Xamarin.Forms :

|Selector|Ejemplo|Descripción|
|---|---|---|
|`.class`|`.header`|Selecciona todos los elementos con la `StyleClass` propiedad que contiene ' header '. Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`#id`|`#email`|Selecciona todos los elementos con el `StyleId` valor establecido en `email` . Si `StyleId` no se establece, fallback en `x:Name` . Cuando se usa XAML, `x:Name` se prefiere `StyleId` . Tenga en cuenta que este selector distingue mayúsculas de minúsculas.|
|`*`|`*`|Selecciona todos los elementos.|
|`element`|`label`|Selecciona todos los elementos de tipo `Label` , pero no las subclases. Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|
|`^base`|`^contentpage`|Selecciona todos los elementos con `ContentPage` como clase base, incluido a `ContentPage` sí mismo. Tenga en cuenta que este selector no distingue entre mayúsculas y minúsculas y no forma parte de la especificación de CSS.|
|`element,element`|`label,button`|Selecciona todos los `Button` elementos y todos los `Label` elementos. Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|
|`element element`|`stacklayout label`|Selecciona todos los `Label` elementos dentro de `StackLayout` . Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|
|`element>element`|`stacklayout>label`|Selecciona todos los `Label` elementos con `StackLayout` como elemento primario directo. Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|
|`element+element`|`label+entry`|Selecciona todos los `Entry` elementos directamente después de `Label` . Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|
|`element~element`|`label~entry`|Selecciona todos los `Entry` elementos precedidos por un `Label` . Tenga en cuenta que este selector no distingue mayúsculas de minúsculas.|

Los estilos con selectores coincidentes se aplican consecutivamente, en orden de definición. Los estilos definidos en un elemento específico siempre se aplican en último lugar.

> [!TIP]
> Los selectores se pueden combinar sin limitación, como `StackLayout>ContentView>label.email` .

Actualmente no se admiten los siguientes selectores:

- `[attribute]`
- `@media` y `@supports`
- `:` y `::`

> [!NOTE]
> La especificidad y las invalidaciones de especificidad no se admiten.

## <a name="property-reference"></a>Referencia de propiedades

Las siguientes propiedades CSS son compatibles con Xamarin.Forms (en la columna **valores** , los tipos están en _cursiva_, mientras que los literales de cadena son `gray` ):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_color_ \| de`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_cadena_ \|`initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_color_ \| de`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_doble_ \|`initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_doble_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_color_ \| de`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_doble_ \|`initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|valor _float_ \| `auto` \| `initial`. Además, se puede especificar un porcentaje en el intervalo comprendido entre 0% y 100% con el `%` signo.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|valor _float_ \|`initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|valor _float_ \|`initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_cadena_ \|`initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_doble_ \| _namedsize_ \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_doble_ \|`initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_doble_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_doble_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_grosor_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_grosor_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_grosor_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_grosor_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_grosor_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_doble_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_doble_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_doble_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_grosor_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_doble_ \|`initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _doble_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _doble_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _doble_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _doble_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`y `right` deben evitarse en entornos de derecha a izquierda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, _Double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_doble_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial`es un valor válido para todas las propiedades. Borra el valor (restablece al valor predeterminado) que se estableció desde otro estilo.

Actualmente no se admiten las siguientes propiedades:

- `all: initial`.
- Propiedades de diseño (cuadro o cuadrícula).
- Propiedades abreviadas, como `font` y `border` .

Además, no hay ningún `inherit` valor y, por tanto, no se admite la herencia. Por lo tanto, no es posible, por ejemplo, establecer la `font-size` propiedad en un diseño y esperar que todas las [`Label`](xref:Xamarin.Forms.Label) instancias del diseño hereden el valor. La única excepción es la `direction` propiedad, que tiene un valor predeterminado de `inherit` .

`Span`Los elementos de destino tienen un problema conocido que evita que los intervalos sean el destino de los estilos CSS por elemento y nombre (mediante el `#` símbolo). El `Span` elemento se deriva de `GestureElement` , que no tiene la `StyleClass` propiedad, por lo que los intervalos no admiten el destino de clase CSS. Para obtener más información, vea [no se puede aplicar el estilo CSS al control span](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Xamarin.Formspropiedades específicas

Xamarin.FormsTambién se admiten las siguientes propiedades CSS específicas (en la columna **valores** , los tipos son _cursiva_, mientras que los literales de cadena son `gray` ):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_color_ \| de`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_color_ \| de`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_color_ \| de`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_color_ \| de`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`solo se admite en `ScrollView` . |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|texto entre comillas _quoted text_ \|`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_color_ \| de`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_doble_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_color_ \| de`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_cadena_ \|`initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin.FormsPropiedades específicas del shell

Xamarin.FormsTambién se admiten las siguientes propiedades CSS específicas del shell (en la columna **valores** , los tipos son _cursiva_, mientras que los literales de cadena son `gray` ):

|Propiedad|Se aplica a|Valores|Ejemplo|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_color_ \| de`initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_color_ \| de`initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_color_ \| de`initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_color_ \| de`initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_color_ \| de`initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_color_ \| de`initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_color_ \| de`initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_color_ \| de`initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_color_ \| de`initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_color_ \| de`initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_color_ \| de`initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

`color`Se admiten los siguientes valores:

- `X11`[colores](https://en.wikipedia.org/wiki/X11_color_names), que coinciden con los colores CSS, los colores predefinidos de UWP y los Xamarin.Forms colores. Tenga en cuenta que estos valores de color no distinguen mayúsculas de minúsculas.
- colores hex: `#rgb` , `#argb` , `#rrggbb` ,`#aarrggbb`
- colores RGB: `rgb(255,0,0)` , `rgb(100%,0%,0%)` . Los valores están en el intervalo 0-255 o 0%-100%.
- colores RGBA: `rgba(255, 0, 0, 0.8)` , `rgba(100%, 0%, 0%, 0.8)` . El valor de opacidad está en el intervalo 0.0-1.0.
- HSL Colors: `hsl(120, 100%, 50%)` . El valor h está en el intervalo 0-360, mientras que s y l están en el intervalo 0%-100%.
- colores de HSLA: `hsla(120, 100%, 50%, .8)` . El valor de opacidad está en el intervalo 0.0-1.0.

### <a name="thickness"></a>Thickness

Se admiten uno, dos, tres o cuatro `thickness` valores, cada uno separado por un espacio en blanco:

- Un valor único indica el grosor uniforme.
- Dos valores indican el grosor vertical y horizontal.
- Tres valores indican superior, después horizontal (izquierda y derecha) y, a continuación, el grosor inferior.
- Cuatro valores indican la parte superior, después, la parte inferior y, a continuación, el grosor izquierdo.

> [!NOTE]
> `thickness`Los valores CSS se diferencian de [`Thickness`](xref:Xamarin.Forms.Thickness) los valores XAML. Por ejemplo, en XAML, un valor de dos valores `Thickness` indica el grosor horizontal y, a continuación, el grosor vertical, mientras que un valor de cuatro valores `Thickness` indica izquierda, superior, derecha y bajo grosor inferior. Además, `Thickness` los valores XAML están delimitados por comas.

### <a name="namedsize"></a>NamedSize

Se admiten los siguientes valores que no distinguen mayúsculas de minúsculas `namedsize` :

- `default`
- `micro`
- `small`
- `medium`
- `large`

El significado exacto de cada valor depende de la `namedsize` plataforma y depende de la vista.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS en Xamarin.Forms con Xamarin. University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Formsvídeo de CSS 3,0**

## <a name="related-links"></a>Vínculos relacionados

- [MonkeyAppCSS (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
