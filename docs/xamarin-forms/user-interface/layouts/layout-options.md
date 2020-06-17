---
title: "opciones de diseño en Xamarin.Forms " Description: "cada Xamarin.Forms vista tiene las propiedades HorizontalOptions y VerticalOptions, de tipo LayoutOptions. En este artículo se explica el efecto que tiene cada valor de LayoutOptions en la alineación y la expansión de una vista. "
MS. Prod: Xamarin ms. AssetID: 7CAB5631-5153-4DEF-8AD7-C6011CE44307 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/10/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="layout-options-in-xamarinforms"></a>Opciones de diseño enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Cada Xamarin.Forms vista tiene las propiedades HorizontalOptions y VerticalOptions, de tipo LayoutOptions. En este artículo se explica el efecto que tiene cada valor de LayoutOptions en la alineación y la expansión de una vista._

## <a name="overview"></a>Introducción

La [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estructura encapsula dos preferencias de diseño:

- **Alignment** : la alineación preferida de la vista, que determina su posición y tamaño dentro de su diseño primario.
- **Expansión** : solo lo usa un [`StackLayout`](xref:Xamarin.Forms.StackLayout) y indica si la vista debe utilizar espacio adicional, si está disponible.

Estas preferencias de diseño se pueden aplicar a un [`View`](xref:Xamarin.Forms.View) , en relación con su elemento primario, estableciendo la [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedad o de `View` en uno de los campos públicos de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estructura. Los campos públicos son los siguientes:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Los `Start` `Center` campos,, `End` y `Fill` se utilizan para definir la alineación de la vista dentro del diseño primario:

- Para la alineación horizontal, [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) coloca el [`View`](xref:Xamarin.Forms.View) en el lado izquierdo del diseño primario y, para la alineación vertical, coloca `View` en la parte superior del diseño primario.
- Para la alineación horizontal y vertical, [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) centra horizontal o verticalmente el [`View`](xref:Xamarin.Forms.View) .
- Para la alineación horizontal, [`End`](xref:Xamarin.Forms.LayoutOptions.End) coloca el [`View`](xref:Xamarin.Forms.View) en el lado derecho del diseño primario y, para la alineación vertical, coloca `View` en la parte inferior del diseño primario.
- En el caso de la alineación horizontal, [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) garantiza que el [`View`](xref:Xamarin.Forms.View) ancho del diseño primario y para la alineación vertical, garantiza que `View` rellene el alto del diseño primario.

Los `StartAndExpand` `CenterAndExpand` valores,, `EndAndExpand` y `FillAndExpand` se usan para definir la preferencia de alineación y si la vista ocupará más espacio si está disponible en el elemento primario [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> El valor predeterminado de las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de una vista es [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

## <a name="alignment"></a>Alineación

La alineación controla cómo se coloca una vista dentro de su diseño primario cuando el diseño primario contiene espacio no utilizado (es decir, el diseño primario es mayor que el tamaño combinado de todos sus elementos secundarios).

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo respeta los `Start` `Center` campos,, `End` y `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) en las vistas secundarias que están en la dirección opuesta a la `StackLayout` orientación. Por lo tanto, las vistas secundarias dentro de una orientación vertical `StackLayout` pueden establecer sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propiedades en uno de los `Start` `Center` campos,, `End` o `Fill` . Del mismo modo, las vistas secundarias dentro de una orientación horizontal `StackLayout` pueden establecer sus [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades en uno de los `Start` `Center` campos,, `End` o `Fill` .

No [`StackLayout`](xref:Xamarin.Forms.StackLayout) respeta los `Start` `Center` campos,, `End` y en las `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) vistas secundarias que se encuentran en la misma dirección que la `StackLayout` orientación. Por consiguiente, una orientada verticalmente `StackLayout` omite `Start` los `Center` campos,, `End` o `Fill` si se establecen en las [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades de las vistas secundarias. De forma similar, una orientación horizontal `StackLayout` omite los `Start` `Center` campos,, `End` o `Fill` si se establecen en las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propiedades de las vistas secundarias.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)generalmente invalida las solicitudes de tamaño especificadas mediante [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) las [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) propiedades y.

En el siguiente ejemplo de código XAML se muestra una orientación vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) en la que cada elemento secundario [`Label`](xref:Xamarin.Forms.Label) establece su [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propiedad en uno de los cuatro campos de alineación de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

A continuación se muestra el código de C# equivalente:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

El código da como resultado el diseño que se muestra en las siguientes capturas de pantallas:

[![](layout-options-images/alignment.png "Alignment Layout Options")](layout-options-images/alignment-large.png#lightbox "Alignment Layout Options")

## <a name="expansion"></a>Expansión

La expansión controla si una vista ocupará más espacio, si está disponible, dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Si `StackLayout` contiene espacio sin usar (es decir, el valor de `StackLayout` es mayor que el tamaño combinado de todos sus elementos secundarios), el espacio no utilizado se comparte por igual entre todas las vistas secundarias que solicitan la expansión estableciendo sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades o en un [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) campo que utiliza el `AndExpand` sufijo. Tenga en cuenta que cuando se usa todo el espacio de `StackLayout` , las opciones de expansión no tienen ningún efecto.

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo puede expandir las vistas secundarias en la dirección de su orientación. Por lo tanto, una orientación vertical `StackLayout` puede expandir vistas secundarias que establecen sus [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades en uno de los `StartAndExpand` `CenterAndExpand` campos,, `EndAndExpand` o `FillAndExpand` , si `StackLayout` contiene espacio sin usar. Del mismo modo, una orientación horizontal `StackLayout` puede expandir vistas secundarias que establecen sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propiedades en uno de los `StartAndExpand` `CenterAndExpand` campos,, `EndAndExpand` o `FillAndExpand` , si `StackLayout` contiene espacio sin usar.

[`StackLayout`](xref:Xamarin.Forms.StackLayout)No puede expandir las vistas secundarias en la dirección opuesta a su orientación. Por lo tanto, en una orientación vertical `StackLayout` , establecer la [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propiedad en una vista secundaria en [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) tiene el mismo efecto que establecer la propiedad en [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) .

> [!NOTE]
> Tenga en cuenta que al habilitar la expansión no se cambia el tamaño de una vista a menos que use [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) .

En el siguiente ejemplo de código XAML se muestra una orientación vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) en la que cada elemento secundario [`Label`](xref:Xamarin.Forms.Label) establece su [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedad en uno de los cuatro campos de expansión de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

A continuación se muestra el código de C# equivalente:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

El código da como resultado el diseño que se muestra en las siguientes capturas de pantallas:

[![](layout-options-images/expansion.png "Expansion Layout Options")](layout-options-images/expansion-large.png#lightbox "Expansion Layout Options")

Cada [`Label`](xref:Xamarin.Forms.Label) ocupa la misma cantidad de espacio dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Aun así, solo el último elemento `Label`, que establece su propiedad [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand), tiene un tamaño diferente. Además, cada uno `Label` está separado por un rojo pequeño [`BoxView`](xref:Xamarin.Forms.BoxView) , lo que permite que el espacio que `Label` ocupa se vea fácilmente.

## <a name="summary"></a>Resumen

En este artículo se ha explicado el efecto que [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) tiene cada valor de estructura en la alineación y la expansión de una vista, con respecto a su elemento primario. Los `Start` `Center` campos,, `End` y `Fill` se utilizan para definir la alineación de la vista dentro del diseño primario, y los `StartAndExpand` `CenterAndExpand` campos,, `EndAndExpand` y `FillAndExpand` se usan para definir las preferencias de alineación y para determinar si la vista ocupará más espacio, si está disponible, dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

## <a name="related-links"></a>Vínculos relacionados

- [LayoutOptions (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
