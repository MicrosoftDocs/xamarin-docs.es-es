---
title: Adición de un reconocedor de gesto de pulsar
description: En este artículo se explica cómo usar el gesto de pulsar para la detección de pulsaciones en una aplicación de Xamarin.Forms. La detección de pulsaciones se implementa con la clase TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771336"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Adición de un reconocedor de gesto de pulsar

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_El gesto de pulsar se usa para la detección de pulsaciones y se implementa con la clase TapGestureRecognizer._

Para que se pueda hacer clic en un elemento de interfaz de usuario con el gesto de pulsar, cree una instancia de [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer), controle el evento [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped), y agregue el nuevo reconocedor de gestos a la colección [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) en el elemento de interfaz de usuario. En el ejemplo de código siguiente se muestra un elemento `TapGestureRecognizer` adjunto a un elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

De forma predeterminada, la imagen responderá a pulsaciones simples. Establezca la propiedad [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) para que espere una pulsación doble (o más pulsaciones si es necesario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Cuando se establece [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) por encima de uno, el controlador de eventos solo se ejecuta si las pulsaciones se producen dentro de un período de tiempo concreto (que no se puede configurar). Si la segunda pulsación (o las posteriores) no se producen dentro de ese período, se omiten y se reinicia el "recuento de pulsaciones".

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso de Xaml

Un reconocedor de gestos se puede agregar a un control en Xaml mediante propiedades adjuntas. La sintaxis para agregar un elemento [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a una imagen se muestra a continuación (en este caso se define un evento de *pulsación doble*):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

El código para el controlador de eventos (en el ejemplo) incrementa un contador y cambia la imagen de color a blanco y negro.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Uso de ICommand

Las aplicaciones que usan el patrón Model-View-ViewModel (MVVM) suelen usar `ICommand` en lugar de conectar controladores de eventos directamente. El elemento [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) puede admitir fácilmente `ICommand` estableciendo el enlace en el código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

o con Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

El código completo para este modelo de vista se puede encontrar en el ejemplo. A continuación se muestran los detalles de implementación de `Command` relevantes:

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>Vínculos relacionados

- [TapGesture (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
