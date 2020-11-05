---
title: Xamarin.Forms ProgressBar
description: Xamarin.FormsProgressBar es un control que representa visualmente el progreso como una barra horizontal que se rellena en función de una propiedad float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 824851c1406dfefb5f276be069f92040d03a5c98
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370525"
---
# <a name="no-locxamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

El Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) control representa visualmente el progreso como una barra horizontal que se rellena con un porcentaje representado por un `float` valor. La `ProgressBar` clase hereda de [`View`](xref:Xamarin.Forms.View) .

En las capturas de pantalla siguientes se muestra un elemento `ProgressBar` en iOS y Android:

![Captura de pantalla de ProgressBar en iOS y Android](progressbar-images/progressbars-default.png "ProgressBar en iOS y Android")

El `ProgressBar` control define dos propiedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) es un `float` valor que representa el progreso actual como un valor de 0 a 1. `Progress` los valores menores que 0 se fijarán en 0; los valores mayores que 1 se fijarán en 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) es un valor de tipo `Color` que afecta al color de la barra interior que representa el progreso actual.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se puede aplicar un `ProgressBar` estilo a y ser el destino de los enlaces de datos.

El `ProgressBar` control también define un `ProgressTo` método que anima la barra desde su valor actual hasta un valor especificado. Para obtener más información, vea [animar un componente ProgressBar](#animate-a-progressbar).

> [!NOTE]
> No `ProgressBar` acepta la manipulación de usuarios, por lo que se omite cuando se usa la tecla TAB para seleccionar controles.

## <a name="create-a-progressbar"></a>Crear un ProgressBar

`ProgressBar`Se puede crear una instancia de en XAML. Su `Progress` propiedad determina el porcentaje de relleno de la barra de color interna. El valor predeterminado de la `Progress` propiedad es 0. En el ejemplo siguiente se muestra cómo crear una instancia `ProgressBar` de en XAML con el `Progress` conjunto de propiedades opcional:

```xaml
<ProgressBar Progress="0.5" />
```

`ProgressBar`También se puede crear en el código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> No utilice opciones de diseño horizontal sin restricciones como `Center` , `Start` o `End` con `ProgressBar` . En UWP, `ProgressBar` se contrae en una barra de ancho cero. Mantenga el `HorizontalOptions` valor predeterminado de `Fill` y no use un ancho de `Auto` al colocar un `ProgressBar` en un `Grid` diseño.

## <a name="progressbar-appearance-properties"></a>Propiedades de apariencia de ProgressBar

La `ProgressColor` propiedad define el color de la barra interior cuando la `Progress` propiedad es mayor que cero. En el ejemplo siguiente se muestra cómo crear una instancia `ProgressBar` de en XAML con el `ProgressColor` conjunto de propiedades:

```xaml
<ProgressBar ProgressColor="Orange" />
```

La `ProgressColor` propiedad también se puede establecer al crear un `ProgressBar` en el código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Las siguientes capturas de pantallas muestran el `ProgressBar` con la `ProgressColor` propiedad establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de ProgressBar con estilo en iOS y Android](progressbar-images/progressbars-styled.png "ProgressBar con estilo en iOS y Android")

## <a name="animate-a-progressbar"></a>Animar un ProgressBar

El `ProgressTo` método anima `ProgressBar` desde su `Progress` valor actual hasta un valor proporcionado a lo largo del tiempo. El método acepta un `float` valor de progreso, una `uint` duración en milisegundos, un `Easing` valor de enumeración y devuelve un `Task<bool>` . En el código siguiente se muestra cómo animar un `ProgressBar` :

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obtener más información acerca de la `Easing` enumeración, consulte [funciones de aceleración en Xamarin.Forms ](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ProgressBar](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)