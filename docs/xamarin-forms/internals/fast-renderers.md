---
title: Xamarin.FormsRepresentadores rápidos
description: En este artículo se presentan los representadores rápidos, que reducen los costos de inflación y representación de un Xamarin.Forms control en Android mediante el acoplamiento de la jerarquía de control nativa resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 29f79e4aed0314fe1590fa26c8e4b052e14a94d6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198065"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.FormsRepresentadores rápidos

Tradicionalmente, la mayoría de los representadores de control originales en Android se componen de dos vistas:

- Control nativo, como `Button` o `TextView` .
- Contenedor `ViewGroup` que controla algunos de los trabajos de diseño, el control de gestos y otras tareas.

Sin embargo, este enfoque tiene una implicación de rendimiento en que se crean dos vistas para cada control lógico, lo que da como resultado un árbol visual más complejo que requiere más memoria y más procesamiento para representarse en la pantalla.

Los representadores rápidos reducen los costos de inflación y representación de un Xamarin.Forms control en una sola vista. Por lo tanto, en lugar de crear dos vistas y agregarlas al árbol de vista, solo se crea una. Esto mejora el rendimiento al crear menos objetos, lo que, a su vez, significa un árbol de vista menos complejo y menos uso de memoria (lo que también da lugar a menos pausas de recolección de elementos no utilizados).

Los representadores rápidos están disponibles para los siguientes controles en Xamarin.Forms en Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`MediaElement`](xref:Xamarin.Forms.MediaElement)

Funcionalmente, estos representadores rápidos no son diferentes a los representadores heredados. A partir de Xamarin.Forms 4,0 y versiones posteriores, todas las aplicaciones que tienen como destino `FormsAppCompatActivity` usarán estos representadores rápidos de forma predeterminada. Los representadores de todos los controles nuevos, incluidos [`ImageButton`](xref:Xamarin.Forms.ImageButton) y [`CollectionView`](xref:Xamarin.Forms.CollectionView) , usan el enfoque de representador rápido.

Las mejoras de rendimiento cuando se usan representadores rápidos variarán para cada aplicación, en función de la complejidad del diseño. Por ejemplo, las mejoras de rendimiento de x2 son posibles al desplazarse a través de un [`ListView`](xref:Xamarin.Forms.ListView) que contiene miles de filas de datos, donde las celdas de cada fila se componen de controles que usan representadores rápidos, lo que da como resultado un desplazamiento más suave visible.

> [!NOTE]
> Se pueden crear representadores personalizados para los representadores rápidos mediante el mismo enfoque que se usa para los representadores heredados. Para obtener más información, consulte [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Representadores personalizados).

## <a name="backwards-compatibility"></a>Compatibilidad con versiones anteriores

Los representadores rápidos se pueden invalidar con los siguientes enfoques:

1. Habilitar los representadores heredados agregando la siguiente línea de código a la `MainActivity` clase antes de llamar a `Forms.Init` :

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Usar representadores personalizados que tienen como destino los representadores heredados. Los representadores personalizados existentes seguirán funcionando con los representadores heredados.
1. Especificar un distinto `View.Visual` , como `Material` , que usa representadores diferentes. Para obtener más información sobre el material visual, vea el [ Xamarin.Forms material visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
