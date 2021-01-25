---
ms.openlocfilehash: 48905340b579381cd1883f18a57ed9e40ca93261
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690021"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Image`](xref:Xamarin.Forms.Image) de Xamarin.Forms en XAML.
> - Personalizar la apariencia de `Image`.
> - Mostrar un archivo de imagen local de cada proyecto de plataforma.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo mostrar una imagen y personalizar su aspecto. En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una vista de imagen que muestra una imagen local, en iOS y Android](../images/local-file.png "Vista de imagen que muestra una imagen local")](../images/local-file-large.png#lightbox "Vista de imagen que muestra una imagen local")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
