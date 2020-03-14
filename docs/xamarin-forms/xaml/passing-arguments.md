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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306564"
---
# <a name="passing-arguments-in-xaml"></a>Pasar argumentos en XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_En este artículo se muestra el uso de los atributos XAML que se pueden usar para pasar argumentos a constructores no predeterminados, para llamar a métodos de generador y para especificar el tipo de un argumento genérico._

## <a name="overview"></a>Información general

A menudo es necesario crear instancias de objetos con constructores que requieren argumentos, o mediante una llamada a un método de creación estático. Esto se puede lograr en XAML mediante los atributos `x:Arguments` y `x:FactoryMethod`:

- El atributo `x:Arguments` se utiliza para especificar argumentos de constructor para un constructor no predeterminado o para una declaración de objeto Factory Method. Para obtener más información, vea [pasar argumentos de constructor](#constructor_arguments).
- El atributo `x:FactoryMethod` se utiliza para especificar un Factory Method que se puede utilizar para inicializar un objeto. Para obtener más información, consulte [llamar a métodos de generador](#factory_methods).

Además, el atributo `x:TypeArguments` se puede utilizar para especificar los argumentos de tipo genérico para el constructor de un tipo genérico. Para obtener más información, vea [especificar un argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Pasar argumentos de Constructor

Los argumentos se pueden pasar a un constructor no predeterminado mediante el atributo `x:Arguments`. Cada argumento de constructor debe estar delimitado dentro de un elemento XML que representa el tipo del argumento. Xamarin.Forms es compatible con los siguientes elementos para los tipos básicos:

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

En el ejemplo de código siguiente se muestra el uso del atributo `x:Arguments` con tres constructores [`Color`](xref:Xamarin.Forms.Color) :

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

El número de elementos dentro de la etiqueta de `x:Arguments` y los tipos de estos elementos deben coincidir con uno de los constructores de [`Color`](xref:Xamarin.Forms.Color) . El [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) `Color` con un solo parámetro requiere un valor de escala de grises de 0 (negro) a 1 (blanco). El [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) de `Color` con tres parámetros requiere un valor rojo, verde y azul comprendido entre 0 y 1. El [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) de `Color` con cuatro parámetros agrega un canal alfa como cuarto parámetro.

Las capturas de pantallas siguientes muestran el resultado de llamar a cada constructor de [`Color`](xref:Xamarin.Forms.Color) con los valores de argumento especificados:

![BoxView. color especificado con x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Llamar a métodos de fábrica

Se puede llamar a los métodos de generador en XAML especificando el nombre del método mediante el atributo `x:FactoryMethod` y sus argumentos mediante el atributo `x:Arguments`. Un Factory Method es un método `public static` que devuelve objetos o valores del mismo tipo que la clase o estructura que define los métodos.

La estructura [`Color`](xref:Xamarin.Forms.Color) define una serie de métodos de generador y en el ejemplo de código siguiente se muestra cómo llamar a tres de ellos:

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

El número de elementos dentro de la etiqueta `x:Arguments`, y los tipos de estos elementos, deben coincidir con los argumentos del Factory Method al que se llama. El Factory Method [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) requiere cuatro parámetros de [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) , que representan los valores rojo, verde, azul y alfa, comprendidos entre 0 y 255, respectivamente. El Factory Method [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) requiere cuatro parámetros de [`Double`](https://docs.microsoft.com/dotnet/api/system.double) , que representan los valores de matiz, saturación, luminosidad y alfa, comprendidos entre 0 y 1, respectivamente. El Factory Method [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) requiere un [`String`](https://docs.microsoft.com/dotnet/api/system.string) que representa el color RGB hexadecimal (a).

Las capturas de pantallas siguientes muestran el resultado de llamar a cada [`Color`](xref:Xamarin.Forms.Color) Factory Method con los valores de argumento especificados:

![BoxView. color especificado con x:FactoryMethod y x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Si se especifica un argumento de tipo genérico

Los argumentos de tipo genérico para el constructor de un tipo genérico se pueden especificar utilizando el atributo `x:TypeArguments`, como se muestra en el ejemplo de código siguiente:

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

La clase [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) es una clase genérica y debe crearse una instancia de con un atributo `x:TypeArguments` que coincida con el tipo de destino. En la clase [`On`](xref:Xamarin.Forms.On) , el atributo [`Platform`](xref:Xamarin.Forms.On.Platform) puede aceptar un solo valor de `string` o varios valores de `string` delimitados por comas. En este ejemplo, la propiedad [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) se establece en un [`Thickness`](xref:Xamarin.Forms.Thickness)específico de la plataforma.

## <a name="summary"></a>Resumen

En este artículo se muestra usando los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados, para llamar a métodos de fábrica y para especificar el tipo de argumento genérico.

## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Pasar argumentos de constructor (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Llamar a métodos de generador (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
