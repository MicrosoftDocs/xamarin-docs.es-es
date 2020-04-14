---
ms.openlocfilehash: cae1ec70880fe694584e14b732608725bcbb1d91
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "71059808"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre las [entradas](~/get-started/tutorials/entry/index.yml) (Entry).

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> - Responder a una aplicación que se inicia, está en modo de suspensión o se reanuda.
> - Conservar datos entre los cambios de estado del ciclo de vida.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo conservar los datos entre los cambios de estado del ciclo de vida. En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida, en iOS y Android](../images/persist-data.png "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")](../images/persist-data-large.png#lightbox "Entrada cuya propiedad Text persiste a través de los cambios de estado del ciclo de vida")
