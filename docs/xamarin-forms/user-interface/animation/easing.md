---
title: Funciones de aceleración en Xamarin.Forms
description: Xamarin.Forms incluye una clase de entradas y salidas lentas que permite especificar una función de transferencia que controla cómo se aceleran las animaciones o se ralentizan mientras se ejecutan. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 50b64b394314ae2f63ab1f756f1cc73ba29e59e7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372852"
---
# <a name="easing-functions-in-no-locxamarinforms"></a>Funciones de aceleración en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms incluye una clase de entradas y salidas lentas que permite especificar una función de transferencia que controla cómo se aceleran las animaciones o se ralentizan mientras se ejecutan. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas._

La [`Easing`](xref:Xamarin.Forms.Easing) clase define un número de funciones de aceleración que pueden usar las animaciones:

- La [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) función de aceleración rebota la animación al principio.
- La [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) función de aceleración rebota la animación al final.
- La [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) función de entradas y salidas lentas acelera lentamente la animación.
- La [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) función de aceleración acelera la animación al principio y ralentiza la animación al final.
- La [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) función de aceleración ralentiza rápidamente la animación.
- La [`Linear`](xref:Xamarin.Forms.Easing.Linear) función de aceleración usa una velocidad constante y es la función de aceleración predeterminada.
- La [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) función de aceleración agiliza la animación.
- La [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) función de entradas y salidas lentas acelera la animación al principio y suaviza suavemente la animación al final.
- La [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) función de entradas y salidas lentas desacelera suavemente la animación.
- La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) función de aceleración hace que la animación se acelere muy rápidamente hacia el final.
- La [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) función de aceleración hace que la animación se ralentice rápidamente hacia el final.

Los `In` `Out` sufijos y indican si el efecto que proporciona la función de aceleración se percibe al principio de la animación, al final o a ambos.

Además, se pueden crear funciones de aceleración personalizadas. Para obtener más información, vea [funciones de aceleración personalizadas](#custom-easing-functions).

## <a name="consuming-an-easing-function"></a>Consumir una función de aceleración

Los métodos de extensión de animación de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase permiten especificar una función de aceleración como el último parámetro del método, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Al especificar una función de aceleración para una animación, la velocidad de la animación no es lineal y produce el efecto proporcionado por la función de aceleración. Al omitir una función de aceleración al crear una animación, la animación utiliza la [`Linear`](xref:Xamarin.Forms.Easing.Linear) función de aceleración predeterminada, que genera una velocidad lineal.

Para obtener más información sobre el uso de los métodos de extensión de animación en la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase, vea [animaciones simples](~/xamarin-forms/user-interface/animation/simple.md). La clase también puede usar las funciones de aceleración [`Animation`](xref:Xamarin.Forms.Animation) . Para obtener más información, vea [animaciones personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

## <a name="custom-easing-functions"></a>Funciones de aceleración personalizadas

Hay tres enfoques principales para crear una función de aceleración personalizada:

1. Cree un método que tome un `double` argumento y devuelva un `double` resultado.
1. Creará un control `Func<double, double>`.
1. Especifique la función de entradas y salidas lentas como argumento para el [`Easing`](xref:Xamarin.Forms.Easing) constructor.

En los tres casos, la función de aceleración personalizada debe devolver 0 para un argumento de 0 y 1 para un argumento de 1. Sin embargo, se puede devolver cualquier valor entre los valores de argumento de 0 y 1. Cada enfoque se tratará ahora a su vez.

### <a name="custom-easing-method"></a>Método de aceleración personalizado

Una función de aceleración personalizada se puede definir como un método que toma un `double` argumento y devuelve un `double` resultado, como se muestra en el ejemplo de código siguiente:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

El `CustomEase` método trunca el valor de entrada a los valores 0, 0,2, 0,4, 0,6, 0,8 y 1. Por lo tanto, la [`Image`](xref:Xamarin.Forms.Image) instancia se traduce en saltos discretos, en lugar de sin problemas.

### <a name="custom-easing-func"></a>FUNC. de aceleración personalizada

También se puede definir una función de aceleración personalizada como `Func<double, double>` , como se muestra en el ejemplo de código siguiente:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

`CustomEaseFunc`Representa una función de entradas y salidas lentas que se inicia con rapidez, reduce el tiempo y revierte el curso y, a continuación, invierte el curso de nuevo para acelerar rápidamente hacia el final. Por lo tanto, mientras el movimiento global de la [`Image`](xref:Xamarin.Forms.Image) instancia es hacia abajo, también invierte temporalmente el curso a mitad de la animación.

### <a name="custom-easing-constructor"></a>Constructor de entradas y salidas personalizadas

También se puede definir una función de aceleración personalizada como argumento para el [`Easing`](xref:Xamarin.Forms.Easing) constructor, tal y como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La función de aceleración personalizada se especifica como un argumento de función lambda para el [`Easing`](xref:Xamarin.Forms.Easing) constructor y usa el `Math.Cos` método para crear un efecto de eliminación lenta que el método detiene `Math.Exp` . Por lo tanto, la [`Image`](xref:Xamarin.Forms.Image) instancia se traduce para que parezca colocarse en su lugar final.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas. Xamarin.Forms incluye una [`Easing`](xref:Xamarin.Forms.Easing) clase que permite especificar una función de transferencia que controla el modo en que las animaciones se aceleran o reducen a medida que se ejecutan.

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Funciones de aceleración (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Pronuncia](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)