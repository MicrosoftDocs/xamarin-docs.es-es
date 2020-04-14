---
ms.openlocfilehash: d6dbc82e56959399c2befb6a12f0a2cf3793ee5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "67560024"
---
El tamaño y la posición de las vistas secundarias dentro de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) dependen de los valores de las propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de las vistas secundarias, y de los valores de las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) se pueden establecer en los campos de la estructura [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions), que encapsula dos preferencias de diseño:

- **Alineación**: indica la alineación preferida de la vista secundaria, que determina su posición y su tamaño en el diseño principal.
- **Expansión**: indica si la vista secundaria debe usar espacio adicional, en caso de que esté disponible (solo lo usa [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`StackLayout`](xref:Xamarin.Forms.StackLayout) para establecer las opciones de alineación y expansión para cada [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Este código establece las preferencias de alineación de las cuatro primeras instancias de [`Label`](xref:Xamarin.Forms.Label) y las preferencias de expansión de las cuatro últimas instancias de `Label`. Los campos [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) y [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) se usan para definir la alineación de las instancias de [`Label`](xref:Xamarin.Forms.Label) en el [`StackLayout`](xref:Xamarin.Forms.StackLayout) primario. Los campos [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) y [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) se usan para definir la preferencia de alineación y para indicar si la vista ocupará más espacio, en caso de que esté disponible en el `StackLayout` primario.

    > [!NOTE]
    > El valor predeterminado de las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de una vista es [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un elemento StackLayout, con las opciones de alineación y expansión establecidas, en iOS y Android](../images/alignment-expansion.png "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")](../images/alignment-expansion-large.png#lightbox "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo respeta las preferencias de alineación de las vistas secundarias que tienen la dirección opuesta a la orientación del `StackLayout`. Por lo tanto, las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) dentro del `StackLayout` con orientación vertical establecen sus propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) en uno de los campos de alineación siguientes:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que coloca [`Label`](xref:Xamarin.Forms.Label) en el lado izquierdo del [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centra [`Label`](xref:Xamarin.Forms.Label) en el [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), que coloca [`Label`](xref:Xamarin.Forms.Label) en el lado derecho del [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garantiza que [`Label`](xref:Xamarin.Forms.Label) llena el ancho del [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo puede expandir las vistas secundarias en la dirección de su orientación. Por lo tanto, un `StackLayout` con orientación vertical puede expandir las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) que establecen sus propiedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en uno de los campos de alineación. Esto significa que, para la alineación vertical, cada `Label` ocupa la misma cantidad de espacio en el `StackLayout`. Aun así, solo el último elemento `Label`, que establece su propiedad [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand), tiene un tamaño diferente.

    > [!IMPORTANT]
    > Cuando se usa todo el espacio de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout), las preferencias de expansión no tienen ningún efecto.

    Para obtener más información sobre la alineación y la expansión, vea [Opciones de diseño de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`StackLayout`](xref:Xamarin.Forms.StackLayout) para establecer las opciones de alineación y expansión para cada [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Este código establece las preferencias de alineación de las cuatro primeras instancias de [`Label`](xref:Xamarin.Forms.Label) y las preferencias de expansión de las cuatro últimas instancias de `Label`. Los campos [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) y [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) se usan para definir la alineación de las instancias de [`Label`](xref:Xamarin.Forms.Label) en el [`StackLayout`](xref:Xamarin.Forms.StackLayout) primario. Los campos [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) y [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) se usan para definir la preferencia de alineación y para indicar si la vista ocupará más espacio, en caso de que esté disponible en el `StackLayout` primario.

    > [!NOTE]
    > El valor predeterminado de las propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) y [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de una vista es [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un elemento StackLayout, con las opciones de alineación y expansión establecidas, en iOS y Android](../images/alignment-expansion.png "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")](../images/alignment-expansion-large.png#lightbox "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo respeta las preferencias de alineación de las vistas secundarias que tienen la dirección opuesta a la orientación del `StackLayout`. Por lo tanto, las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) dentro del `StackLayout` con orientación vertical establecen sus propiedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) en uno de los campos de alineación siguientes:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que coloca [`Label`](xref:Xamarin.Forms.Label) en el lado izquierdo del [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centra [`Label`](xref:Xamarin.Forms.Label) en el [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), que coloca [`Label`](xref:Xamarin.Forms.Label) en el lado derecho del [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garantiza que [`Label`](xref:Xamarin.Forms.Label) llena el ancho del [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) solo puede expandir las vistas secundarias en la dirección de su orientación. Por lo tanto, un `StackLayout` con orientación vertical puede expandir las vistas secundarias de [`Label`](xref:Xamarin.Forms.Label) que establecen sus propiedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en uno de los campos de alineación. Esto significa que, para la alineación vertical, cada `Label` ocupa la misma cantidad de espacio en el `StackLayout`. Aun así, solo el último elemento `Label`, que establece su propiedad [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) en [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand), tiene un tamaño diferente.

    > [!IMPORTANT]
    > Cuando se usa todo el espacio de un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout), las preferencias de expansión no tienen ningún efecto.

    Para obtener más información sobre la alineación y la expansión, vea [Opciones de diseño de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).
