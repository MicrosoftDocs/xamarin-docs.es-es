---
title: "Adición de un reconocedor de gestos de reducir" description: "En este artículo, se explica cómo usar el gesto de reducir para realizar un zoom interactivo de una imagen en la ubicación donde se realice el gesto."
ms.prod: xamarin ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 01/21/2016 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="adding-a-pinch-gesture-recognizer"></a>Agregar un reconocedor de gestos de reducir

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_El gesto de reducir se usa para realizar un zoom interactivo y se implementa con la clase PinchGestureRecognizer. Un escenario común para el gesto de reducir es realizar un zoom interactivo de una imagen en la ubicación donde se realice el gesto. Esto se logra al escalar el contenido de la ventanilla, como se demuestra en este artículo._

Para que un elemento de interfaz de usuario se pueda mover con el gesto de reducir, cree una instancia de [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer), controle el evento [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) y agregue el nuevo reconocedor de gestos a la colección [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) en el elemento de interfaz de usuario. En el siguiente ejemplo de código, se muestra un elemento `PinchGestureRecognizer` asociado a un elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Esto también se puede lograr en XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Después, se agrega el código del controlador de eventos `OnPinchUpdated` al archivo de código subyacente:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Crear un contenedor de PinchToZoom

Para controlar el gesto de reducir y realizar esta operación de zoom, se necesita un cálculo matemático para transformar la interfaz de usuario. Esta sección contiene una clase auxiliar generalizada para realizar la operación matemática, que puede usarse para realizar un zoom interactivo en cualquier elemento de la interfaz de usuario. En el ejemplo de código siguiente se muestra la clase `PinchToZoomContainer`:

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

Esta clase se puede encapsular en un elemento de interfaz de usuario para que el gesto de reducir amplíe o reduzca el elemento de interfaz de usuario encapsulado. En el siguiente ejemplo de código de XAML, se muestra cómo encapsular `PinchToZoomContainer` en un elemento [`Image`](xref:Xamarin.Forms.Image):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

En el siguiente ejemplo de código, se muestra cómo el elemento `PinchToZoomContainer` encapsula un elemento [`Image`](xref:Xamarin.Forms.Image) en una página de C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Cuando el elemento [`Image`](xref:Xamarin.Forms.Image) recibe un gesto de reducir, la imagen mostrada se ampliará o reducirá. La acción de zoom se realiza mediante el método `PinchZoomContainer.OnPinchUpdated`, que se muestra en el siguiente ejemplo de código:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Este método actualiza el nivel de zoom del elemento de la interfaz de usuario encapsulada basándose en el gesto de reducir del usuario. Para lograr esto, se usan los valores de las propiedades [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) y [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) de la instancia de [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) para calcular el factor de escala que se aplicará en el origen del gesto de reducir. El elemento del usuario encapsulado se amplía o reduce en el origen del gesto de reducir al establecer las propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX), [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) y [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) en los valores calculados.

## <a name="related-links"></a>Vínculos relacionados

- [PinchGesture (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
