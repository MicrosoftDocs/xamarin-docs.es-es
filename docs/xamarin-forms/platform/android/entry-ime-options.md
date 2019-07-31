---
title: Opciones del editor de métodos de entrada de entrada en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo utilizar el específico de la plataforma Android que establece las opciones del editor de métodos de entrada para el teclado en pantalla para una entrada.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 3711e85bd30deb06f351f4539c5ffc7e4236efb6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653601"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opciones del editor de métodos de entrada de entrada en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este conjunto específico de la plataforma Android establece las opciones del editor de métodos de entrada (IME) para [`Entry`](xref:Xamarin.Forms.Entry)el teclado en pantalla para un. Esto incluye la configuración del botón de acción del usuario en la esquina inferior del teclado en pantalla y las interacciones con el `Entry`. Se consume en XAML estableciendo el [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propiedad adjunta a un valor de la [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

El `Entry.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la opción de acción del método de entrada para el teclado en pantalla para el [ `Entry` ](xref:Xamarin.Forms.Entry), con el [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración que proporciona los siguientes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica que se requiere ninguna acción específica de clave y que el control subyacente producirá sí solo si puede. Esto será `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica que ninguna clave de la acción estará disponible.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica que la clave de acción llevará a cabo una operación de "Ir", al usuario en el destino del texto escrito.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica que la clave de acción realiza una operación de "búsqueda", al usuario a los resultados de buscar el texto han escrito.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica que la clave de acción llevará a cabo una operación de "Enviar", que se entrega el texto a su destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica que la clave de acción llevará a cabo una operación de "siguiente", tomar el usuario al siguiente campo que aceptará el texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica que la clave de acción llevará a cabo una operación de "done", el teclado en pantalla de cierre.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica que la clave de acción llevará a cabo una operación de "anterior", tomar el usuario al campo anterior que aceptará el texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – la máscara para seleccionar las opciones de acción.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica que el corrector ortográfico se obtenga la información del usuario, ni sugerir correcciones según lo que el usuario ha escrito anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica que la interfaz de usuario no debería pasar a pantalla completa.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica que no se mostrará ninguna interfaz de usuario para el texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica que no se mostrará ninguna interfaz de usuario para las acciones personalizadas.

El resultado es que un determinado [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) se aplica el valor para el teclado en pantalla para el [ `Entry` ](xref:Xamarin.Forms.Entry), que establece el método de entrada en las opciones del editor:

[![Entrada específica plataforma editor del método de entrada](entry-ime-options-images/entry-imeoptions.png "entrada específica plataforma editor del método de entrada")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "entrada específica plataforma editor del método de entrada")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
