---
ms.openlocfilehash: 69b0ddb8e0feec64e99f0019eb0c2f4417c6c2ff
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690017"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Button`](xref:Xamarin.Forms.Button) de Xamarin.Forms en XAML.
> - Responder al elemento `Button` que se pulsa.
> - Cambiar la apariencia del objeto `Button`.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo personalizar un [`Button`](xref:Xamarin.Forms.Button). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de un botón con una apariencia visual modificada, en iOS y Android](../images/change-button-appearance.png "Botón con apariencia modificada")](../images/change-button-appearance-large.png#lightbox "Botón con apariencia modificada")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
