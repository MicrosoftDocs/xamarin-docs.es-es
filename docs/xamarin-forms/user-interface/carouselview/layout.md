---
title: CarouselView de Xamarin. Forms
description: De forma predeterminada, un CarouselView mostrará sus elementos en una lista horizontal. Sin embargo, también tiene acceso a los mismos diseños como CollectionView, incluida una orientación vertical.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908347"
---
# <a name="xamarinforms-carouselview-layouts"></a>Diseños CarouselView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>Introducción

Gran parte de la funcionalidad de diseño disponible para CarouselView se origina desde CollectionView. Puede consultar la [documentación del diseño](../collectionview/layout.md) de la CollectionView para ver el uso de diversos diseños.

## <a name="differences-from-collectionview"></a>Diferencias de CollectionView

De forma predeterminada, los elementos de un CarouselView se orientarán horizontalmente, como es la función típica de un carrusel en las aplicaciones.

CarouselView también proporciona algunas propiedades adicionales:

> [!IMPORTANT]
> Las propiedades adicionales de CarouselView todavía están en desarrollo y esta lista no se ha completado todavía.

| API | Función |
|---|---|---|
| NumberOfSideItems | Establece el número de elementos que aparecen a cada lado del elemento actual. El valor predeterminado es 0.
| PeekAreaInsets | Proporciona una manera de indicar visiblemente al usuario que el CarouselView tiene elementos adicionales a los que desplazarse ajustando su nivel de visibilidad junto al elemento actual.

## <a name="setting-the-number-of-fully-visible-items"></a>Establecer el número de elementos totalmente visibles

De forma predeterminada, CarouselView muestra un elemento en su totalidad en la pantalla. Los usuarios pueden establecer `NumberOfSideItems` la propiedad para permitir que se muestren más elementos adyacentes al elemento actual. Tenga en cuenta que los valores `PeekAreaInsets` establecidos en seguirán aplicándose.

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[ ![Captura de pantalla de un CarouselView con elementos laterales, en](carouselview-images/side-items.png "elementos del lado CarouselView") de Android] (carouselview-images/side-items-large.png#lightbox "Elementos del lado de CarouselView")

## <a name="making-adjacent-items-partially-visible"></a>Mostrar elementos adyacentes parcialmente visibles

Cuando se usa un CarouselView en la aplicación, puede resultar útil indicar al usuario que el CarouselView funciona de este modo estableciendo la `PeekAreaInsets` propiedad en un valor distinto de cero (valor predeterminado), que los expone parcialmente en la pantalla.

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[ ![Captura de pantalla de un CarouselView con elementos laterales, en](carouselview-images/peek-area-insets.png "elementos del lado CarouselView") de Android] (carouselview-images/peek-area-insets-large.png#lightbox "Elementos del lado de CarouselView")

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [Documentación del diseño de CollectionView](../collectionview/layout.md)
