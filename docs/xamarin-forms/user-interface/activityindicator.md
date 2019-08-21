---
title: Indicador de actividad en Xamarin. Forms
description: El control ActivityIndicator indica a los usuarios que la aplicación está ocupada en una actividad prolongada, sin indicar ningún progreso. En este artículo se explica cómo usar una ActivityIndicator en XAML y en el código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: de93927728271e9f0d9d2ef850b70eeeee249c84
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658109"
---
# <a name="xamarinforms-activityindicator"></a>ActivityIndicator de Xamarin. Forms
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

El control Xamarin.[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) Forms muestra una animación para mostrar que la aplicación está ocupada en una actividad prolongada. A diferencia [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)de, el `ActivityIndicator` no proporciona ninguna indicación de progreso. `ActivityIndicator` Hereda [de`View`](xref:Xamarin.Forms.View).

Las capturas de pantallas `ActivityIndicator` siguientes muestran un control en iOS y Android:

![Captura de pantalla de ActivityIndicator en iOS y Android](activityindicator-images/activityindicators-default.png "Captura de pantalla de ActivityIndicator en iOS y Android")

El `ActivityIndicator` control define las siguientes propiedades:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)es un `Color` valor que define el color `ActivityIndicator`de presentación de.
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)es un `bool` valor que indica `ActivityIndicator` si debe ser visible y animar, u ocultarse. Cuando el valor es `false` `ActivityIndicator` , no está visible.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que `ActivityIndicator` se puede aplicar un estilo a y ser el destino de los enlaces de datos.

## <a name="create-an-activityindicator"></a>Creación de un ActivityIndicator

Se `ActivityIndicator` puede crear una instancia de la clase en XAML. Su `IsRunning` propiedad determina si el control está visible y animando. La `IsRunning` propiedad tiene como valor `false`predeterminado. En el ejemplo siguiente se muestra cómo crear una `ActivityIndicator` instancia de en XAML con `IsRunning` el conjunto de propiedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

También `ActivityIndicator` se puede crear en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propiedades de apariencia de ActivityIndicator

La `Color` propiedad define el `ActivityIndicator` color. En el ejemplo siguiente se muestra cómo crear una `ActivityIndicator` instancia de en XAML `Color` con el conjunto de propiedades:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propiedad también se puede establecer al crear un `ActivityIndicator` en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Las siguientes capturas de `ActivityIndicator` pantallas muestran `Color` el con la `Color.Orange` propiedad establecida en en iOS y Android:

![Captura de pantalla de ActivityIndicator con estilo en iOS y Android](activityindicator-images/activityindicators-styled.png "Captura de pantalla de ActivityIndicator con estilo en iOS y Android")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [Barra de progreso](~/xamarin-forms/user-interface/progressbar.md)
