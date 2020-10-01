---
title: Capacidades de dispositivos de doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo usar la clase DualScreenInfo de Xamarin.Forms para optimizar la experiencia de la aplicación para dispositivos de doble pantalla como Surface Duo y Surface Neo.
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4e6bbd40fb80c2884013647ced3c3660ab4af738
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562683"
---
# <a name="no-locxamarinforms-dualscreeninfo-helper-class"></a>Clase auxiliar DualScreenInfo de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La clase `DualScreenInfo` permite determinar en qué panel se encuentra la vista, qué tamaño tiene, en qué posición está el dispositivo, el ángulo de la bisagra y mucho más.

## <a name="configure-dualscreeninfo"></a>Configuración de DualScreenInfo

Siga estas instrucciones para crear un diseño de doble pantalla en la aplicación:

1. Siga las instrucciones de la sección [Primeros pasos](index.md) para agregar NuGet y configurar la clase `MainActivity` de Android.
1. Agregue `using Xamarin.Forms.DualScreen;` al archivo de clase.
1. Use la clase `DualScreenInfo.Current` en la aplicación.

## <a name="properties"></a>Propiedades

- Cuando se distribuye en dos pantallas, `SpanningBounds` devuelve dos rectángulos que indican los límites de cada área visible. Si la ventana no se distribuye, se devolverá una matriz vacía.
- `HingeBounds` indica la posición de la bisagra en la pantalla.
- `IsLandscape` indica si el dispositivo está en posición horizontal. Esto resulta útil porque las API de orientación nativas no notifican correctamente la orientación cuando una aplicación se distribuye en dos pantallas.
- `SpanMode` indica si el diseño está en modo de alto, de ancho o de un solo panel.

Además, el evento `PropertyChanged` se desencadena cuando cambian las propiedades, y el evento `HingeAngleChanged` se desencadena cuando cambia el ángulo de la bisagra.

## <a name="poll-hinge-angle-on-android-and-uwp"></a>Sondeo del angulo de bisagra en Android y UWP

El método siguiente está disponible cuando se tiene acceso a `DualScreenInfo` desde proyectos de la plataforma Android y UWP:

- `GetHingeAngleAsync` recupera el ángulo actual de la bisagra del dispositivo. Al usar el simulador, se puede establecer el valor de HingeAngle si se modifica el sensor de presión.

Este método se puede invocar desde los representadores personalizados en Android y UWP. En el código siguiente se muestra un ejemplo de representador personalizado de Android:

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-in-your-application-window"></a>Acceso a DualScreenInfo en la ventana de la aplicación

En el código siguiente se muestra cómo acceder a `DualScreenInfo` en la ventana de la aplicación:

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented top-bottom
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented side-by-side
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-layouts"></a>Aplicación de DualScreenInfo a los diseños

La clase `DualScreenInfo` tiene un constructor que puede tomar un diseño y proporcionar información sobre él con respecto a las dos pantallas del dispositivo:

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}" />
        <ColumnDefinition Width="{Binding Column2Width}" />
        <ColumnDefinition Width="{Binding Column3Width}" />
    </Grid.ColumnDefinitions>
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="End"
           Text="I should be on the left side of the hinge" />
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="Start"
           Grid.Column="2"
           Text="I should be on the right side of the hinge" />
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

En la captura de pantalla siguiente se muestra el diseño resultante:

![Posicionamiento de un objeto Grid en dos pantallas](dual-screen-info-images/grid-on-two-screens.png)

## <a name="related-links"></a>Vínculos relacionados

- [DualScreen (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)