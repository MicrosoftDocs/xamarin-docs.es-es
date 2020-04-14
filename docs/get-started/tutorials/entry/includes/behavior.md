---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "61343479"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Entry`](xref:Xamarin.Forms.Entry) para personalizar su comportamiento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Este código establece las propiedades que personalizan el comportamiento de [`Entry`](xref:Xamarin.Forms.Entry). La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la longitud de entrada aceptable para `Entry` y la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false` para deshabilitar el corrector ortográfico. De forma similar, la propiedad [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) está establecida en `false` para deshabilitar la predicción de texto y la predicción de texto automática. Además, la propiedad [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garantiza que los caracteres escritos se enmascaran con un carácter de contraseña (un círculo negro).

    > [!NOTE]
    > Para algunos escenarios de entrada de texto, como escribir una contraseña, el corrector ortográfico y la predicción de texto proporcionan una experiencia negativa y, por tanto, deben deshabilitarse.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe que cada carácter se sustituye por un carácter de máscara de contraseña y que el número máximo de caracteres que se puede especificar son 15:

    [![Captura de pantalla de una entrada con texto enmascarado por caracteres de contraseña, en iOS y Android](../images/customize-behavior.png "Entrada con caracteres de contraseña enmascarados")](../images/customize-behavior-large.png#lightbox "Entrada con caracteres de contraseña enmascarados")

    Para obtener más información sobre la personalización del comportamiento de [`Entry`](xref:Xamarin.Forms.Entry), consulte la guía de la [Entrada de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Entry`](xref:Xamarin.Forms.Entry) para personalizar su comportamiento:

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Este código establece las propiedades que personalizan el comportamiento de [`Entry`](xref:Xamarin.Forms.Entry). La propiedad [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limita la longitud de entrada aceptable para `Entry` y la propiedad [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) está establecida en `false` para deshabilitar el corrector ortográfico. De forma similar, la propiedad [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) está establecida en `false` para deshabilitar la predicción de texto y la predicción de texto automática. Además, la propiedad [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) garantiza que los caracteres escritos se enmascaran con un carácter de contraseña (un círculo negro).

    > [!NOTE]
    > Para algunos escenarios de entrada de texto, como escribir una contraseña, el corrector ortográfico y la predicción de texto proporcionan una experiencia negativa y, por tanto, deben deshabilitarse.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe que cada carácter se sustituye por un carácter de máscara de contraseña y que el número máximo de caracteres que se puede especificar son 15:

    [![Captura de pantalla de una entrada con texto enmascarado por caracteres de contraseña, en iOS y Android](../images/customize-behavior.png "Entrada con caracteres de contraseña enmascarados")](../images/customize-behavior-large.png#lightbox "Entrada con caracteres de contraseña enmascarados")

    Para obtener más información sobre la personalización del comportamiento de [`Entry`](xref:Xamarin.Forms.Entry), consulte la guía de la [Entrada de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
