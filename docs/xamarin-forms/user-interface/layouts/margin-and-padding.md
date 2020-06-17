---
title: "margin and padding" Description: "las propiedades margin y padding controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas ".
MS. Prod: Xamarin ms. AssetID: BEB096BB-51DF-410F-B0F1-D235287B0F4A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/27/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="margin-and-padding"></a>Margen y relleno

_Las propiedades margin y padding controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas._

## <a name="overview"></a>Introducción

Margin y padding son conceptos de diseño relacionados:

- La [`Margin`](xref:Xamarin.Forms.View.Margin) propiedad representa la distancia entre un elemento y sus elementos adyacentes, y se usa para controlar la posición de representación del elemento y la posición de representación de sus vecinos. `Margin`los valores se pueden especificar en las clases de [diseño](~/xamarin-forms/user-interface/controls/layouts.md) y de [vista](~/xamarin-forms/user-interface/controls/views.md) .
- La [`Padding`](xref:Xamarin.Forms.Layout.Padding) propiedad representa la distancia entre un elemento y sus elementos secundarios, y se utiliza para separar el control de su propio contenido. `Padding`los valores se pueden especificar en las clases de [diseño](~/xamarin-forms/user-interface/controls/layouts.md) .

En el siguiente diagrama se ilustran los dos conceptos:

[![](margin-and-padding-images/margins-and-padding-sml.png "Margins and Padding Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "Margins and Padding Concepts")

Tenga en cuenta que [`Margin`](xref:Xamarin.Forms.View.Margin) los valores son aditivos. Por consiguiente, si dos elementos adyacentes especifican un margen de 20 píxeles, la distancia entre los elementos será de 40 píxeles. Además, los márgenes y el relleno son aditivos cuando se aplican ambos, en que la distancia entre un elemento y cualquier contenido será el margen más el relleno.

## <a name="specifying-a-thickness"></a>Especificar un grosor

Las [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Layout.Padding) propiedades y son de tipo [`Thickness`](xref:Xamarin.Forms.Thickness) . Existen tres posibilidades al crear una `Thickness` estructura:

- Cree una [`Thickness`](xref:Xamarin.Forms.Thickness) estructura definida por un único valor uniforme. El valor Single se aplica a los lados izquierdo, superior, derecho e inferior del elemento.
- Cree una [`Thickness`](xref:Xamarin.Forms.Thickness) estructura definida por valores horizontales y verticales. El valor horizontal se aplica simétricamente a los lados izquierdo y derecho del elemento, con el valor vertical que se aplica de forma simétrica a los lados superior e inferior del elemento.
- Cree una [`Thickness`](xref:Xamarin.Forms.Thickness) estructura definida por cuatro valores distintos que se apliquen a los lados izquierdo, superior, derecho e inferior del elemento.

En el siguiente ejemplo de código XAML se muestran las tres posibilidades:

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness`los valores pueden ser negativos, lo que suele recortar o sobredibujar el contenido.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado la diferencia entre las [`Margin`](xref:Xamarin.Forms.View.Margin) propiedades y y [`Padding`](xref:Xamarin.Forms.Layout.Padding) Cómo establecerlas. Las propiedades controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [Márgen](xref:Xamarin.Forms.View.Margin)
- [Espaciado interno](xref:Xamarin.Forms.Layout.Padding)
- [Thickness](xref:Xamarin.Forms.Thickness)
