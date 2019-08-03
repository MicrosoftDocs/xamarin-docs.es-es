---
title: Indicador de actividad en Xamarin. Forms
description: El control ActivityIndicator indica a los usuarios que la aplicación está ocupada en una actividad prolongada, sin indicar ningún progreso. En este artículo se explica cómo usar una ActivityIndicator en XAML y en el código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739160"
---
# <a name="xamarinforms-activityindicator"></a>ActivityIndicator de Xamarin. Forms
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin. Forms[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) es un control que muestra una animación para mostrar que la aplicación está ocupada en una actividad prolongada. A diferencia [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)de, el `ActivityIndicator` no proporciona ninguna indicación de progreso. `ActivityIndicator` Hereda [de`View`](xref:Xamarin.Forms.View).

En la captura de pantalla `ActivityIndicator` siguiente se muestra un control de iOS y Android:

![Captura de pantalla de ActivityIndicator en iOS y Android](activityindicator-images/activityindicators-default.png "Captura de pantalla de ActivityIndicator en iOS y Android")

El `ActivityIndicator` control define las siguientes propiedades:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)es un `bool` valor que indica `ActivityIndicator` si debe ser visible y animar, u ocultarse. Cuando el valor es `false` `ActivityIndicator` , no será visible.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)es un `Color` valor que define el color `ActivityIndicator`de presentación de.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que `ActivityIndicator` se puede aplicar un estilo a y ser el destino de los enlaces de datos.

## <a name="create-an-activityindicator"></a>Creación de un ActivityIndicator

Se `ActivityIndicator` puede crear una instancia de en XAML. Su `IsRunning` propiedad se puede establecer para determinar si el control está visible y animando. Si no `IsRunning` se establece la propiedad, el `ActivityIndicator` valor predeterminado `false` es y no será visible. En el ejemplo siguiente se muestra cómo crear una `ActivityIndicator` instancia de en XAML con `IsRunning` el conjunto de propiedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

También `ActivityIndicator` se puede crear en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propiedades de apariencia de ActivityIndicator

La `Color` propiedad se puede establecer para definir el `ActivityIndicator` color. En el ejemplo siguiente se muestra cómo crear una `ActivityIndicator` instancia de en XAML `Color` con el conjunto de propiedades:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propiedad también se puede establecer al crear un `ActivityIndicator` en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

En la captura de pantalla `ActivityIndicator` siguiente se `Color` `Color.Orange` muestra el con la propiedad establecida en en iOS y Android:

![Captura de pantalla de ActivityIndicator con estilo en iOS y Android](activityindicator-images/activityindicators-styled.png "Captura de pantalla de ActivityIndicator con estilo en iOS y Android")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [Barra de progreso](~/xamarin-forms/user-interface/progressbar.md)
