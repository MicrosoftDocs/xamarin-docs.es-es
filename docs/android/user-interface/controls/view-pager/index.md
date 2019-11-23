---
title: ViewPager
description: ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 600a94a0ee9eb5bcf06dc19d95cf9e77132a2e81
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029062"
---
# <a name="viewpager"></a>ViewPager

_ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip._

## <a name="overview"></a>Información general

Un escenario común en el desarrollo de aplicaciones es la necesidad de proporcionar a los usuarios la navegación gestural entre las vistas del mismo nivel. En este enfoque, el usuario se desliza hacia la izquierda o la derecha para tener acceso a las páginas de contenido (por ejemplo, en un asistente para la instalación o en una presentación). Puede crear estas vistas deslizantes mediante el widget `ViewPager`, disponible en la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). El `ViewPager` es un widget de diseño compuesto por varias vistas secundarias donde cada vista secundaria constituye una página en el diseño: 

[![capturas de pantallas de la aplicación TreePager con el dedo horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Normalmente, `ViewPager` se utiliza junto con [fragmentos](~/android/platform/fragments/index.md); sin embargo, hay algunas situaciones en las que podría querer usar `ViewPager` sin la complejidad adicional de `Fragment`s.

`ViewPager` usa un patrón de adaptador para proporcionarle las vistas que se van a mostrar. El adaptador que se usa aquí es conceptualmente similar al que usa [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; proporciona una implementación de `PagerAdapter` para generar las páginas que el `ViewPager` muestra al usuario. Las páginas mostradas por `ViewPager` pueden ser `View`s o `Fragment`s. Cuando se muestran `View`s, el adaptador subclases de la clase base `PagerAdapter` de Android. Si se muestran `Fragment`s, el adaptador subclase el `FragmentPagerAdapter`de Android. La biblioteca de compatibilidad de Android también incluye `FragmentPagerAdapter` (una subclase de `PagerAdapter`) para ayudarle con los detalles de la conexión de `Fragment`s a los datos. 

En esta guía se muestran ambos enfoques: 

- En [Viewpager con vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), se desarrolla una aplicación de [TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager) para demostrar cómo usar `ViewPager` para mostrar vistas de un catálogo de árbol (una galería de imágenes de árboles de deciduous y perenne). 
    `PagerTabStrip` y `PagerTitleStrip` se usan para mostrar los títulos que ayudan en la navegación por la página.

- En [Viewpager con fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), se desarrolla una aplicación de [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) ligeramente más compleja para mostrar cómo usar `ViewPager` con `Fragment`s para compilar una aplicación que presenta problemas matemáticos como tarjetas flash y responde a los datos proporcionados por el usuario. 

## <a name="requirements"></a>Requisitos

Para usar `ViewPager` en el proyecto de la aplicación, debe instalar el paquete de la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Arquitectura

Se usan tres componentes para implementar la navegación gestural con `ViewPager`:

- ViewPager
- Adaptador
- Indicador de buscapersonas

A continuación se resume cada uno de estos componentes.

### <a name="viewpager"></a>ViewPager

`ViewPager` es un administrador de diseño que muestra una colección de `View`s de una en una. Su trabajo es detectar el gesto de deslizar rápidamente del usuario y navegar a la vista siguiente o anterior según corresponda. Por ejemplo, en la captura de pantalla siguiente se muestra un `ViewPager` realizar la transición de una imagen a la siguiente en respuesta a un gesto del usuario: 

[![primer plano de la aplicación TreePager mostrar una transición entre vistas](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adaptador

`ViewPager` extrae sus datos de un *adaptador*. El trabajo del adaptador es crear los `View`s que muestra el `ViewPager`, proporcionándoles según sea necesario. En el diagrama siguiente se muestra este concepto &ndash; el adaptador crea y rellena `View`s y los proporciona al `ViewPager`. Como el `ViewPager` detecta los gestos de deslizamiento del usuario, pide al adaptador que proporcione el `View` adecuado para mostrar: 

[Diagrama de ![que ilustra cómo el adaptador conecta imágenes y nombres a ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

En este ejemplo concreto, cada `View` se construye a partir de una imagen de árbol y un nombre de árbol antes de pasarse al `ViewPager`. 

### <a name="pager-indicator"></a>Indicador de buscapersonas

`ViewPager` puede usarse para mostrar un conjunto de datos grande (por ejemplo, una galería de imágenes puede contener cientos de imágenes). Para ayudar al usuario a navegar en conjuntos de datos grandes, `ViewPager` suele ir acompañado de un *indicador de buscapersonas* que muestra una cadena. La cadena puede ser el título de la imagen, un título o simplemente la posición de la vista actual dentro del conjunto de datos. 

Hay dos vistas que pueden generar esta información de navegación: `PagerTabStrip` y `PagerTitleStrip.` muestra una cadena en la parte superior de una `ViewPager`y cada una de ellas extrae sus datos del adaptador del `ViewPager`para que siempre permanezcan sincronizadas con el `View`que se muestra actualmente. La diferencia entre ellos es que `PagerTabStrip` incluye un indicador visual para la cadena "actual", mientras que `PagerTitleStrip` no (como se muestra en estas capturas de pantallas): 

[![capturas de pantallas de la aplicación TreePager con PagerTitleStrip y PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

En esta guía se muestra cómo immplement `ViewPager`, adaptador y componentes de aplicación de indicador e integrarlos para admitir la navegación de gestural. 

## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
