---
ms.openlocfilehash: d00ddd66c6b609131c363d8c13c0490b08eff8f0
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690196"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Editor`](xref:Xamarin.Forms.Editor) de Xamarin.Forms en XAML.
> - Responder al texto en el cambio del objeto `Editor`.
> - Personalizar el comportamiento del objeto `Editor`.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo personalizar el comportamiento de un [`Editor`](xref:Xamarin.Forms.Editor). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de un editor con ajuste automático de tamaño, en iOS y Android](../images/customize-behavior.png "Editor con ajuste automático de tamaño")](../images/customize-behavior-large.png#lightbox "Editor con ajuste automático de tamaño")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
