---
title: Indicador de actividad en Xamarin.Forms
description: El control ActivityIndicator indica a los usuarios que la aplicación está ocupada en una actividad prolongada, sin indicar ningún progreso. En este artículo se explica cómo usar una ActivityIndicator en XAML y en el código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76136067f40d378a6bfa28698f15b6872bb40c93
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562357"
---
# <a name="no-locxamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

El Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) control muestra una animación para mostrar que la aplicación está ocupada en una actividad prolongada. A diferencia [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) de, el `ActivityIndicator` no proporciona ninguna indicación de progreso. `ActivityIndicator`Hereda de [`View`](xref:Xamarin.Forms.View) .

Las capturas de pantallas siguientes muestran un `ActivityIndicator` control en iOS y Android:

![Captura de pantalla de ActivityIndicator en iOS y Android](activityindicator-images/activityindicators-default.png "Captura de pantalla de ActivityIndicator en iOS y Android")

El `ActivityIndicator` control define las siguientes propiedades:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) es un `Color` valor que define el color de presentación de `ActivityIndicator` .
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) es un `bool` valor que indica si `ActivityIndicator` debe ser visible y animar, u ocultarse. Cuando el valor es `false` , `ActivityIndicator` no está visible.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se puede aplicar un `ActivityIndicator` estilo a y ser el destino de los enlaces de datos.

## <a name="create-an-activityindicator"></a>Creación de un ActivityIndicator

`ActivityIndicator`Se puede crear una instancia de la clase en XAML. Su `IsRunning` propiedad determina si el control está visible y animando. La `IsRunning` propiedad tiene como valor predeterminado `false` . En el ejemplo siguiente se muestra cómo crear una instancia `ActivityIndicator` de en XAML con el `IsRunning` conjunto de propiedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

`ActivityIndicator`También se puede crear en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propiedades de apariencia de ActivityIndicator

La `Color` propiedad define el `ActivityIndicator` color. En el ejemplo siguiente se muestra cómo crear una instancia `ActivityIndicator` de en XAML con el `Color` conjunto de propiedades:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propiedad también se puede establecer al crear un `ActivityIndicator` en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Las siguientes capturas de pantallas muestran el `ActivityIndicator` con la `Color` propiedad establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de ActivityIndicator con estilo en iOS y Android](activityindicator-images/activityindicators-styled.png "Captura de pantalla de ActivityIndicator con estilo en iOS y Android")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ActivityIndicator](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)