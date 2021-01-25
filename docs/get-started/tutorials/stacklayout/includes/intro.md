---
ms.openlocfilehash: d5acf27dfbda0dcd31b00600cee2f16b98aaa70a
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634797"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`StackLayout`](xref:Xamarin.Forms.StackLayout) de Xamarin.Forms en XAML.
> - Especificar la orientación del elemento `StackLayout`.
> - Controlar la alineación y expansión de las vistas secundarias y dentro del elemento `StackLayout`.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo alinear controles dentro de un [`StackLayout`](xref:Xamarin.Forms.StackLayout). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de vistas secundarias en un elemento StackLayout, con las opciones de alineación y expansión establecidas, en iOS y Android](../images/alignment-expansion-reduced.png "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")](../images/alignment-expansion-large.png#lightbox "StackLayout que contiene instancia de etiqueta con la alineación y la expansión establecidas")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
