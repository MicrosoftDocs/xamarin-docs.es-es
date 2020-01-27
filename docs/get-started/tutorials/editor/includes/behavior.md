---
ms.openlocfilehash: bd3f37082443e93f10f60d9466fe22aae8571b14
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "61373443"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Editor`](xref:Xamarin.Forms.Editor) para personalizar su comportamiento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Este código establece las propiedades que personalizan el comportamiento de [`Editor`](xref:Xamarin.Forms.Editor). La propiedad [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) está establecida en [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), lo cual indica que la altura de `Editor` aumentará cuando se rellene con texto y disminuirá a medida que se elimine texto. La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la longitud de entrada que se acepta para `Editor`. Además, la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false` para deshabilitar el corrector ortográfico, mientras que la propiedad `IsTextPredictionEnabled` está establecida en `false` para deshabilitar la predicción de texto y la predicción de texto automática.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Escriba texto en [`Editor`](xref:Xamarin.Forms.Entry) y observe que la altura de `Editor` aumenta a medida que se rellena con texto y disminuye a medida que se elimina texto, y que el número máximo de caracteres que se puede especificar son 200:

    [![Captura de pantalla de un editor con ajuste automático de tamaño, en iOS y Android](../images/customize-behavior.png "Editor con ajuste automático de tamaño")](../images/customize-behavior-large.png#lightbox "Editor con ajuste automático de tamaño")

    Para obtener más información sobre la personalización del comportamiento de [`Editor`](xref:Xamarin.Forms.Editor), consulte la guía del [Editor de Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Editor`](xref:Xamarin.Forms.Editor) para personalizar su comportamiento:

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Este código establece las propiedades que personalizan el comportamiento de [`Editor`](xref:Xamarin.Forms.Editor). La propiedad [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) está establecida en [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), lo cual indica que la altura de `Editor` aumentará cuando se rellene con texto y disminuirá a medida que se elimine texto. La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la longitud de entrada que se acepta para `Editor`. Además, la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false` para deshabilitar el corrector ortográfico, mientras que la propiedad `IsTextPredictionEnabled` está establecida en `false` para deshabilitar la predicción de texto y la predicción de texto automática.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Escriba texto en [`Editor`](xref:Xamarin.Forms.Entry) y observe que la altura de `Editor` aumenta a medida que se rellena con texto y disminuye a medida que se elimina texto, y que el número máximo de caracteres que se puede especificar son 200:

    [![Captura de pantalla de un editor con ajuste automático de tamaño, en iOS y Android](../images/customize-behavior.png "Editor con ajuste automático de tamaño")](../images/customize-behavior-large.png#lightbox "Editor con ajuste automático de tamaño")

    Para obtener más información sobre la personalización del comportamiento de [`Editor`](xref:Xamarin.Forms.Editor), consulte la guía del [Editor de Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
