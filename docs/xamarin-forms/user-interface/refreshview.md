---
title: Xamarin.Forms RefreshView
description: Xamarin.Forms RefreshView Es un control contenedor que proporciona funcionalidad de extracción para actualizar el contenido desplazable.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- RefreshView
- Universal Windows Platform
ms.openlocfilehash: 83802683aee722468acf9bcc827ba66f45c05e6b
ms.sourcegitcommit: cd0c0999b53e825b60471bfbfd4144cfcd783587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225486"
---
# <a name="xamarinforms-refreshview"></a>Xamarin.Forms RefreshView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

`RefreshView`Es un control contenedor que proporciona funcionalidad de extracción para actualizar el contenido desplazable. Por lo tanto, el elemento secundario de `RefreshView` debe ser un control desplazable, como [`ScrollView`](xref:Xamarin.Forms.ScrollView) , [`CollectionView`](xref:Xamarin.Forms.CollectionView) o [`ListView`](xref:Xamarin.Forms.ListView) .

`RefreshView` define las siguientes propiedades:

- `Command`, de tipo `ICommand` , que se ejecuta cuando se desencadena una actualización.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `IsRefreshing`, de tipo `bool` , que indica el estado actual de `RefreshView` .
- `RefreshColor`, de tipo `Color` , el color del círculo de progreso que aparece durante la actualización.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> En Universal Windows Platform , la dirección de extracción de un `RefreshView` se puede establecer con una específica de la plataforma. Para obtener más información, vea [ RefreshView dirección de extracción](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Creación de una clase RefreshView

En el ejemplo siguiente se muestra cómo crear una instancia `RefreshView` de en XAML:

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

`RefreshView`También se puede crear en el código:

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

En este ejemplo, `RefreshView` proporciona la funcionalidad de extracción para actualizar a un [`ScrollView`](xref:Xamarin.Forms.ScrollView) cuyo elemento secundario es [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) . `FlexLayout`Utiliza un diseño enlazable para generar su contenido enlazando a una colección de elementos y establece la apariencia de cada elemento con un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Para obtener más información sobre los diseños enlazables, vea [diseños enlazables en Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

El valor de la `RefreshView.IsRefreshing` propiedad indica el estado actual de `RefreshView` . Cuando el usuario desencadena una actualización, esta propiedad pasará automáticamente a `true` . Una vez finalizada la actualización, debe restablecer la propiedad a `false` .

Cuando el usuario inicia una actualización, `ICommand` se ejecuta la definida por la `Command` propiedad, que debe actualizar los elementos que se muestran. Se muestra una visualización de actualización mientras se produce la actualización, que consta de un círculo de progreso animado:

[![Captura de pantalla de una RefreshView actualización de datos, en iOS y Android](refreshview-images/default-progress-circle.png "[! Operador. NO-LOC (RefreshView)] actualizar datos")](refreshview-images/default-progress-circle-large.png#lightbox "[! Operador. NO-LOC (RefreshView)] actualizar datos")

> [!NOTE]
> Al establecer manualmente la `IsRefreshing` propiedad en `true` , se desencadenará la visualización de la actualización y se ejecutará el `ICommand` definido por la `Command` propiedad.

## <a name="refreshview-appearance"></a>RefreshViewaparición

Además de las propiedades que `RefreshView` heredan de la [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase, `RefreshView` también define la `RefreshColor` propiedad. Esta propiedad se puede establecer para definir el color del círculo de progreso que aparece durante la actualización:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

En la captura de pantalla siguiente se muestra un `RefreshView` con el `RefreshColor` conjunto de propiedades:

[![Captura de pantalla RefreshView con un círculo de progreso de verde, en iOS y Android](refreshview-images/teal-progress-circle.png "[! Operador. NO-LOC (RefreshView)] con un círculo de progreso de verde azulado")](refreshview-images/teal-progress-circle-large.png#lightbox "[! Operador. NO-LOC (RefreshView)] con un círculo de progreso de verde azulado")

Además, la `BackgroundColor` propiedad se puede establecer en un [`Color`](xref:Xamarin.Forms.Color) que representa el color de fondo del círculo de progreso.

> [!NOTE]
> En iOS, la `BackgroundColor` propiedad establece el color de fondo del `UIView` que contiene el círculo de progreso.

## <a name="disable-a-refreshview"></a>Deshabilitar unRefreshView

Una aplicación puede entrar en un estado en el que la extracción para actualizar no es una operación válida. En tales casos, `RefreshView` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false` . Esto impedirá que los usuarios puedan desencadenar la incorporación de cambios a la actualización.

Como alternativa, al definir la `Command` propiedad, `CanExecute` `ICommand` se puede especificar el delegado de para habilitar o deshabilitar el comando.

## <a name="related-links"></a>Vínculos relacionados

- [RefreshViewAdventureWorks](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [Diseños enlazables enXamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshViewEspecífico de la plataforma de dirección de extracción](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
