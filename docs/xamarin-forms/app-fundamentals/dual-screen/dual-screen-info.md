---
title: DualScreenInfo de Xamarin.Forms
description: En esta guía se explica cómo usar DualScreenInfo de Xamarin.Forms para optimizar la experiencia de la aplicación para dispositivos de doble pantalla como Surface Duo y Surface Neo.
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: cae704b7d6300a9dc5eb456f0dec8989813c6581
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145619"
---
# <a name="xamarinforms-dualscreeninfo"></a>DualScreenInfo de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

_DualScreenInfo se puede usar para detectar e informar sobre los cambios y la relación actual de un diseño con las pantallas dobles._

## <a name="properties"></a>Propiedades

- `SpanningBounds` cuando se distribuye en dos pantallas, se devolverán dos rectángulos que indican los límites de cada área visible. Si la ventana no se distribuye, se devolverá una matriz vacía.
- `HingeBounds` posición de la bisagra en la pantalla.
- `IsLandscape` indica si el dispositivo está en posición horizontal. Esto resulta útil porque las API de orientación nativas no notifican correctamente la orientación cuando la aplicación se distribuye en dos pantallas.
- `PropertyChanged` se desencadena cuando cambia una propiedad.
- `SpanMode` indica si el diseño está en modo de alto, de ancho o de un solo panel.

## <a name="android-only-property"></a>Propiedad exclusiva de Android

Esta propiedad solo está disponible cuando se accede a DualScreenInfo desde el proyecto de la plataforma Android.
Puede usar esta propiedad desde un representador personalizado.

- `GetHingeAngleAsync` recupera el ángulo actual de la bisagra del dispositivo. Al usar el simulador, se puede establecer el valor de HingeAngle si se modifica el sensor de presión.

## <a name="android-custom-renderer-for-polling-hinge-angle"></a>Representador personalizado de Android para sondear el ángulo de la bisagra

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

## <a name="access-dualscreeninfo-for-application-window"></a>Acceso a DualScreenInfo para la ventana de la aplicación

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
    // window is spanned across two screens and oriented as landscape
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented as portrait
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-your-own-layouts"></a>Aplicación de DualScreenInfo a diseños propios

DualScreenInfo tiene un constructor que puede tomar un diseño y proporcionar información sobre él con respecto a las dos pantallas del dispositivo.

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}"></ColumnDefinition>
        <ColumnDefinition Width="{Binding Column2Width}"></ColumnDefinition>
        <ColumnDefinition Width="{Binding Column3Width}"></ColumnDefinition>
    </Grid.ColumnDefinitions>

    <Label FontSize="Large" VerticalOptions="Center" HorizontalOptions="End" Text="I should be on the left side of the hinge"></Label>
    <Label FontSize="Large" VerticalOptions="Center" HorizontalOptions="Start" Grid.Column="2" Text="I should be on the right side of the hinge"></Label>
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

![](dual-screen-info-images/grid-on-two-screens.png "Positioning Grid on Two Screens")

## <a name="related-links"></a>Vínculos relacionados

- [DualScreen (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
