---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 9c80535dfddd2a279fac66ab159c2a600681bb71
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457008"
---
# <a name="gridviewpager"></a>GridViewPager

En el ejemplo [GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager) se muestra cómo implementar el modelo de navegación del selector 2D para el desgaste de Android.

![Captura de pantalla de ejemplo de GridViewPager en una visualización cuadrada](gridviewpager-images/gridviewpager.png)

En primer lugar, agregue el paquete NuGet de [soporte técnico de Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Wear/) para el proyecto.

El XML de diseño tiene el siguiente aspecto:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Crear un [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(o una subclase como [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para proporcionar las vistas que se van a mostrar cuando el usuario navega.

El [adaptador de ejemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) muestra cómo implementar los métodos necesarios, incluidas las invalidaciones para `RowCount` , `GetColumnCount` , `GetBackground` y `GetFragment`

Conecte el adaptador como se muestra a continuación:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```

## <a name="related-links"></a>Vínculos relacionados

- [Documento del selector de 2D de Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documentos de Android. support. portátil](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (ejemplo)](/samples/xamarin/monodroid-samples/wear-gridviewpager)