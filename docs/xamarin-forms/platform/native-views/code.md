---
title: Vistas nativas en C#
description: Vistas nativas de iOS, Android y UWP pueden hacer referencia directamente desde las páginas de Xamarin.Forms creadas con C#. En este artículo se muestra cómo agregar vistas nativas a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medición.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: ebaf5bf2fcb82dd98147819ea9e089dcd427affc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644450"
---
# <a name="native-views-in-c"></a>Vistas nativas en C#

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_Vistas nativas de iOS, Android y UWP pueden hacer referencia directamente desde las páginas de Xamarin.Forms creadas con C#. En este artículo se muestra cómo agregar vistas nativas a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medición._

## <a name="overview"></a>Información general

Cualquier control de Xamarin.Forms que permita `Content` debe establecerse, o que tenga un `Children` colección, puede agregar vistas específicas de plataforma. Por ejemplo, un iOS `UILabel` pueden agregarse directamente a la [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad, o a la [ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children) colección. Sin embargo, tenga en cuenta que esta funcionalidad requiere el uso de `#if` define en las soluciones de proyecto de Xamarin.Forms compartido y no está disponible desde soluciones de la biblioteca estándar de .NET de Xamarin.Forms.

Las capturas de pantalla siguientes muestran específicos de la plataforma, las vistas que se han agregado a un objeto Xamarin.Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "Que contiene vistas específicas de la plataforma de StackLayout")](code-images/screenshots.png#lightbox "StackLayout que contiene vistas específicas de plataforma")

La capacidad de agregar vistas específicas de plataforma a un diseño de Xamarin.Forms se habilita mediante dos métodos de extensión en cada plataforma:

- `Add` : agrega una vista específica de la plataforma para la [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) colección de un diseño.
- `ToView` : toma una vista específica de la plataforma y lo encapsula como un objeto Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View) que se pueden establecer como el `Content` propiedad de un control.

Mediante estos métodos en un proyecto compartido Xamarin.Forms, es necesario importar el espacio de nombres de Xamarin.Forms específicos de la plataforma adecuada:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Plataforma universal de Windows (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Agregar vistas específicas de la plataforma en cada plataforma

Las secciones siguientes muestran cómo agregar vistas específicas de plataforma a un diseño de Xamarin.Forms en cada plataforma.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra cómo agregar un `UILabel` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) y un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

El ejemplo supone que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra cómo agregar un `TextView` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) y un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

El ejemplo supone que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En el ejemplo de código siguiente se muestra cómo agregar un `TextBlock` a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) y un [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

El ejemplo supone que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Reemplazar las mediciones de plataforma para las vistas personalizadas

Vistas personalizadas en cada plataforma implementan a menudo solo correctamente medida para el escenario de diseño para el que se diseñaron. Por ejemplo, una vista personalizada que se han diseñado que solo ocupe la mitad del ancho disponible del dispositivo. Sin embargo, después de que se comparten con otros usuarios, la vista personalizada puede requerir hasta ocupar el ancho total disponible del dispositivo. Por lo tanto, puede ser necesario reemplazar una implementación de medición de vistas personalizadas cuando se reutilizan en un diseño de Xamarin.Forms. Por ese motivo, la `Add` y `ToView` métodos de extensión ofrecen invalidaciones que permiten a los delegados de medida que se especifique, que pueden reemplazar el diseño de vista personalizada cuando se agrega a un diseño de Xamarin.Forms.

Las siguientes secciones muestran cómo reemplazar el diseño de vistas personalizadas, para corregir su uso de la API de medición.

### <a name="ios"></a>iOS

El siguiente ejemplo de código muestra la `CustomControl` (clase), que hereda de `UILabel`:

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

Una instancia de esta vista se agrega a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), tal y como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que el `CustomControl.SizeThatFits` invalidación siempre devuelve un valor de 150 de alto, se mostrará la vista con un espacio vacío por encima y debajo de él, como se muestra en la siguiente captura de pantalla:

![](code-images/ios-bad-measurement.png "control personalizado con la implementación de SizeThatFits incorrecta de iOS")

Una solución a este problema consiste en proporcionar un `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

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

Este método utiliza el ancho proporcionado por el `CustomControl.SizeThatFits` método, pero sustituye la altura de 150 por una altura de 70. Cuando el `CustomControl` instancia se agrega a la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), el `FixSize` método se puede especificar como el `GetDesiredSizeDelegate` para corregir la medida incorrecta proporcionada por el `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

El resultado en la vista personalizada que se muestra correctamente, sin espacio en blanco por encima y debajo de él, como se muestra en la captura de pantalla siguiente:

![](code-images/ios-good-measurement.png "control personalizado con invalidación GetDesiredSize iOS")

### <a name="android"></a>Android

El siguiente ejemplo de código muestra la `CustomControl` (clase), que hereda de `TextView`:

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

Una instancia de esta vista se agrega a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), tal y como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que el `CustomControl.OnMeasure` invalidación siempre devuelve la mitad del ancho solicitado, la vista se mostrarán ocupando sólo la mitad el ancho disponible del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/android-bad-measurement.png "Control personalizado de Android con implementación OnMeasure incorrecta")

Una solución a este problema consiste en proporcionar un `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

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

Este método utiliza el ancho proporcionado por el `CustomControl.OnMeasure` método, pero la multiplica por dos. Cuando el `CustomControl` instancia se agrega a la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), el `FixSize` método se puede especificar como el `GetDesiredSizeDelegate` para corregir la medida incorrecta proporcionada por el `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Esto da como resultado la vista personalizada se muestra correctamente, ocupan el ancho del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/android-good-measurement.png "Control personalizado de Android con el delegado GetDesiredSize personalizado")

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

El siguiente ejemplo de código muestra la `CustomControl` (clase), que hereda de `Panel`:

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

Una instancia de esta vista se agrega a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), tal y como se muestra en el ejemplo de código siguiente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Sin embargo, dado que el `CustomControl.ArrangeOverride` invalidación siempre devuelve la mitad del ancho solicitado, se recortará la vista a la mitad el ancho disponible del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/winrt-bad-measurement.png "Control personalizado UWP con implementación ArrangeOverride incorrecta")

Una solución a este problema consiste en proporcionar un `ArrangeOverrideDelegate` implementación, al agregar la vista para la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), tal y como se muestra en el ejemplo de código siguiente:

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

Este método utiliza el ancho proporcionado por el `CustomControl.ArrangeOverride` método, pero la multiplica por dos. Esto da como resultado la vista personalizada se muestra correctamente, ocupan el ancho del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/winrt-good-measurement.png "Control personalizado UWP con el delegado ArrangeOverride")

## <a name="summary"></a>Resumen

En este artículo se explica cómo agregar vistas nativas a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medición.


## <a name="related-links"></a>Vínculos relacionados

- [NativeEmbedding (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
