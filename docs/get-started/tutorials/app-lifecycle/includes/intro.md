---
ms.openlocfilehash: cae1ec70880fe694584e14b732608725bcbb1d91
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059808"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre las [entradas](~/get-started/tutorials/entry/index.yml) (Entry).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Responder a una aplicación que se inicia, está en modo de suspensión o se reanuda.
> - Conservar datos entre los cambios de estado del ciclo de vida.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo conservar los datos entre los cambios de estado del ciclo de vida. En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de un objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida, en iOS y Android](../images/persist-data.png "Objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida")](../images/persist-data-large.png#lightbox "Objeto Entry cuya propiedad Text se conserva a lo largo de los cambios de estado del ciclo de vida")
