---
Título: ' Xamarin.Forms ScrollView ' Descripción: ' en este artículo se explica cómo usar la Xamarin.Forms clase ScrollView para presentar diseños que no caben en una sola pantalla y que tienen espacio de contenido para el teclado '.
MS. Prod: MS. AssetID: MS. Technology: Author: MS. Author: MS. Date: no-LOC:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)contiene diseños y les permite desplazarse fuera de la ventana desplegable. `ScrollView`también se usa para permitir que las vistas se muevan automáticamente a la parte visible de la pantalla cuando se muestra el teclado.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidad

[`ScrollView`](xref:Xamarin.Forms.ScrollView)se puede usar para asegurarse de que las vistas mayores se muestran bien en teléfonos más pequeños. Por ejemplo, un diseño que funciona en un iPhone 6S puede estar recortado en un iPhone 4S. El uso de `ScrollView` permite que las partes recortadas del diseño se muestren en la pantalla más pequeña.

## <a name="usage"></a>Uso

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)los objetos no deben estar anidados. Además, `ScrollView` no se deben anidar con otros controles que proporcionen desplazamiento, como `ListView` y `WebView` .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)expone una `Content` propiedad, que se puede establecer en una sola vista o diseño. Considere este ejemplo de un diseño con un boxView muy grande, seguido de un `Entry` :

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

En C#:

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Antes de que el usuario se desplace hacia abajo, solo `BoxView` está visible:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Tenga en cuenta que cuando el usuario comienza a escribir texto en el `Entry` , la vista se desplaza para mantenerlo visible en la pantalla:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propiedades

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define las siguientes propiedades:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)Obtiene un [`Size`](xref:Xamarin.Forms.Size) valor que representa el tamaño del contenido.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)Obtiene o establece un [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) valor de enumeración que representa la dirección de desplazamiento del `ScrollView` .
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)Obtiene un `double` que representa la posición de desplazamiento X actual.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)Obtiene un `double` que representa la posición de desplazamiento Y actual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)Obtiene o establece un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa Cuándo está visible la barra de desplazamiento horizontal.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)Obtiene o establece un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa Cuándo está visible la barra de desplazamiento vertical.

> [!NOTE]
> El desplazamiento se puede deshabilitar estableciendo la [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propiedad en `Neither` .

## <a name="methods"></a>Métodos

[`ScrollView`](xref:Xamarin.Forms.ScrollView)proporciona un `ScrollToAsync` método, que se puede utilizar para desplazar la vista usando las coordenadas o especificando una vista determinada que debe hacerse visible.

Al utilizar coordenadas, especifique las `x` `y` coordenadas y, junto con un valor booleano que indica si se debe animar el desplazamiento:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> El `ScrollToAsync` método no producirá un desplazamiento cuando la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propiedad esté establecida en `Neither` .

Al desplazarse a un elemento determinado, la `ScrollToPosition` enumeración especifica dónde aparecerá el elemento en la vista.

- **Centro** &ndash; de desplaza el elemento hasta el centro de la parte visible de la vista.
- **Fin** &ndash; de desplaza el elemento hasta el final de la parte visible de la vista.
- **MakeVisible** &ndash; desplaza el elemento para que esté visible dentro de la vista.
- **Iniciar** &ndash; desplaza el elemento hasta el inicio de la parte visible de la vista.

La `IsAnimated` propiedad especifica cómo se desplazará la vista. Cuando se establece en `true` , se utilizará una animación Smooth, en lugar de mover el contenido al instante en la vista.

## <a name="events"></a>Eventos

[`ScrollView`](xref:Xamarin.Forms.ScrollView)define un solo evento, `Scrolled` . `Scrolled`se genera cuando la vista ha terminado de desplazarse. El controlador de eventos de `Scrolled` toma `ScrolledEventArgs` , que tiene `ScrollX` las `ScrollY` propiedades y. A continuación se muestra cómo actualizar una etiqueta con la posición de desplazamiento actual de `ScrollView` :

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Tenga en cuenta que las posiciones de desplazamiento pueden ser negativas debido al efecto de rebote al desplazarse al final de una lista.

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
