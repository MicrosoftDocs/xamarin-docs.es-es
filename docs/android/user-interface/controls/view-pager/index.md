---
title: ViewPager
description: ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 427ace2043f966b617a258b5f50fa42f943e707e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457658"
---
# <a name="viewpager"></a>ViewPager

_ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar la navegación gestural con ViewPager, con y sin fragmentos. También se describe cómo agregar indicadores de página mediante PagerTitleStrip y PagerTabStrip._

## <a name="overview"></a>Información general

Un escenario común en el desarrollo de aplicaciones es la necesidad de proporcionar a los usuarios la navegación gestural entre las vistas del mismo nivel. En este enfoque, el usuario se desliza hacia la izquierda o la derecha para tener acceso a las páginas de contenido (por ejemplo, en un asistente para la instalación o en una presentación). Puede crear estas vistas de deslizamiento mediante el `ViewPager` widget, disponible en la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). `ViewPager`Es un widget de diseño compuesto por varias vistas secundarias donde cada vista secundaria constituye una página en el diseño: 

[![Capturas de pantallas de la aplicación TreePager con el dedo horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

Normalmente, `ViewPager` se usa junto con [fragmentos](~/android/platform/fragments/index.md); sin embargo, hay algunas situaciones en las que podría querer usar `ViewPager` sin la complejidad agregada de `Fragment` s.

`ViewPager` utiliza un patrón de adaptador para proporcionarle las vistas que se van a mostrar. El adaptador que se usa aquí es conceptualmente similar al que se usa en [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; se proporciona una implementación de `PagerAdapter` para generar las páginas que `ViewPager` muestra al usuario. Las páginas mostradas por `ViewPager` pueden ser `View` s o `Fragment` s. Cuando `View` se muestran s, el adaptador subclases de la `PagerAdapter` clase base de Android. Si `Fragment` se muestran s, el adaptador subclases de Android `FragmentPagerAdapter` . La biblioteca de compatibilidad de Android también incluye `FragmentPagerAdapter` (una subclase de `PagerAdapter` ) para ayudarle con los detalles de la conexión `Fragment` a los datos. 

En esta guía se muestran ambos enfoques: 

- En [Viewpager con vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), se desarrolla una aplicación de [TreePager](/samples/xamarin/monodroid-samples/userinterface-treepager) para mostrar cómo usar `ViewPager` para mostrar vistas de un catálogo de árbol (una galería de imágenes de árboles de deciduous y perenne). 
    `PagerTabStrip`  y `PagerTitleStrip` se usan para mostrar los títulos que ayudan en la navegación de páginas.

- En [Viewpager con fragmentos](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), se desarrolla una aplicación de [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) ligeramente más compleja para demostrar cómo se usa `ViewPager` con `Fragment` para crear una aplicación que presenta problemas matemáticos como tarjetas flash y responde a los datos proporcionados por el usuario. 

## <a name="requirements"></a>Requisitos

Para usar `ViewPager` en el proyecto de la aplicación, debe instalar el paquete de la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough). 

## <a name="architecture"></a>Arquitectura

Se usan tres componentes para implementar la navegación gestural con `ViewPager` :

- ViewPager
- Adapter (Adaptador)
- Indicador de buscapersonas

A continuación se resume cada uno de estos componentes.

### <a name="viewpager"></a>ViewPager

`ViewPager` es un administrador de diseño que muestra una colección de `View` uno en uno. Su trabajo es detectar el gesto de deslizar rápidamente del usuario y navegar a la vista siguiente o anterior según corresponda. Por ejemplo, en la captura de pantalla siguiente se muestra cómo `ViewPager` realizar la transición de una imagen a la siguiente en respuesta a un gesto del usuario: 

[![Primer plano de la aplicación TreePager mostrar una transición entre vistas](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>Adapter (Adaptador)

`ViewPager` extrae sus datos de un *adaptador*. El trabajo del adaptador es crear los `View` s mostrados por `ViewPager` , proporcionándoles según sea necesario. En el diagrama siguiente se muestra este concepto que &ndash; el adaptador crea y rellena `View` s y los proporciona al `ViewPager` . Como `ViewPager` detecta los gestos de deslizamiento del usuario, pide al adaptador que proporcione el adecuado `View` para mostrar: 

[![Diagrama que ilustra cómo el adaptador conecta imágenes y nombres a ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

En este ejemplo concreto, cada `View` se construye a partir de una imagen de árbol y un nombre de árbol antes de pasarse a `ViewPager` . 

### <a name="pager-indicator"></a>Indicador de buscapersonas

`ViewPager` se puede usar para mostrar un conjunto de datos grande (por ejemplo, una galería de imágenes puede contener cientos de imágenes). Para ayudar al usuario a navegar en conjuntos de datos grandes, `ViewPager` a menudo viene acompañado de un *indicador de buscapersonas* que muestra una cadena. La cadena puede ser el título de la imagen, un título o simplemente la posición de la vista actual dentro del conjunto de datos. 

Hay dos vistas que pueden generar esta información de navegación: `PagerTabStrip` y `PagerTitleStrip.` cada una muestra una cadena en la parte superior de un `ViewPager` , y cada una de ellas extrae sus datos del `ViewPager` adaptador de para que siempre permanezcan sincronizados con el que se muestra actualmente `View` . La diferencia entre ellos es que `PagerTabStrip` incluye un indicador visual para la cadena "actual" mientras que no `PagerTitleStrip` (como se muestra en estas capturas de pantallas): 

[![Capturas de pantallas de la aplicación TreePager con PagerTitleStrip y PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

En esta guía se muestra cómo immplement `ViewPager` , adaptadores e indicadores de los componentes de la aplicación e integrarlos para admitir la navegación gestural. 

## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (ejemplo)](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)