---
title: ''
description: Se puede hacer referencia directamente a las vistas nativas de iOS, Android y UWP desde Xamarin.Forms las páginas creadas con C#. En este artículo se muestra cómo agregar vistas nativas a un Xamarin.Forms diseño creado con C# y cómo invalidar el diseño de vistas personalizadas para corregir el uso de la API de medición.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 18cdeccbdff86a6b20aab4b33db259f1f06ee096
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139599"
---
# <a name="native-views-in-c"></a>Vistas nativas en C\#

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_Se puede hacer referencia directamente a las vistas nativas de iOS, Android y UWP desde Xamarin.Forms las páginas creadas con C#. En este artículo se muestra cómo agregar vistas nativas a un Xamarin.Forms diseño creado con C# y cómo invalidar el diseño de vistas personalizadas para corregir el uso de la API de medición._

## <a name="overview"></a>Información general

Cualquier Xamarin.Forms control que permita `Content` establecer o que tenga una `Children` colección puede agregar vistas específicas de la plataforma. Por ejemplo, un iOS `UILabel` se puede agregar directamente a la [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propiedad o a la [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) colección. Sin embargo, tenga en cuenta que esta funcionalidad requiere el uso de `#if` definiciones en Xamarin.Forms soluciones de proyecto compartidas y no está disponible en las Xamarin.Forms soluciones de biblioteca de .net Standard.

En las siguientes capturas de pantallas se muestran vistas específicas de la plataforma que se han agregado a un Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

[![](code-images/screenshots-sml.png "StackLayout Containing Platform-Specific Views")](code-images/screenshots.png#lightbox "StackLayout Containing Platform-Specific Views")

La capacidad de agregar vistas específicas de la plataforma a un Xamarin.Forms diseño está habilitada por dos métodos de extensión en cada plataforma:

- `Add`: agrega una vista específica de la plataforma a la [`Children`](xref:Xamarin.Forms.Layout`1.Children) colección de un diseño.
- `ToView`: toma una vista específica de la plataforma y la ajusta como una Xamarin.Forms [`View`](xref:Xamarin.Forms.View) que se puede establecer como la `Content` propiedad de un control.

El uso de estos métodos en un Xamarin.Forms proyecto compartido requiere la importación del espacio de nombres específico de la plataforma adecuado Xamarin.Forms :

- **iOS** : Xamarin.Forms . Plataforma. iOS
- **Android** : Xamarin.Forms . Plataforma. Android
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms . Plataforma. UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Adición de vistas específicas de la plataforma en cada plataforma

En las secciones siguientes se muestra cómo agregar vistas específicas de la plataforma a un Xamarin.Forms diseño en cada plataforma.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra cómo agregar un `UILabel` a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) y un [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

En el ejemplo se supone que las `stackLayout` instancias de y `contentView` se han creado previamente en XAML o C#.

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra cómo agregar un `TextView` a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) y un [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

En el ejemplo se supone que las `stackLayout` instancias de y `contentView` se han creado previamente en XAML o C#.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En el ejemplo de código siguiente se muestra cómo agregar un `TextBlock` a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) y un [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

En el ejemplo se supone que las `stackLayout` instancias de y `contentView` se han creado previamente en XAML o C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Invalidar medidas de plataforma para vistas personalizadas

Las vistas personalizadas en cada plataforma a menudo solo implementan correctamente la medida para el escenario de diseño para el que se diseñaron. Por ejemplo, una vista personalizada puede haberse diseñado para ocupar solo la mitad del ancho disponible del dispositivo. Sin embargo, después de compartirse con otros usuarios, la vista personalizada puede ser necesaria para ocupar el ancho total disponible del dispositivo. Por lo tanto, puede ser necesario invalidar una implementación de medida de vistas personalizadas cuando se reutiliza en un Xamarin.Forms diseño. Por ese motivo, los `Add` `ToView` métodos de extensión y proporcionan invalidaciones que permiten especificar los delegados de medida, lo que puede invalidar el diseño de la vista personalizada cuando se agrega a un Xamarin.Forms diseño.

En las secciones siguientes se muestra cómo invalidar el diseño de vistas personalizadas para corregir el uso de la API de medición.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra la `CustomControl` clase, que se hereda de `UILabel` :

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Una instancia de esta vista se agrega a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que la `CustomControl.SizeThatFits` invalidación siempre devuelve un alto de 150, la vista se mostrará con un espacio vacío por encima y por debajo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/ios-bad-measurement.png "iOS CustomControl with Bad SizeThatFits Implementation")

Una solución a este problema es proporcionar una `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Este método usa el ancho proporcionado por el `CustomControl.SizeThatFits` método, pero sustituye el alto de 150 por un alto de 70. Cuando `CustomControl` se agrega la instancia a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , se `FixSize` puede especificar el método como `GetDesiredSizeDelegate` para corregir la medida incorrecta proporcionada por la `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Esto hace que la vista personalizada se muestre correctamente, sin espacio vacío por encima y por debajo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/ios-good-measurement.png "iOS CustomControl with GetDesiredSize Override")

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra la `CustomControl` clase, que se hereda de `TextView` :

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Una instancia de esta vista se agrega a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que la `CustomControl.OnMeasure` invalidación siempre devuelve la mitad del ancho solicitado, se mostrará la vista ocupando solo la mitad del ancho disponible del dispositivo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/android-bad-measurement.png "Android CustomControl with Bad OnMeasure Implementation")

Una solución a este problema es proporcionar una `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Este método usa el ancho proporcionado por el `CustomControl.OnMeasure` método, pero lo multiplica por dos. Cuando `CustomControl` se agrega la instancia a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , se `FixSize` puede especificar el método como `GetDesiredSizeDelegate` para corregir la medida incorrecta proporcionada por la `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Esto hace que la vista personalizada se muestre correctamente, ocupando el ancho del dispositivo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/android-good-measurement.png "Android CustomControl with Custom GetDesiredSize Delegate")

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En el ejemplo de código siguiente se muestra la `CustomControl` clase, que se hereda de `Panel` :

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Una instancia de esta vista se agrega a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , como se muestra en el ejemplo de código siguiente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Sin embargo, dado que la `CustomControl.ArrangeOverride` invalidación siempre devuelve la mitad del ancho solicitado, la vista se recortará hasta la mitad del ancho disponible del dispositivo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/winrt-bad-measurement.png "UWP CustomControl with Bad ArrangeOverride Implementation")

Una solución a este problema es proporcionar una `ArrangeOverrideDelegate` implementación, al agregar la vista a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , tal y como se muestra en el ejemplo de código siguiente:

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Este método usa el ancho proporcionado por el `CustomControl.ArrangeOverride` método, pero lo multiplica por dos. Esto hace que la vista personalizada se muestre correctamente, ocupando el ancho del dispositivo, tal como se muestra en la siguiente captura de pantalla:

![](code-images/winrt-good-measurement.png "UWP CustomControl with ArrangeOverride Delegate")

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo agregar vistas nativas a un Xamarin.Forms diseño creado con C# y cómo invalidar el diseño de vistas personalizadas para corregir el uso de la API de medición.

## <a name="related-links"></a>Vínculos relacionados

- [NativeEmbedding (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
