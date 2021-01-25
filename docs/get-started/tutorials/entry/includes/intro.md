---
ms.openlocfilehash: 1ff81c0399a0abb321831f8a72dfb2a8560816c4
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690186"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Entry`](xref:Xamarin.Forms.Entry) de Xamarin.Forms en XAML.
> - Responder al texto en el cambio del objeto `Entry`.
> - Personalizar el comportamiento del objeto `Entry`.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo personalizar el comportamiento de un [`Entry`](xref:Xamarin.Forms.Entry). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una entrada con texto enmascarado por caracteres de contraseña, en iOS y Android](../images/customize-behavior.png "Entrada con caracteres de contraseña enmascarados")](../images/customize-behavior-large.png#lightbox "Entrada con caracteres de contraseña enmascarados")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
