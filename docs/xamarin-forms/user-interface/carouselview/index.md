---
title: " Xamarin.Forms CarouselView" Descripción: "CarouselView es una vista para presentar datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos".
MS. Prod: Xamarin ms. AssetID: 5b673347-CDBA-4532-820f-fb5f070c86bc ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/08/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introduction"></a>[Introducción](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)Es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.

## <a name="data"></a>[Data](populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en cualquier colección que implementa `IEnumerable` . La apariencia de cada elemento se puede definir estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Diseño](layout.md)

De forma predeterminada, un mostrará [`CarouselView`](xref:Xamarin.Forms.CarouselView) sus elementos en una lista horizontal. Sin embargo, también tiene acceso a los mismos diseños como CollectionView, incluida una orientación vertical.

## <a name="interaction"></a>[Interacción](interaction.md)

Se puede tener acceso al elemento mostrado actualmente en un [`CarouselView`](xref:Xamarin.Forms.CarouselView) a través de las `CurrentItem` `Position` propiedades y.

## <a name="empty-views"></a>[Vistas vacías](emptyview.md)

En [`CarouselView`](xref:Xamarin.Forms.CarouselView) , se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrolling"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.
