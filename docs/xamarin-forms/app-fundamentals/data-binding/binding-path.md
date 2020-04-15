---
title: Ruta de acceso de enlace de Xamarin.Forms
description: En este artículo se explica cómo usar los enlaces de datos de Xamarin.Forms para acceder a subpropiedades y miembros de colección con la propiedad Path de la clase Binding.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 79d8df3300b302512a7de4140968dbc4c8e79abc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940342"
---
# <a name="xamarinforms-binding-path"></a>Ruta de acceso de enlace de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

En todos los ejemplos de enlace de datos anteriores, la propiedad [`Path`](xref:Xamarin.Forms.Binding.Path) de la clase `Binding` (o la propiedad [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) de la extensión de marcado `Binding`) se había establecido en una sola propiedad. En realidad es posible establecer `Path` en una *subpropiedad* (una propiedad de una propiedad), o bien en un miembro de una colección.

Por ejemplo, suponga que la página contiene un control `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

La propiedad `Time` de `TimePicker` es de tipo `TimeSpan`, pero es posible que quiera crear un enlace de datos que haga referencia a la propiedad `TotalSeconds` de ese valor `TimeSpan`. Este es el enlace de datos:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

La propiedad `Time` es de tipo `TimeSpan`, que tiene una propiedad `TotalSeconds`. Las propiedades `Time` y `TotalSeconds` se conectan simplemente con un punto. Los elementos de la cadena `Path` siempre hacen referencia a propiedades y no a los tipos de estas propiedades.

Ese y otros muchos ejemplos se muestran en la página **Variaciones de la ruta de acceso**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=netstandard"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

En el segundo control `Label`, el origen de enlace es la propia página. La propiedad `Content` es de tipo `StackLayout`, que tiene una propiedad `Children` de tipo `IList<View>`, que a su vez tiene una propiedad `Count` que indica el número de elementos secundarios.

## <a name="paths-with-indexers"></a>Rutas de acceso con indizadores

El enlace en el tercer control `Label` de las páginas **Variaciones de la ruta de acceso** hace referencia a la clase [`CultureInfo`](xref:System.Globalization.CultureInfo) del espacio de nombres `System.Globalization`:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

El origen se establece en la propiedad estática `CultureInfo.CurrentCulture`, que es un objeto de tipo `CultureInfo`. Esa clase define una propiedad denominada `DateTimeFormat` de tipo [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) que contiene una colección `DayNames`. El índice selecciona el cuarto elemento.

El cuarto control `Label` hace algo similar pero para la referencia cultural asociada con Francia. La propiedad `Source` del enlace se establece en el objeto `CultureInfo` con un constructor:

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

Vea [Pasar argumentos de constructor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obtener más información sobre cómo especificar argumentos de constructor en XAML.

El último ejemplo es similar al segundo, salvo que hace referencia a uno de los elementos secundarios del elemento `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Ese elemento secundario es un control `Label`, que tiene una propiedad `Text` de tipo `String`, que a su vez tiene una propiedad `Length`. El primer objeto `Label` notifica el valor `TimeSpan` establecido en el elemento `TimePicker`, de modo que cuando se cambia el texto, el último objeto `Label` también cambia.

Esta es la ejecución del programa:

[![Variaciones de ruta de acceso](binding-path-images/pathvariations-small.png "Variaciones de ruta de acceso")](binding-path-images/pathvariations-large.png#lightbox "Variaciones de ruta de acceso")

## <a name="debugging-complex-paths"></a>Depuración de rutas de acceso complejas

Las definiciones de ruta de acceso complejas pueden ser difíciles de construir: necesita saber el tipo de cada subpropiedad o el tipo de los elementos de la colección para agregar correctamente la subpropiedad siguiente, pero los propios tipos no aparecen en la ruta de acceso. Una técnica adecuada consiste en crear la ruta de acceso de forma incremental y observar los resultados intermedios. Para ese último ejemplo, se podría empezar sin ninguna definición de `Path`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Eso muestra el tipo del origen de enlace, o `DataBindingDemos.PathVariationsPage`. Ya sabe que `PathVariationsPage` se deriva de `ContentPage`, por lo que tiene una propiedad `Content`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

Ahora, se revela que el tipo de la propiedad `Content` es `Xamarin.Forms.StackLayout`. Agregue la propiedad `Children` a `Path` y el tipo es `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que es una clase interna para Xamarin.Forms, pero obviamente un tipo de colección. Agregue un índice a lo anterior y el tipo será `Xamarin.Forms.Label`. Continúe de esta manera.

A medida que Xamarin.Forms procesa la ruta de acceso de enlace, instala un controlador `PropertyChanged` en todos los objetos de la ruta de acceso que implementen la interfaz `INotifyPropertyChanged`. Por ejemplo, el enlace final reacciona ante un cambio en el primer objeto `Label` porque cambia la propiedad `Text`.

Si una propiedad en la ruta de acceso de enlace no implementa `INotifyPropertyChanged`, se omitirán todos los cambios a esa propiedad. Algunos cambios podrían invalidar completamente la ruta de acceso de enlace, por lo que solo debe usar esta técnica cuando la cadena de propiedades y subpropiedades nunca sea no válida.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
