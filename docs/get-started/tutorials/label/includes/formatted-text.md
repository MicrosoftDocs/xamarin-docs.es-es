---
ms.openlocfilehash: 4352ff38e785a0a222cf8d2d64511ed14b3ae192
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689601"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración [`Label`](xref:Xamarin.Forms.Label) para presentar texto que usa varios formatos, en un solo objeto `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Este código muestra el texto, en un solo objeto [`Label`](xref:Xamarin.Forms.Label) que usa varios formatos. El texto del primer elemento [`Span`](xref:Xamarin.Forms.Span) se muestra con el formato establecido en `Label`, mientras que el texto en las instancias segunda y tercera de `Span` se muestra con el formato establecido en `Label` y el formato adicional establecido en cada `Span`.

    > [!NOTE]
    > La propiedad [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) es de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que consta de una o varias instancias[`Span`](xref:Xamarin.Forms.Span).

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Observe que el aspecto del objeto [`Label`](xref:Xamarin.Forms.Label) ha cambiado:

    [![Captura de pantalla de una etiqueta que muestra texto con formato, en iOS y Android](../images/label-formatted-text.png "Etiqueta con texto con formato")](../images/label-formatted-text-large.png#lightbox "Etiqueta con texto con formato")

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre la configuración del aspecto de [`Span`](xref:Xamarin.Forms.Span), consulte [Texto con formato](~/xamarin-forms/user-interface/text/label.md#formatted-text) en la [Guía sobre las etiquetas de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración [`Label`](xref:Xamarin.Forms.Label) para presentar texto que usa varios formatos, en un solo objeto `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Este código muestra el texto, en un solo objeto [`Label`](xref:Xamarin.Forms.Label) que usa varios formatos. El texto del primer elemento [`Span`](xref:Xamarin.Forms.Span) se muestra con el formato establecido en `Label`, mientras que el texto en las instancias segunda y tercera de `Span` se muestra con el formato establecido en `Label` y el formato adicional establecido en cada `Span`.

    > [!NOTE]
    > La propiedad [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) es de tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que consta de una o varias instancias[`Span`](xref:Xamarin.Forms.Span).

1. Si la aplicación se sigue ejecutando, guarde los cambios hechos al archivo, y la interfaz de usuario de la aplicación se actualizará automáticamente en su simulador o emulador. De lo contrario, en la barra de herramientas de Visual Studio para Mac, presione el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Observe que el aspecto del objeto [`Label`](xref:Xamarin.Forms.Label) ha cambiado:

    [![Captura de pantalla de una etiqueta que muestra texto con formato, en iOS y Android](../images/label-formatted-text.png "Etiqueta con texto con formato")](../images/label-formatted-text-large.png#lightbox "Etiqueta con texto con formato")

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre la configuración del aspecto de [`Span`](xref:Xamarin.Forms.Span), consulte [Texto con formato](~/xamarin-forms/user-interface/text/label.md#formatted-text) en la [Guía sobre las etiquetas de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
