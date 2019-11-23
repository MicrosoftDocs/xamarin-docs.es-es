---
title: Uso de UrhoSharp en Xamarin.Forms
description: Este artículo explica el uso de UrhoSharp agregar gráficos 3D a una aplicación de Xamarin.Forms para visualización avanzada.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 7045bd4d3343d0c11c6cd52fa02cdc005175b8a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772931"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Uso de UrhoSharp en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>¿Qué es UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) es un poderoso motor 3D para desarrolladores de Xamarin y. NET. El [Introducción](~/graphics-games/urhosharp/introduction.md) contiene más explicaciones acerca de la biblioteca de UrhoSharp y [estas notas](~/graphics-games/urhosharp/using.md) describen cómo programar en segundo plano y las acciones.

UrhoSharp puede usarse para representar gráficos en las aplicaciones de Xamarin.Forms.
Esto [ejemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample) muestra cómo se podría usar UrhoSharp para construir un gráfico 3D interactivo:

![](urhosharp-images/ios-animation.gif "Gráfico interactivo 3D de UrhoSharp en iOS")
![](urhosharp-images/android-animation.gif "gráfico interactiva 3D de UrhoSharp en Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Agregar los paquetes de UrhoSharp Nuget

Antes de uso de UrhoSharp, los desarrolladores necesitan agregar el paquete UrhoSharp Nuget a su solución. Esta guía se da por supuesto un proyecto de Xamarin.Forms con un iOS, Android y .NET Standard proyecto de biblioteca. Todo el código se escribirá en el proyecto de biblioteca .NET Standard; pero UrhoSharp Nuget debe agregarse a los proyectos de Android y iOS demasiado.

El paquete UrhoSharp.Forms Nuget contiene todos los objetos necesarios para crear objetos de UrhoSharp. El paquete de nuget UrhoSharp.Forms incluye la `UrhoSurface` (clase), que se usa para el host de UrhoSharp en Xamarin.Forms.
Para empezar, haga clic con el botón derecho en la carpeta **paquetes** en el proyecto de biblioteca de .net Standard y seleccione **agregar paquetes.** ... Escriba el término de búsqueda **UrhoSharp. Forms**, seleccione **UrhoSharp para Xamarin. Forms**y haga clic en **Agregar paquete**.

[![](urhosharp-images/add-package-sml.png "Agregar cuadro de diálogo paquetes")](urhosharp-images/add-package.png#lightbox "Agregar cuadro de diálogo paquetes")

El paquete UrhoSharp.Forms NuGet se agregarán al proyecto:

![](urhosharp-images/packages.png "Carpeta de paquetes")

Repita los pasos anteriores para los proyectos específicos de la plataforma (por ejemplo, iOS y Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Tutorial: Agregar UrhoSharp a una aplicación de Xamarin.Forms

El código en el ejemplo de Xamarin.Forms UrhoSharp estos pasos:

1. [Crear una página de formularios de Xamarin](#1)
2. [Agregar el UrhoSurface](#2)
3. [Crear una aplicación Urho](#3)
4. [Agregue la clase de gráficos para el UrhoSurface](#4)
5. [Interactuar con UrhoSharp](#5)

Tenga en cuenta que el ejemplo usa C# 6 funciones y podrían no compilarse en versiones anteriores de Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. crear una página de Xamarin Forms

El código siguiente muestra una página de Xamarin.Forms `UrhoPage` antes de que se ha agregado ningún código relacionado con Urho:

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. agregar UrhoSurface

UrhoSharp puede hospedarse en un `ContentPage` al igual que otros controles de Xamarin.Forms.
El fragmento de código siguiente se muestra un `UrhoSurface` agregado a la página de Xamarin.Forms:

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. compilar una aplicación de Urho

Hacer referencia a la `Charts` clase para la implementación de los gráficos 3D de Urho utilizados en este ejemplo. El esquema de código básico se muestra a continuación: tenga en cuenta que la clase implementa `Urho.Application` que es diferente a la `Xamarin.Forms.Application` clase que se implementa en **App.cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

El [UrhoSharp documentación](~/graphics-games/urhosharp/index.md) contiene más información sobre cómo crear acciones y escenas 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Agregue la clase Charts a UrhoSurface

Use el `UrhoSurface.Show<T>` método genérico para agregar la aplicación Urho a la página de Xamarin.Forms. El siguiente fragmento de código muestra el código adicional necesario para crear el `Charts` clase:

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Nota: el `Show<T>` método es asincrónico y se debe llamar a con el `await` palabra clave.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interactuar con UrhoSharp

El ejemplo permite que las barras del gráfico se seleccionen y se puede modificar. El `Charts` clase expone la `Bars` y `SelectedBar` para habilitar esta interacción.

Cada "bar" tiene un controlador de eventos de selección agregado después de la `Charts` clase representa, repitiendo el expuesto `Bars` colección:

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

El controlador de eventos usa el valor de Xamarin.Forms `Slider` control para ajustar el alto de la barra determinado:

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Por último, conectar los dos `Slider` controles para que cuando cambia su valor se ve afectado el lienzo de UrhoSharp. El primer control deslizante gira la imagen del gráfico 3D y el segundo control deslizante ajusta el alto de la barra seleccionada.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Las animaciones en el [superior de la página](#what-is-urhosharp) mostrar la ejecución del ejemplo.

## <a name="summary"></a>Resumen

Esta página muestra el uso de UrhoSharp para agregar la visualización de datos 3D para Xamarin.Forms. Leer el [UrhoSharp documentación](~/graphics-games/urhosharp/index.md) para obtener más información sobre cómo crear escenas Urho que pueden incluirse en las aplicaciones de Xamarin.Forms mediante el método mostrado anteriormente.

## <a name="related-links"></a>Vínculos relacionados

- [Gráficos de ejemplo (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
