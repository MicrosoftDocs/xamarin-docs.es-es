---
ms.openlocfilehash: 456bc5fc0c30563c5950f4cacf3e3e7bdb177134
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689693"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`CollectionView`](xref:Xamarin.Forms.CollectionView) para que establezca la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) en `Single`, así como un controlador para el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged):

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Este código permite la selección de elementos únicos en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y establece el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) en un controlador de eventos denominado `OnSelectionChanged`. El controlador de eventos se creará en el paso siguiente.

1. En el **Explorador de soluciones**, en el proyecto **CollectionViewTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnSelectionChanged` a la clase:

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Si se selecciona un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView), se desencadena el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged), el cual ejecuta el método `OnSelectionChanged`. El argumento `sender` relativo al método es el objeto `CollectionView` responsable de desencadenar el evento, que se puede usar para acceder al objeto `CollectionView`. El argumento [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) relativo al método `OnSelectionChanged` proporciona el elemento seleccionado.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView que responde a la selección de elementos en iOS y Android](../images/item-selection.png "Selección de elementos CollectionView")](../images/item-selection-large.png#lightbox "Selección de elementos CollectionView")

    Establezca un punto de interrupción en el controlador de eventos `OnSelectionChanged` y seleccione un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examine el valor de la variable `selectedItem` para asegurarse de que contenga los datos del elemento seleccionado.

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre la selección de elementos, consulte [Selección de CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`CollectionView`](xref:Xamarin.Forms.CollectionView) para que establezca la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) en `Single`, así como un controlador para el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged):

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Este código permite la selección de elementos únicos en [`CollectionView`](xref:Xamarin.Forms.CollectionView) y establece el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) en un controlador de eventos denominado `OnSelectionChanged`. El controlador de eventos se creará en el paso siguiente.

1. En el **Panel de solución**, en el proyecto **CollectionViewTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, agregue el controlador de eventos `OnSelectionChanged` a la clase:

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Si se selecciona un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView), se desencadena el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged), el cual ejecuta el método `OnSelectionChanged`. El argumento `sender` relativo al método es el objeto `CollectionView` responsable de desencadenar el evento, que se puede usar para acceder al objeto `CollectionView`. El argumento [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) relativo al método `OnSelectionChanged` proporciona el elemento seleccionado.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView que responde a la selección de elementos en iOS y Android](../images/item-selection.png "Selección de elementos CollectionView")](../images/item-selection-large.png#lightbox "Selección de elementos CollectionView")

    Establezca un punto de interrupción en el controlador de eventos `OnSelectionChanged` y seleccione un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examine el valor de la variable `selectedItem` para asegurarse de que contenga los datos del elemento seleccionado.

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre la selección de elementos, consulte [Selección de CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).
