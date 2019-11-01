---
title: ProgressBar de Xamarin. Forms
description: El ProgressBar de Xamarin. Forms es un control que representa visualmente el progreso como una barra horizontal que se rellena en función de una propiedad float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201947"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar de Xamarin. Forms
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

El control de Xamarin. Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) representa visualmente el progreso como una barra horizontal que se rellena con un porcentaje representado por un valor de `float`. La clase `ProgressBar` hereda de [`View`](xref:Xamarin.Forms.View).

Las siguientes capturas de pantallas muestran un `ProgressBar` en iOS y Android:

![Captura de pantalla de ProgressBar en iOS y Android](progressbar-images/progressbars-default.png "ProgressBar en iOS y Android")

El control `ProgressBar` define dos propiedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) es un valor `float` que representa el progreso actual como un valor de 0 a 1. `Progress` valores inferiores a 0 se fijarán en 0, los valores mayores que 1 se fijarán en 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) es una `Color` que afecta al color de la barra interior que representa el progreso actual.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que se puede aplicar estilo al `ProgressBar` y ser el destino de los enlaces de datos.

El control `ProgressBar` también define un método de `ProgressTo` que anima la barra desde su valor actual hasta un valor especificado. Para obtener más información, vea [animar un componente ProgressBar](#animate-a-progressbar).

> [!NOTE]
> El `ProgressBar` no acepta la manipulación de usuarios, por lo que se omite cuando se usa la tecla TAB para seleccionar controles.

## <a name="create-a-progressbar"></a>Crear un ProgressBar

Se puede crear una instancia de `ProgressBar` en XAML. Su propiedad `Progress` determina el porcentaje de relleno de la barra de color interna. El valor predeterminado de la propiedad `Progress` es 0. En el ejemplo siguiente se muestra cómo crear una instancia de un `ProgressBar` en XAML con el conjunto de propiedades de `Progress` opcional:

```xaml
<ProgressBar Progress="0.5" />
```

También se puede crear un `ProgressBar` en el código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> No utilice opciones de diseño horizontal sin restricciones como `Center`, `Start`o `End` con `ProgressBar`. En UWP, el `ProgressBar` se contrae en una barra de ancho cero. Mantenga el valor de `HorizontalOptions` predeterminado de `Fill` y no use un ancho de `Auto` al colocar un `ProgressBar` en un diseño de `Grid`.

## <a name="progressbar-appearance-properties"></a>Propiedades de apariencia de ProgressBar

La propiedad `ProgressColor` define el color de la barra interior cuando el valor de la propiedad `Progress` es mayor que cero. En el ejemplo siguiente se muestra cómo crear una instancia de un `ProgressBar` en XAML con el conjunto de propiedades `ProgressColor`:

```xaml
<ProgressBar ProgressColor="Orange" />
```

También se puede establecer la propiedad `ProgressColor` al crear un `ProgressBar` en el código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Las capturas de pantallas siguientes muestran el `ProgressBar` con la propiedad `ProgressColor` establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de ProgressBar con estilo en iOS y Android](progressbar-images/progressbars-styled.png "ProgressBar con estilo en iOS y Android")

## <a name="animate-a-progressbar"></a>Animar un ProgressBar

El método `ProgressTo` anima el `ProgressBar` de su valor de `Progress` actual a un valor proporcionado a lo largo del tiempo. El método acepta un valor de progreso `float`, un `uint` duración en milisegundos, un valor de enumeración `Easing` y devuelve un `Task<bool>`. En el código siguiente se muestra cómo animar una `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obtener más información sobre la enumeración `Easing`, vea [funciones de aceleración en Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
