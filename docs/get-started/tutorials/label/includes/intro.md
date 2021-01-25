---
ms.openlocfilehash: 4922151676190c9f79b640d9c32887b811c842b1
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689987"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Label`](xref:Xamarin.Forms.Label) de Xamarin.Forms en XAML.
> - Cambiar la apariencia del objeto `Label`.
> - Presentar texto, en un solo objeto `Label`, que tiene varios formatos.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo mostrar texto en [`Label`](xref:Xamarin.Forms.Label). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una etiqueta que muestra texto con formato, en iOS y Android](../images/label-formatted-text.png "Etiqueta con texto con formato")](../images/label-formatted-text-large.png#lightbox "Etiqueta con texto con formato")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
