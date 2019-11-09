---
title: Funciones de aceleración en Xamarin. Forms
description: Xamarin. Forms incluye una clase de entradas y salidas lentas que le permite especificar una función de transferencia que controla el modo en que las animaciones se aceleran o reducen a medida que se ejecutan. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 56ea31d1e1be8bbad4a27dd7ffd844aa03f75bbb
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842831"
---
# <a name="easing-functions-in-xamarinforms"></a>Funciones de aceleración en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin. Forms incluye una clase de entradas y salidas lentas que le permite especificar una función de transferencia que controla el modo en que las animaciones se aceleran o reducen a medida que se ejecutan. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas._

La clase [`Easing`](xref:Xamarin.Forms.Easing) define un número de funciones de aceleración que pueden usar las animaciones:

- La función de aceleración de [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) rebota la animación al principio.
- La función de aceleración de [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) rebota la animación al final.
- La función de aceleración [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) acelera lentamente la animación.
- La función de aceleración de [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) acelera la animación al principio y ralentiza la animación al final.
- La función de aceleración de [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) ralentiza rápidamente la animación.
- La función de aceleración de [`Linear`](xref:Xamarin.Forms.Easing.Linear) usa una velocidad constante y es la función de aceleración predeterminada.
- La función de aceleración de [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) agiliza la animación.
- La función de aceleración de [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) agiliza la animación al principio y reduce suavemente la animación al final.
- La función de entradas y salidas lentas [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) desacelera suavemente la animación.
- La función de aceleración de [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) hace que la animación se acelere muy rápidamente hacia el final.
- La función de aceleración de [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) hace que la animación se ralentice rápidamente hacia el final.

Los sufijos `In` y `Out` indican si el efecto que proporciona la función de aceleración es apreciable al principio de la animación, al final o a ambos.

Además, se pueden crear funciones de aceleración personalizadas. Para obtener más información, vea [funciones de aceleración personalizadas](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumir una función de aceleración

Los métodos de extensión de animación de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) permiten especificar una función de aceleración como el último parámetro de método, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Al especificar una función de aceleración para una animación, la velocidad de la animación no es lineal y produce el efecto proporcionado por la función de aceleración. Omitir una función de entradas y salidas lentas al crear una animación hace que la animación use la función de aceleración [`Linear`](xref:Xamarin.Forms.Easing.Linear) predeterminada, que genera una velocidad lineal.

Para obtener más información sobre el uso de los métodos de extensión de animación en la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) , vea [animaciones simples](~/xamarin-forms/user-interface/animation/simple.md). La clase [`Animation`](xref:Xamarin.Forms.Animation) también puede usar las funciones de aceleración. Para obtener más información, vea [animaciones personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funciones de aceleración personalizadas

Hay tres enfoques principales para crear una función de aceleración personalizada:

1. Cree un método que toma un argumento `double` y devuelve un `double` resultado.
1. Creará un control `Func<double, double>`.
1. Especifique la función de entradas y salidas lentas como argumento para el constructor de [`Easing`](xref:Xamarin.Forms.Easing) .

En los tres casos, la función de aceleración personalizada debe devolver 0 para un argumento de 0 y 1 para un argumento de 1. Sin embargo, se puede devolver cualquier valor entre los valores de argumento de 0 y 1. Cada enfoque se tratará ahora a su vez.

### <a name="custom-easing-method"></a>Método de aceleración personalizado

Una función de aceleración personalizada se puede definir como un método que toma un argumento `double` y devuelve un `double` resultado, como se muestra en el ejemplo de código siguiente:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

El método `CustomEase` trunca el valor de entrada a los valores 0, 0,2, 0,4, 0,6, 0,8 y 1. Por lo tanto, la instancia de [`Image`](xref:Xamarin.Forms.Image) se traduce en saltos discretos, en lugar de sin problemas.

### <a name="custom-easing-func"></a>FUNC. de aceleración personalizada

También se puede definir una función de aceleración personalizada como `Func<double, double>`, como se muestra en el ejemplo de código siguiente:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

La `CustomEaseFunc` representa una función de entradas y salidas lentas que comienza con rapidez, reduce el tiempo y revierte el curso y, a continuación, invierte el curso de nuevo para acelerar rápidamente hacia el final. Por lo tanto, mientras que el movimiento global de la instancia de [`Image`](xref:Xamarin.Forms.Image) es hacia abajo, también invierte temporalmente el curso a mitad de la animación.

### <a name="custom-easing-constructor"></a>Constructor de entradas y salidas personalizadas

También se puede definir una función de aceleración personalizada como argumento para el constructor [`Easing`](xref:Xamarin.Forms.Easing) , como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La función de aceleración personalizada se especifica como un argumento de función lambda para el constructor [`Easing`](xref:Xamarin.Forms.Easing) y usa el método `Math.Cos` para crear un efecto de eliminación lenta que se detiene en el método `Math.Exp`. Por lo tanto, la instancia de [`Image`](xref:Xamarin.Forms.Image) se traduce para que parezca colocarse en su lugar final.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas. Xamarin. Forms incluye una clase [`Easing`](xref:Xamarin.Forms.Easing) que le permite especificar una función de transferencia que controla cómo se aceleran las animaciones o se ralentizan mientras se ejecutan.

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Funciones de aceleración (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Pronuncia](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
