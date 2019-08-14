---
title: Diseños de Xamarin.Forms
description: Xamarin.Forms tiene varios diseños y características para organizar el contenido en pantalla y en este artículo se explica a ellos.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 7d63dafbb17a3757f5d15f65e977b4b0cb8bc44a
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68980860"
---
# <a name="layouts-in-xamarinforms"></a>Diseños de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms tiene varios diseños y características para organizar el contenido en pantalla.

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Vídeo de diseños de Xamarin. Forms**

A continuación, se describe cada control de diseño, así como detalles sobre cómo controlar los cambios de orientación de pantalla:

* **[StackLayout](stack-layout.md)** : se usa para organizar las vistas linealmente, horizontal o verticalmente. Las vistas en un StackLayout se pueden alinear al centro, izquierdo o derecho del diseño.
* **[AbsoluteLayout](absolute-layout.md)** : se usa para organizar las vistas estableciendo coordenadas & tamaño en términos de valores absolutos o proporciones. AbsoluteLayout puede utilizarse para las vistas de capas como fijarlos a la izquierda, derecha o centrado.
* **[RelativeLayout](relative-layout.md)** : se usa para organizar las vistas estableciendo restricciones en relación con las dimensiones de sus elementos primarios & posición.
* **[Grid](grid.md)** : se usa para organizar las vistas en una cuadrícula. En cuanto a los valores absolutos o proporciones, se pueden especificar las filas y columnas.
* **[FlexLayout](flex-layout.md)** : se usa para organizar las vistas horizontal o verticalmente con ajuste.
* **[Marco](frame.md)** : se usa para ajustar una vista con un borde, o marco, que se puede configurar con color, sombra paralela, etc.
* **[ScrollView](scroll-view.md)** : se usa para proporcionar el desplazamiento cuando una vista no cabe por completo en los límites de la pantalla.
* **[LayoutOptions](layout-options.md)** : define la alineación y la expansión de una vista, con respecto a su elemento primario.
* **[Transparencia de entrada](#input_transparency)** : especifica si un elemento recibe una entrada.
* **[Margin y padding](margin-and-padding.md)** : muestra cómo controlar el comportamiento del diseño cuando un elemento se representa en la interfaz de usuario.
* **[Orientación del dispositivo](device-orientation.md)** : explica cómo controlar los cambios de orientación del dispositivo.
* **[Diseño en dispositivos de tableta y escritorio](tablet.md)** : muestra cómo optimizar para pantallas más grandes en cada plataforma.
* **[Diseños enlazables](bindable-layouts.md)** : permite que las clases de diseño generen su contenido enlazando a una colección de elementos.
* **[Crear un diseño personalizado](custom.md)** : explica cómo crear una clase de diseño personalizado.
* **[Compresión de diseño](layout-compression.md)** : quita el diseño especificado del árbol visual para intentar mejorar el rendimiento de la representación de páginas.

Controles de la plataforma también pueden usarse directamente en los diseños de Xamarin.Forms con [ **incrustación nativa** ](~/xamarin-forms/platform/native-views/index.md) (nuevo en Xamarin.Forms 2.2), y puede [ **crear diseños personalizados** ](custom.md) para satisfacer los requisitos específicos.

El gráfico siguiente muestra los controles de diseño:

[![](images/layouts-sml.png "Los diseños de Xamarin.Forms")](images/layouts.png#lightbox "los diseños de Xamarin.Forms")

## <a name="choosing-the-right-layout"></a>Elegir el diseño correcto

Los diseños que elija en la aplicación pueden ayudar a o perjudicarle dado que está creando una aplicación de Xamarin.Forms atractiva y utilizable. Tomar algún tiempo para pensar en cómo cada funciona de diseño puede ayudarle a escribir código de interfaz de usuario más limpio y más escalable. Una pantalla puede tener una combinación de diseños diferentes para lograr un diseño específico.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

El `StackLayout` se utiliza para mostrar las vistas a lo largo de una línea horizontal o vertical. Posición y el tamaño en el diseño se determina basándose en una vista `HeightRequest`, `WidthRequest`, `HorizontalOptions` y `VerticalOptions`. `StackLayout` a menudo se usa como el diseño de base, organizar otros diseños en la pantalla.

Para obtener un ejemplo de cuándo `StackLayout` podría ser una buena opción, considere la posibilidad de una aplicación que necesita para mostrar un botón y una etiqueta, con la etiqueta que se alinea a la izquierda y el botón alineado a la derecha.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

El `FlexLayout` es similar a `StackLayout` que muestra vistas secundarias horizontal o verticalmente:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Sin embargo, si hay demasiados elementos secundarios para que quepa en una sola fila o columna propio, `FlexLayout` también es capaz de ajuste de esas vistas. `FlexLayout` se basa en el módulo de diseño CSS cuadro Flexible y tiene muchas de las mismas opciones integradas para colocar y alinear a sus elementos secundarios.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

El `AbsoluteLayout` se utiliza para mostrar las vistas, con el tamaño y posición que se va a especificar como valores explícitos o en relación con el tamaño del diseño. A diferencia de `StackLayout` y `Grid`, `AbsoluteLayout` permite secundarios vistas se superponen. A diferencia de `RelativeLayout`, `AbsoluteLayout` no le permite colocar los elementos fuera de la pantalla.

Para obtener un ejemplo de cuándo `AbsoluteLayout` podría ser una buena opción, considere la posibilidad de una aplicación que necesita para presentar las colecciones de objetos como pilas. Esto suele aparecer cuando se presentan los álbumes de fotografías o canciones. El código siguiente proporciona la apariencia de una pila, con elementos girados para sugerir el contenido de la pila:

En XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Cada `Image` se muestra en la misma posición (en el centro del espacio horizontal)
- El `Padding` es considerado por `AbsoluteLayout`, a diferencia `RelativeLayout`, que pasa por alto.
- `AbsoluteLayout.LayoutFlags` Especifica cómo se interpretará los límites de diseño. En este caso `PositionProportional`, significa que las coordenadas será una proporción del tamaño del diseño, mientras que el tamaño se interpretará como un tamaño específico.
- `AbsoluteLayout.Layoutbounds` Especifica la posición horizontal, posición vertical, ancho y alto en ese orden.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

El `RelativeLayout` se utiliza para mostrar las vistas, con el tamaño y posición especificados como valores en relación con los valores de la distribución o en otra vista. Valores relativos no es necesario para que coincida con la que corresponde el valor en la vista relacionada. Por ejemplo, es posible establecer una vista `Width` propiedad sea proporcional a otra vista `X` propiedad.

RelativeLayout se puede usar para crear interfaces de usuario que se escalan de forma proporcional entre tamaños de los dispositivos. El XAML siguiente implementa un diseño con los cuadros en las esquinas superiores, con un asta de la bandera con la marca en el centro:

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Como las restricciones se especifican los tamaños y posiciones.
- Se denomina el asta de la bandera para que la marca (verde del cuadro) se puede establecer la posición en relación con el asta de la bandera.
- Las expresiones de restricción tienen `Factor` y `Constant` propiedades, que pueden utilizarse para definir las posiciones y tamaños como múltiplos (o fracciones de segundo) de las propiedades de otros objetos, además de una constante. Las constantes pueden ser negativas.

### <a name="gridgridmd"></a>[Grid](grid.md)

El `Grid` se utiliza para mostrar elementos en filas y columnas. Tenga en cuenta que la cuadrícula no es una tabla, por lo que no tiene el concepto de celdas, filas de encabezado y pie de página o bordes entre filas y columnas. En general, la cuadrícula no es adecuada para mostrar datos tabulares. Para que use, considere la posibilidad de un [ListView](~/xamarin-forms/user-interface/listview/index.md) o [TableView](~/xamarin-forms/user-interface/tableview.md).

Para obtener un ejemplo de cuándo una `Grid` es el diseño correcto para usar, considere la posibilidad de una entrada numérica para una calculadora. Una entrada numérica para una calculadora puede constar de cuatro filas y tres columnas, cada uno con un botón. El código siguiente implementa este diseño:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Las cuadrículas y las columnas se especifican explícitamente, no se inferirá del contenido.
- `Height` y `Width` valores pueden establecerse en estrella, lo que significa que la cuadrícula establecerá esos valores para rellenar el espacio disponible.
- Posición de cada botón se especifica mediante `Grid.Row`  &  `Grid.Column` propiedades.

### <a name="frameframemd"></a>[Frame](frame.md)

Un `Frame` diseño se usa para crear un borde personalizable alrededor de `View`los elementos secundarios. Se puede usar para colocar un borde alrededor de un control como `Button` o `Label` o puede ajustar otros diseños para crear objetos de interfaz de usuario más complejos, como una tarjeta. `Frame`

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

El [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estructura puede utilizarse para definir la alineación y la expansión de una vista, en relación con su elemento primario.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Margen y relleno](margin-and-padding.md)

El [ `Margin` ](xref:Xamarin.Forms.View.Margin) y [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedades controlan el comportamiento de diseño cuando se procesa un elemento en la interfaz de usuario.

<a name="input_transparency" />

### <a name="input-transparency"></a>Entrada de transparencia

Cada elemento tiene un [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propiedad que se usa para definir si el elemento recibe la entrada. Su valor predeterminado es `false`, lo que garantiza que el elemento recibe la entrada.

Cuando esta propiedad está establecida en una clase de contenedor, como una clase de diseño, las transferencias de su valor a los elementos secundarios. Por consiguiente, establecer el [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propiedad `true` en un diseño de clase dará como resultado de los elementos dentro del diseño no recibir datos de entrada.

### <a name="device-orientationdevice-orientationmd"></a>[Orientación del dispositivo](device-orientation.md)

Xamarin.Forms y sus diseños integrados son capaces de controlar los cambios de orientación del dispositivo. Considere la posibilidad de que las orientaciones que admitirá la aplicación, así como cómo realizará el espacio proporcionado en los modos vertical y horizontal.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Diseño para aplicaciones de tableta y escritorio](tablet.md)

iOS, Android y plataforma Universal de Windows todos admiten tamaños de pantalla más grandes en dispositivos de tableta (así como equipos portátiles y de escritorio para Windows). Xamarin.Forms le permite optimizar la aplicación para pantallas más grandes para detectar el tipo de dispositivo y el ajusta el diseño de página o usar una página totalmente diferente por completo para pantallas más grandes.

### <a name="bindable-layoutsbindable-layoutsmd"></a>[Diseños enlazables](bindable-layouts.md)

La `BindableLayout` clase permite que cualquier clase de diseño que derive de [`Layout<T>`](xref:Xamarin.Forms.Layout`1) la clase genere su contenido enlazando a una colección de elementos, con la opción [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)de establecer la apariencia de cada elemento con.

### <a name="creating-a-custom-layoutcustommd"></a>[Creación de un diseño personalizado](custom.md)

Xamarin.Forms define cuatro clases de diseño - [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout), y [ `Grid` ](xref:Xamarin.Forms.Grid), y cada uno de ellos organiza sus elementos secundarios de forma diferente. Sin embargo, a veces su necesarias para organizar el contenido de la página con un diseño no proporcionan Xamarin.Forms. En este artículo se explica cómo escribir una clase de diseño personalizado y muestra una orientación distinción `WrapLayout` clase que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, ajusta la presentación de los elementos secundarios subsiguientes a las filas adicionales.

### <a name="layout-compressionlayout-compressionmd"></a>[Compresión de diseño](layout-compression.md)

Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de representación de página. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

## <a name="making-your-choice"></a>Para facilitar su elección

Tenga en cuenta que en la mayoría de los casos, puede usar más de una opción de diseño para implementar el diseño deseado. Cuando hay varias opciones válidas, considere qué enfoque será más fácil para su situación.
No se pueden realizar la mayoría de los diseños con un solo diseño, por lo que los diseños de anidamiento como sean necesarios para crear diseños más complejos.

## <a name="related-links"></a>Vínculos relacionados

- [Directrices de interfaz humana de Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Sitio Web de diseño de Android](https://developer.android.com/design/index.html)
- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
