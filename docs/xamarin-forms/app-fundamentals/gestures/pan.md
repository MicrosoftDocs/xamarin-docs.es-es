---
title: Adición de un reconocedor de gesto de desplazamiento lateral
description: En este artículo se explica cómo usar un gesto de desplazamiento lateral para desplazar una imagen de forma horizontal y vertical, a fin de poder ver todo el contenido de imagen cuando se muestre en una ventanilla más pequeña que las dimensiones de la imagen.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 73e312a1af56091a7e579d3fcbcea810ee0efb1e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "68820973"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Adición de un reconocedor de gesto de desplazamiento lateral

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_El gesto de desplazamiento lateral se usa para detectar el movimiento de los dedos alrededor de la pantalla y aplicar ese movimiento al contenido, y se implementa con la clase `PanGestureRecognizer`. Un escenario habitual para el gesto de desplazamiento lateral consiste en desplazar una imagen de forma horizontal y vertical, para poder ver todo el contenido de imagen cuando se muestre en una ventanilla más pequeña que las dimensiones de la imagen. Esto se logra moviendo la imagen dentro de la ventanilla, y se muestra en este artículo._

Para que un elemento de interfaz de usuario se pueda mover con el gesto de desplazamiento lateral, cree una instancia de [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer), controle el evento [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) y agregue el nuevo reconocedor de gestos a la colección [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) en el elemento de interfaz de usuario. En el siguiente ejemplo de código, se muestra un elemento `PanGestureRecognizer` asociado a un elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Esto también se puede lograr en XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Después, se agrega el código del controlador de eventos `OnPanUpdated` al archivo de código subyacente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

## <a name="creating-a-pan-container"></a>Creación de un contenedor de desplazamiento lateral

Esta sección contiene una clase auxiliar generalizada que realiza el desplazamiento lateral de forma libre, que normalmente es adecuado para navegar dentro de imágenes o mapas. El control del gesto de desplazamiento lateral para realizar esta operación requiere un cálculo matemático para transformar la interfaz de usuario. Este cálculo matemático se usa para el desplazamiento lateral solo dentro de los límites del elemento de interfaz de usuario ajustado. En el ejemplo de código siguiente se muestra la clase `PanContainer`:

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Esta clase se puede encapsular en un elemento de interfaz de usuario para que el gesto desplace el elemento de interfaz de usuario encapsulado. En el ejemplo de código XAML siguiente se muestra la encapsulación de `PanContainer` en un elemento [`Image`](xref:Xamarin.Forms.Image):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

En el ejemplo de código siguiente se muestra cómo el elemento `PanContainer` encapsula un elemento [`Image`](xref:Xamarin.Forms.Image) en una página de C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

En los dos ejemplos, las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) se establecen en los valores de alto y ancho de la imagen que se va a mostrar.

Cuando el elemento [`Image`](xref:Xamarin.Forms.Image) recibe un gesto de desplazamiento lateral, se desplaza lateralmente la imagen mostrada. El desplazamiento lateral se realiza mediante el método `PanContainer.OnPanUpdated`, que se muestra en el ejemplo de código siguiente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Este método actualiza el contenido visible del elemento de interfaz de usuario encapsulado, en función del gesto de desplazamiento lateral del usuario. Esto se logra mediante el uso de los valores de las propiedades [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) y [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) de la instancia de [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) para calcular la dirección y la distancia del desplazamiento lateral. Las propiedades `App.ScreenWidth` y `App.ScreenHeight` proporcionan el alto y ancho de la ventanilla, y se establecen en los valores de ancho y alto de la pantalla del dispositivo por los proyectos específicos de la plataforma correspondiente. Después, se realiza el desplazamiento lateral del elemento de usuario encapsulado, mediante el establecimiento de sus propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) en los valores calculados.

Al desplazar lateralmente el contenido de un elemento que no ocupa toda la pantalla, el alto y ancho de la ventanilla se pueden obtener de las propiedades [`Height`](xref:Xamarin.Forms.VisualElement.Height) y [`Width`](xref:Xamarin.Forms.VisualElement.Width) del elemento.

> [!NOTE]
> Mostrar imágenes de alta resolución puede aumentar considerablemente la superficie de memoria de una aplicación. Por tanto, solo se deberían crear cuando sea necesario y deberían liberarse en cuanto la aplicación ya no las necesite. Para más información, vea [Optimizar los recursos de imagen](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Vínculos relacionados

- [PanGesture (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
