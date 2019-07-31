---
title: Funciones de aceleración en Xamarin.Forms
description: Xamarin.Forms incluye una clase de entrada y salida lenta que le permite especificar una función de transferencia que controla cómo las animaciones aceleran o ralentizar se están ejecutando. En este artículo se muestra cómo usar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 0be72e29ebfed9a3f43d96fd66b7f4597459a834
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656766"
---
# <a name="easing-functions-in-xamarinforms"></a>Funciones de aceleración en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms incluye una clase de entrada y salida lenta que le permite especificar una función de transferencia que controla cómo las animaciones aceleran o ralentizar se están ejecutando. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración._


El [ `Easing` ](xref:Xamarin.Forms.Easing) clase define una serie de funciones de aceleración que puede consumir las animaciones:

- El [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn) función de aceleración rebota la animación al principio.
- El [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut) función de aceleración rebota la animación al final.
- El [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn) lentamente en función de aceleración acelera la animación.
- El [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut) función de aceleración acelera la animación al principio y ralentiza la animación al final.
- El [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut) rápidamente en función de aceleración disminuye la velocidad de la animación.
- El [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) función de aceleración usa una velocidad constante y es el valor predeterminado función de aceleración.
- El [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn) sin problemas en función de aceleración acelera la animación.
- El [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut) sin problemas en función de aceleración acelera la animación al principio y disminuye la velocidad sin problemas la animación al final.
- El [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut) sin problemas en función de aceleración disminuye la velocidad de la animación.
- El [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) función de aceleración hace que la animación acelerar rápidamente hacia el final.
- El [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) función de aceleración hace que la animación que se ralentice rápidamente hacia el final.

El `In` y `Out` sufijos indican si el efecto de la función de aceleración es apreciable al principio de la animación, al final, o ambos.

Además, se pueden crear funciones de aceleración. Para obtener más información, consulte [funciones de aceleración personalizada](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumo de una función de aceleración

Los métodos de extensión de la animación en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase permite que una función de aceleración que se especifique como parámetro del método final, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Al especificar una función de aceleración para una animación, la velocidad de animación se convierte en no lineal y genera el efecto de la función de aceleración. Si se omite una función de aceleración al crear una animación hace que la animación que utilice el valor predeterminado [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) función, lo que produce una velocidad lineal de aceleración.

Para obtener más información sobre el uso de los métodos de extensión de la animación en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) de clases, vea [las animaciones sencillas](~/xamarin-forms/user-interface/animation/simple.md). Funciones de aceleración también pueden utilizarse en el [ `Animation` ](xref:Xamarin.Forms.Animation) clase. Para obtener más información, consulte [animaciones personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funciones de aceleración personalizadas

Existen tres enfoques principales para crear una función de aceleración personalizada:

1. Crear un método que toma un `double` argumento y devuelve un `double` resultado.
1. Creará un control `Func<double, double>`.
1. Especifique la función de aceleración como argumento para el [ `Easing` ](xref:Xamarin.Forms.Easing) constructor.

En los tres casos, la función de aceleración personalizada debe devolver 0 para un argumento de 0 y 1 para un argumento de 1. Sin embargo, se puede devolver cualquier valor entre los valores de argumento de 0 y 1. A su vez ahora se explicará cada enfoque.

### <a name="custom-easing-method"></a>Método de aceleración personalizadas

Se puede definir una función de aceleración personalizada como un método que toma un `double` argumento y devuelve un `double` como resultado, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

El `CustomEase` método trunca el valor entrante a los valores 0, 0,2, 0,4, 0.6, 0.8 y 1. Por lo tanto, el [ `Image` ](xref:Xamarin.Forms.Image) instancia se traduce en saltos discretos, en lugar de forma fluida.

### <a name="custom-easing-func"></a>Aceleración Func personalizadas

También se puede definir una función de aceleración personalizada como un `Func<double, double>`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

El `CustomEase` `Func` representa una función de entradas y salidas lenta que comienza rápido, se ralentiza e invierte el curso y, a continuación, invierte nuevo curso para acelerar rápidamente hacia el final. Por tanto, mientras el movimiento general de la [ `Image` ](xref:Xamarin.Forms.Image) instancia está hacia abajo, invierte también temporalmente a mitad de camino a través de la animación del curso.

### <a name="custom-easing-constructor"></a>Constructor de aceleración personalizadas

También se puede definir una función de aceleración personalizada como el argumento de la [ `Easing` ](xref:Xamarin.Forms.Easing) constructor, tal y como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La función de aceleración personalizada se especifica como un argumento de función lambda a la [ `Easing` ](xref:Xamarin.Forms.Easing) constructor y se usa el `Math.Cos` método para crear un efecto de lento que se atenúa por la `Math.Exp` método. Por lo tanto, el [ `Image` ](xref:Xamarin.Forms.Image) instancia se traduce para que aparezca a colocar en su lugar definitiva de almacenamiento final.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración. Xamarin.Forms incluye la clase [`Easing`](xref:Xamarin.Forms.Easing), que permite especificar una función de transferencia que controla cómo se aceleran o ralentizan las animaciones durante su ejecución.



## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Funciones de aceleración (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Aceleración](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
