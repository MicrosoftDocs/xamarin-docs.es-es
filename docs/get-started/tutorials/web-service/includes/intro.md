---
ms.openlocfilehash: e46c86def8a22450cf8087cf36a6bb05dd24ef70
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570806"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre las [etiquetas](~/get-started/tutorials/label/index.yml) (Label).
- Tutorial sobre los [botones](~/get-started/tutorials/button/index.yml) (Button).
- Tutorial sobre [CollectionView](~/get-started/tutorials/collectionview/index.yml).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Usar el Administrador de paquetes NuGet para agregar Newtonsoft.Json a un proyecto de Xamarin.Forms.
> - Crear las clases del servicio web.
> - Usar las clases del servicio web.

Va a usar Visual Studio 2019, o Visual Studio para Mac, para crear una aplicación sencilla que muestre cómo recuperar datos de repositorio para .NET de la API web de GitHub. Los datos recuperados se mostrarán en una vista [`CollectionView`](xref:Xamarin.Forms.CollectionView). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de una serie de repositorios para .NET de GitHub, en iOS y Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)
