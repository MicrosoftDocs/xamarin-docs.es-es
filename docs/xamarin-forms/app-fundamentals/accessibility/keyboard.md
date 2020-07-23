---
title: Accesibilidad del teclado
description: En lugar de usar la secuencia de tabulación predeterminada, a veces es necesario ajustar la accesibilidad de la interfaz de usuario mediante la especificación de la secuencia de tabulación con una combinación de las propiedades IsTabStop y TabIndex.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47770b4f57da530677dead051fc657ab24e776f5
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933814"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Accesibilidad del teclado en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

Los usuarios que emplean lectores de pantalla, o que tienen problemas de movilidad, pueden tener dificultades para usar aplicaciones que no proporcionan acceso adecuado con el teclado. En las aplicaciones de Xamarin.Forms se puede especificar un orden de tabulación esperado para mejorar su facilidad de uso y accesibilidad. La especificación de un orden de tabulación para los controles permite la navegación con el teclado, prepara las páginas de la aplicación para recibir la entrada en un orden concreto y permite que los lectores de pantalla lean elementos activables para el usuario.

De forma predeterminada, el orden de tabulación de los controles es el mismo orden en que se están indicados en XAML o agregados mediante programación a una colección secundaria. Este es el orden en que se navegará por los controles con un teclado y en que se leerán con un lector de pantalla; a menudo, este orden predeterminado es el mejor orden posible. Sin embargo, el orden predeterminado no es siempre el mismo que el orden previsto, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

La captura de pantalla siguiente muestra el orden de tabulación predeterminado para este ejemplo de código:

![Orden de tabulación basado en filas predeterminado](keyboard-images/default-tab-order.png)

Aquí, el orden de tabulación está basado en filas y es el orden en el cual los controles se indican en el XAML. Por lo tanto, al presionar la tecla TAB se navega por instancias [`Entry`](xref:Xamarin.Forms.Entry) de nombre, seguidas por instancias `Entry` de apellido. Sin embargo, una experiencia más intuitiva sería usar una navegación por tabulación de la columna en primer lugar, para que al presionar la tecla TAB se navegara por los pares de nombre y apellido. Esto puede lograrse mediante la especificación del orden de tabulación de los controles de entrada.

> [!NOTE]
> En la Plataforma universal de Windows, se pueden definir métodos abreviados de teclado que proporcionen una manera intuitiva para que los usuarios puedan navegar rápidamente e interactuar con la interfaz de usuario visible de la aplicación a través de un teclado, en lugar de a través de funciones táctiles o un mouse. Para obtener más información, consulte [Setting VisualElement Access Keys](~/xamarin-forms/platform/windows/visualelement-access-keys.md) (Configuración de claves de acceso de VisualElement).

## <a name="setting-the-tab-order"></a>Configuración del orden de tabulación

La propiedad `VisualElement.TabIndex` se utiliza para indicar el orden en que las instancias [`VisualElement`](xref:Xamarin.Forms.VisualElement) reciben el foco cuando el usuario navega por los controles presionando la tecla TAB. El valor predeterminado de la propiedad es 0, y se puede establecer en cualquier valor `int`.

Las reglas siguientes aplican cuando se usa el orden de tabulación predeterminado, o cuando se establece la propiedad `TabIndex`:

- Las instancias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) con `TabIndex` igual a 0 se agregan al orden de tabulación en función de su orden de declaración en colecciones secundarias o XAML.
- Las instancias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) con `TabIndex` mayor que 0 se agregan al orden de tabulación en función de su valor `TabIndex`.
- Las instancias de [`VisualElement`](xref:Xamarin.Forms.VisualElement) con `TabIndex` menor que 0 se agregan al orden de tabulación y aparecen antes que cualquier valor 0.
- Los conflictos relacionados con `TabIndex` se resuelven por orden de declaración.

Tras definirse un orden de tabulación, al presionar la tecla TAB se recorrerá cíclicamente el foco a través de controles en orden de `TabIndex` ascendente, con un ajuste alrededor del principio una vez alcanzado el control final.

El siguiente ejemplo de XAML muestra la propiedad `TabIndex` establecida en los controles de entrada para habilitar la navegación por tabulación de la columna en primer lugar:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

La captura de pantalla siguiente muestra el orden de tabulación para este ejemplo de código:

![Orden de tabulación basado en columnas](keyboard-images/correct-tab-order.png)

Aquí, el orden de tabulación está basado en columnas. Por lo tanto, al presionar la tecla TAB se navega por pares [`Entry`](xref:Xamarin.Forms.Entry) de nombre y apellido.

> [!IMPORTANT]
> Los lectores de pantalla de iOS y Android respetarán la propiedad `TabIndex` de un objeto [`VisualElement`](xref:Xamarin.Forms.VisualElement) al leer los elementos accesible en la pantalla.

## <a name="excluding-controls-from-the-tab-order"></a>Exclusión de controles del orden de tabulación

Además de establecer el orden de tabulación de los controles, puede ser necesario excluir los controles del orden de tabulación. Una manera de conseguirlo es estableciendo la propiedad [`IsEnabled`](xref:Xamarin.Forms.VisualElement) de los controles en `false`, ya que los controles deshabilitados se excluyen del orden de tabulación.

Sin embargo, puede ser necesario excluir los controles del orden de tabulación incluso cuando no están deshabilitados. Esto puede lograrse con la propiedad `VisualElement.IsTabStop`, que indica si un elemento [`VisualElement`](xref:Xamarin.Forms.VisualElement) se incluye en la navegación por tabulación. Su valor predeterminado es `true`, y cuando su valor es `false`, la infraestructura de navegación mediante tabulación omite el control, independientemente de si se ha definido un `TabIndex`.

## <a name="supported-controls"></a>Controles admitidos

Las propiedades `TabIndex` y `IsTabStop` se admiten en los siguientes controles, que aceptan entradas de teclado en una o varias plataformas:

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Cada uno de estos controles no puede recibir el foco del tabulador en todas las plataformas.

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
