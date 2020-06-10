---
title: "Using UrhoSharp in Xamarin.Forms " Description: "en este artículo se explica cómo se puede usar UrhoSharp para agregar gráficos 3D a una Xamarin.Forms aplicación para la visualización avanzada".
MS. Prod: Xamarin ms. AssetID: 0646B98E-CC04-4537-9715-9F82338FD7FF ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/11/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="using-urhosharp-in-xamarinforms"></a>Usar UrhoSharp enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>¿Qué es UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) es un motor 3D eficaz para los desarrolladores de Xamarin y .net. En la [Introducción](~/graphics-games/urhosharp/introduction.md) se explica más acerca de la biblioteca UrhoSharp y en [estas notas](~/graphics-games/urhosharp/using.md) se describe cómo programar las escenas y las acciones.

UrhoSharp se puede usar para representar gráficos en Xamarin.Forms aplicaciones.
Este [ejemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample) muestra cómo se podría usar UrhoSharp para construir un gráfico 3D interactivo:

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Adición de los paquetes NuGet de UrhoSharp

Antes de usar UrhoSharp, los desarrolladores deben agregar el paquete NuGet UrhoSharp a su solución. En esta guía se da por supuesto que se trata de un Xamarin.Forms proyecto con un proyecto de biblioteca de iOS, Android y .net Standard. Todo el código se escribirá en el proyecto de biblioteca de .NET Standard; pero también se debe agregar el NuGet UrhoSharp a los proyectos de iOS y Android.

El paquete NuGet UrhoSharp. Forms contiene todos los objetos necesarios para crear objetos UrhoSharp. El paquete NuGet UrhoSharp. Forms incluye la `UrhoSurface` clase, que se usa para hospedar UrhoSharp en Xamarin.Forms .
Para empezar, haga clic con el botón derecho en la carpeta **paquetes** en el proyecto de biblioteca de .net Standard y seleccione **agregar paquetes.**... Escriba el término de búsqueda **UrhoSharp. Forms**, seleccione **UrhoSharp para Xamarin.Forms **y, a continuación, haga clic en **Agregar paquete**.

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

El paquete NuGet UrhoSharp. Forms se agregará al proyecto:

![](urhosharp-images/packages.png "Packages Folder")

Repita los pasos anteriores para los proyectos específicos de la plataforma (como iOS y Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Tutorial: agregar UrhoSharp a una Xamarin.Forms aplicación

En estos pasos se describe el código del Xamarin.Forms ejemplo UrhoSharp:

1. [Crear una página de Xamarin Forms](#1-create-a-xamarin-forms-page)
2. [Agregar UrhoSurface](#2-add-the-urhosurface)
3. [Compilación de una aplicación de Urho](#3-build-an-urho-application)
4. [Agregue la clase Charts a UrhoSurface](#4-add-the-charts-class-to-the-urhosurface)
5. [Interacción con UrhoSharp](#5-interacting-with-urhosharp)

Tenga en cuenta que el ejemplo usa características de C# 6 y puede no compilarse en versiones anteriores de Visual Studio.

### <a name="1-create-a-xamarin-forms-page"></a>1. crear una página de Xamarin Forms

En el código siguiente se muestra una Xamarin.Forms página `UrhoPage` antes de que se haya agregado cualquier código relacionado con Urho:

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

### <a name="2-add-the-urhosurface"></a>2. agregar UrhoSurface

UrhoSharp se puede hospedar en un `ContentPage` como otros Xamarin.Forms controles.
El siguiente fragmento de código muestra una `UrhoSurface` agregada a la Xamarin.Forms página:

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

### <a name="3-build-an-urho-application"></a>3. compilar una aplicación de Urho

Consulte la `Charts` clase para la implementación de los gráficos Urho 3D que se usan en este ejemplo. A continuación se muestra el esquema de código básico: tenga en cuenta que la clase implementa, `Urho.Application` que es diferente de la `Xamarin.Forms.Application` clase que se implementa en **app.CS**.

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

La [documentación de UrhoSharp](~/graphics-games/urhosharp/index.md) contiene más información sobre cómo crear escenas y acciones 3D.

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Agregue la clase Charts a UrhoSurface

Use el `UrhoSurface.Show<T>` método genérico para agregar la aplicación Urho a la Xamarin.Forms página. El siguiente fragmento de código muestra el código adicional necesario para crear la `Charts` clase:

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

Nota: el `Show<T>` método es asincrónico y se debe llamar con la `await` palabra clave.

### <a name="5-interacting-with-urhosharp"></a>5. interactuar con UrhoSharp

El ejemplo permite seleccionar y modificar las barras del gráfico. La `Charts` clase expone `Bars` y `SelectedBar` para habilitar esta interacción.

Cada "barra" tiene un controlador de eventos de selección agregado una vez `Charts` representada la clase, recorriendo en iteración la `Bars` colección expuesta:

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

El controlador de eventos usa el valor del Xamarin.Forms `Slider` control para ajustar el alto de la barra determinada:

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

Por último, conecte los dos `Slider` controles para que cuando su valor cambie el lienzo UrhoSharp se vea afectado. El primer control deslizante gira la imagen del gráfico 3D y el segundo control deslizante ajusta el alto de la barra seleccionada.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Las animaciones en la [parte superior de la página](#what-is-urhosharp) muestran el ejemplo en ejecución.

## <a name="summary"></a>Resumen

En esta página se muestra cómo se puede usar UrhoSharp para agregar la visualización de datos 3D a Xamarin.Forms . Lea la [documentación de UrhoSharp](~/graphics-games/urhosharp/index.md) para obtener más información sobre cómo crear escenas de Urho que se pueden incluir en Xamarin.Forms aplicaciones con el método mostrado anteriormente.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de gráficos (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
