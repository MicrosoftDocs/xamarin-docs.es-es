---
title: ProgressBar de Xamarin. Forms
description: El ProgressBar de Xamarin. Forms es un control que representa visualmente el progreso como una barra horizontal que se rellena en función de una propiedad float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: a4cfc6c54eb2864707f328106761af029e0734cf
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658080"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar de Xamarin. Forms
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

El control Xamarin. [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) Forms representa visualmente el progreso como una barra horizontal que se rellena con un porcentaje representado `float` por un valor. La `ProgressBar` clase hereda de [`View`](xref:Xamarin.Forms.View).

Las siguientes capturas de `ProgressBar` pantallas muestran un en iOS y Android:

![Captura de pantalla de ProgressBar en iOS y Android](progressbar-images/progressbars-default.png "ProgressBar en iOS y Android")

El `ProgressBar` control define dos propiedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)es un `float` valor que representa el progreso actual como un valor de 0 a 1. `Progress`los valores menores que 0 se fijarán en 0; los valores mayores que 1 se fijarán en 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)es un `Color` valor de tipo que afecta al color de la barra interior que representa el progreso actual.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que `ProgressBar` se puede aplicar un estilo a y ser el destino de los enlaces de datos.

El `ProgressBar` control también define un `ProgressTo` método que anima la barra desde su valor actual hasta un valor especificado. Para obtener más información, vea [animar un componente ProgressBar](#animate-a-progressbar).

> [!NOTE]
> No `ProgressBar` acepta la manipulación de usuarios, por lo que se omite cuando se usa la tecla TAB para seleccionar controles.

## <a name="create-a-progressbar"></a>Crear un ProgressBar

Se `ProgressBar` puede crear una instancia de en XAML. Su `Progress` propiedad determina el porcentaje de relleno de la barra de color interna. El valor `Progress` predeterminado de la propiedad es 0. En el ejemplo siguiente se muestra cómo crear una `ProgressBar` instancia de en XAML con `Progress` el conjunto de propiedades opcional:

```xaml
<ProgressBar Progress="0.5" />
```

También `ProgressBar` se puede crear en el código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> No utilice opciones de diseño horizontal sin restricciones `Center`como, `Start`o `End` con `ProgressBar`. En UWP, `ProgressBar` se contrae en una barra de ancho cero. Mantenga el valor `HorizontalOptions` predeterminado de `Fill` y no use un ancho de `Auto` al colocar un `ProgressBar` en un `Grid` diseño.

## <a name="progressbar-appearance-properties"></a>Propiedades de apariencia de ProgressBar

La `ProgressColor` propiedad define el color de la barra interior `Progress` cuando la propiedad es mayor que cero. En el ejemplo siguiente se muestra cómo crear una `ProgressBar` instancia de en XAML `ProgressColor` con el conjunto de propiedades:

```xaml
<ProgressBar OnColor="Orange" />
```

La `ProgressColor` propiedad también se puede establecer al crear un `ProgressBar` en el código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Las siguientes capturas de `ProgressBar` pantallas muestran `ProgressColor` el con la `Color.Orange` propiedad establecida en en iOS y Android:

![Captura de pantalla de ProgressBar con estilo en iOS y Android](progressbar-images/progressbars-styled.png "ProgressBar con estilo en iOS y Android")

## <a name="animate-a-progressbar"></a>Animar un ProgressBar

El `ProgressTo` método anima `ProgressBar` desde su valor actual `Progress` hasta un valor proporcionado a lo largo del tiempo. El método acepta un `float` valor de progreso, `uint` una duración en milisegundos, `Easing` un valor de enumeración `Task<bool>`y devuelve un. En el código siguiente se muestra cómo animar un `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obtener más información sobre `Easing` la enumeración, consulte [funciones de aceleración en Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
