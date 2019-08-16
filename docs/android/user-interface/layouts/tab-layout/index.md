---
title: Diseños con pestañas
description: Información general sobre los diseños con pestañas en Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 5f67ec30ce04993701634387f7c2023a0f92004f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522351"
---
# <a name="tabbed-layouts"></a>Diseños con pestañas


## <a name="overview"></a>Información general

Las pestañas son un conocido patrón de interfaz de usuario en aplicaciones móviles debido a su simplicidad y facilidad de uso. Proporcionan una manera coherente y sencilla de desplazarse entre varias pantallas en una aplicación. Android tiene varias API para las interfaces con pestañas: 

- **Barra** &ndash; Esto forma parte de un nuevo conjunto de API que se presentó en Android 3,0 (nivel de API 11) con el objetivo de proporcionar una interfaz de navegación y cambio de vista coherente. Se ha trasladado a Android 2,2 (nivel de API 8) con la [biblioteca de compatibilidad de Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

- **PagerTabStrip** Indica las páginas actual, siguiente y anterior `ViewPager`de. &ndash; `ViewPager`solo está disponible a través de la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obtener más información `PagerTabStrip`sobre, vea [ViewPager](~/android/user-interface/controls/view-pager/index.md).

- **Barra de herramientas** es un componente de barra de acción más reciente y flexible `ActionBar`que reemplaza a. &ndash; `Toolbar` `Toolbar`está disponible en Android 5,0 Lollipop o posterior y también está disponible para versiones anteriores de Android mediante el paquete NuGet de la [biblioteca de compatibilidad de Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) . 
    `Toolbar`es actualmente el componente de la barra de acciones recomendado que se usará en las aplicaciones Android.
    Para obtener más información, vea [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Vínculos relacionados

- [Diseño material: pestañas](https://material.io/guidelines/components/tabs.html)- [barra](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Biblioteca de compatibilidad de Android V7 paquete NuGet de NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [V7 biblioteca AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
