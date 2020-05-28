---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f314f3145b3573184cb8cdf7370394c975c66859
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127087"
---
# <a name="passing-arguments-in-xaml"></a>Paso de argumentos en XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_En este artículo se muestra el uso de los atributos XAML que se pueden usar para pasar argumentos a constructores no predeterminados, para llamar a métodos de generador y para especificar el tipo de un argumento genérico._

## <a name="overview"></a>Información general

A menudo es necesario crear instancias de objetos con constructores que requieran argumentos o mediante una llamada a un método de creación estático. Esto se puede lograr en XAML mediante el uso de los `x:Arguments` `x:FactoryMethod` atributos y:

- El `x:Arguments` atributo se utiliza para especificar argumentos de constructor para un constructor no predeterminado o para una declaración de objeto Factory Method. Para obtener más información, vea [pasar argumentos de constructor](#constructor_arguments).
- El `x:FactoryMethod` atributo se utiliza para especificar un Factory Method que se puede utilizar para inicializar un objeto. Para obtener más información, consulte [llamar a métodos de generador](#factory_methods).

Además, el `x:TypeArguments` atributo se puede utilizar para especificar los argumentos de tipo genérico para el constructor de un tipo genérico. Para obtener más información, vea [especificar un argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Pasar argumentos de constructor

Los argumentos se pueden pasar a un constructor no predeterminado mediante el `x:Arguments` atributo. Cada argumento de constructor debe delimitarse dentro de un elemento XML que representa el tipo del argumento. Xamarin.Formsadmite los siguientes elementos para los tipos básicos:

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

En el ejemplo de código siguiente se muestra cómo usar el `x:Arguments` atributo con tres [`Color`](xref:Xamarin.Forms.Color) constructores:

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

El número de elementos dentro de la `x:Arguments` etiqueta, así como los tipos de estos elementos, deben coincidir con uno de los [`Color`](xref:Xamarin.Forms.Color) constructores. El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) con un solo parámetro requiere un valor de escala de grises de 0 (negro) a 1 (blanco). El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) con tres parámetros requiere un valor rojo, verde y azul comprendido entre 0 y 1. El `Color` [constructor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) con cuatro parámetros agrega un canal alfa como cuarto parámetro.

Las capturas de pantallas siguientes muestran el resultado de llamar a cada [`Color`](xref:Xamarin.Forms.Color) constructor con los valores de argumento especificados:

![BoxView. color especificado con x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Llamar a métodos de generador

Se puede llamar a los métodos de generador en XAML especificando el nombre del método mediante el `x:FactoryMethod` atributo y sus argumentos mediante el `x:Arguments` atributo. Un Factory Method es un `public static` método que devuelve objetos o valores del mismo tipo que la clase o estructura que define los métodos.

La [`Color`](xref:Xamarin.Forms.Color) estructura define una serie de métodos de generador y en el ejemplo de código siguiente se muestra cómo llamar a tres de ellos:

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

El número de elementos dentro de la `x:Arguments` etiqueta, así como los tipos de estos elementos, deben coincidir con los argumentos del Factory Method que se va a llamar. El [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) Factory Method requiere cuatro [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) parámetros, que representan los valores rojo, verde, azul y alfa, comprendidos entre 0 y 255, respectivamente. El [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) Factory Method requiere cuatro [`Double`](https://docs.microsoft.com/dotnet/api/system.double) parámetros, que representan los valores de matiz, saturación, luminosidad y alfa, comprendidos entre 0 y 1, respectivamente. El [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) Factory Method requiere un [`String`](https://docs.microsoft.com/dotnet/api/system.string) que representa el color RGB hexadecimal (a).

Las capturas de pantallas siguientes muestran el resultado de llamar a cada [`Color`](xref:Xamarin.Forms.Color) Factory Method con los valores de argumento especificados:

![BoxView. color especificado con x:FactoryMethod y x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Especificar un argumento de tipo genérico

Los argumentos de tipo genérico para el constructor de un tipo genérico se pueden especificar utilizando el `x:TypeArguments` atributo, como se muestra en el ejemplo de código siguiente:

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

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica y se debe crear una instancia de con un `x:TypeArguments` atributo que coincida con el tipo de destino. En la [`On`](xref:Xamarin.Forms.On) clase, el [`Platform`](xref:Xamarin.Forms.On.Platform) atributo puede aceptar un solo `string` valor o varios valores delimitados por comas `string` . En este ejemplo, la [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) propiedad se establece en un específico de la plataforma [`Thickness`](xref:Xamarin.Forms.Thickness) .

Para obtener más información sobre los argumentos de tipo genérico, vea [genéricos en Xamarin.Forms XAML](generics.md).

## <a name="related-links"></a>Vínculos relacionados

- [Pasar argumentos de constructor (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Llamar a métodos de generador (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Genéricos en Xamarin.Forms XAML](generics.md)
