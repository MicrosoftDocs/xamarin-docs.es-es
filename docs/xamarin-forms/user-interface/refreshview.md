---
title: RefreshView de Xamarin. Forms
description: Xamarin. Forms RefreshView es un control contenedor que proporciona funcionalidad de extracción para actualizar el contenido desplazable.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
ms.openlocfilehash: b53c58a5e859bf7752855c3954666a062261599d
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697744"
---
# <a name="xamarinforms-refreshview"></a>RefreshView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)

El `RefreshView` es un control contenedor que proporciona funcionalidad de extracción para actualizar el contenido desplazable. Por lo tanto, el elemento secundario de un `RefreshView` debe ser un control desplazable, como [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`CollectionView`](xref:Xamarin.Forms.CollectionView)o [`ListView`](xref:Xamarin.Forms.ListView).

`RefreshView` define las siguientes propiedades:

- `Command`, de tipo `ICommand`, que se ejecuta cuando se desencadena una actualización.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `IsRefreshing`, de tipo `bool`, que indica el estado actual de la `RefreshView`.
- `RefreshColor`, de tipo `Color`, el color del círculo de progreso que aparece durante la actualización.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> En el Plataforma universal de Windows, la dirección de extracción de una `RefreshView` se puede establecer con una plataforma específica. Para obtener más información, vea [dirección de extracción de RefreshView](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Creación de un RefreshView

En el ejemplo siguiente se muestra cómo crear una instancia de un `RefreshView` en XAML:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

También se puede crear un `RefreshView` en el código:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

En este ejemplo, el `RefreshView` proporciona la funcionalidad de extracción para actualizar a una [`ScrollView`](xref:Xamarin.Forms.ScrollView) cuyo elemento secundario es un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout). En el `FlexLayout` se usa un diseño enlazable para generar su contenido enlazando con una colección de elementos y se establece la apariencia de cada elemento con un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obtener más información sobre los diseños enlazables, vea [diseños enlazables en Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

El valor de la propiedad `RefreshView.IsRefreshing` indica el estado actual del `RefreshView`. Cuando el usuario desencadena una actualización, esta propiedad pasará automáticamente a `true`. Una vez finalizada la actualización, debe restablecer la propiedad a `false`.

Cuando el usuario inicia una actualización, se ejecuta el `ICommand` definido por la propiedad `Command`, que debe actualizar los elementos que se muestran. Se muestra una visualización de actualización mientras se produce la actualización, que consta de un círculo de progreso animado:

[![Captura de pantalla de una RefreshView actualización de datos, en iOS y Android](refreshview-images/default-progress-circle.png "RefreshView actualizar datos")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView actualizar datos")

> [!NOTE]
> Establecer manualmente la propiedad `IsRefreshing` en `true` desencadenará la visualización de la actualización y ejecutará el `ICommand` definido por la propiedad `Command`.

## <a name="refreshview-appearance"></a>Apariencia de RefreshView

Además de las propiedades que `RefreshView` heredan de la clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) , `RefreshView` también define la propiedad `RefreshColor`. Esta propiedad se puede establecer para definir el color del círculo de progreso que aparece durante la actualización:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

En la captura de pantalla siguiente se muestra un `RefreshView` con el conjunto de propiedades `RefreshColor`:

[![Captura de pantalla de un RefreshView con un círculo de progreso de verde azulado, en iOS y Android](refreshview-images/teal-progress-circle.png "RefreshView con un círculo de progreso de verde azulado")](refreshview-images/teal-progress-circle-large.png#lightbox "RefreshView con un círculo de progreso de verde azulado")

Además, la propiedad `BackgroundColor` se puede establecer en un [`Color`](xref:Xamarin.Forms.Color) que representa el color de fondo del círculo de progreso.

> [!NOTE]
> En iOS, la propiedad `BackgroundColor` establece el color de fondo del `UIView` que contiene el círculo de progreso.

## <a name="disable-a-refreshview"></a>Deshabilitar un RefreshView

Una aplicación puede entrar en un estado en el que la extracción para actualizar no es una operación válida. En tales casos, el `RefreshView` se puede deshabilitar estableciendo su propiedad `IsEnabled` en `false`. Esto impedirá que los usuarios puedan desencadenar la incorporación de cambios a la actualización.

Como alternativa, al definir la propiedad `Command`, se puede especificar el delegado `CanExecute` de la `ICommand` para habilitar o deshabilitar el comando.

## <a name="related-links"></a>Vínculos relacionados

- [RefreshView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)
- [Diseños enlazables en Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [Específico de la plataforma de dirección de extracción RefreshView](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
