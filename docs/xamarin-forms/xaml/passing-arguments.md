---
title: Pasar argumentos en XAML
description: En este artículo muestra cómo utilizar los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados, para llamar a métodos de fábrica y para especificar el tipo de argumento genérico.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767596"
---
# <a name="passing-arguments-in-xaml"></a>Pasar argumentos en XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_En este artículo muestra cómo utilizar los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados, para llamar a métodos de fábrica y para especificar el tipo de argumento genérico._

## <a name="overview"></a>Información general

A menudo es necesario crear instancias de objetos con constructores que requieren argumentos, o mediante una llamada a un método de creación estático. Esto puede lograrse en XAML mediante el uso de la `x:Arguments` y `x:FactoryMethod` atributos:

- El `x:Arguments` atributo se utiliza para especificar los argumentos de constructor para un constructor no predeterminado, o para una declaración de objeto del método de fábrica. Para obtener más información, consulte [pasar argumentos de Constructor](#constructor_arguments).
- El `x:FactoryMethod` atributo se utiliza para especificar un método de fábrica que puede usarse para inicializar un objeto. Para obtener más información, consulte [llamar a métodos de generador](#factory_methods).

Además, el `x:TypeArguments` atributo puede utilizarse para especificar los argumentos de tipo genérico para el constructor de un tipo genérico. Para obtener más información, consulte [especificando un argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Pasar argumentos de Constructor

Se pueden pasar argumentos a un constructor no predeterminado mediante el `x:Arguments` atributo. Cada argumento de constructor debe estar delimitado dentro de un elemento XML que representa el tipo del argumento. Xamarin.Forms es compatible con los siguientes elementos para los tipos básicos:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

En el ejemplo de código siguiente se muestra cómo utilizar el `x:Arguments` atributo con tres [ `Color` ](xref:Xamarin.Forms.Color) constructores:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

El número de elementos dentro de la `x:Arguments` etiqueta y los tipos de estos elementos, deben coincidir con uno de los [ `Color` ](xref:Xamarin.Forms.Color) constructores. El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) con un solo parámetro requiere un valor de escala de grises de 0 (negro) a 1 (blanco). El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) con tres parámetros requiere un valor de color rojo, verde y azul comprendida entre 0 y 1. El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) con cuatro parámetros agrega un canal alfa como cuarto parámetro.

Las capturas de pantalla siguientes muestran el resultado de llamar a cada uno de ellos [ `Color` ](xref:Xamarin.Forms.Color) constructor con los valores de argumento especificado:

![BoxView. color especificado con x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Llamar a métodos de fábrica

Pueden llamar a los métodos de fábrica en XAML mediante la especificación del método con el nombre del `x:FactoryMethod` atributo y sus argumentos mediante el `x:Arguments` atributo. Un método de fábrica es un `public static` método que devuelve objetos o valores del mismo tipo que la clase o estructura que define los métodos.

El [ `Color` ](xref:Xamarin.Forms.Color) estructura define una serie de métodos de generador y el código siguiente muestra que realiza la llamada tres de ellas:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

El número de elementos dentro de la `x:Arguments` etiqueta y los tipos de estos elementos, deben coincidir con los argumentos de la llamada al método de fábrica. El [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) método de fábrica requiere cuatro [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) parámetros, que representan los valores de rojos, verdes, azules y alfabéticos, comprendida entre 0 y 255, respectivamente. El [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) método de fábrica requiere cuatro [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) parámetros, que representan el matiz, saturación, luminosidad y los valores alfa, comprendida entre 0 y 1 respectivamente. El [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) método factory requiere un [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) que representa el formato hexadecimal (A) color RGB.

Las capturas de pantalla siguientes muestran el resultado de llamar a cada uno de ellos [ `Color` ](xref:Xamarin.Forms.Color) método de fábrica con los valores de argumento especificado:

![BoxView. color especificado con x:FactoryMethod y x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Si se especifica un argumento de tipo genérico

Argumentos de tipo genérico para el constructor de un tipo genérico pueden especificarse mediante la `x:TypeArguments` de atributo, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

El [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica y se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. En la clase [ `On` ](xref:Xamarin.Forms.On), el atributo [ `Platform` ](xref:Xamarin.Forms.On.Platform) puede aceptar un único valor `string` o varios valores `string` delimitados por comas. En este ejemplo, el [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin) propiedad está establecida en una plataforma específica [ `Thickness` ](xref:Xamarin.Forms.Thickness).

## <a name="summary"></a>Resumen

En este artículo se muestra usando los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados, para llamar a métodos de fábrica y para especificar el tipo de argumento genérico.

## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Pasar argumentos de Constructor (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Llamar a métodos de fábrica (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
