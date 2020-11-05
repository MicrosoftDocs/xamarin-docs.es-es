---
title: Opciones del editor de métodos de entrada de entrada en Android
description: Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados. En este artículo se explica cómo utilizar el específico de la plataforma Android que establece las opciones del editor de métodos de entrada para el teclado en pantalla para una entrada.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79ceca6f028ec5d0399251e178d82d1b2fff81ef
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373346"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opciones del editor de métodos de entrada de entrada en Android

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Este conjunto específico de la plataforma Android establece las opciones del editor de métodos de entrada (IME) para el teclado en pantalla para un [`Entry`](xref:Xamarin.Forms.Entry) . Esto incluye establecer el botón acción del usuario en la esquina inferior del teclado en pantalla y las interacciones con `Entry` . Se consume en XAML estableciendo la [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propiedad adjunta en un valor de la [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, se puede usar desde C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

El `Entry.On<Android>` método especifica que este específico de la plataforma solo se ejecutará en Android. [ `Entry.SetImeOptions` ] (XREF: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. entry. SetImeOptions ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Entry}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. ImeFlags)), en el [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la opción de acción del método de entrada para el teclado en pantalla para el [`Entry`](xref:Xamarin.Forms.Entry) , con la [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración que proporciona los valores siguientes:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica que no se requiere ninguna clave de acción específica y que el control subyacente producirá su propia si es posible. Será `Next` o `Done` .
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica que no habrá ninguna clave de acción disponible.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica que la clave de acción llevará a cabo una operación de "Go", que tomará al usuario el destino del texto que escribió.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica que la clave de acción realiza una operación de "búsqueda", tomando al usuario el resultado de buscar el texto que ha escrito.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica que la clave de acción llevará a cabo una operación de "envío", entregando el texto a su destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica que la clave de acción llevará a cabo una operación "siguiente", que tomará el usuario en el siguiente campo que aceptará texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica que la clave de acción llevará a cabo una operación "Done", cerrando el teclado en pantalla.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica que la clave de acción realizará una operación "anterior" y tomará el usuario en el campo anterior que aceptará texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) : la máscara para seleccionar las opciones de acción.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica que el corrector ortográfico no aprenderá del usuario ni sugerirá correcciones en función de lo que el usuario haya escrito previamente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica que la interfaz de usuario no debe ir a pantalla completa.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica que no se mostrará ninguna interfaz de usuario para el texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica que no se mostrará ninguna interfaz de usuario para las acciones personalizadas.

El resultado es que [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) se aplica un valor especificado al teclado en pantalla para [`Entry`](xref:Xamarin.Forms.Entry) , que establece las opciones del editor de métodos de entrada:

[![Específico de la plataforma del editor de métodos de entrada de entrada](entry-ime-options-images/entry-imeoptions.png "Específico de la plataforma del editor de métodos de entrada de entrada")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "Específico de la plataforma del editor de métodos de entrada de entrada")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API de AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)