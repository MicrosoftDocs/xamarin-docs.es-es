---
title: Xamarin.FormsIndicatorView
description: ''
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6bf11fd80dd5226ae26dd392e80f784a9b296bf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131994"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.FormsIndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView`Es un control que muestra los indicadores que representan el número de elementos y la posición actual en un `CarouselView` :

[![Captura de pantalla de CarouselView y IndicatorView en iOS y Android](indicatorview-images/circles.png "Círculos IndicatorView")](indicatorview-images/circles-large.png#lightbox "Círculos IndicatorView")

`IndicatorView`está disponible en Xamarin.Forms 4,4 en las plataformas iOS y Android, y en 4,5 en el plataforma universal de Windows. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, o bien a la `MainActivity` clase en Android, antes de llamar a `Forms.Init` :

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` define las siguientes propiedades:

- `Count`, de tipo `int` , el número de indicadores.
- `HideSingle`, de tipo `bool` , indica si el indicador debe ocultarse cuando solo existe uno. El valor predeterminado es `true`.
- `IndicatorColor`, de tipo `Color` , el color de los indicadores.
- `IndicatorSize`, de tipo `double` , el tamaño de los indicadores. El valor predeterminado es 6,0.
- `IndicatorLayout`, de tipo `Layout<View>` , define la clase de diseño utilizada para representar `IndicatorView` . Esta propiedad está establecida por Xamarin.Forms y, por lo general, no es necesario que la configuren los desarrolladores.
- `IndicatorTemplate`, de tipo `DataTemplate` , la plantilla que define la apariencia de cada indicador.
- `IndicatorsShape`, de tipo `IndicatorShape` , la forma de cada indicador.
- `ItemsSource`, de tipo `IEnumerable` , la colección para la que se mostrarán los indicadores. Esta propiedad se establecerá automáticamente cuando `CarouselView.IndicatorView` se establezca la propiedad.
- `MaximumVisible`, de tipo `int` , el número máximo de indicadores visibles. El valor predeterminado es `int.MaxValue`.
- `Position`, de tipo `int` , el índice del indicador actualmente seleccionado. Esta propiedad usa un `TwoWay` enlace. Esta propiedad se establecerá automáticamente cuando `CarouselView.IndicatorView` se establezca la propiedad.
- `SelectedIndicatorColor`, de tipo `Color` , el color del indicador que representa el elemento actual del objeto `CarouselView` .

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

## <a name="create-an-indicatorview"></a>Creación de un IndicatorView

En el ejemplo siguiente se muestra cómo crear una instancia `IndicatorView` de en XAML:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

En este ejemplo, `IndicatorView` se representa debajo de `CarouselView` , con un indicador para cada elemento en `CarouselView` . `IndicatorView`Se rellena con datos estableciendo la `CarouselView.IndicatorView` propiedad en el `IndicatorView` objeto. Cada indicador es un círculo gris claro, mientras que el indicador que representa el elemento actual del `CarouselView` es gris oscuro.

> [!IMPORTANT]
> Al establecer la propiedad `CarouselView.IndicatorView` , se obtiene el enlace de la `IndicatorView.Position` propiedad a la `CarouselView.Position` propiedad y la `IndicatorView.ItemsSource` propiedad se enlaza a la `CarouselView.ItemsSource` propiedad.

## <a name="change-indicator-shape"></a>Cambiar forma de indicador

La `IndicatorView` clase tiene una `IndicatorsShape` propiedad, que determina la forma de los indicadores. Esta propiedad se puede establecer en uno de los `IndicatorShape` miembros de enumeración:

- `Circle`Especifica que las formas de indicador serán circulares. Este es el valor predeterminado de la propiedad `IndicatorView.IndicatorsShape`.
- `Square`indica que las formas de indicador serán cuadradas.

En el ejemplo siguiente se muestra una `IndicatorView` configurada para usar indicadores cuadrados:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Cambiar tamaño del indicador

La `IndicatorView` clase tiene una `IndicatorSize` propiedad, de tipo `double` , que determina el tamaño de los indicadores en unidades independientes del dispositivo. El valor predeterminado de esta propiedad es 6,0.

En el ejemplo siguiente se muestra una `IndicatorView` configurada para mostrar indicadores mayores:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limitar el número de indicadores mostrados

La `IndicatorView` clase tiene una `MaximumVisible` propiedad, de tipo `int` , que determina el número máximo de indicadores visibles.

En el ejemplo siguiente se muestra una `IndicatorView` configurada para mostrar un máximo de seis indicadores:

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Definir la apariencia del indicador

La apariencia de cada indicador puede definirse estableciendo la `IndicatorView.IndicatorTemplate` propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada indicador. En este ejemplo, cada indicador es un [`Image`](xref:Xamarin.Forms.Image) que muestra un icono de fuente mediante la `FontImage` extensión de marcado.

Las capturas de pantallas siguientes muestran los indicadores representados mediante un icono de fuente:

[![Captura de pantalla de un IndicatorView con plantilla, en iOS y Android](indicatorview-images/templated.png "IndicatorView con plantilla")](indicatorview-images/templated-large.png#lightbox "IndicatorView con plantilla")

Para obtener más información sobre la `FontImage` extensión de marcado, consulte [extensión de marcado FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Vínculos relacionados

- [IndicatorView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Extensión de marcado FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
