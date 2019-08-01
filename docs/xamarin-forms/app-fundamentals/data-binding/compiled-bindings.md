---
title: Enlaces compilados de Xamarin.Forms
description: En este artículo se explica cómo usar enlaces compilados para mejorar el rendimiento del enlace de datos en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 8effabc66b51b1cf32e3be455428c44eeea3f26d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656223"
---
# <a name="xamarinforms-compiled-bindings"></a>Enlaces compilados de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Los enlaces compilados se resuelven más rápidamente que los enlaces clásicos, lo cual mejora el rendimiento del enlace de datos en las aplicaciones de Xamarin.Forms._

Los enlaces de datos tienen dos problemas principales:

1. No hay ninguna validación de las expresiones de enlace en tiempo de compilación. Alternativamente, los enlaces se resuelven en tiempo de ejecución. Por lo tanto, los enlaces no válidos no se detectan hasta el tiempo de ejecución, cuando la aplicación no se comporta según lo esperado o aparecen mensajes de error.
1. No son rentables. Los enlaces se resuelven en tiempo de ejecución mediante la inspección de objetos de uso general (reflejo); el trabajo adicional que supone llevarlo a cabo varía en función de la plataforma.

Los enlaces compilados mejoran el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms mediante la resolución de expresiones de enlace en tiempo de compilación en lugar de en tiempo de ejecución. Además, esta validación en tiempo de compilación de expresiones de enlace permite una mejor experiencia de solución de problemas, porque los enlaces no válidos se notifican como errores de compilación.

El proceso para usar enlaces compilados es el siguiente:

1. Habilite la compilación XAML. Para obtener más información acerca de la compilación XAML, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilación XAML).
1. Establezca un atributo `x:DataType` de un elemento [`VisualElement`](xref:Xamarin.Forms.VisualElement) para el tipo del objeto al cual `VisualElement` y sus elementos secundarios se enlazará. Tenga en cuenta que este atributo puede volver a definirse en cualquier ubicación en una jerarquía de vistas.

> [!NOTE]
> Se recomienda establecer el atributo `x:DataType` en el mismo nivel de la jerarquía de vistas en que está establecido [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext).

En tiempo de compilación XAML, las expresiones de enlace no válidas se notificarán como errores de compilación. Sin embargo, el compilador XAML solo notificará un error de compilación para la primera expresión de enlace no válida que encuentre. Las expresiones de enlace válidas que se definen en `VisualElement` o en sus elementos secundarios se compilarán, independientemente de si [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) está establecido en XAML o en código. La compilación de una expresión de enlace genera un código compilado que obtendrá un valor de una propiedad en el *origen* y lo establecerá en la propiedad en el *destino* que se especifica en el marcado. Además, dependiendo de la expresión de enlace, el código generado puede observar cambios en el valor de la propiedad de *origen* y actualizar la propiedad de *destino*, y puede insertar los cambios desde el *destino* de nuevo al *origen*.

> [!IMPORTANT]
> Los enlaces compilados actualmente están deshabilitados para las expresiones de enlace que definen la propiedad [`Source`](xref:Xamarin.Forms.Binding.Source). Esto es así porque la propiedad `Source` siempre se establece mediante la extensión de marcado `x:Reference`, que no se puede resolver en tiempo de compilación.

## <a name="using-compiled-bindings"></a>Uso de enlaces compilados

En la página **Compiled Color Selector** (Selector de colores compilados) se muestra el uso de enlaces compilados entre las vistas de Xamarin.Forms y las propiedades de ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

El elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) raíz crea una instancia de `HslColorViewModel` e inicializa la propiedad `Color` dentro de las etiquetas de elemento de propiedad para la propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). El elemento `StackLayout` raíz también define el atributo `x:DataType` como el tipo ViewModel, lo cual indica que todas las expresiones de enlace en la jerarquía de vistas `StackLayout` raíz se compilarán. Esto se puede comprobar cambiando cualquiera de las expresiones de enlace para enlazar a una propiedad ViewModel inexistente, lo cual generará a un error de compilación.

> [!IMPORTANT]
> El atributo `x:DataType` puede volver a definirse en cualquier punto de una jerarquía de vistas.

Los elementos [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) y las vistas [`Slider`](xref:Xamarin.Forms.Slider) heredan el contexto de enlace del elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Todas estas vistas son destinos de enlace que hacen referencia a las propiedades de origen en ViewModel. Para la propiedad [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) y la propiedad [`Label.Text`](xref:Xamarin.Forms.Label.Text), los enlaces de datos son `OneWay`; las propiedades de las vista se establecen a partir de las propiedades en ViewModel. Sin embargo, la propiedad [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) utiliza un enlace `TwoWay`. Esto permite que cada `Slider` se establezca a partir de ViewModel, y que ViewModel se establezca a partir de cada `Slider`.

Cuando la aplicación se ejecuta por primera vez, los elementos [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label), y los elementos [`Slider`](xref:Xamarin.Forms.Slider) están establecidos a partir de ViewModel en base a la propiedad `Color` inicial establecida cuando se creó una instancia de ViewModel. Esto se muestra en la captura de pantalla siguiente:

[![Selector de colores compilados](compiled-bindings-images/compiledcolorselector-small.png "Compiled Color Selector")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Compiled Color Selector")

A medida que se manipulan los controles deslizantes, los elementos [`BoxView`](xref:Xamarin.Forms.BoxView) y [`Label`](xref:Xamarin.Forms.Label) se actualizan del modo correspondiente.

Para obtener más información acerca de este selector de colores, consulte [ViewModels and Property-Change Notifications](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications) (ViewModels y las notificaciones de cambio de propiedad).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Uso de enlaces compilados en DataTemplate

Los enlaces en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se interpretan en el contexto del objeto del cual se crea la plantilla. Por lo tanto, cuando utilice enlaces de compilación en `DataTemplate`, `DataTemplate` debe declarar el tipo de su objeto de datos mediante el atributo `x:DataType`.

En la página **Compiled Color List** (Lista de colores compilados) se muestra el uso de enlaces compilados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

La propiedad [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) está establecida en la propiedad `NamedColor.All` estática. La clase `NamedColor` usa la fijación de .NET para enumerar todos los campos públicos estáticos en la estructura de [`Color`](xref:Xamarin.Forms.Color) y almacenarlos con sus nombres en una colección que sea accesible desde la propiedad `All` estática. Por lo tanto, `ListView` se rellena con todas las instancias de `NamedColor`. Para cada elemento de `ListView`, el contexto de enlace para el elemento está establecido en un objeto `NamedColor`. Los elementos [`BoxView`](xref:Xamarin.Forms.BoxView) y [`Label`](xref:Xamarin.Forms.Label) de [`ViewCell`](xref:Xamarin.Forms.ViewCell) están enlazados a propiedades `NamedColor`.

Tenga en cuenta que [`x:DataType``NamedColor` define el atributo `DataTemplate` para ser del tipo `DataTemplate`, lo cual indica que todas las expresiones de enlace en la jerarquía de vistas ](xref:Xamarin.Forms.DataTemplate) se compilarán. Esto se puede comprobar cambiando cualquiera de las expresiones de enlace para enlazar a una propiedad `NamedColor` inexistente, lo cual generará a un error de compilación.

Cuando la aplicación se ejecuta por primera vez, [`ListView`](xref:Xamarin.Forms.ListView) se rellena con instancias de `NamedColor`. Cuando un elemento de `ListView` está seleccionado, la propiedad [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) se establece en el color del elemento seleccionado en `ListView`:

[![Lista de colores compilados](compiled-bindings-images/compiledcolorlist-small.png "Compiled Color List]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Al seleccionar otros elementos de [`ListView`](xref:Xamarin.Forms.BoxView) se actualiza el color de [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Combinación de enlaces compilados con enlaces clásicos

Las expresiones de enlace solo se compilan para la jerarquía de vistas en la cual está definido el atributo `x:DataType`. Por contra, todas las vistas en una jerarquía en la cual el atributo `x:DataType` no esté definido utilizarán enlaces clásicos. Por lo tanto, es posible combinar enlaces compilados y enlaces clásicos en una página. Por ejemplo, en la sección anterior, las vistas dentro de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) utilizan enlaces compilados, mientras que el elemento [`BoxView`](xref:Xamarin.Forms.BoxView) que se establece en el color seleccionado en [`ListView`](xref:Xamarin.Forms.ListView), no lo hace.

Una estructuración cuidadosa de atributos `x:DataType`, por lo tanto, puede conseguir que una página utilice enlaces compilados y clásicos. De forma alternativa, el atributo `x:DataType` se puede volver a definir en cualquier punto en una jerarquía de vistas para `null` utilizando la extensión de marcado `x:Null`. Esto indica que las expresiones de enlace dentro de la jerarquía de vistas utilizarán enlaces clásicos. La página *Mixed Bindings* (Enlaces mixtos) muestra este enfoque:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

El elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) raíz establece el atributo `x:DataType` para ser del tipo `HslColorViewModel`, lo cual indica que todas las expresiones de enlace en la jerarquía de vistas `StackLayout` se compilarán. Sin embargo, el `StackLayout` interno redefine el atributo `x:DataType` en `null` con la expresión de marcado `x:Null`. Por lo tanto, las expresiones de enlace dentro del `StackLayout` interno usan enlaces clásicos. Solo [`BoxView`](xref:Xamarin.Forms.BoxView), dentro de la jerarquía de vistas `StackLayout` raíz, utiliza enlaces compilados.

Para obtener más información acerca de la expresión de marcado `x:Null`, consulte [x:Null Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#null) (Extensión de marcado x:Null).

## <a name="performance"></a>Rendimiento

Los enlaces compilados mejoran el rendimiento del enlace de datos, con unas ventajas de rendimiento variables. Las pruebas de unidades muestran lo siguiente:

- Un enlace compilado que utiliza la notificación de cambio de propiedad (es decir, un enlace `OneWay`, `OneWayToSource` o `TwoWay`) se resuelve aproximadamente 8 veces más rápidamente que un enlace clásico.
- Un enlace compilado que no utiliza la notificación de cambio de propiedad (es decir, un enlace `OneTime`) se resuelve aproximadamente 20 veces más rápidamente que un enlace clásico.
- El establecimiento de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) en un enlace compilado que utiliza la notificación de cambio de propiedad (es decir, un enlace `OneWay`, `OneWayToSource` o `TwoWay`) se resuelve aproximadamente 5 veces más rápidamente que el establecimiento de `BindingContext` en un enlace clásico.
- El establecimiento de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) en un enlace compilado que no utiliza la notificación de cambio de propiedad (es decir, un enlace `OneTime`) se resuelve aproximadamente 7 veces más rápidamente que el establecimiento de `BindingContext` en un enlace clásico.

Estas diferencias de rendimiento se pueden ampliar en dispositivos móviles, dependiendo de la plataforma que se utilice, la versión del sistema operativo que se utilice y el dispositivo en el que se ejecute la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Demos de enlace de datos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
