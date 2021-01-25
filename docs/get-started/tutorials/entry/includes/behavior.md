---
ms.openlocfilehash: f4fccbfe7e00a353682b0dfe787c291b38e5cd76
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690122"
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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe que cada carácter se sustituye por un carácter de máscara de contraseña y que el número máximo de caracteres que se puede especificar son 15:

    [![Captura de pantalla de una entrada con texto enmascarado por caracteres de contraseña, en iOS y Android](../images/customize-behavior.png "Entrada con caracteres de contraseña enmascarados")](../images/customize-behavior-large.png#lightbox "Entrada con caracteres de contraseña enmascarados")

    En Visual Studio, detenga la aplicación.

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

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio para Mac, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Escriba texto en [`Entry`](xref:Xamarin.Forms.Entry) y observe que cada carácter se sustituye por un carácter de máscara de contraseña y que el número máximo de caracteres que se puede especificar son 15:

    [![Captura de pantalla de una entrada con texto enmascarado por caracteres de contraseña, en iOS y Android](../images/customize-behavior.png "Entrada con caracteres de contraseña enmascarados")](../images/customize-behavior-large.png#lightbox "Entrada con caracteres de contraseña enmascarados")

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre la personalización del comportamiento de [`Entry`](xref:Xamarin.Forms.Entry), consulte la guía de la [Entrada de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
