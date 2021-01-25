---
ms.openlocfilehash: 24fdf5849e94fb80cad40635a44cc8da434d83cc
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634902"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`Grid`](xref:Xamarin.Forms.Grid) de Xamarin.Forms en XAML.
> - Especificar columnas y filas de la `Grid`.
> - Distribuir contenido en varias columnas o varias filas en la `Grid`.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo diseñar controles dentro de un [`Grid`](xref:Xamarin.Forms.Grid). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una cuadrícula que tiene contenido que abarca varias columnas y filas, en iOS y Android](../images/span-columns-rows.png "Cuadrícula con contenido que abarca filas y columnas")](../images/span-columns-rows-large.png#lightbox "Cuadrícula con contenido que abarca filas y columnas")

También usará la [Recarga activa de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) para ver cambios en la UI sin tener que volver a compilar su aplicación.
