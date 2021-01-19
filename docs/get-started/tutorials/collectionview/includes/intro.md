---
ms.openlocfilehash: b91538b7eff340315e04dd2972bb181e34310b2b
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558946"
---
Antes de intentar este tutorial, debe haber completado correctamente lo siguiente:

- Inicio rápido [Creación de una primera aplicación de Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial sobre el [diseño de pila](~/get-started/tutorials/stacklayout/index.yml) (StackLayout).
- Tutorial sobre la [cuadrícula](~/get-started/tutorials/grid/index.yml) (Grid).
- Tutorial sobre las [etiquetas](~/get-started/tutorials/label/index.yml) (Label).
- Tutorial sobre las [imágenes](~/get-started/tutorials/image/index.yml) (Image).

En este tutorial aprenderá a:

> [!div class="checklist"]
>
> - Crear una instancia de [`CollectionView`](xref:Xamarin.Forms.CollectionView) de Xamarin.Forms en XAML.
> - Rellenar el objeto `CollectionView` con datos.
> - Responder a elementos de `CollectionView` que están seleccionados.
> - Personalizar la apariencia de los elementos `CollectionView`.

Va a usar Visual Studio 2019, o bien Visual Studio para Mac, para crear una aplicación sencilla que demuestre cómo personalizar el aspecto de un elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView). En las capturas de pantalla siguientes se muestra la aplicación final:

[![Captura de pantalla de un elemento CollectionView cuyos elementos están basados en una plantilla de datos](../images/customize-item-appearance-reduced.png "CollectionView que muestra datos basados en plantilla")](../images/customize-item-appearance-large.png#lightbox "CollectionView que muestra datos basados en plantilla")
